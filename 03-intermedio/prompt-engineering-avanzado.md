# 🧠 Prompt Engineering Avanzado

> **Dominando la comunicación con IA.** Ya conoces lo básico. Ahora vamos a técnicas avanzadas que usan los profesionales: cuándo usar chain of thought, cómo estructurar prompts complejos, optimización de costos y patrones que resuelven problemas de arquitectura.

## 🎯 ¿Qué aprenderás aquí?

- ✅ **Chain of Thought:** Cuándo y cómo usar razonamiento estructurado
- ✅ **Role prompting:** Obtener perspectivas expertas específicas
- ✅ **Optimización:** Reducir tokens sin perder calidad
- ✅ **Prompt chaining:** Dividir problemas complejos en pasos
- ✅ **Meta-prompting:** Prompts que mejoran prompts
- ✅ **Casos reales:** Arquitectura, migraciones, code reviews

## 🧩 **Chain of Thought: Cuándo y cómo usar**

### **El problema del razonamiento complejo:**

**❌ Prompt directo (falla con problemas complejos):**
```
"¿Por qué mi app Android es lenta?"
→ Respuesta genérica, no aplicable
```

**✅ Chain of Thought (razonamiento paso a paso):**
```
"Analiza este problema sistemáticamente:
1. Primero identifica posibles causas
2. Luego evalúa probabilidad de cada una
3. Finalmente propón diagnósticos específicos"
→ Análisis estructurado y actionable
```

### **Cuándo usar Chain of Thought:**

**✅ Úsalo para:**
- **Debugging complejo:** Múltiples variables interactuando
- **Arquitectura de sistemas:** Decisiones con trade-offs
- **Code reviews:** Análisis multidimensional
- **Migraciones:** Múltiples riesgos y consideraciones

**❌ No lo uses para:**
- **Tareas simples:** "Crea una función que sume dos números"
- **Respuestas rápidas:** Cuando necesitas velocidad sobre profundidad
- **Límite de tokens:** CoT consume más tokens

### **Patrón de CoT efectivo:**

```
CONTEXTO: [Situación específica con detalles]
ANÁLISIS REQUERIDO:
1. [Área específica a analizar]
2. [Segunda área]
3. [Tercera área]

Para cada área, proporciona:
- Diagnóstico específico
- Evidencia que buscar
- Solución priorizada

INFORMACIÓN DISPONIBLE: [Datos concretos]
```

### **CoT con auto-verificación:**

```
PASO 1: Diseño inicial
[Propón solución]

PASO 2: Validación crítica
¿Qué podría fallar? ¿Cómo escala? ¿Qué falta?

PASO 3: Refinamiento
Ajusta basándote en la validación anterior

PASO 4: Implementación
Plan concreto y priorizado
```

## 🎭 **Role Prompting: Perspectivas expertas**

### **¿Por qué funciona el role prompting?**

Los LLMs han sido entrenados con contenido de expertos específicos. Al adoptar un rol, activan patrones de conocimiento más específicos y relevantes.

### **Patrones de roles efectivos:**

**Para análisis técnico:**
```
Actúa como [SENIOR ROLE] con [X años] de experiencia que ha [EXPERIENCIA ESPECÍFICA].

Tu experiencia incluye:
- [Situación relevante 1]
- [Situación relevante 2]
- [Pattern o anti-pattern conocido]

SITUACIÓN ACTUAL: [Tu problema]

Basándote en tu experiencia, ¿qué recomendarías?
Sé específico sobre los riesgos que has visto antes.
```

**Para múltiples perspectivas:**
```
Necesito feedback de diferentes roles sobre [DECISIÓN]:

PERSPECTIVA 1 - [ROL]: [Qué evaluar]
PERSPECTIVA 2 - [ROL]: [Qué evaluar]
PERSPECTIVA 3 - [ROL]: [Qué evaluar]

Para cada perspectiva:
- Principales concerns
- Deal-breakers si los hay
- Recomendación específica
```

### **Roles que funcionan mejor:**

- **"Senior X con Y años de experiencia":** Más específico que "experto"
- **"CTO que ha visto fallar Z":** Experiencia en fracasos es valiosa
- **"Arquitecto que migró de A a B":** Experiencia específica en transiciones
- **"Developer que mantiene sistema con X usuarios":** Scale-specific knowledge

## 💰 **Optimización de tokens: Calidad sin costo**

### **El costo oculto de los prompts:**

```
Prompt verboso: 500 tokens × $0.03/1K = $0.015 por request
Prompt optimizado: 150 tokens × $0.03/1K = $0.0045 por request

Con 10K requests/mes: $150 vs $45 = $105 ahorrados/mes
```

### **Técnicas de compresión efectivas:**

**1. Usar abreviaciones estándar:**
```
❌ "JavaScript application"
✅ "JS app"

❌ "database performance optimization"
✅ "DB perf opt"
```

**2. Estructura concisa:**
```
❌ "I need you to analyze this code and tell me..."
✅ "Analyze this code for:"

❌ "The following information is provided..."
✅ "CONTEXT:"
```

**3. Bullet points sobre párrafos:**
```
❌ "The application is experiencing slow response times due to database queries that are not optimized and caching that is not properly implemented."

✅ "ISSUES:
- Slow DB queries
- Poor caching implementation"
```

### **Mantener calidad en prompts optimizados:**

**Template optimizado que funciona:**
```
TASK: [Acción específica]
CONTEXT: [Mínimo contexto necesario]
CONSTRAINTS: [Limitaciones importantes]
OUTPUT: [Formato esperado]

[Información específica]
```

## 🔗 **Prompt Chaining: Divide y vencerás**

### **Cuándo usar prompt chaining:**

**✅ Problemas complejos que requieren:**
- Múltiples tipos de análisis
- Decisiones secuenciales
- Validación entre pasos
- Diferentes tipos de expertise

**❌ No uses chaining para:**
- Tareas simples que un prompt resuelve
- Cuando necesitas respuesta inmediata
- Procesos que no se benefician de pasos intermedios

### **Patrón de chaining efectivo:**

```
PASO 1: Análisis → Lista estructurada de findings
PASO 2: Síntesis → Patrones y relaciones entre findings
PASO 3: Solución → Recomendaciones basadas en síntesis
PASO 4: Implementación → Plan concreto y priorizado
```

### **Ejemplo: Architectural Decision**

**Paso 1 - Requirements Analysis:**
```
Extrae requirements funcionales y no-funcionales de esta descripción:
[Descripción del proyecto]

OUTPUT: Lista categorizada de requirements
NO propongas soluciones aún.
```

**Paso 2 - Architecture Options:**
```
Basándote en estos requirements: [OUTPUT PASO 1]

Propón 3 opciones arquitectónicas diferentes:
- Monolito modular
- Microservicios
- Serverless

Para cada opción: pros/cons específicos para estos requirements.
```

**Paso 3 - Decision Matrix:**
```
Crea matriz de decisión para estas opciones: [OUTPUT PASO 2]

Evalúa cada opción en:
- Complejidad implementación
- Time to market
- Escalabilidad
- Team expertise required

Recomienda opción con justificación.
```

## 🧬 **Meta-prompting: Prompts que mejoran prompts**

### **¿Qué es meta-prompting?**

Usar IA para mejorar tus propios prompts. Es especialmente útil cuando un prompt no está generando los resultados esperados.

### **Patrón de debugging de prompts:**

```
Este prompt no genera buenos resultados:

PROMPT PROBLEMÁTICO: "[Tu prompt]"

PROBLEMAS OBSERVADOS:
- [Problema específico 1]
- [Problema específico 2]

RESULTADOS ESPERADOS:
- [Qué debería generar]

REESCRIBE el prompt para:
1. Ser más específico
2. Incluir contexto necesario
3. Clarificar el output esperado
```

### **Mejora iterativa:**

```
PROMPT ACTUAL: "[Prompt]"

CRITERIOS DE MEJORA:
1. ¿Incluye contexto suficiente?
2. ¿Es específico sobre el output?
3. ¿Considera edge cases?
4. ¿Es eficiente en tokens?

MEJORA el prompt aplicando estos criterios.
Explica qué cambios hiciste y por qué.
```

## 🏢 **Casos de uso avanzados**

### **1. Code Review Sistemático**

```
Actúa como senior code reviewer siguiendo estos principios:

1. "Simplicidad sobre cleverness"
2. "Seguridad first, performance second"
3. "Si no se puede testear fácilmente, está mal diseñado"

CÓDIGO: [código aquí]

REVISA aplicando cada principio:
- Issues encontrados
- Severity (critical/high/medium/low)
- Refactoring sugerido

Prioriza fixes por severity y impacto.
```

### **2. Migration Planning**

```
Actúa como migration specialist con experiencia en [TIPO] migrations.

MIGRACIÓN: [Tecnología A] → [Tecnología B]

CODEBASE ACTUAL:
- [Característica 1]
- [Característica 2]
- [Complejidad específica]

MIGRATION STRATEGY:
1. **Risk Assessment**: ¿Qué puede quebrar?
2. **Phased Approach**: Pasos específicos
3. **Rollback Plan**: Si algo falla
4. **Success Metrics**: Cómo medir progreso

CONSTRAINTS:
- Timeline: [X semanas]
- Team size: [X developers]
- Zero downtime requirement
```

### **3. Architecture Decision Records (ADR)**

```
Genera ADR para esta decisión arquitectónica:

CONTEXTO: [Situación que requiere decisión]

OPCIONES EVALUADAS:
1. [Opción A] - [Pro/con principales]
2. [Opción B] - [Pro/con principales]
3. [Opción C] - [Pro/con principales]

CRITERIOS DE DECISIÓN:
- [Criterio 1]
- [Criterio 2]
- [Criterio 3]

TEAM CONTEXT:
- Experiencia: [nivel]
- Timeline: [restricción]
- Constraints: [limitaciones]

FORMATO ADR:
- Status: [proposed/accepted]
- Context: [problema a resolver]
- Decision: [opción elegida]
- Consequences: [positivas y negativas]
```

## 📊 **Midiendo efectividad de prompts**

### **Métricas para evaluar prompts:**

| **Métrica** | **Cómo medir** | **Target** |
|-------------|----------------|------------|
| **Relevancia** | ¿Aborda el problema específico? | >8/10 |
| **Completitud** | ¿Cubre todos los requirements? | >90% |
| **Accionabilidad** | ¿Puedo implementar inmediatamente? | Sí/No |
| **Precisión técnica** | ¿El código/solución funciona? | >95% |
| **Eficiencia** | Tokens usados vs valor generado | <200 tokens por insight útil |

### **A/B testing de prompts:**

```
EXPERIMENTO: Code review prompts

VARIANT A (Control): "Review this code and suggest improvements"

VARIANT B (Structured):
"Review systematically:
1. Correctness
2. Performance
3. Security
4. Maintainability
Prioritize by severity."

MEDIR:
- Tiempo para implementar sugerencias
- Número de issues reales encontrados
- Calidad de las sugerencias (1-10)
```

## 🚀 **Patrones emergentes**

### **Constitutional AI approach:**
```
Sigue estos principios al [TAREA]:

PRINCIPIO 1: [Regla fundamental]
PRINCIPIO 2: [Regla fundamental]
PRINCIPIO 3: [Regla fundamental]

Si hay conflicto entre principios, prioriza en este orden.
Explica trade-offs cuando aplique.
```

### **Debate-style prompting:**
```
Simula debate entre dos expertos sobre [DECISIÓN]:

EXPERTO A (Pro-[OPCIÓN]): Argumenta ventajas con casos reales
EXPERTO B (Pro-[ALTERNATIVA]): Argumenta contra con experiencias

MODERADOR: Hace preguntas difíciles a ambos

CONTEXTO: [Tu situación específica]
```

## 💡 **Best practices consolidadas**

### **Para prompts de producción:**

1. **Sé específico sobre el contexto:** "App React con 50K usuarios" vs "app web"
2. **Define output format:** JSON, bullet points, código con tests
3. **Incluye constrainsts:** Timeline, team size, tech stack
4. **Usa ejemplos cuando sea complejo:** Few-shot para patrones específicos
5. **Añade validación:** "¿Falta algo en esta solución?"

### **Para optimización de costos:**

1. **Estructura jerárquica:** Contexto → Task → Constraints → Output
2. **Abreviaciones estándar:** JS, DB, API, perf, opt
3. **Bullet points:** Más densos que párrafos
4. **Reusar prompts:** Templates para casos similares

### **Para casos complejos:**

1. **Chain cuando hay múltiples tipos de análisis**
2. **Role prompting para expertise específica**
3. **Meta-prompting cuando los resultados no son buenos**
4. **A/B testing para optimizar prompts críticos**

---

## 🚀 **¿Qué sigue?**

Dominas técnicas avanzadas de prompt engineering. Ahora vamos a ver cómo desarrollar aplicaciones completas con LLMs:

**➡️ [Siguiente: Desarrollo con LLMs](./desarrollo-llms.md)**

---

## 💡 **Key Takeaways**

- **Chain of Thought:** Para problemas complejos, no para tareas simples
- **Role prompting:** Activa conocimiento específico en LLMs
- **Optimización:** 70% reducción de tokens sin perder calidad
- **Chaining:** Divide problemas complejos en pasos manejables
- **Meta-prompting:** Usa IA para mejorar tus propios prompts

*El prompt engineering avanzado es la diferencia entre respuestas genéricas y soluciones profesionales. Estas técnicas te permiten obtener valor real de IA en problemas complejos de arquitectura y desarrollo.*