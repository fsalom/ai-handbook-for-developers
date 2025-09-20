# 🛠️ MCPs Avanzados

> **Conectando IA con tu stack único.** Ya conoces MCPs básicos. Ahora vamos a crear MCPs personalizados para tus herramientas específicas, implementar seguridad robusta, optimizar rendimiento y crear ecosistemas de MCPs que escalen en producción.

## 🎯 ¿Qué aprenderás aquí?

- ✅ **MCPs personalizados:** Cuándo y cómo construir para tu stack específico
- ✅ **Seguridad avanzada:** Autenticación, autorización, limitación de velocidad
- ✅ **Optimización de rendimiento:** Caché, agrupación de conexiones, procesamiento asíncrono
- ✅ **Estrategias de pruebas:** Cómo probar MCPs de manera efectiva
- ✅ **Patrones de despliegue:** CI/CD, monitoreo, escalamiento
- ✅ **Ecosistemas MCP:** Orquestación de múltiples MCPs

## 🔧 **MCPs Personalizados: Cuándo vale la inversión**

### **Matriz de decisión: Construir vs Comprar vs Adaptar**

| **Factor** | **Construir Personalizado** | **Usar Existente** | **Adaptar Existente** |
|------------|---------------------------|-------------------|---------------------|
| **Popularidad de herramienta** | Propietaria/nicho | Ampliamente usada | Popular pero personalizada |
| **Lógica de negocio** | Compleja/única | Estándar | Alguna personalización |
| **Tiempo de desarrollo** | 2-8 semanas | 1 día | 1-2 semanas |
| **Mantenimiento** | Propiedad completa | Comunidad | Compartido |
| **Ventaja competitiva** | Alta | Ninguna | Media |
| **Experiencia del equipo** | Alta | Cualquiera | Media |

### **Cálculo de ROI para MCPs personalizados:**

```python
def calculate_mcp_roi(
    development_hours,
    developer_hourly_rate,
    time_saved_per_use,
    uses_per_month,
    team_size,
    months_to_evaluate
):
    development_cost = development_hours * developer_hourly_rate

    monthly_time_saved = time_saved_per_use * uses_per_month * team_size
    monthly_value = monthly_time_saved * developer_hourly_rate

    total_value = monthly_value * months_to_evaluate
    roi = (total_value - development_cost) / development_cost

    break_even_months = development_cost / monthly_value

    return {
        'roi_percentage': roi * 100,
        'break_even_months': break_even_months,
        'total_value': total_value
    }
```

### **Casos de uso para MCPs personalizados:**

**🟢 Escenarios de ROI alto:**
- **Herramientas internas:** Personalizaciones de JIRA, APIs internos
- **Sistemas propietarios:** Bases de datos específicas de empresa, sistemas legacy
- **Automatización de flujos:** Procesos multi-paso únicos de tu empresa
- **Requisitos de cumplimiento:** Regulaciones específicas de industria

**🟡 Escenarios de ROI medio:**
- **Herramientas populares con personalizaciones:** Slack con flujos específicos
- **Agregación de APIs:** Combinando múltiples servicios
- **Transformación de datos:** Formatos de datos específicos de empresa

**🔴 Escenarios de ROI bajo:**
- **Herramientas estándar:** GitHub, AWS (usar MCPs existentes)
- **Uso único:** Herramientas que usas raramente
- **Operaciones simples:** Que se pueden hacer manualmente en <5 min

## 🏗️ **Arquitectura de MCPs Personalizados**

### **Patrón 1: Envoltorio de Herramienta Simple**

**Cuándo usar:** Herramienta existente con API simple
**Ejemplo:** API interno de empresa

```typescript
// mcp-company-api/src/server.ts
import { Server } from '@modelcontextprotocol/sdk/server';
import { CompanyAPIClient } from './api-client';

class CompanyAPIMCP {
  private apiClient: CompanyAPIClient;

  constructor() {
    this.apiClient = new CompanyAPIClient({
      baseUrl: process.env.COMPANY_API_URL,
      apiKey: process.env.COMPANY_API_KEY
    });
  }

  async handleToolCall(name: string, args: any) {
    switch (name) {
      case 'get_employee_info':
        return this.getEmployeeInfo(args.employee_id);

      case 'update_project_status':
        return this.updateProjectStatus(args.project_id, args.status);

      case 'search_documents':
        return this.searchDocuments(args.query, args.filters);
    }
  }

  private async getEmployeeInfo(employeeId: string) {
    try {
      const employee = await this.apiClient.employees.get(employeeId);
      return {
        success: true,
        data: {
          name: employee.name,
          department: employee.department,
          role: employee.role,
          projects: employee.current_projects
        }
      };
    } catch (error) {
      return {
        success: false,
        error: `Failed to fetch employee info: ${error.message}`
      };
    }
  }
}
```

### **Patrón 2: Orquestador de Flujo Complejo**

**Cuándo usar:** Procesos multi-paso que involucran múltiples sistemas
**Ejemplo:** Automatización de despliegue

**Funcionalidades que ofrece:**
```
🚀 Desplegar Aplicación
   ├─ Validar precondiciones
   ├─ Construir y probar
   ├─ Desplegar al entorno
   ├─ Verificar salud del sistema
   └─ Notificar al equipo

🔄 Rollback de Emergencia
   ├─ Detectar fallo
   ├─ Volver a versión anterior
   └─ Confirmar estabilidad

📊 Estado del Despliegue
   └─ Información en tiempo real
```

**Flujo de trabajo conceptual:**
```
📝 Desarrollador solicita despliegue
    ↓
🔍 MCP valida precondiciones
    ↓ (si OK)
🔨 Construye y ejecuta pruebas
    ↓ (si éxito)
🚀 Despliega al entorno objetivo
    ↓
🩺 Verifica salud del sistema
    ↓
📢 Notifica resultado al equipo
```

**Sistemas que coordina:** GitHub + Docker + Kubernetes + Slack
**Tiempo promedio:** 5-15 minutos según complejidad
**ROI:** Reduce tiempo manual de 45 min a 5 min (88% ahorro)
```

### **Patrón 3: Agregación y Análisis de Datos**

**Cuándo usar:** Combinando datos de múltiples fuentes para insights
**Ejemplo:** MCP de inteligencia de negocio

**Fuentes de datos que unifica:**
```
📊 Analytics Web ──┐
                   ├─→ 📋 Informe Ejecutivo Unificado
💰 Sistema CRM ────┤    ├─ Métricas consolidadas
                   │    ├─ Tendencias cruzadas
🎧 Soporte ────────┤    └─ Recomendaciones IA
                   │
💳 Finanzas ───────┘
```

**Flujo de análisis:**
```
🎯 Solicitud de informe ejecutivo
    ↓
🔄 Recopila datos de 4 sistemas en paralelo
    ↓
🧮 Correlaciona métricas entre sistemas
    ↓
📈 Identifica tendencias y anomalías
    ↓
🤖 Genera recomendaciones automáticas
    ↓
📄 Entrega informe unificado
```

**Valor de negocio:**
- **Tiempo ahorrado:** De 4 horas manuales a 2 minutos automatizados
- **Precisión:** Elimina errores de consolidación manual
- **Frecuencia:** Permite informes diarios vs mensuales
- **ROI:** €22/hora × 4 horas × 22 días = €1,936/mes ahorrados

## 🔐 **Seguridad Avanzada para MCPs**

### **Estrategias de autenticación por contexto:**

**🔹 Autenticación básica (equipos pequeños)**
```
🔑 Clave API estática
   ├─ Fácil implementación
   ├─ Rotación manual
   └─ ⚠️ Uso: <10 usuarios
```

**🔹 Autenticación intermedia (equipos medianos)**
```
🎫 JSON Web Tokens (JWT)
   ├─ Expiración automática
   ├─ Roles y permisos
   ├─ Sin estado del servidor
   └─ ✅ Uso: 10-100 usuarios
```

**🔹 Autenticación avanzada (empresarial)**
```
🏢 OAuth2 + Control de acceso basado en roles
   ├─ Integración con SSO corporativo
   ├─ Auditoría completa
   ├─ Permisos granulares
   └─ 🚀 Uso: 100+ usuarios
```

**Flujo de decisión de seguridad:**
```
¿Cuántos usuarios?
   ├─ <10 → Clave API simple
   ├─ 10-100 → JWT con roles
   └─ 100+ → OAuth2 + RBAC

¿Datos sensibles?
   ├─ No → Autenticación básica
   └─ Sí → Cifrado E2E + auditoría

¿Cumplimiento regulatorio?
   ├─ No → Estándar de industria
   └─ Sí → Cumplimiento específico
```

### **Limitación de velocidad y prevención de abuso:**

**Estrategia escalonada por rol:**
```
🔴 Administrador: 1,000 req/min
   └─ Acceso completo para operaciones críticas

🟡 Desarrollador: 100 req/min
   └─ Uso normal de desarrollo y pruebas

🟢 Consultor: 50 req/min
   └─ Consultas básicas y reportes
```

**Flujo de control de velocidad:**
```
📥 Solicitud entrante
    ↓
🔍 Identificar usuario (email/API key)
    ↓
📊 Consultar límites por rol
    ↓
⏱️ Verificar ventana de tiempo
    ↓ (si dentro del límite)
✅ Procesar solicitud
    ↓ (si excede límite)
❌ Bloquear con error 429
```

**Beneficios de implementación:**
- **Protección:** Previene ataques DDoS y abuso
- **Estabilidad:** Mantiene rendimiento para todos los usuarios
- **Costos:** Controla gastos de APIs externas

## ⚡ **Optimización de Rendimiento**

### **Agrupación de conexiones y gestión de recursos:**

**Estrategias de optimización:**

**🔹 Agrupación de conexiones**
```
📦 Pool de Conexiones
   ├─ Máximo: 10 conexiones activas
   ├─ Mínimo: 2 conexiones persistentes
   ├─ Timeout inactivo: 30 segundos
   └─ 📈 Mejora: 5x velocidad de respuesta
```

**🔹 Caché inteligente**
```
💾 Cache LRU (Menos Usado Recientemente)
   ├─ Tamaño: 1,000 entradas
   ├─ TTL: 5 minutos
   ├─ Hit ratio objetivo: >80%
   └─ 📉 Reduce latencia: 50ms → 5ms
```

**Flujo de consulta optimizada:**
```
📥 Consulta entrante
    ↓
🔍 Verificar caché primero
    ↓ (si hit)
⚡ Respuesta inmediata (5ms)
    ↓ (si miss)
🏊 Obtener conexión del pool
    ↓
🗄️ Ejecutar consulta en BD
    ↓
💾 Almacenar en caché
    ↓
📤 Devolver resultado (50ms)
```

**Impacto en rendimiento:**
- **Latencia:** 90% reducción con caché
- **Throughput:** 5x más consultas concurrentes
- **Recursos:** 60% menos conexiones desperdiciadas

### **Procesamiento asíncrono y colas:**

**Arquitectura de colas de trabajo:**
```
📝 Tareas Largas (>30 segundos)
    ↓
📥 Cola de Trabajo (Redis)
    ↓
👷 Pool de Workers (4 procesos)
    ↓
📊 Seguimiento de Progreso
    ↓
📋 Resultado Final
```

**Casos de uso típicos:**
- **Análisis de grandes conjuntos de datos** (5-30 min)
- **Generación de informes complejos** (2-15 min)
- **Procesamiento de imágenes/vídeos** (1-10 min)
- **Migraciones de datos** (10-120 min)

**Flujo de trabajo asíncrono:**
```
🚀 Usuario solicita tarea pesada
    ↓
🎫 MCP genera ID de tarea único
    ↓
📥 Encola tarea en Redis
    ↓
⚡ Respuesta inmediata con ID
    ↓
👷 Worker procesa en background
    ↓
📊 Usuario consulta progreso (0-100%)
    ↓
✅ Resultado disponible cuando termine
```

**Beneficios del patrón asíncrono:**
- **Respuesta inmediata:** Usuario no espera bloqueado
- **Resistencia a fallos:** Reintentos automáticos (3x)
- **Escalabilidad:** Más workers = más throughput
- **Transparencia:** Progreso en tiempo real

## 🧪 **Estrategias de Pruebas para MCPs**

### **Marco de pruebas unitarias:**

```typescript
// tests/company-api-mcp.test.ts
import { describe, it, expect, beforeEach, afterEach } from 'vitest';
import { CompanyAPIMCP } from '../src/company-api-mcp';
import { MockAPIClient } from './mocks/api-client';

describe('CompanyAPIMCP', () => {
  let mcp: CompanyAPIMCP;
  let mockAPI: MockAPIClient;

  beforeEach(() => {
    mockAPI = new MockAPIClient();
    mcp = new CompanyAPIMCP(mockAPI);
  });

  describe('get_employee_info', () => {
    it('should return employee information', async () => {
      // Arrange
      const employeeId = 'emp123';
      mockAPI.employees.get.mockResolvedValue({
        id: employeeId,
        name: 'John Doe',
        department: 'Engineering',
        role: 'Senior Developer'
      });

      // Act
      const result = await mcp.handleToolCall('get_employee_info', { employee_id: employeeId });

      // Assert
      expect(result.success).toBe(true);
      expect(result.data.name).toBe('John Doe');
      expect(mockAPI.employees.get).toHaveBeenCalledWith(employeeId);
    });

    it('should handle API errors gracefully', async () => {
      // Arrange
      mockAPI.employees.get.mockRejectedValue(new Error('Employee not found'));

      // Act
      const result = await mcp.handleToolCall('get_employee_info', { employee_id: 'invalid' });

      // Assert
      expect(result.success).toBe(false);
      expect(result.error).toContain('Employee not found');
    });
  });
});
```

### **Pruebas de integración:**

```typescript
// tests/integration/mcp-integration.test.ts
describe('MCP Integration Tests', () => {
  let mcpServer: MCPServer;
  let testClient: MCPTestClient;

  beforeAll(async () => {
    // Start MCP server in test mode
    mcpServer = new MCPServer({
      port: 0, // Random port
      environment: 'test'
    });

    await mcpServer.start();
    testClient = new MCPTestClient(mcpServer.port);
  });

  afterAll(async () => {
    await mcpServer.stop();
  });

  it('should handle complete workflow', async () => {
    // Test complete user workflow
    const response1 = await testClient.callTool('search_documents', {
      query: 'project status'
    });

    expect(response1.success).toBe(true);
    expect(response1.data.documents).toHaveLength(3);

    const response2 = await testClient.callTool('update_project_status', {
      project_id: response1.data.documents[0].project_id,
      status: 'completed'
    });

    expect(response2.success).toBe(true);
  });
});
```

### **Pruebas de carga:**

```typescript
// tests/performance/load-test.ts
import { Worker } from 'worker_threads';

async function runLoadTest() {
  const numberOfWorkers = 10;
  const requestsPerWorker = 100;

  const workers = Array.from({ length: numberOfWorkers }, () =>
    new Worker('./load-test-worker.js', {
      workerData: { requests: requestsPerWorker }
    })
  );

  const startTime = Date.now();
  const results = await Promise.all(workers.map(worker =>
    new Promise((resolve) => {
      worker.on('message', resolve);
    })
  ));

  const endTime = Date.now();
  const totalRequests = numberOfWorkers * requestsPerWorker;
  const duration = endTime - startTime;
  const requestsPerSecond = totalRequests / (duration / 1000);

  console.log(`Load test completed:`);
  console.log(`Total requests: ${totalRequests}`);
  console.log(`Duration: ${duration}ms`);
  console.log(`Requests/second: ${requestsPerSecond.toFixed(2)}`);

  // Assert performance requirements
  expect(requestsPerSecond).toBeGreaterThan(50); // At least 50 RPS
  expect(results.every(r => r.successRate > 0.95)).toBe(true); // 95% success rate
}
```

## 🚀 **Despliegue y Monitoreo**

### **CI/CD pipeline para MCPs:**

```yaml
# .github/workflows/mcp-deploy.yml
name: Deploy MCP
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run unit tests
        run: npm run test:unit

      - name: Run integration tests
        run: npm run test:integration
        env:
          TEST_DATABASE_URL: ${{ secrets.TEST_DATABASE_URL }}

      - name: Run security scan
        run: npm audit --audit-level moderate

  deploy:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v3

      - name: Build Docker image
        run: |
          docker build -t mcp-server:${{ github.sha }} .
          docker tag mcp-server:${{ github.sha }} mcp-server:latest

      - name: Deploy to staging
        run: |
          kubectl set image deployment/mcp-server \
            mcp-server=mcp-server:${{ github.sha }} \
            --namespace=staging

      - name: Run smoke tests
        run: npm run test:smoke
        env:
          MCP_SERVER_URL: https://mcp-staging.company.com

      - name: Deploy to production
        if: success()
        run: |
          kubectl set image deployment/mcp-server \
            mcp-server=mcp-server:${{ github.sha }} \
            --namespace=production
```

### **Monitoreo y observabilidad:**

```typescript
// src/monitoring/metrics.ts
import { createPrometheusMetrics } from 'prometheus-client';

export class MCPMetrics {
  private requestCounter = createPrometheusMetrics.counter({
    name: 'mcp_requests_total',
    help: 'Total number of MCP requests',
    labelNames: ['tool', 'status', 'user_role']
  });

  private requestDuration = createPrometheusMetrics.histogram({
    name: 'mcp_request_duration_seconds',
    help: 'Duration of MCP requests in seconds',
    labelNames: ['tool'],
    buckets: [0.1, 0.5, 1, 2, 5, 10]
  });

  private activeConnections = createPrometheusMetrics.gauge({
    name: 'mcp_active_connections',
    help: 'Number of active MCP connections'
  });

  recordRequest(tool: string, status: string, duration: number, userRole: string) {
    this.requestCounter.inc({ tool, status, user_role: userRole });
    this.requestDuration.observe({ tool }, duration);
  }

  setActiveConnections(count: number) {
    this.activeConnections.set(count);
  }
}

// src/monitoring/health-check.ts
export class MCPHealthCheck {
  async getHealthStatus(): Promise<HealthStatus> {
    const checks = await Promise.allSettled([
      this.checkDatabaseConnection(),
      this.checkExternalAPIs(),
      this.checkMemoryUsage(),
      this.checkDiskSpace()
    ]);

    const overallHealth = checks.every(check =>
      check.status === 'fulfilled' && check.value.healthy
    );

    return {
      healthy: overallHealth,
      timestamp: new Date().toISOString(),
      checks: checks.map((check, index) => ({
        name: ['database', 'external_apis', 'memory', 'disk'][index],
        healthy: check.status === 'fulfilled' ? check.value.healthy : false,
        message: check.status === 'fulfilled' ? check.value.message : check.reason
      }))
    };
  }
}
```

## 🌐 **Ecosistemas MCP: Orquestación múltiple**

### **Patrón de orquestador MCP:**

```typescript
class MCPOrchestrator {
  private mcpClients: Map<string, MCPClient> = new Map();
  private routingRules: RoutingRule[] = [];

  async registerMCP(name: string, client: MCPClient, capabilities: string[]) {
    this.mcpClients.set(name, client);

    // Register routing rules
    capabilities.forEach(capability => {
      this.routingRules.push({
        pattern: capability,
        mcpName: name,
        priority: this.calculatePriority(capability)
      });
    });

    // Sort by priority
    this.routingRules.sort((a, b) => b.priority - a.priority);
  }

  async executeTask(task: string, args: any): Promise<any> {
    // Find best MCP for this task
    const route = this.findBestRoute(task);

    if (!route) {
      throw new Error(`No MCP found for task: ${task}`);
    }

    const mcp = this.mcpClients.get(route.mcpName)!;

    try {
      const result = await mcp.executeTask(task, args);

      // Learn from successful execution
      this.updateRouting(task, route.mcpName, true);

      return result;
    } catch (error) {
      // Try fallback MCP
      const fallbackRoute = this.findFallbackRoute(task, route.mcpName);

      if (fallbackRoute) {
        const fallbackMCP = this.mcpClients.get(fallbackRoute.mcpName)!;
        return await fallbackMCP.executeTask(task, args);
      }

      throw error;
    }
  }

  private findBestRoute(task: string): RoutingRule | null {
    return this.routingRules.find(rule =>
      this.matchesPattern(task, rule.pattern)
    ) || null;
  }
}
```

### **Cross-MCP workflows:**

```typescript
class CrossMCPWorkflow {
  constructor(private orchestrator: MCPOrchestrator) {}

  async executeComplexWorkflow(workflowDefinition: WorkflowDefinition) {
    const context = new WorkflowContext();

    for (const step of workflowDefinition.steps) {
      try {
        const stepResult = await this.executeStep(step, context);
        context.addResult(step.id, stepResult);

        // Check if workflow should continue
        if (step.condition && !this.evaluateCondition(step.condition, context)) {
          break;
        }
      } catch (error) {
        // Handle step failure
        if (step.onError === 'abort') {
          throw error;
        } else if (step.onError === 'continue') {
          context.addError(step.id, error);
          continue;
        } else if (step.onError === 'retry') {
          const retryResult = await this.retryStep(step, context);
          context.addResult(step.id, retryResult);
        }
      }
    }

    return context.getFinalResult();
  }

  private async executeStep(step: WorkflowStep, context: WorkflowContext) {
    // Resolve step arguments from context
    const resolvedArgs = this.resolveArguments(step.args, context);

    // Execute through orchestrator
    return await this.orchestrator.executeTask(step.task, resolvedArgs);
  }
}
```

---

## 🚀 **¿Qué sigue?**

Has dominado MCPs avanzados y arquitecturas complejas. Has completado el nivel avanzado. Ahora estás listo para el nivel experto con casos de uso específicos por stack:

**➡️ [Siguiente: Casos de Uso por Stack (Nivel Experto)](../05-experto/casos-uso-stack.md)**

---

## 💡 **Puntos Clave**

- **MCPs personalizados:** Construir cuando ROI justifica inversión (>€45K valor/año)
- **Seguridad:** Enfoque multicapa con autenticación, limitación de velocidad y auditoría
- **Rendimiento:** Agrupación de conexiones + caché = 10x mejora
- **Pruebas:** Pruebas unitarias + integración + carga esenciales
- **Despliegue:** CI/CD automatizado con verificaciones de salud
- **Orquestación:** Múltiples MCPs trabajando juntos desbloquean nuevas capacidades

*Los MCPs avanzados no son solo herramientas—son bloques de construcción para crear ecosistemas de IA que se adapten específicamente a tu organización. La inversión en MCPs personalizados bien diseñados paga dividendos a largo plazo.*