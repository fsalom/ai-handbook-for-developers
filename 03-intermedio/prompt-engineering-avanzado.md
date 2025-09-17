# 🧠 Prompt Engineering Avanzado

> **Dominando la comunicación con IA.** Ya conoces lo básico. Ahora vamos a técnicas avanzadas que usan los profesionales: chain of thought, few-shot complex, optimización de tokens y prompts que resuelven problemas de arquitectura compleja.

## 🎯 ¿Qué aprenderás aquí?

- ✅ Chain of Thought y razonamiento estructurado
- ✅ Few-shot learning complejo
- ✅ Role prompting avanzado con contexto
- ✅ Optimización de tokens y costos
- ✅ Meta-prompting y self-correction
- ✅ Prompt chaining para tareas complejas

## 🧩 **Chain of Thought (CoT) Avanzado**

### **CoT Básico vs Avanzado**

**❌ CoT Básico:**
```
Explica por qué mi app Android es lenta paso a paso.
```

**✅ CoT Avanzado:**
```
Actúa como un senior Android performance engineer. Analiza este problema sistemáticamente:

CONTEXTO: App Android con 50k+ usuarios activos, lag notable en scrolling

INFORMACIÓN DEL SISTEMA:
- Dispositivos afectados: Gama media (4GB RAM)
- Versión Android: 8.0 - 13.0
- Arquitectura: MVVM + Room + Retrofit
- Síntomas: Stuttering en RecyclerView, ANRs ocasionales

ANÁLISIS REQUERIDO:
1. **Memory Analysis**: ¿Qué patrones de memory leaks podrían causar esto?
2. **Threading Issues**: ¿Hay operaciones blocking en main thread?
3. **Database Performance**: ¿Las queries de Room están optimizadas?
4. **Network Layer**: ¿Retrofit está configurado eficientemente?
5. **UI Rendering**: ¿El layout hierarchy es eficiente?

Para cada área, proporciona:
- Método de diagnóstico específico
- Herramientas a usar (Profiler, Systrace, etc.)
- Solución priorizada por impacto

CÓDIGO SOSPECHOSO:
[incluir fragmentos relevantes aquí]

Razona paso a paso, mostrando tu proceso de debugging.
```

### **CoT con verificación auto-corregida**

```
Diseña la arquitectura de un sistema de chat en tiempo real.

PASO 1: Análisis de requisitos
- Lista los componentes principales
- Identifica patrones de comunicación
- Estima la carga esperada

PASO 2: Diseño inicial
- Propón arquitectura high-level
- Selecciona tecnologías

PASO 3: Validación crítica
- ¿Qué podría fallar en tu diseño?
- ¿Cómo escala con 100k usuarios concurrentes?
- ¿Qué single points of failure existen?

PASO 4: Refinamiento
- Ajusta el diseño basándote en la validación
- Añade redundancia y failover

PASO 5: Plan de implementación
- Ordena desarrollo por prioridades
- Identifica MVPs y milestones

Muestra tu razonamiento en cada paso y cómo cada decisión impacta las siguientes.
```

## 🎭 **Role Prompting Avanzado**

### **Multi-persona approach**

```
Necesito feedback sobre esta API design. Quiero perspectivas de diferentes roles:

API DESIGN:
[tu diseño aquí]

PERSPECTIVA 1 - SENIOR BACKEND DEVELOPER:
Evalúa desde arquitectura técnica, performance, escalabilidad.

PERSPECTIVA 2 - FRONTEND DEVELOPER:
¿Qué tan fácil es integrar esta API? ¿Falta algo para el UI?

PERSPECTIVA 3 - DEVOPS ENGINEER:
Consideraciones de deployment, monitoring, troubleshooting.

PERSPECTIVA 4 - SECURITY EXPERT:
Vulnerabilidades potenciales, mejores prácticas de seguridad.

PERSPECTIVA 5 - PRODUCT MANAGER:
¿Cumple con los requerimientos de negocio? ¿Es mantenible?

Para cada perspectiva, proporciona:
- Principales concerns
- Sugerencias específicas
- Deal-breakers si los hay
```

### **Role prompting con contexto histórico**

```
Actúa como un CTO con 15 años de experiencia que ha visto múltiples ciclos de tecnología.

SITUACIÓN: Mi startup quiere migrar de monolito PHP a microservicios.

Tu experiencia incluye:
- Has visto fracasar migraciones prematuras
- Conoces los true costs de microservicios
- Entiendes trade-offs business vs technical
- Has manejado equipos de 5-50 developers

CONTEXTO DE LA EMPRESA:
- 10 developers
- 500k usuarios activos
- Revenue: $2M ARR
- Monolito actual funciona pero "no escala"

PREGUNTA: ¿Recomendarías esta migración ahora?

Responde considerando:
- Timing y recursos
- Riesgos vs beneficios
- Alternativas menos disruptivas
- Lessons learned de tus experiencias anteriores

Sé brutalmente honesto sobre los pitfalls.
```

## 🔄 **Few-shot Learning Complejo**

### **Pattern recognition con evolución**

```
Aprende este patrón de refactoring y aplícalo:

EJEMPLO 1 - Python Legacy:
ANTES:
def get_user_data(user_id):
    conn = sqlite3.connect('db.sqlite')
    cursor = conn.cursor()
    cursor.execute(f"SELECT * FROM users WHERE id = {user_id}")
    result = cursor.fetchone()
    conn.close()
    return result

DESPUÉS:
@dataclass
class User:
    id: int
    name: str
    email: str

class UserRepository:
    def __init__(self, db_session: Session):
        self.db = db_session

    def get_by_id(self, user_id: int) -> Optional[User]:
        return self.db.query(User).filter(User.id == user_id).first()


PATRONES APLICADOS:
- SQL injection fix
- Connection management
- Type safety
- Separation of concerns
- Repository pattern

EJEMPLO 2 - Flutter State Management:
ANTES:
class CounterWidget extends StatefulWidget {
  @override
  _CounterWidgetState createState() => _CounterWidgetState();
}

class _CounterWidgetState extends State<CounterWidget> {
  int _counter = 0;

  void _increment() {
    setState(() {
      _counter++;
    });
    // HTTP call here
    http.post('/api/counter', body: {'count': _counter});
  }
}


DESPUÉS:
class CounterBloc extends Bloc<CounterEvent, CounterState> {
  final CounterRepository repository;

  CounterBloc(this.repository) : super(CounterInitial()) {
    on<IncrementPressed>(_onIncrement);
  }

  Future<void> _onIncrement(IncrementPressed event, Emitter<CounterState> emit) async {
    try {
      final newCount = state.count + 1;
      emit(CounterLoading());
      await repository.updateCount(newCount);
      emit(CounterSuccess(newCount));
    } catch (e) {
      emit(CounterError(e.toString()));
    }
  }
}


PATRONES APLICADOS:
- Business logic separation
- Error handling
- Testable architecture
- State management
- Repository pattern

AHORA APLICA ESTOS PATRONES A:
class ViewController: UIViewController {
    @IBOutlet weak var tableView: UITableView!
    var users: [User] = []

    override func viewDidLoad() {
        super.viewDidLoad()
        loadUsers()
    }

    func loadUsers() {
        let url = URL(string: "https://api.example.com/users")!
        URLSession.shared.dataTask(with: url) { data, response, error in
            guard let data = data else { return }
            self.users = try! JSONDecoder().decode([User].self, from: data)
            DispatchQueue.main.async {
                self.tableView.reloadData()
            }
        }.resume()
    }
}

Identifica los problemas y aplica los mismos patrones de refactoring.
```

## 🎯 **Optimización de Tokens**

### **Técnicas de compresión de prompts**

**❌ Prompt verboso (450 tokens):**
```
I am a senior software developer working on a large-scale e-commerce application. We are currently using a microservices architecture with Node.js and Express for our backend services. Our database is PostgreSQL and we're using Redis for caching. We have been experiencing performance issues lately, particularly with our product search functionality. The search is becoming slow when users search for products, especially when they use multiple filters like price range, brand, category, and availability. Our current implementation queries the database directly for each search request and doesn't cache the results effectively. We need to optimize this system to handle increased traffic and provide faster search results. Can you help us design a better solution?
```

**✅ Prompt optimizado (180 tokens):**
```
E-commerce app optimization needed:

STACK: Node.js/Express, PostgreSQL, Redis
PROBLEM: Slow product search with filters (price, brand, category, availability)
CURRENT: Direct DB queries, poor caching
GOAL: Handle high traffic, faster results

Design optimized search solution.
```

### **Token-aware prompt structure**

```python
def create_optimized_prompt(context, task, constraints):
    # Usar abreviaciones estándar
    abbreviations = {
        "JavaScript": "JS",
        "TypeScript": "TS",
        "database": "DB",
        "application": "app",
        "function": "fn",
        "performance": "perf",
        "optimization": "opt"
    }

    # Estructura concisa pero completa
    prompt = f"""
    CONTEXT: {compress_text(context, abbreviations)}
    TASK: {task}
    CONSTRAINTS: {format_constraints(constraints)}

    Provide: code + brief explanation
    """

    return prompt
```

### **Costo por token awareness**

```python
def estimate_prompt_cost(prompt, model="gpt-4"):
    costs = {
        "gpt-4": {"input": 0.03, "output": 0.06},  # per 1K tokens
        "gpt-3.5-turbo": {"input": 0.001, "output": 0.002}
    }

    estimated_input_tokens = len(prompt.split()) * 1.3  # rough estimate
    estimated_output_tokens = 500  # estimate based on task

    total_cost = (
        (estimated_input_tokens / 1000) * costs[model]["input"] +
        (estimated_output_tokens / 1000) * costs[model]["output"]
    )

    return {
        "estimated_cost": total_cost,
        "input_tokens": estimated_input_tokens,
        "output_tokens": estimated_output_tokens
    }
```

## 🔗 **Prompt Chaining**

### **Multi-step architectural design**

**Paso 1: Requirements analysis**
```
Analiza estos requirements para una app de delivery:

USER STORIES:
- Usuario puede ordenar comida de restaurantes cercanos
- Restaurante puede gestionar menú y órdenes
- Delivery puede ver órdenes asignadas y actualizar status
- Admin puede monitorear toda la operación

CONSTRAINTS:
- 100k usuarios activos
- 1000 restaurantes
- 500 deliveries concurrentes
- Real-time updates críticos

OUTPUT: Lista de functional y non-functional requirements estructurados.
NO diseñes arquitectura aún, solo análisis.
```

**Paso 2: Architecture proposal**
```
Basándote en los requirements analizados anteriormente, diseña arquitectura:

INPUT: [Output del paso anterior]

DECISIONES A TOMAR:
- Monolito vs Microservicios
- Database strategy (SQL vs NoSQL vs Hybrid)
- Real-time communication (WebSockets, SSE, Push)
- Caching strategy
- File storage (images, etc.)

OUTPUT: High-level architecture diagram (text format) + technology decisions justificadas.
```

**Paso 3: Implementation roadmap**
```
Crea roadmap de implementación para la arquitectura diseñada:

INPUT: [Architecture output del paso anterior]

PRIORIZACIÓN:
- MVP features vs nice-to-have
- Technical risk assessment
- Team capacity (10 developers, 6 months)
- Dependencies entre componentes

OUTPUT:
- Sprint breakdown (2-week sprints)
- Critical path identification
- Risk mitigation strategies
- Testing strategy per component
```

## 🧬 **Meta-prompting**

### **Self-improving prompts**

```
Tu tarea es mejorar el siguiente prompt iterativamente:

PROMPT INICIAL:
"Crea una función que ordene una lista"

CRITERIOS DE MEJORA:
1. Especificidad técnica
2. Contexto de uso
3. Requerimientos de performance
4. Error handling expectations
5. Testing requirements

PROCESO:
1. Identifica qué falta en el prompt actual
2. Reescribe el prompt incorporando mejoras
3. Evalúa si el prompt mejorado obtendría mejores resultados
4. Si no, itera nuevamente

OBJETIVO: Prompt que genere código production-ready.
```

### **Prompt debugging**

```
Este prompt no está generando los resultados esperados:

PROMPT PROBLEMÁTICO:
"Optimiza esta query SQL para que sea más rápida"

RESULTADOS OBTENIDOS:
- Respuestas muy genéricas
- No considera el schema específico
- Sugerencias no aplicables

DIAGNÓSTICO REQUERIDO:
1. ¿Qué información crucial falta?
2. ¿Cómo debería estructurarse mejor?
3. ¿Qué contexto técnico necesita?

REESCRIBE el prompt para obtener optimizaciones SQL específicas y aplicables.
```

## 🧪 **Técnicas experimentales avanzadas**

### **Constitutional AI approach**

```
Actúa como un code reviewer siguiendo estos principios constitucionales:

PRINCIPIOS BASE:
1. "Prefiere soluciones simples sobre complejas"
2. "La legibilidad es más importante que la cleverness"
3. "Seguridad first, performance second, elegancia third"
4. "Si no puedes testear fácilmente, está mal diseñado"

CÓDIGO A REVISAR:
[código aquí]

PROCESO:
1. Revisa aplicando cada principio
2. Si encuentras conflictos entre principios, explica el trade-off
3. Prioriza fixes basándote en la jerarquía de principios
4. Sugiere refactoring que alinee mejor con los principios

OBJETIVO: Code review que no solo encuentre bugs, sino que eleve la calidad arquitectural.
```

### **Debate-style prompting**

```
Tengo que decidir entre GraphQL y REST para mi nueva API.

SETUP: Simula un debate entre dos senior architects.

ARCHITECT A (Pro-GraphQL):
- Argumenta ventajas de GraphQL
- Usa casos reales de implementación exitosa
- Aborda concerns típicos sobre complexity

ARCHITECT B (Pro-REST):
- Defiende REST con argumentos sólidos
- Menciona GraphQL pitfalls de proyectos reales
- Enfatiza simplicity y proven patterns

MODERADOR (tú):
- Hace preguntas difíciles a ambos
- Busca debilidades en cada argumento
- Evalúa fit para mi proyecto específico

MI CONTEXTO:
- Team de 8 developers (mix junior/senior)
- API para mobile app + web dashboard
- Timeframe: 4 meses para MVP

FORMATO: Debate de 3 rondas + recomendación final fundamentada.
```

## 📊 **Measuring prompt effectiveness**

### **A/B testing your prompts**

```python
class PromptExperiment:
    def __init__(self, name):
        self.name = name
        self.variants = {}
        self.results = {}

    def add_variant(self, variant_name, prompt):
        self.variants[variant_name] = prompt

    def measure_effectiveness(self, variant, criteria):
        """
        Criteria:
        - accuracy: Does it solve the problem correctly?
        - completeness: Does it address all requirements?
        - efficiency: How concise is the response?
        - actionability: Can I immediately implement the solution?
        """
        scores = {}
        for criterion in criteria:
            scores[criterion] = self.evaluate_criterion(variant, criterion)

        return {
            "variant": variant,
            "scores": scores,
            "total_score": sum(scores.values()) / len(scores)
        }

# Ejemplo de uso:
experiment = PromptExperiment("code_review")

experiment.add_variant("basic",
    "Review this code and suggest improvements")

experiment.add_variant("structured",
    """Review this code systematically:

    1. Correctness: Does it work as intended?
    2. Performance: Any bottlenecks?
    3. Security: Vulnerabilities present?
    4. Maintainability: Is it readable and modular?

    Prioritize issues by severity.""")
```

### **Quality metrics for prompt outputs**

| Metric | Description | How to measure |
|--------|-------------|----------------|
| **Relevance** | Addresses the specific problem | 1-10 scale, manual review |
| **Completeness** | Covers all requirements | Checklist completion % |
| **Accuracy** | Technical correctness | Code compiles/tests pass |
| **Actionability** | Can implement immediately | Binary: yes/no |
| **Efficiency** | Concise but complete | Response length vs value |

## 🚀 **Advanced use cases**

### **Code migration assistant**

```
Actúa como un migration specialist con experiencia en large-scale modernizations.

MIGRATION TASK: Legacy Java Spring Boot 2.x → Spring Boot 3.x + Virtual Threads

CURRENT CODEBASE ANALYSIS:
- 200+ REST endpoints
- Heavy use of @Async methods
- ThreadPool configurations
- Blocking I/O operations
- Custom thread management

MIGRATION STRATEGY NEEDED:
1. **Compatibility Assessment**: What breaks in Spring Boot 3.x?
2. **Virtual Threads Adoption**: Where to apply for maximum benefit?
3. **Performance Impact**: Expected improvements/regressions
4. **Migration Steps**: Detailed phased approach
5. **Risk Mitigation**: Rollback strategies
6. **Testing Strategy**: How to validate the migration

CONSTRAINTS:
- Zero downtime deployment required
- Feature development can't stop
- Team of 12 developers
- 6-week timeline

Provide detailed migration blueprint with code examples for each phase.
```

### **Architecture decision records (ADRs) generator**

```
Generate an ADR for this architectural decision:

DECISION CONTEXT:
We're building a real-time collaborative editing system (like Google Docs) and need to choose the conflict resolution strategy.

OPTIONS CONSIDERED:
1. Operational Transformation (OT)
2. Conflict-free Replicated Data Types (CRDTs)
3. Central locking with optimistic updates

EVALUATION CRITERIA:
- Implementation complexity
- Performance with 100+ concurrent editors
- Offline support requirements
- Consistency guarantees needed

TEAM CONTEXT:
- 6 senior engineers
- 4-month timeline
- Experience with WebRTC, WebSockets
- No prior experience with CRDTs

GENERATE ADR using standard format:
- Title
- Status (proposed/accepted/superseded)
- Context
- Decision
- Consequences (positive/negative)
- Alternatives considered
- Implementation notes
```

## 🔧 **Tools and automation**

### **Prompt versioning system**

```python
from dataclasses import dataclass
from typing import Dict, List
import hashlib
import json

@dataclass
class PromptVersion:
    version: str
    content: str
    metadata: Dict
    performance_metrics: Dict

class PromptManager:
    def __init__(self):
        self.versions: Dict[str, List[PromptVersion]] = {}

    def save_prompt(self, name: str, content: str, metadata: Dict = None):
        version_hash = hashlib.md5(content.encode()).hexdigest()[:8]

        if name not in self.versions:
            self.versions[name] = []

        prompt_version = PromptVersion(
            version=f"v{len(self.versions[name]) + 1}_{version_hash}",
            content=content,
            metadata=metadata or {},
            performance_metrics={}
        )

        self.versions[name].append(prompt_version)
        return prompt_version.version

    def get_best_prompt(self, name: str) -> PromptVersion:
        if name not in self.versions:
            return None

        # Return version with highest performance score
        return max(
            self.versions[name],
            key=lambda v: v.performance_metrics.get('total_score', 0)
        )
```

### **Prompt testing framework**

```python
class PromptTestCase:
    def __init__(self, name, prompt, expected_criteria):
        self.name = name
        self.prompt = prompt
        self.expected_criteria = expected_criteria

    def run_test(self, ai_client):
        response = ai_client.generate(self.prompt)

        results = {}
        for criterion, validator in self.expected_criteria.items():
            results[criterion] = validator(response)

        return {
            'test_name': self.name,
            'passed': all(results.values()),
            'details': results,
            'response': response
        }

# Ejemplo de uso:
def test_code_generation_completeness(response):
    return all([
        'function' in response or 'def' in response or 'class' in response,
        'test' in response.lower(),
        len(response.split('\n')) > 5
    ])

test_case = PromptTestCase(
    "code_generation_basic",
    "Create a Python function to validate email addresses with tests",
    {
        'completeness': test_code_generation_completeness,
        'has_tests': lambda r: 'test_' in r or 'assert' in r,
        'has_docstring': lambda r: '"""' in r or "'''" in r
    }
)
```

## 🚀 **¿Qué sigue?**

Dominas técnicas avanzadas de prompt engineering. Ahora vamos a ver cómo desarrollar aplicaciones completas con LLMs:

**➡️ [Siguiente: Desarrollo con LLMs](./desarrollo-llms.md)**

---

## 📚 **Recursos y referencias**

### **Papers y research:**
- "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models"
- "Constitutional AI: Harmlessness from AI Feedback"
- "Self-Consistency Improves Chain of Thought Reasoning"

### **Tools recomendadas:**
- **PromptPerfect**: Optimización automática de prompts
- **LangSmith**: Prompt debugging y testing
- **Weights & Biases**: Experiment tracking
- **OpenAI Playground**: Rapid prototyping

### **Prompt libraries:**
- [Awesome ChatGPT Prompts](https://github.com/f/awesome-chatgpt-prompts)
- [LangChain Hub](https://smith.langchain.com/hub)
- [OpenAI Cookbook](https://github.com/openai/openai-cookbook)

---

*El prompt engineering avanzado es parte arte, parte ciencia. Con estas técnicas, puedes resolver problemas de arquitectura compleja y obtener outputs de calidad profesional. En la siguiente sección aprenderás a integrar estos prompts en aplicaciones robustas.*