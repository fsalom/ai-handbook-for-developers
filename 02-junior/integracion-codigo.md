# 🔌 Integrando IA en tu código

> **De la consola al código.** Ya sabes hacer prompts efectivos. Ahora vamos a integrar IA directamente en tus aplicaciones con APIs REST, manejo de errores robusto y ejemplos prácticos en múltiples tecnologías.

## 🎯 ¿Qué aprenderás aquí?

- ✅ Conceptos básicos de APIs de IA
- ✅ Autenticación y configuración
- ✅ Manejo robusto de respuestas y errores
- ✅ Ejemplos prácticos en Python, iOS, Android, Flutter y PHP
- ✅ Buenas prácticas de integración
- ✅ Optimización de costos y performance

## 🌐 **APIs de IA principales**

### **Comparación de proveedores:**

| Proveedor | API Base | Fortaleza | Mejor para | Pricing |
|-----------|----------|-----------|------------|---------|
| **OpenAI** | `api.openai.com` | GPT-4, Code generation | Apps generales, chatbots | $0.03/1k tokens |
| **Anthropic** | `api.anthropic.com` | Claude, Análisis complejo | Code analysis, reasoning | $0.25/1k tokens |
| **Google** | `ai.google.dev` | Gemini, Multimodal | Imágenes, integración Google | $0.125/1k tokens |
| **Cohere** | `api.cohere.com` | Embeddings, Classification | Search, classification | $0.02/1k tokens |

### **Casos de uso por API:**

| Caso de uso | API recomendada | Por qué |
|-------------|-----------------|---------|
| **Chatbot simple** | OpenAI GPT-3.5 | Costo-efectivo, rápido |
| **Code analysis** | Anthropic Claude | Mejor razonamiento |
| **Content generation** | OpenAI GPT-4 | Creatividad alta |
| **Image understanding** | Google Gemini | Multimodal nativo |
| **Search/RAG** | Cohere | Embeddings optimizados |

## 🔑 **Configuración inicial**

### **1. Obtener API Keys**

**OpenAI:**
1. Ve a [platform.openai.com](https://platform.openai.com)
2. Crea cuenta → API Keys → Create new key
3. Guarda la key: `sk-proj-...`

**Anthropic:**
1. Ve a [console.anthropic.com](https://console.anthropic.com)
2. Settings → API Keys → Create Key
3. Guarda la key: `sk-ant-...`

### **2. Variables de entorno**

**Todas las tecnologías:**
```bash
# .env file
OPENAI_API_KEY=sk-proj-your-key-here
ANTHROPIC_API_KEY=sk-ant-your-key-here
GOOGLE_AI_KEY=your-google-key
```

⚠️ **NUNCA hardcodees API keys en tu código**

## 💻 **Ejemplos por tecnología**

### **Python - Análisis de código con Claude**

**Instalación:**
```bash
pip install anthropic python-dotenv
```

**Código completo:**
```python
import os
import anthropic
from dotenv import load_dotenv
import json
from typing import Optional, Dict, Any

# Cargar variables de entorno
load_dotenv()

class AICodeAnalyzer:
    def __init__(self):
        self.client = anthropic.Anthropic(
            api_key=os.getenv("ANTHROPIC_API_KEY")
        )

    def analyze_code(self, code: str, language: str) -> Optional[Dict[str, Any]]:
        """
        Analiza código usando Claude y devuelve sugerencias
        """
        try:
            prompt = f"""
            Analiza este código {language} y proporciona feedback estructurado:

            ```{language}
            {code}
            ```

            Devuelve respuesta en JSON con esta estructura:
            {{
                "issues": [
                    {{"type": "performance", "description": "...", "line": 10}},
                    {{"type": "security", "description": "...", "line": 15}}
                ],
                "suggestions": ["sugerencia 1", "sugerencia 2"],
                "score": 8.5
            }}
            """

            response = self.client.messages.create(
                model="claude-3-sonnet-20240229",
                max_tokens=1000,
                messages=[{"role": "user", "content": prompt}]
            )

            # Parsear respuesta JSON
            analysis = json.loads(response.content[0].text)
            return analysis

        except anthropic.RateLimitError:
            print("Rate limit alcanzado. Esperando...")
            return None
        except anthropic.APIError as e:
            print(f"Error de API: {e}")
            return None
        except json.JSONDecodeError:
            print("Respuesta no válida de la API")
            return None
        except Exception as e:
            print(f"Error inesperado: {e}")
            return None

# Uso del analizador
def main():
    analyzer = AICodeAnalyzer()

    # Código de ejemplo para analizar
    python_code = '''
    def get_user(id):
        conn = sqlite3.connect("users.db")
        cursor = conn.cursor()
        cursor.execute(f"SELECT * FROM users WHERE id = {id}")
        result = cursor.fetchone()
        return result
    '''

    analysis = analyzer.analyze_code(python_code, "python")

    if analysis:
        print("📊 Análisis completado:")
        print(f"Score: {analysis['score']}/10")
        print("\n🚨 Issues encontrados:")
        for issue in analysis['issues']:
            print(f"- {issue['type']}: {issue['description']} (línea {issue['line']})")

        print("\n💡 Sugerencias:")
        for suggestion in analysis['suggestions']:
            print(f"- {suggestion}")

if __name__ == "__main__":
    main()
```

### **iOS/Swift - Chatbot con OpenAI**

**Configuración en Xcode:**
```swift
// Package.swift dependencies
.package(url: "https://github.com/MacPaw/OpenAI.git", from: "0.2.4")
```

**Código completo:**
```swift
import Foundation
import OpenAI

class AIChatService: ObservableObject {
    private let openAI: OpenAI
    @Published var messages: [ChatMessage] = []
    @Published var isLoading = false

    init() {
        // Cargar API key desde Info.plist (más seguro)
        guard let apiKey = Bundle.main.infoDictionary?["OPENAI_API_KEY"] as? String else {
            fatalError("OPENAI_API_KEY not found in Info.plist")
        }
        self.openAI = OpenAI(apiToken: apiKey)
    }

    func sendMessage(_ text: String) async {
        let userMessage = ChatMessage(role: .user, content: text)

        DispatchQueue.main.async {
            self.messages.append(userMessage)
            self.isLoading = true
        }

        do {
            let query = ChatQuery(
                model: .gpt3_5Turbo,
                messages: messages.map { message in
                    Chat(role: message.role.toChatRole(), content: message.content)
                },
                maxTokens: 150,
                temperature: 0.7
            )

            let result = try await openAI.chats(query: query)

            if let response = result.choices.first?.message.content {
                let aiMessage = ChatMessage(role: .assistant, content: response)

                DispatchQueue.main.async {
                    self.messages.append(aiMessage)
                    self.isLoading = false
                }
            }

        } catch let error as APIErrorResponse {
            DispatchQueue.main.async {
                self.handleAPIError(error)
                self.isLoading = false
            }
        } catch {
            DispatchQueue.main.async {
                self.handleGenericError(error)
                self.isLoading = false
            }
        }
    }

    private func handleAPIError(_ error: APIErrorResponse) {
        switch error.error.type {
        case .rateLimitExceeded:
            print("Rate limit exceeded. Please wait.")
        case .insufficientQuota:
            print("Insufficient quota. Check your plan.")
        case .invalidRequestError:
            print("Invalid request: \(error.error.message)")
        default:
            print("API Error: \(error.error.message)")
        }
    }

    private func handleGenericError(_ error: Error) {
        print("Network error: \(error.localizedDescription)")
    }
}

// Modelo de datos
struct ChatMessage: Identifiable {
    let id = UUID()
    let role: MessageRole
    let content: String
    let timestamp = Date()
}

enum MessageRole {
    case user, assistant

    func toChatRole() -> Chat.Role {
        switch self {
        case .user: return .user
        case .assistant: return .assistant
        }
    }
}

// Vista SwiftUI
struct ChatView: View {
    @StateObject private var chatService = AIChatService()
    @State private var messageText = ""

    var body: some View {
        VStack {
            ScrollView {
                LazyVStack(alignment: .leading) {
                    ForEach(chatService.messages) { message in
                        MessageBubble(message: message)
                    }

                    if chatService.isLoading {
                        HStack {
                            ProgressView()
                            Text("Thinking...")
                        }
                        .padding()
                    }
                }
            }

            HStack {
                TextField("Type a message...", text: $messageText)
                    .textFieldStyle(RoundedBorderTextFieldStyle())

                Button("Send") {
                    if !messageText.isEmpty {
                        Task {
                            await chatService.sendMessage(messageText)
                            messageText = ""
                        }
                    }
                }
                .disabled(chatService.isLoading)
            }
            .padding()
        }
        .navigationTitle("AI Chat")
    }
}
```

### **Android/Kotlin - Text generation con coroutines**

**build.gradle (Module: app):**
```kotlin
implementation "com.squareup.okhttp3:okhttp:4.12.0"
implementation "org.jetbrains.kotlinx:kotlinx-coroutines-android:1.7.3"
implementation "com.squareup.moshi:moshi-kotlin:1.15.0"
```

**Código completo:**
```kotlin
import kotlinx.coroutines.*
import okhttp3.*
import okhttp3.MediaType.Companion.toMediaType
import okhttp3.RequestBody.Companion.toRequestBody
import com.squareup.moshi.Moshi
import com.squareup.moshi.kotlin.reflect.KotlinJsonAdapterFactory
import java.io.IOException

// Data classes para la API
data class OpenAIRequest(
    val model: String,
    val messages: List<Message>,
    val max_tokens: Int = 150,
    val temperature: Double = 0.7
)

data class Message(
    val role: String,
    val content: String
)

data class OpenAIResponse(
    val choices: List<Choice>
)

data class Choice(
    val message: Message
)

class AITextGenerator {
    private val client = OkHttpClient.Builder()
        .connectTimeout(30, java.util.concurrent.TimeUnit.SECONDS)
        .readTimeout(30, java.util.concurrent.TimeUnit.SECONDS)
        .build()

    private val moshi = Moshi.Builder()
        .add(KotlinJsonAdapterFactory())
        .build()

    private val apiKey = BuildConfig.OPENAI_API_KEY // Definido en build.gradle

    suspend fun generateText(
        prompt: String,
        onSuccess: (String) -> Unit,
        onError: (String) -> Unit
    ) = withContext(Dispatchers.IO) {
        try {
            val request = OpenAIRequest(
                model = "gpt-3.5-turbo",
                messages = listOf(
                    Message(role = "user", content = prompt)
                )
            )

            val requestJson = moshi.adapter(OpenAIRequest::class.java).toJson(request)
            val requestBody = requestJson.toRequestBody("application/json".toMediaType())

            val httpRequest = Request.Builder()
                .url("https://api.openai.com/v1/chat/completions")
                .addHeader("Authorization", "Bearer $apiKey")
                .addHeader("Content-Type", "application/json")
                .post(requestBody)
                .build()

            val response = client.newCall(httpRequest).execute()

            when {
                response.isSuccessful -> {
                    val responseBody = response.body?.string()
                    val openAIResponse = moshi.adapter(OpenAIResponse::class.java)
                        .fromJson(responseBody)

                    val generatedText = openAIResponse?.choices?.firstOrNull()?.message?.content
                        ?: "No response generated"

                    withContext(Dispatchers.Main) {
                        onSuccess(generatedText)
                    }
                }
                response.code == 429 -> {
                    withContext(Dispatchers.Main) {
                        onError("Rate limit exceeded. Please wait and try again.")
                    }
                }
                response.code == 401 -> {
                    withContext(Dispatchers.Main) {
                        onError("Invalid API key. Please check your configuration.")
                    }
                }
                else -> {
                    withContext(Dispatchers.Main) {
                        onError("HTTP Error: ${response.code} - ${response.message}")
                    }
                }
            }

        } catch (e: IOException) {
            withContext(Dispatchers.Main) {
                onError("Network error: ${e.message}")
            }
        } catch (e: Exception) {
            withContext(Dispatchers.Main) {
                onError("Unexpected error: ${e.message}")
            }
        }
    }
}

// Uso en Activity/Fragment
class MainActivity : AppCompatActivity() {
    private val aiGenerator = AITextGenerator()
    private val scope = CoroutineScope(Dispatchers.Main + SupervisorJob())

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        findViewById<Button>(R.id.generateButton).setOnClickListener {
            val prompt = findViewById<EditText>(R.id.promptEditText).text.toString()

            if (prompt.isNotEmpty()) {
                generateAIResponse(prompt)
            }
        }
    }

    private fun generateAIResponse(prompt: String) {
        scope.launch {
            aiGenerator.generateText(
                prompt = prompt,
                onSuccess = { response ->
                    findViewById<TextView>(R.id.responseTextView).text = response
                },
                onError = { error ->
                    findViewById<TextView>(R.id.responseTextView).text = "Error: $error"
                }
            )
        }
    }

    override fun onDestroy() {
        super.onDestroy()
        scope.cancel()
    }
}
```

### **Flutter - Code completion assistant**

**pubspec.yaml:**
```yaml
dependencies:
  http: ^1.1.0
  flutter_dotenv: ^5.1.0
  provider: ^6.1.1
```

**Código completo:**
```dart
import 'dart:convert';
import 'dart:io';
import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
import 'package:flutter_dotenv/flutter_dotenv.dart';

// Modelos de datos
class AIRequest {
  final String model;
  final List<Map<String, String>> messages;
  final int maxTokens;
  final double temperature;

  AIRequest({
    required this.model,
    required this.messages,
    this.maxTokens = 150,
    this.temperature = 0.7,
  });

  Map<String, dynamic> toJson() {
    return {
      'model': model,
      'messages': messages,
      'max_tokens': maxTokens,
      'temperature': temperature,
    };
  }
}

class AIResponse {
  final String content;
  final String error;

  AIResponse({this.content = '', this.error = ''});

  factory AIResponse.fromJson(Map<String, dynamic> json) {
    try {
      final choices = json['choices'] as List;
      final content = choices[0]['message']['content'] as String;
      return AIResponse(content: content);
    } catch (e) {
      return AIResponse(error: 'Failed to parse response: $e');
    }
  }
}

// Servicio de IA
class AIService {
  static const String _baseUrl = 'https://api.openai.com/v1/chat/completions';
  late final String _apiKey;

  AIService() {
    _apiKey = dotenv.env['OPENAI_API_KEY'] ?? '';
    if (_apiKey.isEmpty) {
      throw Exception('OPENAI_API_KEY not found in .env file');
    }
  }

  Future<AIResponse> completeCode({
    required String code,
    required String language,
    String context = '',
  }) async {
    try {
      final prompt = '''
      Complete this $language code with best practices:

      Context: $context

      Code to complete:
      ```$language
      $code
      ```

      Return only the completed code, no explanations.
      ''';

      final request = AIRequest(
        model: 'gpt-3.5-turbo',
        messages: [
          {'role': 'user', 'content': prompt}
        ],
      );

      final response = await http.post(
        Uri.parse(_baseUrl),
        headers: {
          'Content-Type': 'application/json',
          'Authorization': 'Bearer $_apiKey',
        },
        body: jsonEncode(request.toJson()),
      );

      if (response.statusCode == 200) {
        final jsonResponse = jsonDecode(response.body);
        return AIResponse.fromJson(jsonResponse);
      } else if (response.statusCode == 429) {
        return AIResponse(error: 'Rate limit exceeded. Please wait.');
      } else if (response.statusCode == 401) {
        return AIResponse(error: 'Invalid API key.');
      } else {
        return AIResponse(error: 'HTTP ${response.statusCode}: ${response.reasonPhrase}');
      }
    } on SocketException {
      return AIResponse(error: 'No internet connection.');
    } on HttpException catch (e) {
      return AIResponse(error: 'HTTP error: ${e.message}');
    } catch (e) {
      return AIResponse(error: 'Unexpected error: $e');
    }
  }
}

// Provider para manejo de estado
class CodeCompletionProvider extends ChangeNotifier {
  final AIService _aiService = AIService();

  String _code = '';
  String _completedCode = '';
  bool _isLoading = false;
  String _error = '';

  String get code => _code;
  String get completedCode => _completedCode;
  bool get isLoading => _isLoading;
  String get error => _error;

  void updateCode(String newCode) {
    _code = newCode;
    notifyListeners();
  }

  Future<void> completeCode(String language) async {
    if (_code.isEmpty) return;

    _isLoading = true;
    _error = '';
    notifyListeners();

    try {
      final response = await _aiService.completeCode(
        code: _code,
        language: language,
        context: 'This is part of a larger application',
      );

      if (response.error.isNotEmpty) {
        _error = response.error;
      } else {
        _completedCode = response.content;
      }
    } catch (e) {
      _error = 'Failed to complete code: $e';
    } finally {
      _isLoading = false;
      notifyListeners();
    }
  }
}

// Widget principal
class CodeCompletionScreen extends StatefulWidget {
  @override
  _CodeCompletionScreenState createState() => _CodeCompletionScreenState();
}

class _CodeCompletionScreenState extends State<CodeCompletionScreen> {
  final TextEditingController _codeController = TextEditingController();
  String _selectedLanguage = 'dart';
  late CodeCompletionProvider _provider;

  @override
  void initState() {
    super.initState();
    _provider = CodeCompletionProvider();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('AI Code Completion'),
        backgroundColor: Colors.blue,
      ),
      body: ChangeNotifierProvider.value(
        value: _provider,
        child: Consumer<CodeCompletionProvider>(
          builder: (context, provider, child) {
            return Padding(
              padding: const EdgeInsets.all(16.0),
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.stretch,
                children: [
                  // Selector de lenguaje
                  DropdownButton<String>(
                    value: _selectedLanguage,
                    onChanged: (value) {
                      setState(() {
                        _selectedLanguage = value!;
                      });
                    },
                    items: ['dart', 'python', 'javascript', 'swift', 'kotlin']
                        .map((lang) => DropdownMenuItem(
                              value: lang,
                              child: Text(lang.toUpperCase()),
                            ))
                        .toList(),
                  ),

                  SizedBox(height: 16),

                  // Input de código
                  Expanded(
                    flex: 1,
                    child: TextField(
                      controller: _codeController,
                      maxLines: null,
                      expands: true,
                      decoration: InputDecoration(
                        labelText: 'Your Code',
                        border: OutlineInputBorder(),
                        alignLabelWithHint: true,
                      ),
                      onChanged: provider.updateCode,
                    ),
                  ),

                  SizedBox(height: 16),

                  // Botón de completar
                  ElevatedButton(
                    onPressed: provider.isLoading
                        ? null
                        : () => provider.completeCode(_selectedLanguage),
                    child: provider.isLoading
                        ? CircularProgressIndicator(color: Colors.white)
                        : Text('Complete Code'),
                  ),

                  SizedBox(height: 16),

                  // Resultado
                  Expanded(
                    flex: 1,
                    child: Container(
                      padding: EdgeInsets.all(12),
                      decoration: BoxDecoration(
                        border: Border.all(color: Colors.grey),
                        borderRadius: BorderRadius.circular(4),
                      ),
                      child: provider.error.isNotEmpty
                          ? Text(
                              'Error: ${provider.error}',
                              style: TextStyle(color: Colors.red),
                            )
                          : SingleChildScrollView(
                              child: Text(
                                provider.completedCode.isEmpty
                                    ? 'Completed code will appear here...'
                                    : provider.completedCode,
                                style: TextStyle(
                                  fontFamily: 'monospace',
                                  fontSize: 12,
                                ),
                              ),
                            ),
                    ),
                  ),
                ],
              ),
            );
          },
        ),
      ),
    );
  }

  @override
  void dispose() {
    _codeController.dispose();
    super.dispose();
  }
}

// Main app
void main() async {
  await dotenv.load(fileName: ".env");
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'AI Code Completion',
      theme: ThemeData(primarySwatch: Colors.blue),
      home: CodeCompletionScreen(),
    );
  }
}
```

### **PHP/Laravel - Content generation service**

**Instalación:**
```bash
composer require guzzlehttp/guzzle
composer require vlucas/phpdotenv
```

**Código completo:**
```php
<?php

namespace App\Services;

use GuzzleHttp\Client;
use GuzzleHttp\Exception\RequestException;
use Illuminate\Support\Facades\Log;
use Illuminate\Support\Facades\Cache;

class AIContentService
{
    private Client $httpClient;
    private string $apiKey;
    private string $baseUrl;

    public function __construct()
    {
        $this->httpClient = new Client([
            'timeout' => 30,
            'connect_timeout' => 10,
        ]);

        $this->apiKey = config('services.openai.key');
        $this->baseUrl = 'https://api.openai.com/v1/chat/completions';

        if (empty($this->apiKey)) {
            throw new \Exception('OpenAI API key not configured');
        }
    }

    /**
     * Genera contenido usando OpenAI
     *
     * @param string $prompt El prompt para generar contenido
     * @param string $type Tipo de contenido (blog, email, social)
     * @param int $maxTokens Máximo número de tokens
     * @return array
     */
    public function generateContent(
        string $prompt,
        string $type = 'general',
        int $maxTokens = 500
    ): array {
        try {
            // Cache key para evitar llamadas repetidas
            $cacheKey = 'ai_content_' . md5($prompt . $type . $maxTokens);

            // Verificar cache primero
            if (Cache::has($cacheKey)) {
                return Cache::get($cacheKey);
            }

            $systemPrompt = $this->getSystemPrompt($type);

            $requestData = [
                'model' => 'gpt-3.5-turbo',
                'messages' => [
                    [
                        'role' => 'system',
                        'content' => $systemPrompt
                    ],
                    [
                        'role' => 'user',
                        'content' => $prompt
                    ]
                ],
                'max_tokens' => $maxTokens,
                'temperature' => 0.7,
                'top_p' => 1,
                'frequency_penalty' => 0,
                'presence_penalty' => 0,
            ];

            $response = $this->httpClient->post($this->baseUrl, [
                'headers' => [
                    'Authorization' => 'Bearer ' . $this->apiKey,
                    'Content-Type' => 'application/json',
                ],
                'json' => $requestData,
            ]);

            $responseData = json_decode($response->getBody()->getContents(), true);

            if (!isset($responseData['choices'][0]['message']['content'])) {
                throw new \Exception('Invalid response from OpenAI API');
            }

            $result = [
                'success' => true,
                'content' => trim($responseData['choices'][0]['message']['content']),
                'tokens_used' => $responseData['usage']['total_tokens'] ?? 0,
                'model' => $responseData['model'] ?? 'unknown',
                'cached' => false,
            ];

            // Cache por 1 hora
            Cache::put($cacheKey, $result, 3600);

            Log::info('AI content generated', [
                'type' => $type,
                'tokens_used' => $result['tokens_used'],
                'prompt_length' => strlen($prompt),
            ]);

            return $result;

        } catch (RequestException $e) {
            return $this->handleApiError($e);
        } catch (\Exception $e) {
            Log::error('AI content generation failed', [
                'error' => $e->getMessage(),
                'prompt' => substr($prompt, 0, 100),
            ]);

            return [
                'success' => false,
                'error' => 'Failed to generate content: ' . $e->getMessage(),
                'content' => '',
            ];
        }
    }

    /**
     * Maneja errores específicos de la API
     */
    private function handleApiError(RequestException $e): array
    {
        $statusCode = $e->getResponse() ? $e->getResponse()->getStatusCode() : 0;
        $errorMessage = $e->getMessage();

        switch ($statusCode) {
            case 400:
                $error = 'Bad request. Please check your prompt.';
                break;
            case 401:
                $error = 'Invalid API key.';
                break;
            case 429:
                $error = 'Rate limit exceeded. Please try again later.';
                break;
            case 500:
                $error = 'OpenAI server error. Please try again.';
                break;
            default:
                $error = 'API request failed: ' . $errorMessage;
        }

        Log::error('OpenAI API error', [
            'status_code' => $statusCode,
            'error' => $errorMessage,
        ]);

        return [
            'success' => false,
            'error' => $error,
            'content' => '',
            'status_code' => $statusCode,
        ];
    }

    /**
     * Obtiene el prompt del sistema basado en el tipo
     */
    private function getSystemPrompt(string $type): string
    {
        $prompts = [
            'blog' => 'You are a professional blog writer. Create engaging, SEO-friendly content with clear structure and compelling headlines.',
            'email' => 'You are an email marketing expert. Write persuasive, personalized emails that drive engagement and conversions.',
            'social' => 'You are a social media expert. Create viral-worthy, engaging content optimized for social media platforms.',
            'technical' => 'You are a technical writer. Create clear, accurate documentation and explanations for developers.',
            'general' => 'You are a helpful writing assistant. Create high-quality, relevant content based on user requirements.',
        ];

        return $prompts[$type] ?? $prompts['general'];
    }

    /**
     * Valida y limpia el prompt de entrada
     */
    public function validatePrompt(string $prompt): array
    {
        $minLength = 10;
        $maxLength = 4000;

        if (strlen($prompt) < $minLength) {
            return [
                'valid' => false,
                'error' => "Prompt must be at least {$minLength} characters long."
            ];
        }

        if (strlen($prompt) > $maxLength) {
            return [
                'valid' => false,
                'error' => "Prompt must be less than {$maxLength} characters long."
            ];
        }

        // Limpiar prompt
        $cleanPrompt = strip_tags($prompt);
        $cleanPrompt = preg_replace('/\s+/', ' ', $cleanPrompt);

        return [
            'valid' => true,
            'prompt' => trim($cleanPrompt)
        ];
    }
}

// Controller para usar el servicio
class AIContentController extends Controller
{
    private AIContentService $aiService;

    public function __construct(AIContentService $aiService)
    {
        $this->aiService = $aiService;
    }

    /**
     * Genera contenido via API
     */
    public function generate(Request $request)
    {
        $request->validate([
            'prompt' => 'required|string',
            'type' => 'string|in:blog,email,social,technical,general',
            'max_tokens' => 'integer|min:50|max:2000',
        ]);

        // Validar prompt
        $validation = $this->aiService->validatePrompt($request->prompt);
        if (!$validation['valid']) {
            return response()->json([
                'error' => $validation['error']
            ], 400);
        }

        $result = $this->aiService->generateContent(
            $validation['prompt'],
            $request->type ?? 'general',
            $request->max_tokens ?? 500
        );

        return response()->json($result);
    }

    /**
     * Genera contenido para blog específicamente
     */
    public function generateBlogPost(Request $request)
    {
        $request->validate([
            'title' => 'required|string|max:200',
            'keywords' => 'array|max:10',
            'tone' => 'string|in:professional,casual,academic,creative',
            'length' => 'string|in:short,medium,long',
        ]);

        $lengthTokens = [
            'short' => 300,
            'medium' => 600,
            'long' => 1000,
        ];

        $keywords = implode(', ', $request->keywords ?? []);
        $tone = $request->tone ?? 'professional';
        $tokens = $lengthTokens[$request->length ?? 'medium'];

        $prompt = "Write a {$tone} blog post about '{$request->title}'.";

        if (!empty($keywords)) {
            $prompt .= " Include these keywords naturally: {$keywords}.";
        }

        $prompt .= " Make it engaging, informative, and SEO-friendly.";

        $result = $this->aiService->generateContent($prompt, 'blog', $tokens);

        return response()->json($result);
    }
}

// Configuración en config/services.php
/*
'openai' => [
    'key' => env('OPENAI_API_KEY'),
],
*/

// Routes en api.php
/*
Route::post('/ai/generate', [AIContentController::class, 'generate']);
Route::post('/ai/blog', [AIContentController::class, 'generateBlogPost']);
*/
```

## 🛡️ **Manejo robusto de errores**

### **Tipos de errores comunes:**

| Error | Código HTTP | Causa | Solución |
|-------|-------------|-------|----------|
| **Rate Limit** | 429 | Demasiadas requests | Implementar exponential backoff |
| **Invalid Key** | 401 | API key inválida | Verificar configuración |
| **Quota Exceeded** | 429 | Límite de créditos | Upgrade del plan o wait |
| **Bad Request** | 400 | Prompt inválido | Validar input |
| **Server Error** | 500 | Error del proveedor | Retry con backoff |

### **Pattern de retry con exponential backoff:**

```python
import time
import random
from functools import wraps

def retry_with_backoff(max_retries=3, base_delay=1):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(max_retries):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_retries - 1:
                        raise e

                    # Exponential backoff con jitter
                    delay = base_delay * (2 ** attempt) + random.uniform(0, 1)
                    print(f"Retry {attempt + 1} in {delay:.2f}s: {e}")
                    time.sleep(delay)

            return None
        return wrapper
    return decorator

# Uso:
@retry_with_backoff(max_retries=3)
def call_ai_api():
    # Tu llamada a la API aquí
    pass
```

## 💰 **Optimización de costos**

### **Estrategias para reducir costos:**

**1. Cache inteligente:**
```python
import hashlib
from functools import lru_cache

class AICache:
    def __init__(self):
        self.cache = {}

    def get_cache_key(self, prompt, params):
        content = f"{prompt}_{params}"
        return hashlib.md5(content.encode()).hexdigest()

    def get(self, prompt, params):
        key = self.get_cache_key(prompt, params)
        return self.cache.get(key)

    def set(self, prompt, params, response):
        key = self.get_cache_key(prompt, params)
        self.cache[key] = response
```

**2. Prompt optimization:**
- Usa modelos más baratos para tareas simples (GPT-3.5 vs GPT-4)
- Limita max_tokens apropiadamente
- Pre-procesa input para reducir tokens

**3. Batch processing:**
```python
def process_batch(prompts, batch_size=10):
    for i in range(0, len(prompts), batch_size):
        batch = prompts[i:i + batch_size]
        # Procesar batch completo en una llamada
        yield process_multiple_prompts(batch)
```

## 🚀 **¿Qué sigue?**

Ya sabes cómo integrar IA básicamente en tu código. Ahora vamos a profundizar en técnicas avanzadas de prompt engineering:

**➡️ [Siguiente: Prompt Engineering Avanzado](../03-intermedio/prompt-engineering-avanzado.md)**

---

## 📋 **Checklist de buenas prácticas**

### **Seguridad:** 🛡️
- [ ] API keys en variables de entorno
- [ ] Never hardcode secrets in code
- [ ] Validate y sanitize user input
- [ ] Implement rate limiting
- [ ] Log security events

### **Performance:** ⚡
- [ ] Implement caching strategy
- [ ] Use appropriate timeouts
- [ ] Handle retries with backoff
- [ ] Monitor API usage
- [ ] Optimize token usage

### **Error Handling:** 🔧
- [ ] Handle all HTTP status codes
- [ ] Provide meaningful error messages
- [ ] Log errors for debugging
- [ ] Implement fallback mechanisms
- [ ] Test error scenarios

### **Cost Management:** 💰
- [ ] Monitor API costs
- [ ] Set usage limits
- [ ] Cache frequent requests
- [ ] Use cheaper models when possible
- [ ] Implement budget alerts

---

*La integración de IA en código requiere las mismas buenas prácticas que cualquier API externa: manejo robusto de errores, caching inteligente, y monitoreo constante. En la siguiente sección aprenderemos técnicas avanzadas de prompting.*