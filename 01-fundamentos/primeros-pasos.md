# 🚀 Primeros pasos con IA

> **Tu primera interacción práctica.** Vamos a pasar de la teoría a la práctica. En esta sección aprenderás a hacer tu primera consulta a una IA y entender los conceptos básicos que necesitas como desarrollador.

## 🎯 ¿Qué aprenderás aquí?

- ✅ Hacer tu primera consulta efectiva a ChatGPT/Claude
- ✅ Entender qué son las APIs de IA y cómo funcionan
- ✅ Conceptos básicos de prompts
- ✅ Diferencias entre interfaces web y APIs
- ✅ Cómo evaluar las respuestas de la IA

## 🌐 **Interfaces vs APIs: ¿Cuál usar?**

### **Interfaces Web** 🖥️
**Para empezar y experimentar:**

| Plataforma | URL | Mejor para |
|------------|-----|------------|
| **ChatGPT** | [chat.openai.com](https://chat.openai.com) | Conversaciones, brainstorming, explicaciones |
| **Claude** | [claude.ai](https://claude.ai) | Análisis de código, documentación, reasoning |
| **Gemini** | [gemini.google.com](https://gemini.google.com) | Búsquedas, investigación, multimodal |

### **APIs** 🔌
**Para integrar en tu código:**

| Proveedor | API | Mejor para |
|-----------|-----|------------|
| **OpenAI** | `api.openai.com` | Aplicaciones de producción |
| **Anthropic** | `api.anthropic.com` | Tareas complejas, análisis |
| **Google** | `ai.google.dev` | Integración con servicios Google |

## 💬 **Tu primera consulta efectiva**

### **Paso 1: Accede a una plataforma**
Recomendamos empezar con **Claude** ([claude.ai](https://claude.ai)) porque:
- ✅ Excelente para desarrolladores
- ✅ Maneja bien el código
- ✅ Respuestas detalladas y precisas

### **Paso 2: Formula tu primera pregunta**

**❌ Pregunta vaga:**
```
"Ayúdame con JavaScript"
```

**✅ Pregunta específica:**
```
"Explícame cómo funciona el hoisting en JavaScript con un ejemplo práctico"
```

### **Paso 3: Proporciona contexto**

**❌ Sin contexto:**
```
"Este código no funciona"
```

**✅ Con contexto:**
```
"Tengo este código React que debería mostrar una lista de usuarios,
pero me da un error 'map is not a function'. ¿Puedes ayudarme?

[código aquí]
```

## 📝 **Anatomía de un buen prompt**

### **Estructura básica:**
```
[CONTEXTO] + [TAREA ESPECÍFICA] + [FORMATO DESEADO]
```

### **Ejemplo práctico:**
```
CONTEXTO: "Soy desarrollador frontend trabajando en React"
TAREA: "Necesito crear un componente reutilizable para formularios"
FORMATO: "Muéstrame el código con comentarios explicativos"
```

**Prompt completo:**
```
Soy desarrollador frontend trabajando en React. Necesito crear un
componente reutilizable para formularios que maneje validación básica
(campos requeridos, email válido). Muéstrame el código con comentarios
explicativos y un ejemplo de uso.
```

## 🛠️ **Tipos de consultas útiles para desarrolladores**

### **1. Explicación de conceptos** 📚
```
"Explícame la diferencia entre Promise y async/await en JavaScript
con ejemplos de cuándo usar cada uno"
```

### **2. Depuración** 🐛
```
"Este código Python me da el error [error específico].
¿Puedes identificar el problema y sugerir una solución?

[código aquí]
```

### **3. Revisión de código** 👀
```
"¿Puedes revisar esta función y sugerir mejoras en términos de
rendimiento, legibilidad y buenas prácticas?

[código aquí]
```

### **4. Generación de código** ⚡
```
"Crea una función en Python que conecte a una API REST,
maneje errores HTTP y tenga retry logic. Incluye tests unitarios."
```

### **5. Arquitectura** 🏗️
```
"Estoy diseñando una app de e-commerce. ¿Cuál sería una buena
arquitectura de microservicios? Incluye patrones de diseño recomendados."
```

## 🔑 **Conceptos básicos de APIs de IA**

### **¿Qué es una API de IA?**
Una API (Application Programming Interface) de IA es un punto de acceso que te permite integrar capacidades de inteligencia artificial directamente en tu código.

### **Flujo básico:**
```
Tu App → Envía prompt → API de IA → Procesa → Devuelve respuesta → Tu App
```

### **Ejemplo conceptual (HTTP):**
```http
POST https://api.openai.com/v1/chat/completions
Content-Type: application/json
Authorization: Bearer YOUR_API_KEY

{
  "model": "gpt-4",
  "messages": [
    {"role": "user", "content": "Explica qué es recursión en programación"}
  ]
}
```

### **Respuesta:**
```json
{
  "choices": [
    {
      "message": {
        "content": "La recursión es una técnica de programación donde..."
      }
    }
  ]
}
```

## 🎨 **Buenas prácticas desde el inicio**

### **✅ Haz esto:**
- **Sé específico** en tus preguntas
- **Proporciona contexto** de tu stack tecnológico
- **Incluye código** cuando sea relevante
- **Pregunta por ejemplos** prácticos
- **Pide explicaciones** paso a paso

### **❌ Evita esto:**
- Preguntas demasiado vagas
- Pedir código sin entender cómo funciona
- Asumir que la IA conoce tu proyecto específico
- Usar la primera respuesta sin verificar

## 🔍 **Evaluando respuestas de IA**

### **Señales de buena respuesta:** ✅
- Responde directamente tu pregunta
- Incluye ejemplos prácticos
- Explica el "por qué", no solo el "cómo"
- Menciona limitaciones o consideraciones
- El código compila y funciona

### **Señales de alerta:** ⚠️
- Respuestas demasiado genéricas
- Código que no compila
- Falta de explicación
- Información contradictoria
- No menciona limitaciones

## 🚀 **¿Qué sigue?**

Ya sabes cómo interactuar básicamente con IA. El siguiente paso es aprender cómo integrarla en tu flujo de desarrollo diario:

**➡️ [Siguiente: IA en el Desarrollo Diario](../02-junior/ia-desarrollo-diario.md)**

---

## 📚 **Términos clave**

| Término | Definición |
|---------|------------|
| **Prompt** | La pregunta o instrucción que le das a la IA |
| **API** | Interfaz de programación que permite integrar IA en tu código |
| **Token** | Unidad básica de texto que procesa la IA (aprox. 4 caracteres) |
| **Contexto** | Información adicional que ayuda a la IA a dar mejores respuestas |
| **Temperatura** | Parámetro que controla la creatividad vs precisión de las respuestas |

## 🤔 **FAQ**

**P: ¿Necesito pagar para usar IA?**
R: Las interfaces web tienen planes gratuitos con limitaciones. Las APIs requieren pago por uso.

**P: ¿Es seguro poner mi código en estas plataformas?**
R: Para código no confidencial, sí. Para código propietario, revisa las políticas de privacidad y considera APIs on-premise.

**P: ¿La IA puede reemplazar Stack Overflow?**
R: Es complementaria. La IA es excelente para explicaciones rápidas, pero Stack Overflow sigue siendo valioso para problemas específicos y discusiones de la comunidad.

---

*¿Listo para dar el siguiente paso? En la próxima sección veremos cómo integrar IA en tu workflow diario de desarrollo.*