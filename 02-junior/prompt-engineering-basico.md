# 📝 Prompt Engineering Básico

> **El arte de comunicarse con IA.** Aprende a escribir prompts que obtengan exactamente lo que necesitas. Esta es la habilidad más importante para cualquier desarrollador que use IA.

## 🎯 ¿Qué aprenderás aquí?

- ✅ Anatomía de un prompt efectivo
- ✅ Técnicas fundamentales (zero-shot, few-shot, chain of thought)
- ✅ Errores comunes y cómo evitarlos
- ✅ Templates probados para diferentes casos de uso
- ✅ Ejemplos prácticos en múltiples tecnologías

## 🧬 **Anatomía de un prompt efectivo**

### **Estructura básica:**
```
[CONTEXTO] + [TAREA] + [FORMATO] + [RESTRICCIONES] + [EJEMPLOS]
```

### **Desglose detallado:**

| Componente | Propósito | Ejemplo |
|------------|-----------|---------|
| **CONTEXTO** | Proporciona información de fondo | "Soy desarrollador iOS con 2 años de experiencia" |
| **TAREA** | Define qué quieres lograr específicamente | "Crear un sistema de cache de imágenes" |
| **FORMATO** | Especifica cómo quieres la respuesta | "Código Swift con comentarios y tests" |
| **RESTRICCIONES** | Limitaciones técnicas o de tiempo | "Debe usar URLSession, máximo 500 líneas" |
| **EJEMPLOS** | Muestras de lo que esperas | "Similar a SDWebImage pero más simple" |

## 🎯 **Técnicas fundamentales**

### **1. Zero-shot prompting** 🎲
*Dar instrucciones sin ejemplos previos*

**Cuándo usar:** Tareas simples o cuando no tienes ejemplos

**Ejemplo práctico - Android:**
```
❌ MALO:
"Ayúdame con RecyclerView"

✅ BUENO:
"Crea una RecyclerView en Android Kotlin que muestre una lista de usuarios con foto, nombre y email. Incluye ViewHolder pattern y manejo de clicks."
```

**Resultado esperado:** Código completo y funcional

### **2. Few-shot prompting** 📚
*Proporcionar ejemplos para guiar la respuesta*

**Cuándo usar:** Patrones específicos, formatos complejos

**Ejemplo práctico - Flutter:**
```
Crea widgets Flutter siguiendo este patrón:

EJEMPLO 1:
Input: "botón primario"
Output:
```dart
ElevatedButton(
  onPressed: onPressed,
  style: ElevatedButton.styleFrom(
    backgroundColor: Colors.blue,
  ),
  child: Text(text),
)
```

EJEMPLO 2:
Input: "botón secundario"
Output:
```dart
OutlinedButton(
  onPressed: onPressed,
  style: OutlinedButton.styleFrom(
    side: BorderSide(color: Colors.blue),
  ),
  child: Text(text),
)
```

Ahora crea: "botón de peligro"
```

### **3. Chain of Thought (CoT)** 🧠
*Guiar el razonamiento paso a paso*

**Cuándo usar:** Problemas complejos, debugging, arquitectura

**Ejemplo práctico - PHP/Laravel:**
```
Analiza este problema paso a paso:

Tengo una aplicación Laravel que se vuelve lenta cuando hay muchos usuarios conectados.

Piensa paso a paso:
1. ¿Cuáles podrían ser las causas principales?
2. ¿Cómo diagnosticarías cada una?
3. ¿Qué soluciones específicas recomendarías?
4. ¿En qué orden implementarías las optimizaciones?

[Código o logs relevantes aquí]
```

### **4. Role prompting** 🎭
*Asignar un rol específico a la IA*

**Ejemplo - Python:**
```
Actúa como un senior Python developer especializado en Django y performance optimization.

Revisa este código Django y dame feedback como lo haría un tech lead experimentado:

[código aquí]

Enfócate en:
- Problemas de performance
- Vulnerabilidades de seguridad
- Código que no sigue PEP 8
- Mejores prácticas de Django
```

## ❌ **Errores comunes y cómo evitarlos**

### **1. Prompts demasiado vagos**

**❌ Malo:**
```
"Ayúdame con mi app móvil"
```

**✅ Bueno:**
```
"Mi app Flutter tiene lag al hacer scroll en listas grandes.
Tecnologías: Flutter 3.7, Provider, HTTP requests a API REST.
¿Cómo optimizo el performance del ListView para 1000+ elementos?"
```

### **2. Falta de contexto técnico**

**❌ Malo:**
```
"Este código no funciona"
```

**✅ Bueno:**
```
"Este código Swift me da error 'Thread 1: Fatal error: Unexpectedly found nil'.

Stack: iOS 16, Xcode 14, usando async/await
Error en línea: let user = await UserService.getUser(id)

[código completo aquí]

¿Cuál es la causa y cómo lo soluciono?"
```

### **3. No especificar el formato deseado**

**❌ Malo:**
```
"Explica cómo funciona MVVM en Android"
```

**✅ Bueno:**
```
"Explica el patrón MVVM en Android Kotlin con:
1. Diagrama conceptual en texto
2. Ejemplo de código mínimo (ViewModel, View, Model)
3. Ventajas vs MVP
4. Cuándo usarlo vs cuándo no

Formato: Explicación paso a paso con código comentado"
```

### **4. Pedir demasiado en un solo prompt**

**❌ Malo:**
```
"Crea una app completa de e-commerce con login, carrito, pagos, admin panel, tests, documentación y deployment en AWS"
```

**✅ Bueno:**
```
"Ayúdame a diseñar la arquitectura de una app e-commerce.

Fase 1: Define los módulos principales y sus responsabilidades
Stack: React Native, Node.js, PostgreSQL

Después haremos cada módulo por separado."
```

## 🛠️ **Templates probados por caso de uso**

### **Template: Debugging** 🐛
```
PROBLEMA: [Descripción específica del error]
CONTEXTO: [Stack tecnológico, versiones]
CÓDIGO: [Fragmento relevante]
COMPORTAMIENTO ESPERADO: [Qué debería pasar]
COMPORTAMIENTO ACTUAL: [Qué está pasando]
INTENTÉ: [Soluciones que ya probaste]

¿Cuál es la causa raíz y cuál es la solución más efectiva?
```

### **Template: Code Review** 👀
```
Revisa este [LENGUAJE] como un senior developer:

CONTEXTO: [Propósito del código]
STACK: [Tecnologías usadas]
NIVEL: [Tu experiencia]

[CÓDIGO AQUÍ]

Evalúa:
- ⚡ Performance
- 🛡️ Seguridad
- 📖 Legibilidad
- 🏗️ Arquitectura
- ✅ Best practices

Formato: Lista priorizada con ejemplos de mejora
```

### **Template: Feature Implementation** ⚡
```
Implementa [FEATURE] en [STACK]:

REQUISITOS:
- [Requisito funcional 1]
- [Requisito funcional 2]
- [Restricción técnica 1]

CONTEXTO: [Arquitectura actual]
TIEMPO: [Limitación de tiempo]
NIVEL: [Tu experiencia]

ENTREGABLE: Código + tests + documentación básica
```

### **Template: Learning/Explanation** 📚
```
Explícame [CONCEPTO] para un desarrollador [NIVEL]:

CONTEXTO: Trabajo con [STACK]
OBJETIVO: [Qué quieres lograr]
FORMATO: [Cómo quieres aprender]

Incluye:
- Concepto básico
- Ejemplo práctico en [LENGUAJE]
- Cuándo usar vs cuándo no
- Errores comunes
- Recursos para profundizar
```

## 🎨 **Ejemplos prácticos por tecnología**

### **iOS/Swift - Optimización de memoria**
```
Actúa como un iOS developer senior especializado en memory management.

CONTEXTO: App iOS con problemas de memoria
STACK: Swift 5, UIKit, Core Data, SDWebImage
SÍNTOMAS: Memory warnings, crashes en dispositivos antiguos

Analiza step-by-step:
1. ¿Cuáles son las causas más comunes de memory leaks en UIKit?
2. ¿Cómo detectarías el problema usando Instruments?
3. ¿Qué patrones de código específicos debo revisar?

CÓDIGO SOSPECHOSO:
[imagen de código aquí]

Dame un plan de acción priorizado para resolver los memory issues.
```

### **Android/Kotlin - Arquitectura MVVM**
```
Diseña la arquitectura MVVM para una pantalla de login en Android.

REQUISITOS:
- Validación en tiempo real
- Manejo de estados (loading, success, error)
- Navegación después de login exitoso
- Testing unitario

STACK: Kotlin, Jetpack Compose, Hilt, Retrofit, Room
NIVEL: Intermedio

ENTREGABLE:
1. Estructura de packages
2. Interfaces y contratos
3. ViewModel con LiveData/StateFlow
4. Repository pattern
5. Ejemplo de Unit Test

Código limpio y bien documentado por favor.
```

### **Flutter - Performance en listas**
```
Mi ListView en Flutter tiene performance issues:

PROBLEMA: Lag/stuttering al hacer scroll
DATOS: 500-1000 elementos
CONTENIDO: Imagen + título + descripción + botones

STACK: Flutter 3.7, Provider, cached_network_image
DISPOSITIVOS: Funciona bien en flagship, mal en gama media

CÓDIGO ACTUAL:
```dart
ListView.builder(
  itemCount: items.length,
  itemBuilder: (context, index) {
    return ExpensiveWidget(item: items[index]);
  },
)
```

¿Cuál es la mejor estrategia para optimizar?
Prioriza soluciones por impacto vs esfuerzo de implementación.
```

### **Python/Django - API Design**
```
Diseña una API REST clean para un sistema de blog:

FUNCIONALIDADES:
- CRUD de posts
- Sistema de comentarios
- Autenticación JWT
- Filtros y búsqueda
- Paginación

STACK: Django 4.2, DRF, PostgreSQL, Redis
RESTRICCIONES: Debe escalar a 10k usuarios activos

ARQUITECTURA DESEADA:
- Separation of concerns
- Easy testing
- Clear error handling
- API versioning

ENTREGABLE:
1. URL patterns
2. Serializers
3. ViewSets
4. Models relationships
5. Ejemplo de test

Sigue las mejores prácticas de DRF y RESTful design.
```

### **PHP/Laravel - Refactoring legacy**
```
Tengo código PHP legacy que necesito refactorizar a Laravel:

CÓDIGO LEGACY (ejemplo):
```php
// user_management.php
$conn = mysqli_connect($host, $user, $pass, $db);
$sql = "SELECT * FROM users WHERE active = 1";
$result = mysqli_query($conn, $sql);
while($row = mysqli_fetch_array($result)) {
    echo "<tr><td>".$row['name']."</td></tr>";
}
```

OBJETIVOS:
- Migrar a Laravel 10
- Implementar MVC pattern
- Añadir validación y seguridad
- Mantener funcionalidad existente

PLAN PASO A PASO:
1. ¿Cómo estructuro esto en Laravel?
2. ¿Qué componentes necesito (Model, Controller, View)?
3. ¿Cómo manejo la migración de datos?
4. ¿Qué security improvements debo implementar?

Dame el código Laravel equivalente con mejores prácticas.
```

## 📊 **Midiendo la efectividad de tus prompts**

### **Señales de un buen prompt:** ✅
- La IA hace preguntas de clarificación relevantes
- Las respuestas son específicas y detalladas
- El código generado compila y funciona
- Incluye explicaciones del "por qué"
- Menciona limitaciones y consideraciones

### **Señales de un mal prompt:** ❌
- Respuestas genéricas o demasiado básicas
- Código que no compila o no funciona
- Falta de contexto específico de tu stack
- No aborda tu problema real
- Demasiado teórico, poco práctico

### **Iteración de prompts:**
```
Prompt v1 → Respuesta mediocre → Refinar contexto → Prompt v2 → Mejor respuesta
```

**Ejemplo de iteración:**
```
v1: "Optimiza esta función"
v2: "Optimiza esta función Python que procesa 10k registros y toma 30 segundos"
v3: "Optimiza esta función Python que procesa 10k registros de una CSV, toma 30 segundos, y necesita reducirse a <5 segundos para producción"
```

## 🚀 **Workflow de prompt engineering**

### **1. Preparación** 📋
- [ ] Define objetivo específico
- [ ] Recopila contexto técnico
- [ ] Identifica restricciones
- [ ] Piensa en formato deseado

### **2. Primera versión** ✍️
- [ ] Escribe prompt básico
- [ ] Incluye contexto mínimo
- [ ] Especifica formato
- [ ] Añade un ejemplo si es necesario

### **3. Iteración** 🔄
- [ ] Evalúa la respuesta
- [ ] Identifica qué falta
- [ ] Refina el prompt
- [ ] Prueba versión mejorada

### **4. Validación** ✅
- [ ] ¿El código funciona?
- [ ] ¿Cumple los requisitos?
- [ ] ¿Es mantenible?
- [ ] ¿Hay mejores alternativas?


## 🚀 **¿Qué sigue?**

Ya dominas los fundamentos de prompt engineering. Ahora vamos a ver cómo integrar IA directamente en tu código:

**➡️ [Siguiente: Integrando IA en código](./integracion-codigo.md)**

---

## 📚 **Cheat sheet: Palabras clave que funcionan**

### **Para obtener código específico:**
- "Implementa" (vs "ayúdame")
- "Usando [tecnología específica]"
- "Con tests unitarios"
- "Siguiendo [patrón/convención]"

### **Para debugging:**
- "Analiza paso a paso"
- "Identifica la causa raíz"
- "¿Qué podría estar causando...?"
- "Dame un plan de debugging"

### **Para explicaciones:**
- "Explica como si fuera [nivel]"
- "Con ejemplos prácticos"
- "Ventajas vs desventajas"
- "Cuándo usar vs cuándo no"

### **Para optimización:**
- "Identifica cuellos de botella"
- "Prioriza por impacto"
- "Optimiza para [métrica específica]"
- "Mantén [restricción importante]"

---

*Un buen prompt es como un buen brief: específico, completo y orientado a resultados. En la siguiente sección aprenderás a automatizar estas interacciones integrando IA en tu código.*