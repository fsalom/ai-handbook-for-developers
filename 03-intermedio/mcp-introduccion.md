# 🔌 Model Context Protocol (MCP)

> **Conectando LLMs con el mundo real.** MCP es el protocolo que permite a los LLMs interactuar con herramientas, bases de datos, APIs y sistemas externos. Aprende a crear conexiones potentes entre IA y tus aplicaciones.

## 🎯 ¿Qué aprenderás aquí?

- ✅ Qué es MCP y por qué es revolucionario
- ✅ Arquitectura y componentes principales
- ✅ Casos de uso reales en desarrollo
- ✅ Tu primer MCP desde cero
- ✅ MCPs populares y cómo integrarlos
- ✅ Best practices para producción

## 🧩 **¿Qué es Model Context Protocol?**

### **El problema que resuelve:**

**❌ Antes de MCP:**
```
LLM → Solo texto → Respuesta texto
```
- LLMs aislados del mundo real
- No pueden acceder a datos actuales
- No pueden ejecutar acciones
- Información limitada al training data

**✅ Con MCP:**
```
LLM ↔ MCP Server ↔ Herramientas externas
                  ↔ Bases de datos
                  ↔ APIs
                  ↔ Sistemas de archivos
```

### **Definición técnica:**
MCP es un protocolo estándar que permite a los Large Language Models acceder a **contexto externo** de forma segura y estructurada. Actúa como un puente entre la IA y las herramientas que necesitas en tu workflow.

### **Componentes principales:**

| Componente | Función | Ejemplo |
|------------|---------|---------|
| **MCP Host** | Cliente que usa LLM | Claude Desktop, VSCode |
| **MCP Server** | Proveedor de herramientas | Database connector, File system |
| **Resources** | Datos accesibles | Archivos, URLs, DB records |
| **Tools** | Acciones ejecutables | SQL queries, API calls |
| **Prompts** | Templates reutilizables | Code review, analysis |

## 🏗️ **Arquitectura MCP**

### **Flujo de comunicación:**

```mermaid
graph LR
    A[Usuario] --> B[LLM Host]
    B --> C[MCP Client]
    C --> D[MCP Server]
    D --> E[External Tool]
    E --> D
    D --> C
    C --> B
    B --> A
```

### **Protocolo de intercambio:**

**1. Discovery:**
```json
{
  "jsonrpc": "2.0",
  "method": "tools/list",
  "params": {}
}
```

**2. Tool execution:**
```json
{
  "jsonrpc": "2.0",
  "method": "tools/call",
  "params": {
    "name": "execute_sql",
    "arguments": {
      "query": "SELECT * FROM users WHERE active = 1"
    }
  }
}
```

**3. Response:**
```json
{
  "jsonrpc": "2.0",
  "result": {
    "content": [
      {
        "type": "text",
        "text": "Found 150 active users..."
      }
    ]
  }
}
```

## 💼 **Casos de uso reales**

### **1. Database Integration**
```python
# Con MCP puedes hacer esto:
user_prompt = "Muéstrame los usuarios más activos esta semana"

# El LLM automáticamente:
# 1. Identifica que necesita datos de DB
# 2. Construye la query SQL apropiada
# 3. Ejecuta via MCP server
# 4. Interpreta resultados
# 5. Responde en lenguaje natural
```

### **2. Code Analysis**
```typescript
// El LLM puede:
// 1. Leer tu codebase via MCP
// 2. Analizar arquitectura
// 3. Identificar problemas
// 4. Sugerir mejoras específicas
// 5. Generar refactoring plans
```

### **3. API Orchestration**
```bash
# Prompt: "Crea un usuario en GitHub y añádelo al proyecto X"
# MCP automáticamente:
# 1. Calls GitHub API to create user
# 2. Calls project management API
# 3. Handles authentication
# 4. Reports status
```

## 🛠️ **Tu primer MCP Server**

### **MCP Server básico - Python**

**Instalación:**
```bash
pip install mcp anthropic
```

**Código completo:**
```python
#!/usr/bin/env python3

import asyncio
import json
import sys
from typing import Any, Dict, List, Optional
from mcp.server import Server
from mcp.types import (
    Tool,
    TextContent,
    CallToolResult,
    Resource,
    GetResourceResult
)
import sqlite3
import os
from datetime import datetime

class DatabaseMCPServer:
    def __init__(self, db_path: str = "example.db"):
        self.db_path = db_path
        self.server = Server("database-mcp")
        self.setup_database()
        self.register_handlers()

    def setup_database(self):
        """Initialize example database"""
        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()

        # Create tables
        cursor.execute('''
        CREATE TABLE IF NOT EXISTS users (
            id INTEGER PRIMARY KEY,
            name TEXT NOT NULL,
            email TEXT UNIQUE,
            created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
            active BOOLEAN DEFAULT 1
        )
        ''')

        cursor.execute('''
        CREATE TABLE IF NOT EXISTS projects (
            id INTEGER PRIMARY KEY,
            name TEXT NOT NULL,
            description TEXT,
            owner_id INTEGER,
            status TEXT DEFAULT 'active',
            FOREIGN KEY (owner_id) REFERENCES users (id)
        )
        ''')

        # Insert sample data if empty
        cursor.execute('SELECT COUNT(*) FROM users')
        if cursor.fetchone()[0] == 0:
            sample_users = [
                ('Alice Johnson', 'alice@example.com'),
                ('Bob Smith', 'bob@example.com'),
                ('Carol Davis', 'carol@example.com')
            ]
            cursor.executemany(
                'INSERT INTO users (name, email) VALUES (?, ?)',
                sample_users
            )

        conn.commit()
        conn.close()

    def register_handlers(self):
        """Register MCP handlers"""

        @self.server.list_tools()
        async def list_tools() -> List[Tool]:
            """List available database tools"""
            return [
                Tool(
                    name="execute_sql",
                    description="Execute SQL query on the database",
                    inputSchema={
                        "type": "object",
                        "properties": {
                            "query": {
                                "type": "string",
                                "description": "SQL query to execute"
                            },
                            "params": {
                                "type": "array",
                                "description": "Parameters for the query",
                                "items": {"type": "string"}
                            }
                        },
                        "required": ["query"]
                    }
                ),
                Tool(
                    name="get_table_schema",
                    description="Get schema information for a table",
                    inputSchema={
                        "type": "object",
                        "properties": {
                            "table_name": {
                                "type": "string",
                                "description": "Name of the table"
                            }
                        },
                        "required": ["table_name"]
                    }
                ),
                Tool(
                    name="list_tables",
                    description="List all tables in the database",
                    inputSchema={
                        "type": "object",
                        "properties": {}
                    }
                )
            ]

        @self.server.call_tool()
        async def call_tool(name: str, arguments: Dict[str, Any]) -> CallToolResult:
            """Execute tool calls"""

            if name == "execute_sql":
                return await self._execute_sql(
                    arguments.get("query", ""),
                    arguments.get("params", [])
                )

            elif name == "get_table_schema":
                return await self._get_table_schema(
                    arguments.get("table_name", "")
                )

            elif name == "list_tables":
                return await self._list_tables()

            else:
                return CallToolResult(
                    content=[TextContent(
                        type="text",
                        text=f"Unknown tool: {name}"
                    )]
                )

        @self.server.list_resources()
        async def list_resources() -> List[Resource]:
            """List available resources"""
            return [
                Resource(
                    uri=f"db://{self.db_path}/schema",
                    name="Database Schema",
                    description="Complete database schema information",
                    mimeType="application/json"
                ),
                Resource(
                    uri=f"db://{self.db_path}/stats",
                    name="Database Statistics",
                    description="Database usage statistics",
                    mimeType="application/json"
                )
            ]

        @self.server.get_resource()
        async def get_resource(uri: str) -> GetResourceResult:
            """Get resource content"""
            if uri.endswith("/schema"):
                schema = await self._get_complete_schema()
                return GetResourceResult(
                    contents=[TextContent(
                        type="text",
                        text=json.dumps(schema, indent=2)
                    )]
                )

            elif uri.endswith("/stats"):
                stats = await self._get_database_stats()
                return GetResourceResult(
                    contents=[TextContent(
                        type="text",
                        text=json.dumps(stats, indent=2)
                    )]
                )

            else:
                return GetResourceResult(
                    contents=[TextContent(
                        type="text",
                        text=f"Resource not found: {uri}"
                    )]
                )

    async def _execute_sql(self, query: str, params: List[str] = None) -> CallToolResult:
        """Execute SQL query safely"""
        if params is None:
            params = []

        # Basic SQL injection protection
        dangerous_keywords = ['DROP', 'DELETE', 'TRUNCATE', 'ALTER', 'CREATE']
        query_upper = query.upper()

        for keyword in dangerous_keywords:
            if keyword in query_upper:
                return CallToolResult(
                    content=[TextContent(
                        type="text",
                        text=f"Dangerous SQL keyword '{keyword}' not allowed"
                    )]
                )

        try:
            conn = sqlite3.connect(self.db_path)
            conn.row_factory = sqlite3.Row  # For column names
            cursor = conn.cursor()

            cursor.execute(query, params)

            if query_upper.strip().startswith('SELECT'):
                results = cursor.fetchall()

                if results:
                    # Convert to dict for JSON serialization
                    data = [dict(row) for row in results]
                    response = {
                        "query": query,
                        "row_count": len(results),
                        "data": data
                    }
                else:
                    response = {
                        "query": query,
                        "row_count": 0,
                        "data": []
                    }
            else:
                conn.commit()
                response = {
                    "query": query,
                    "affected_rows": cursor.rowcount,
                    "message": "Query executed successfully"
                }

            conn.close()

            return CallToolResult(
                content=[TextContent(
                    type="text",
                    text=json.dumps(response, indent=2)
                )]
            )

        except Exception as e:
            return CallToolResult(
                content=[TextContent(
                    type="text",
                    text=f"SQL Error: {str(e)}"
                )]
            )

    async def _get_table_schema(self, table_name: str) -> CallToolResult:
        """Get schema for specific table"""
        try:
            conn = sqlite3.connect(self.db_path)
            cursor = conn.cursor()

            cursor.execute(f"PRAGMA table_info({table_name})")
            columns = cursor.fetchall()

            if not columns:
                return CallToolResult(
                    content=[TextContent(
                        type="text",
                        text=f"Table '{table_name}' not found"
                    )]
                )

            schema = {
                "table_name": table_name,
                "columns": [
                    {
                        "name": col[1],
                        "type": col[2],
                        "not_null": bool(col[3]),
                        "default_value": col[4],
                        "primary_key": bool(col[5])
                    }
                    for col in columns
                ]
            }

            conn.close()

            return CallToolResult(
                content=[TextContent(
                    type="text",
                    text=json.dumps(schema, indent=2)
                )]
            )

        except Exception as e:
            return CallToolResult(
                content=[TextContent(
                    type="text",
                    text=f"Schema Error: {str(e)}"
                )]
            )

    async def _list_tables(self) -> CallToolResult:
        """List all tables"""
        try:
            conn = sqlite3.connect(self.db_path)
            cursor = conn.cursor()

            cursor.execute("""
                SELECT name FROM sqlite_master
                WHERE type='table' AND name NOT LIKE 'sqlite_%'
            """)

            tables = [row[0] for row in cursor.fetchall()]
            conn.close()

            return CallToolResult(
                content=[TextContent(
                    type="text",
                    text=json.dumps({"tables": tables}, indent=2)
                )]
            )

        except Exception as e:
            return CallToolResult(
                content=[TextContent(
                    type="text",
                    text=f"Error listing tables: {str(e)}"
                )]
            )

    async def _get_complete_schema(self) -> Dict[str, Any]:
        """Get complete database schema"""
        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()

        # Get all tables
        cursor.execute("""
            SELECT name FROM sqlite_master
            WHERE type='table' AND name NOT LIKE 'sqlite_%'
        """)

        tables = {}
        for (table_name,) in cursor.fetchall():
            cursor.execute(f"PRAGMA table_info({table_name})")
            columns = cursor.fetchall()

            tables[table_name] = {
                "columns": [
                    {
                        "name": col[1],
                        "type": col[2],
                        "not_null": bool(col[3]),
                        "default_value": col[4],
                        "primary_key": bool(col[5])
                    }
                    for col in columns
                ]
            }

        conn.close()
        return {"schema": tables}

    async def _get_database_stats(self) -> Dict[str, Any]:
        """Get database statistics"""
        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()

        stats = {
            "file_size": os.path.getsize(self.db_path),
            "tables": {}
        }

        # Get table counts
        cursor.execute("""
            SELECT name FROM sqlite_master
            WHERE type='table' AND name NOT LIKE 'sqlite_%'
        """)

        for (table_name,) in cursor.fetchall():
            cursor.execute(f"SELECT COUNT(*) FROM {table_name}")
            count = cursor.fetchone()[0]
            stats["tables"][table_name] = {"row_count": count}

        conn.close()
        return stats

    async def run(self):
        """Run the MCP server"""
        async with self.server.stdio() as streams:
            await self.server.run(
                streams[0], streams[1], self.server.create_initialization_options()
            )

async def main():
    server = DatabaseMCPServer()
    await server.run()

if __name__ == "__main__":
    asyncio.run(main())
```

### **Configuración en Claude Desktop**

**1. Archivo de configuración (`claude_desktop_config.json`):**
```json
{
  "mcpServers": {
    "database": {
      "command": "python",
      "args": ["/path/to/your/database_mcp_server.py"],
      "env": {
        "DATABASE_PATH": "/path/to/your/database.db"
      }
    }
  }
}
```

**2. Ubicación del archivo:**
- **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`

### **Probando tu MCP**

**1. Reinicia Claude Desktop**

**2. Verifica la conexión:**
```
"Muéstrame qué tablas tienes disponibles"
```

**3. Ejecuta consultas:**
```
"¿Cuántos usuarios activos tengo?"
"Muéstrame los proyectos más recientes"
"Crea un reporte de usuarios por mes"
```

## 🌟 **MCPs populares**

### **1. Filesystem MCP**
```bash
# Instalación
npm install @anthropic-ai/mcp-server-filesystem

# Configuración
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["@anthropic-ai/mcp-server-filesystem", "/path/to/allowed/directory"]
    }
  }
}
```

**Casos de uso:**
- Análisis de código
- Documentación automática
- Refactoring asistido
- Code review

### **2. GitHub MCP**
```bash
# Instalación
npm install @anthropic-ai/mcp-server-github

# Configuración
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["@anthropic-ai/mcp-server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "your-token"
      }
    }
  }
}
```

**Casos de uso:**
- Issue management
- PR creation y review
- Repository analysis
- Workflow automation

### **3. PostgreSQL MCP**
```python
# Ejemplo de prompt con PostgreSQL MCP
"Analiza las performance de queries en mi base de datos PostgreSQL.
Identifica las consultas más lentas y sugiere optimizaciones."

# El MCP automáticamente:
# 1. Conecta a PostgreSQL
# 2. Ejecuta EXPLAIN ANALYZE
# 3. Revisa índices
# 4. Sugiere mejoras específicas
```

### **4. Slack MCP**
```json
{
  "mcpServers": {
    "slack": {
      "command": "npx",
      "args": ["@anthropic-ai/mcp-server-slack"],
      "env": {
        "SLACK_BOT_TOKEN": "xoxb-your-token",
        "SLACK_APP_TOKEN": "xapp-your-token"
      }
    }
  }
}
```

**Casos de uso:**
- Automated reporting
- Team communication
- Incident response
- Meeting scheduling

## 🎨 **Casos de uso avanzados**

### **1. Code Analysis Pipeline**
```
Prompt: "Analiza mi proyecto React y crea un reporte de calidad"

Flujo MCP:
1. Filesystem MCP → Lee todo el código
2. Database MCP → Guarda métricas
3. GitHub MCP → Compara con issues
4. Slack MCP → Envía reporte al equipo
```

### **2. Automated DevOps**
```python
# MCP para deployment automation
{
  "deploy_app": {
    "steps": [
      "Check GitHub for new commits",
      "Run tests via CI/CD API",
      "Deploy to staging",
      "Run smoke tests",
      "Deploy to production",
      "Notify team via Slack"
    ]
  }
}
```

### **3. Data Analysis Workflow**
```sql
-- El LLM puede hacer esto automáticamente:
-- 1. Conectar a multiple databases
-- 2. Cross-reference data
-- 3. Generate insights
-- 4. Create visualizations
-- 5. Export reports
```

## 🔧 **Best practices para producción**

### **1. Security considerations**

```python
class SecureMCPServer:
    def __init__(self):
        self.allowed_operations = {
            'read': ['SELECT'],
            'write': ['INSERT', 'UPDATE'],
            'admin': ['CREATE', 'DROP', 'ALTER']
        }

    async def validate_query(self, query: str, user_role: str) -> bool:
        query_type = query.strip().upper().split()[0]
        return query_type in self.allowed_operations.get(user_role, [])

    async def sanitize_input(self, input_data: str) -> str:
        # Remove dangerous characters
        dangerous_chars = [';', '--', '/*', '*/', 'xp_', 'sp_']
        for char in dangerous_chars:
            input_data = input_data.replace(char, '')
        return input_data
```

### **2. Performance optimization**

```python
class OptimizedMCPServer:
    def __init__(self):
        self.connection_pool = {}
        self.query_cache = {}

    async def get_connection(self, db_name: str):
        if db_name not in self.connection_pool:
            self.connection_pool[db_name] = await create_connection(db_name)
        return self.connection_pool[db_name]

    async def cached_query(self, query: str, ttl: int = 300):
        cache_key = hashlib.md5(query.encode()).hexdigest()

        if cache_key in self.query_cache:
            result, timestamp = self.query_cache[cache_key]
            if time.time() - timestamp < ttl:
                return result

        # Execute query and cache result
        result = await self.execute_query(query)
        self.query_cache[cache_key] = (result, time.time())
        return result
```

### **3. Error handling robusto**

```python
async def robust_tool_execution(self, tool_name: str, args: Dict) -> CallToolResult:
    try:
        # Validate arguments
        validation_result = await self.validate_arguments(tool_name, args)
        if not validation_result.valid:
            return CallToolResult(
                content=[TextContent(
                    type="text",
                    text=f"Invalid arguments: {validation_result.error}"
                )]
            )

        # Execute with timeout
        result = await asyncio.wait_for(
            self.execute_tool(tool_name, args),
            timeout=30.0
        )

        return result

    except asyncio.TimeoutError:
        return CallToolResult(
            content=[TextContent(
                type="text",
                text="Tool execution timed out"
            )]
        )
    except Exception as e:
        # Log error for debugging
        logger.error(f"Tool execution failed: {tool_name}, {args}, {e}")

        return CallToolResult(
            content=[TextContent(
                type="text",
                text=f"Tool execution failed: {str(e)}"
            )]
        )
```

### **4. Monitoring y logging**

```python
import logging
import time
from functools import wraps

def log_mcp_operations(func):
    @wraps(func)
    async def wrapper(*args, **kwargs):
        start_time = time.time()

        try:
            result = await func(*args, **kwargs)

            logging.info(f"MCP Operation: {func.__name__}, "
                        f"Duration: {time.time() - start_time:.2f}s, "
                        f"Status: Success")

            return result

        except Exception as e:
            logging.error(f"MCP Operation: {func.__name__}, "
                         f"Duration: {time.time() - start_time:.2f}s, "
                         f"Status: Error, "
                         f"Error: {str(e)}")
            raise

    return wrapper

class MonitoredMCPServer(DatabaseMCPServer):
    @log_mcp_operations
    async def call_tool(self, name: str, arguments: Dict[str, Any]) -> CallToolResult:
        return await super().call_tool(name, arguments)
```

## 🚀 **¿Qué sigue?**

Ya dominas MCP básico. Ahora vamos a explorar herramientas de desarrollo más avanzadas:

**➡️ [Siguiente: Herramientas de Desarrollo](./herramientas-desarrollo.md)**

---

## 📚 **Recursos adicionales**

### **Documentación oficial:**
- [MCP Specification](https://spec.modelcontextprotocol.io/)
- [Anthropic MCP Docs](https://modelcontextprotocol.io/)
- [GitHub MCP Examples](https://github.com/anthropics/mcp-examples)

### **MCPs community:**
- [Awesome MCP](https://github.com/punkpeye/awesome-mcp)
- [MCP Registry](https://mcp.directory/)
- [Community Discord](https://discord.gg/anthropic)

### **Templates y boilerplates:**
- [Python MCP Template](https://github.com/anthropics/mcp-python-template)
- [TypeScript MCP Template](https://github.com/anthropics/mcp-typescript-template)
- [Go MCP Template](https://github.com/anthropics/mcp-go-template)

---

*MCP es el futuro de la integración de IA. Con estas bases, puedes conectar LLMs a cualquier sistema o herramienta, creando workflows completamente automatizados y productivos.*