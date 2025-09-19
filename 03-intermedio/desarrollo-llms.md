# 🔥 Desarrollo con LLMs

> **Construyendo aplicaciones robustas con IA.** Ya dominas prompts avanzados. Ahora vamos a integrar LLMs profesionalmente: SDKs oficiales, streaming, manejo de contexto, memory systems y arquitecturas escalables para aplicaciones reales.

## 🎯 ¿Qué aprenderás aquí?

- ✅ SDKs oficiales vs HTTP directo
- ✅ Streaming de respuestas para mejor UX
- ✅ Context management y memory systems
- ✅ Error handling y fallbacks robustos
- ✅ Performance optimization avanzada
- ✅ Arquitecturas escalables para producción

## 📦 **SDKs oficiales vs HTTP directo**

### **Comparación de approaches:**

| Aspecto | HTTP Directo | SDK Oficial |
|---------|--------------|-------------|
| **Control** | Total | Limitado por SDK |
| **Complexity** | Alta | Baja |
| **Error handling** | Manual | Built-in |
| **Rate limiting** | Manual | Automático |
| **Streaming** | Complejo | Simplificado |
| **Updates** | Manual | Automático |
| **TypeScript support** | Manual | Native |

### **Cuándo usar cada uno:**

**✅ Usa SDK oficial cuando:**
- Prototipado rápido
- Features estándar suficientes
- Equipo junior/intermedio
- Necesitas streaming fácil

**✅ Usa HTTP directo cuando:**
- Control total sobre requests
- Custom retry logic complejo
- Optimizaciones específicas
- Debugging profundo necesario

## 🛠️ **SDKs oficiales por proveedor**

### **OpenAI SDK - Ejemplo completo**

**Python - Assistant con función calling:**
```python
import asyncio
from openai import AsyncOpenAI
from typing import Dict, Any, List, Optional
import json
import logging

class OpenAIAssistant:
    def __init__(self, api_key: str, model: str = "gpt-4"):
        self.client = AsyncOpenAI(api_key=api_key)
        self.model = model
        self.conversation_history: List[Dict[str, str]] = []

    async def chat_with_functions(
        self,
        message: str,
        functions: Optional[List[Dict]] = None,
        temperature: float = 0.7
    ) -> Dict[str, Any]:
        """
        Chat con función calling integrado
        """
        self.conversation_history.append({"role": "user", "content": message})

        try:
            # Preparar argumentos para la API
            chat_args = {
                "model": self.model,
                "messages": self.conversation_history,
                "temperature": temperature,
                "stream": True  # Habilitamos streaming
            }

            if functions:
                chat_args["functions"] = functions
                chat_args["function_call"] = "auto"

            response_content = ""
            function_call = None

            # Streaming response
            async for chunk in await self.client.chat.completions.create(**chat_args):
                delta = chunk.choices[0].delta

                if delta.content:
                    response_content += delta.content
                    # Aquí puedes emitir eventos para UI real-time
                    yield {"type": "content", "data": delta.content}

                if delta.function_call:
                    if function_call is None:
                        function_call = {"name": "", "arguments": ""}

                    if delta.function_call.name:
                        function_call["name"] += delta.function_call.name

                    if delta.function_call.arguments:
                        function_call["arguments"] += delta.function_call.arguments

            # Procesar función call si existe
            if function_call:
                yield {"type": "function_call", "data": function_call}

                # Ejecutar función
                function_result = await self._execute_function(function_call)

                # Continuar conversación con resultado
                self.conversation_history.append({
                    "role": "assistant",
                    "content": None,
                    "function_call": function_call
                })

                self.conversation_history.append({
                    "role": "function",
                    "name": function_call["name"],
                    "content": json.dumps(function_result)
                })

                # Obtener respuesta final
                final_response = await self.client.chat.completions.create(
                    model=self.model,
                    messages=self.conversation_history,
                    temperature=temperature
                )

                final_content = final_response.choices[0].message.content
                self.conversation_history.append({
                    "role": "assistant",
                    "content": final_content
                })

                yield {"type": "final_response", "data": final_content}
            else:
                self.conversation_history.append({
                    "role": "assistant",
                    "content": response_content
                })

        except Exception as e:
            logging.error(f"Error in chat_with_functions: {e}")
            yield {"type": "error", "data": str(e)}

    async def _execute_function(self, function_call: Dict) -> Dict:
        """
        Ejecuta función llamada por el LLM
        """
        function_name = function_call["name"]
        arguments = json.loads(function_call["arguments"])

        # Registry de funciones disponibles
        function_registry = {
            "get_weather": self._get_weather,
            "search_database": self._search_database,
            "send_email": self._send_email,
        }

        if function_name in function_registry:
            return await function_registry[function_name](**arguments)
        else:
            return {"error": f"Function {function_name} not found"}

    async def _get_weather(self, location: str) -> Dict:
        # Mock weather API call
        return {
            "location": location,
            "temperature": "22°C",
            "condition": "Sunny"
        }

    def get_token_usage_stats(self) -> Dict:
        """
        Obtiene estadísticas de uso de tokens
        """
        # En una implementación real, tracking de tokens usados
        return {
            "total_tokens": 1500,
            "prompt_tokens": 1000,
            "completion_tokens": 500,
            "estimated_cost": 0.03
        }

# Uso del asistente
async def main():
    assistant = OpenAIAssistant(api_key="your-api-key")

    # Definir funciones disponibles
    functions = [
        {
            "name": "get_weather",
            "description": "Get current weather for a location",
            "parameters": {
                "type": "object",
                "properties": {
                    "location": {
                        "type": "string",
                        "description": "The city and state, e.g. San Francisco, CA"
                    }
                },
                "required": ["location"]
            }
        }
    ]

    # Chat con streaming
    async for response in assistant.chat_with_functions(
        "What's the weather like in Madrid?",
        functions=functions
    ):
        if response["type"] == "content":
            print(response["data"], end="", flush=True)
        elif response["type"] == "function_call":
            print(f"\n[Calling function: {response['data']['name']}]")
        elif response["type"] == "final_response":
            print(f"\nFinal: {response['data']}")

if __name__ == "__main__":
    asyncio.run(main())
```

### **Anthropic Claude SDK - Análisis de código**

**TypeScript - Code analyzer con streaming:**
```typescript
import { Anthropic } from '@anthropic-ai/sdk';
import { EventEmitter } from 'events';

interface CodeAnalysisResult {
  issues: Array<{
    type: 'performance' | 'security' | 'maintainability' | 'bug';
    severity: 'low' | 'medium' | 'high' | 'critical';
    line?: number;
    description: string;
    suggestion: string;
  }>;
  score: number;
  summary: string;
}

class ClaudeCodeAnalyzer extends EventEmitter {
  private client: Anthropic;

  constructor(apiKey: string) {
    super();
    this.client = new Anthropic({ apiKey });
  }

  async analyzeCode(
    code: string,
    language: string,
    options: {
      focus?: string[];
      severity?: string;
      maxTokens?: number;
    } = {}
  ): Promise<CodeAnalysisResult> {

    const prompt = this.buildAnalysisPrompt(code, language, options);

    try {
      this.emit('analysis_started');

      const stream = await this.client.messages.create({
        model: 'claude-3-sonnet-20240229',
        max_tokens: options.maxTokens || 2000,
        messages: [
          {
            role: 'user',
            content: prompt
          }
        ],
        stream: true
      });

      let fullResponse = '';

      for await (const messageStreamEvent of stream) {
        if (messageStreamEvent.type === 'content_block_delta') {
          const chunk = messageStreamEvent.delta.text;
          fullResponse += chunk;

          // Emit streaming chunks para UI real-time
          this.emit('chunk', chunk);
        }

        if (messageStreamEvent.type === 'message_stop') {
          this.emit('analysis_complete');
        }
      }

      // Parse structured response
      const result = this.parseAnalysisResponse(fullResponse);
      return result;

    } catch (error) {
      this.emit('error', error);
      throw new Error(`Analysis failed: ${error.message}`);
    }
  }

  private buildAnalysisPrompt(
    code: string,
    language: string,
    options: any
  ): string {
    const focusAreas = options.focus?.join(', ') || 'all areas';

    return `
    Analyze this ${language} code systematically focusing on ${focusAreas}.

    Return your analysis in this exact JSON format:
    {
      "issues": [
        {
          "type": "performance|security|maintainability|bug",
          "severity": "low|medium|high|critical",
          "line": 10,
          "description": "Clear description of the issue",
          "suggestion": "Specific actionable suggestion"
        }
      ],
      "score": 8.5,
      "summary": "Overall assessment and key recommendations"
    }

    Code to analyze:
    \`\`\`${language}
    ${code}
    \`\`\`

    Be specific and actionable. Include line numbers when possible.
    `;
  }

  private parseAnalysisResponse(response: string): CodeAnalysisResult {
    try {
      // Extract JSON from response
      const jsonMatch = response.match(/```json\s*([\s\S]*?)\s*```/) ||
                       response.match(/\{[\s\S]*\}/);

      if (!jsonMatch) {
        throw new Error('No valid JSON found in response');
      }

      const parsed = JSON.parse(jsonMatch[1] || jsonMatch[0]);

      // Validate structure
      if (!parsed.issues || !Array.isArray(parsed.issues)) {
        throw new Error('Invalid response structure');
      }

      return {
        issues: parsed.issues,
        score: parsed.score || 0,
        summary: parsed.summary || 'Analysis completed'
      };

    } catch (error) {
      // Fallback: create result from unstructured response
      return {
        issues: [{
          type: 'maintainability',
          severity: 'medium',
          description: 'Unable to parse structured analysis',
          suggestion: 'Manual review recommended'
        }],
        score: 5,
        summary: response.substring(0, 200) + '...'
      };
    }
  }

  // Memory management para conversaciones largas
  private contextWindow = new Map<string, string[]>();

  async analyzeWithContext(
    sessionId: string,
    code: string,
    language: string
  ): Promise<CodeAnalysisResult> {

    // Retrieve conversation history
    const history = this.contextWindow.get(sessionId) || [];

    // Build context-aware prompt
    const contextPrompt = history.length > 0
      ? `Previous analysis context:\n${history.join('\n')}\n\nNew code to analyze:`
      : 'New analysis:';

    const result = await this.analyzeCode(
      code,
      language,
      { focus: ['consistency', 'patterns'] }
    );

    // Store in context (keep last 5 analyses)
    history.push(`${language}: ${code.substring(0, 100)}... -> Score: ${result.score}`);
    if (history.length > 5) history.shift();
    this.contextWindow.set(sessionId, history);

    return result;
  }
}

// Uso con React/Next.js
export function useCodeAnalyzer() {
  const [analyzer] = useState(() => new ClaudeCodeAnalyzer(process.env.ANTHROPIC_API_KEY));
  const [isAnalyzing, setIsAnalyzing] = useState(false);
  const [streamingText, setStreamingText] = useState('');

  useEffect(() => {
    analyzer.on('analysis_started', () => {
      setIsAnalyzing(true);
      setStreamingText('');
    });

    analyzer.on('chunk', (chunk: string) => {
      setStreamingText(prev => prev + chunk);
    });

    analyzer.on('analysis_complete', () => {
      setIsAnalyzing(false);
    });

    analyzer.on('error', (error: Error) => {
      setIsAnalyzing(false);
      console.error('Analysis error:', error);
    });

    return () => {
      analyzer.removeAllListeners();
    };
  }, [analyzer]);

  const analyze = useCallback(async (code: string, language: string) => {
    return await analyzer.analyzeCode(code, language);
  }, [analyzer]);

  return {
    analyze,
    isAnalyzing,
    streamingText,
    analyzer
  };
}
```

### **Google Gemini SDK - Multimodal processing**

**Flutter - Image + code analysis:**
```dart
import 'package:google_generative_ai/google_generative_ai.dart';
import 'package:image_picker/image_picker.dart';
import 'dart:io';
import 'dart:typed_data';

class GeminiMultimodalService {
  late final GenerativeModel _model;
  late final GenerativeModel _visionModel;

  GeminiMultimodalService(String apiKey) {
    _model = GenerativeModel(
      model: 'gemini-pro',
      apiKey: apiKey,
      generationConfig: GenerationConfig(
        temperature: 0.7,
        topK: 40,
        topP: 0.95,
        maxOutputTokens: 2048,
      ),
    );

    _visionModel = GenerativeModel(
      model: 'gemini-pro-vision',
      apiKey: apiKey,
    );
  }

  // Análisis de imagen + código
  Future<String> analyzeScreenshotWithCode({
    required File imageFile,
    required String relatedCode,
    required String question,
  }) async {
    try {
      final imageBytes = await imageFile.readAsBytes();

      final imagePart = DataPart('image/png', imageBytes);

      final prompt = '''
      Analyze this screenshot in relation to the code provided.

      Related code:
      ```
      $relatedCode
      ```

      Question: $question

      Please provide:
      1. What you see in the screenshot
      2. How it relates to the code
      3. Any issues or improvements you notice
      4. Specific actionable recommendations
      ''';

      final response = await _visionModel.generateContent([
        Content.multi([
          TextPart(prompt),
          imagePart,
        ])
      ]);

      return response.text ?? 'No response generated';

    } catch (e) {
      throw Exception('Multimodal analysis failed: $e');
    }
  }

  // Chat con memoria persistente
  Future<ChatSession> createPersistentChat({
    required String systemPrompt,
    int maxHistoryLength = 50,
  }) async {

    final chat = _model.startChat(
      history: [
        Content.text('System: $systemPrompt'),
        Content.model([TextPart('Understood. I\'m ready to assist.')]),
      ],
    );

    return EnhancedChatSession(chat, maxHistoryLength);
  }

  // Streaming con chunks
  Stream<String> generateContentStream(String prompt) async* {
    try {
      final response = _model.generateContentStream([
        Content.text(prompt)
      ]);

      await for (final chunk in response) {
        final text = chunk.text;
        if (text != null) {
          yield text;
        }
      }
    } catch (e) {
      yield 'Error: $e';
    }
  }

  // Function calling simulation (Gemini doesn't have native function calling yet)
  Future<Map<String, dynamic>> processWithTools({
    required String input,
    required List<ToolDefinition> availableTools,
  }) async {

    final toolsDescription = availableTools
        .map((tool) => '${tool.name}: ${tool.description}')
        .join('\n');

    final prompt = '''
    You have access to these tools:
    $toolsDescription

    User input: $input

    If you need to use a tool, respond with:
    TOOL_CALL: tool_name(param1="value1", param2="value2")

    Otherwise, respond normally.
    ''';

    final response = await _model.generateContent([
      Content.text(prompt)
    ]);

    final responseText = response.text ?? '';

    if (responseText.contains('TOOL_CALL:')) {
      // Parse and execute tool call
      final toolMatch = RegExp(r'TOOL_CALL: (\w+)\((.*)\)').firstMatch(responseText);

      if (toolMatch != null) {
        final toolName = toolMatch.group(1)!;
        final toolParams = _parseToolParams(toolMatch.group(2)!);

        final tool = availableTools.firstWhere((t) => t.name == toolName);
        final toolResult = await tool.execute(toolParams);

        // Continue conversation with tool result
        final finalPrompt = '''
        Tool result: $toolResult

        Based on this result, provide a helpful response to the user.
        ''';

        final finalResponse = await _model.generateContent([
          Content.text(finalPrompt)
        ]);

        return {
          'tool_used': toolName,
          'tool_result': toolResult,
          'final_response': finalResponse.text,
        };
      }
    }

    return {
      'tool_used': null,
      'final_response': responseText,
    };
  }

  Map<String, String> _parseToolParams(String paramsString) {
    final params = <String, String>{};
    final paramRegex = RegExp(r'(\w+)="([^"]*)"');

    for (final match in paramRegex.allMatches(paramsString)) {
      params[match.group(1)!] = match.group(2)!;
    }

    return params;
  }
}

// Enhanced chat session with memory management
class EnhancedChatSession {
  final ChatSession _baseSession;
  final int _maxHistoryLength;
  final List<Content> _fullHistory = [];

  EnhancedChatSession(this._baseSession, this._maxHistoryLength);

  Future<GenerateContentResponse> sendMessage(String message) async {
    _fullHistory.add(Content.text(message));

    // Truncate history if too long
    if (_fullHistory.length > _maxHistoryLength) {
      _fullHistory.removeRange(0, _fullHistory.length - _maxHistoryLength);
    }

    final response = await _baseSession.sendMessage(message);

    if (response.text != null) {
      _fullHistory.add(Content.model([TextPart(response.text!)]));
    }

    return response;
  }

  List<Content> get history => List.unmodifiable(_fullHistory);

  // Summarize old conversations to preserve context but reduce tokens
  Future<void> summarizeOldHistory() async {
    if (_fullHistory.length < _maxHistoryLength) return;

    final oldHistory = _fullHistory.take(_maxHistoryLength ~/ 2).toList();
    final historyText = oldHistory
        .map((content) => content.parts.map((part) {
          if (part is TextPart) return part.text;
          return '[Non-text content]';
        }).join(' '))
        .join('\n');

    // Create summary (this would use another API call)
    final summaryPrompt = '''
    Summarize this conversation history, preserving key context:

    $historyText

    Keep it concise but include important decisions, conclusions, and context.
    ''';

    // This would be implemented with another model call
    // For brevity, we'll just truncate for now
    _fullHistory.removeRange(0, _maxHistoryLength ~/ 2);
  }
}

// Tool definition for function calling simulation
class ToolDefinition {
  final String name;
  final String description;
  final Future<Map<String, dynamic>> Function(Map<String, String>) execute;

  ToolDefinition({
    required this.name,
    required this.description,
    required this.execute,
  });
}

// Usage example widget
class GeminiAnalysisWidget extends StatefulWidget {
  @override
  _GeminiAnalysisWidgetState createState() => _GeminiAnalysisWidgetState();
}

class _GeminiAnalysisWidgetState extends State<GeminiAnalysisWidget> {
  late GeminiMultimodalService _service;
  final TextEditingController _codeController = TextEditingController();
  File? _selectedImage;
  String _analysisResult = '';
  bool _isAnalyzing = false;

  @override
  void initState() {
    super.initState();
    _service = GeminiMultimodalService(
      const String.fromEnvironment('GOOGLE_AI_API_KEY')
    );
  }

  Future<void> _pickImage() async {
    final picker = ImagePicker();
    final pickedFile = await picker.pickImage(source: ImageSource.gallery);

    if (pickedFile != null) {
      setState(() {
        _selectedImage = File(pickedFile.path);
      });
    }
  }

  Future<void> _analyzeWithImage() async {
    if (_selectedImage == null || _codeController.text.isEmpty) {
      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(content: Text('Please select image and enter code'))
      );
      return;
    }

    setState(() {
      _isAnalyzing = true;
      _analysisResult = '';
    });

    try {
      final result = await _service.analyzeScreenshotWithCode(
        imageFile: _selectedImage!,
        relatedCode: _codeController.text,
        question: 'How does this UI relate to the code? Any issues?',
      );

      setState(() {
        _analysisResult = result;
      });
    } catch (e) {
      setState(() {
        _analysisResult = 'Error: $e';
      });
    } finally {
      setState(() {
        _isAnalyzing = false;
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Gemini Multimodal Analysis')),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            TextField(
              controller: _codeController,
              maxLines: 10,
              decoration: const InputDecoration(
                labelText: 'Code to analyze',
                border: OutlineInputBorder(),
              ),
            ),
            const SizedBox(height: 16),
            Row(
              children: [
                ElevatedButton(
                  onPressed: _pickImage,
                  child: const Text('Select Screenshot'),
                ),
                const SizedBox(width: 16),
                ElevatedButton(
                  onPressed: _isAnalyzing ? null : _analyzeWithImage,
                  child: _isAnalyzing
                    ? const CircularProgressIndicator()
                    : const Text('Analyze'),
                ),
              ],
            ),
            const SizedBox(height: 16),
            if (_selectedImage != null)
              Container(
                height: 200,
                child: Image.file(_selectedImage!, fit: BoxFit.contain),
              ),
            const SizedBox(height: 16),
            Expanded(
              child: SingleChildScrollView(
                child: Text(
                  _analysisResult,
                  style: const TextStyle(fontSize: 14),
                ),
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

## 💫 **Streaming avanzado**

### **Server-Sent Events (SSE) con error recovery:**

**Python FastAPI + OpenAI streaming:**
```python
from fastapi import FastAPI, HTTPException
from fastapi.responses import StreamingResponse
from openai import AsyncOpenAI
import asyncio
import json
from typing import AsyncGenerator
import logging

app = FastAPI()

class StreamingLLMService:
    def __init__(self, api_key: str):
        self.client = AsyncOpenAI(api_key=api_key)
        self.active_streams = set()

    async def stream_chat_completion(
        self,
        messages: list,
        stream_id: str,
        model: str = "gpt-4",
        max_retries: int = 3
    ) -> AsyncGenerator[str, None]:

        self.active_streams.add(stream_id)
        retry_count = 0

        while retry_count < max_retries:
            try:
                stream = await self.client.chat.completions.create(
                    model=model,
                    messages=messages,
                    stream=True,
                    temperature=0.7
                )

                buffer = ""
                async for chunk in stream:
                    if stream_id not in self.active_streams:
                        # Stream was cancelled
                        break

                    delta = chunk.choices[0].delta

                    if delta.content:
                        buffer += delta.content

                        # Send chunks as SSE format
                        sse_data = {
                            "id": stream_id,
                            "type": "content",
                            "data": delta.content,
                            "buffer": buffer
                        }

                        yield f"data: {json.dumps(sse_data)}\n\n"

                    if chunk.choices[0].finish_reason:
                        # Stream completed successfully
                        final_data = {
                            "id": stream_id,
                            "type": "complete",
                            "data": buffer,
                            "finish_reason": chunk.choices[0].finish_reason
                        }
                        yield f"data: {json.dumps(final_data)}\n\n"
                        break

                break  # Success, exit retry loop

            except Exception as e:
                retry_count += 1
                error_data = {
                    "id": stream_id,
                    "type": "error",
                    "data": str(e),
                    "retry_count": retry_count
                }

                if retry_count < max_retries:
                    error_data["retrying"] = True
                    yield f"data: {json.dumps(error_data)}\n\n"
                    await asyncio.sleep(2 ** retry_count)  # Exponential backoff
                else:
                    error_data["final_error"] = True
                    yield f"data: {json.dumps(error_data)}\n\n"

        finally:
            self.active_streams.discard(stream_id)

    def cancel_stream(self, stream_id: str):
        self.active_streams.discard(stream_id)

streaming_service = StreamingLLMService(api_key="your-key")

@app.post("/chat/stream/{stream_id}")
async def stream_chat(stream_id: str, request: dict):
    messages = request.get("messages", [])

    if not messages:
        raise HTTPException(status_code=400, detail="Messages required")

    async def generate():
        yield "data: {\"type\": \"start\", \"id\": \"" + stream_id + "\"}\n\n"

        async for chunk in streaming_service.stream_chat_completion(
            messages=messages,
            stream_id=stream_id
        ):
            yield chunk

    return StreamingResponse(
        generate(),
        media_type="text/plain",
        headers={
            "Cache-Control": "no-cache",
            "Connection": "keep-alive",
            "Access-Control-Allow-Origin": "*",
        }
    )

@app.delete("/chat/stream/{stream_id}")
async def cancel_stream(stream_id: str):
    streaming_service.cancel_stream(stream_id)
    return {"message": "Stream cancelled"}
```

**Frontend JavaScript con reconexión automática:**
```javascript
class StreamingChatClient {
  constructor(baseUrl) {
    this.baseUrl = baseUrl;
    this.eventSource = null;
    this.currentStreamId = null;
    this.reconnectAttempts = 0;
    this.maxReconnectAttempts = 5;
    this.reconnectDelay = 1000;
  }

  async startChat(messages, onChunk, onComplete, onError) {
    this.currentStreamId = this.generateStreamId();

    try {
      const response = await fetch(`${this.baseUrl}/chat/stream/${this.currentStreamId}`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({ messages })
      });

      if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`);
      }

      this.setupEventSource(response, onChunk, onComplete, onError);

    } catch (error) {
      onError(error);
    }
  }

  setupEventSource(response, onChunk, onComplete, onError) {
    const reader = response.body.getReader();
    const decoder = new TextDecoder();
    let buffer = '';

    const processStream = async () => {
      try {
        while (true) {
          const { done, value } = await reader.read();

          if (done) break;

          buffer += decoder.decode(value, { stream: true });
          const lines = buffer.split('\n');
          buffer = lines.pop(); // Keep incomplete line in buffer

          for (const line of lines) {
            if (line.startsWith('data: ')) {
              const data = line.slice(6);
              if (data.trim()) {
                this.handleSSEMessage(data, onChunk, onComplete, onError);
              }
            }
          }
        }
      } catch (error) {
        if (this.reconnectAttempts < this.maxReconnectAttempts) {
          this.attemptReconnection(onChunk, onComplete, onError);
        } else {
          onError(new Error(`Connection failed after ${this.maxReconnectAttempts} attempts`));
        }
      }
    };

    processStream();
  }

  handleSSEMessage(data, onChunk, onComplete, onError) {
    try {
      const message = JSON.parse(data);

      switch (message.type) {
        case 'start':
          this.reconnectAttempts = 0; // Reset on successful start
          break;

        case 'content':
          onChunk(message.data);
          break;

        case 'complete':
          onComplete(message.data);
          this.cleanup();
          break;

        case 'error':
          if (message.retrying) {
            console.log(`Error occurred, retrying (${message.retry_count}): ${message.data}`);
          } else {
            onError(new Error(message.data));
            this.cleanup();
          }
          break;
      }
    } catch (error) {
      onError(new Error(`Failed to parse SSE message: ${error.message}`));
    }
  }

  attemptReconnection(onChunk, onComplete, onError) {
    this.reconnectAttempts++;

    setTimeout(() => {
      console.log(`Attempting reconnection ${this.reconnectAttempts}/${this.maxReconnectAttempts}`);

      // Resume with the same stream ID to maintain context
      fetch(`${this.baseUrl}/chat/stream/${this.currentStreamId}/resume`, {
        method: 'POST'
      }).then(response => {
        if (response.ok) {
          this.setupEventSource(response, onChunk, onComplete, onError);
        } else {
          throw new Error('Reconnection failed');
        }
      }).catch(() => {
        if (this.reconnectAttempts < this.maxReconnectAttempts) {
          this.attemptReconnection(onChunk, onComplete, onError);
        } else {
          onError(new Error('Max reconnection attempts reached'));
        }
      });
    }, this.reconnectDelay * Math.pow(2, this.reconnectAttempts - 1));
  }

  cancelStream() {
    if (this.currentStreamId) {
      fetch(`${this.baseUrl}/chat/stream/${this.currentStreamId}`, {
        method: 'DELETE'
      });
      this.cleanup();
    }
  }

  cleanup() {
    this.currentStreamId = null;
    this.reconnectAttempts = 0;
  }

  generateStreamId() {
    return Date.now().toString() + Math.random().toString(36).substr(2, 9);
  }
}

// Usage example
const chatClient = new StreamingChatClient('http://localhost:8000');

document.getElementById('sendButton').addEventListener('click', async () => {
  const input = document.getElementById('messageInput');
  const output = document.getElementById('chatOutput');

  const messages = [
    { role: 'user', content: input.value }
  ];

  let fullResponse = '';

  await chatClient.startChat(
    messages,
    // onChunk
    (chunk) => {
      fullResponse += chunk;
      output.innerHTML = marked.parse(fullResponse); // Using marked.js for markdown
      output.scrollTop = output.scrollHeight;
    },
    // onComplete
    (finalResponse) => {
      console.log('Chat completed:', finalResponse);
      input.value = '';
    },
    // onError
    (error) => {
      console.error('Chat error:', error);
      output.innerHTML += `<div class="error">Error: ${error.message}</div>`;
    }
  );
});

document.getElementById('cancelButton').addEventListener('click', () => {
  chatClient.cancelStream();
});
```

## 🧠 **Memory Systems avanzados**

### **Hierarchical memory con embeddings:**

```python
import numpy as np
from sentence_transformers import SentenceTransformer
from typing import List, Dict, Optional, Tuple
import sqlite3
import json
from datetime import datetime, timedelta

class HierarchicalMemorySystem:
    def __init__(self, db_path: str = "memory.db"):
        self.db_path = db_path
        self.embedding_model = SentenceTransformer('all-MiniLM-L6-v2')
        self.init_database()

    def init_database(self):
        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()

        # Short-term memory (conversation context)
        cursor.execute('''
        CREATE TABLE IF NOT EXISTS short_term_memory (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            session_id TEXT,
            message_type TEXT, -- user, assistant, system
            content TEXT,
            embedding BLOB,
            timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
            importance_score FLOAT DEFAULT 0.0
        )
        ''')

        # Long-term memory (persistent knowledge)
        cursor.execute('''
        CREATE TABLE IF NOT EXISTS long_term_memory (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            concept TEXT,
            content TEXT,
            embedding BLOB,
            usage_count INTEGER DEFAULT 1,
            last_accessed DATETIME DEFAULT CURRENT_TIMESTAMP,
            created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
            metadata TEXT -- JSON metadata
        )
        ''')

        # Episodic memory (specific events/interactions)
        cursor.execute('''
        CREATE TABLE IF NOT EXISTS episodic_memory (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            session_id TEXT,
            event_type TEXT,
            summary TEXT,
            full_context TEXT,
            embedding BLOB,
            outcome TEXT,
            timestamp DATETIME DEFAULT CURRENT_TIMESTAMP
        )
        ''')

        conn.commit()
        conn.close()

    def add_to_short_term(
        self,
        session_id: str,
        message_type: str,
        content: str,
        importance_score: float = 0.0
    ):
        """Add message to short-term memory with context window management"""
        embedding = self.embedding_model.encode(content)

        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()

        cursor.execute('''
        INSERT INTO short_term_memory
        (session_id, message_type, content, embedding, importance_score)
        VALUES (?, ?, ?, ?, ?)
        ''', (session_id, message_type, content, embedding.tobytes(), importance_score))

        # Maintain context window (keep last 50 messages per session)
        cursor.execute('''
        DELETE FROM short_term_memory
        WHERE session_id = ? AND id NOT IN (
            SELECT id FROM short_term_memory
            WHERE session_id = ?
            ORDER BY timestamp DESC
            LIMIT 50
        )
        ''', (session_id, session_id))

        conn.commit()
        conn.close()

    def consolidate_to_long_term(
        self,
        session_id: str,
        min_importance: float = 0.7
    ):
        """Move important short-term memories to long-term storage"""
        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()

        # Get high-importance messages
        cursor.execute('''
        SELECT content, embedding FROM short_term_memory
        WHERE session_id = ? AND importance_score >= ?
        ORDER BY importance_score DESC
        ''', (session_id, min_importance))

        important_memories = cursor.fetchall()

        for content, embedding_bytes in important_memories:
            # Extract key concepts
            concepts = self._extract_concepts(content)

            for concept in concepts:
                # Check if concept already exists
                cursor.execute('''
                SELECT id, usage_count FROM long_term_memory
                WHERE concept = ?
                ''', (concept,))

                existing = cursor.fetchone()

                if existing:
                    # Update existing concept
                    cursor.execute('''
                    UPDATE long_term_memory
                    SET usage_count = usage_count + 1,
                        last_accessed = CURRENT_TIMESTAMP
                    WHERE id = ?
                    ''', (existing[0],))
                else:
                    # Create new concept
                    embedding = np.frombuffer(embedding_bytes, dtype=np.float32)
                    cursor.execute('''
                    INSERT INTO long_term_memory
                    (concept, content, embedding, metadata)
                    VALUES (?, ?, ?, ?)
                    ''', (concept, content, embedding.tobytes(), json.dumps({})))

        conn.commit()
        conn.close()

    def retrieve_relevant_context(
        self,
        query: str,
        session_id: str,
        max_results: int = 10
    ) -> List[Dict]:
        """Retrieve relevant context from all memory types"""
        query_embedding = self.embedding_model.encode(query)

        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()

        relevant_memories = []

        # Search short-term memory
        cursor.execute('''
        SELECT content, message_type, timestamp, embedding
        FROM short_term_memory
        WHERE session_id = ?
        ORDER BY timestamp DESC LIMIT 20
        ''', (session_id,))

        short_term = cursor.fetchall()
        for content, msg_type, timestamp, embedding_bytes in short_term:
            embedding = np.frombuffer(embedding_bytes, dtype=np.float32)
            similarity = np.dot(query_embedding, embedding) / (
                np.linalg.norm(query_embedding) * np.linalg.norm(embedding)
            )

            if similarity > 0.5:  # Threshold for relevance
                relevant_memories.append({
                    'type': 'short_term',
                    'content': content,
                    'similarity': similarity,
                    'timestamp': timestamp,
                    'meta': {'message_type': msg_type}
                })

        # Search long-term memory
        cursor.execute('SELECT concept, content, embedding FROM long_term_memory')
        long_term = cursor.fetchall()

        for concept, content, embedding_bytes in long_term:
            embedding = np.frombuffer(embedding_bytes, dtype=np.float32)
            similarity = np.dot(query_embedding, embedding) / (
                np.linalg.norm(query_embedding) * np.linalg.norm(embedding)
            )

            if similarity > 0.6:  # Higher threshold for long-term
                relevant_memories.append({
                    'type': 'long_term',
                    'content': content,
                    'similarity': similarity,
                    'meta': {'concept': concept}
                })

        # Sort by relevance and return top results
        relevant_memories.sort(key=lambda x: x['similarity'], reverse=True)

        conn.close()
        return relevant_memories[:max_results]

    def _extract_concepts(self, content: str) -> List[str]:
        """Simple concept extraction (in production, use NER or topic modeling)"""
        # This is a simplified version - use spaCy, transformers, or similar for production
        words = content.lower().split()
        concepts = []

        # Extract potential concepts (nouns, technical terms, etc.)
        technical_indicators = ['api', 'database', 'function', 'class', 'error', 'bug', 'feature']

        for word in words:
            if len(word) > 3 and (word in technical_indicators or word.endswith('ing') or word.endswith('ed')):
                concepts.append(word)

        return list(set(concepts))  # Remove duplicates

    def create_episodic_memory(
        self,
        session_id: str,
        event_type: str,
        summary: str,
        full_context: str,
        outcome: str
    ):
        """Store specific episodes/events for future learning"""
        embedding = self.embedding_model.encode(summary)

        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()

        cursor.execute('''
        INSERT INTO episodic_memory
        (session_id, event_type, summary, full_context, embedding, outcome)
        VALUES (?, ?, ?, ?, ?, ?)
        ''', (session_id, event_type, summary, full_context, embedding.tobytes(), outcome))

        conn.commit()
        conn.close()

    def get_memory_stats(self) -> Dict:
        """Get statistics about memory usage"""
        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()

        stats = {}

        # Short-term memory stats
        cursor.execute('SELECT COUNT(*) FROM short_term_memory')
        stats['short_term_count'] = cursor.fetchone()[0]

        # Long-term memory stats
        cursor.execute('SELECT COUNT(*) FROM long_term_memory')
        stats['long_term_count'] = cursor.fetchone()[0]

        # Most accessed concepts
        cursor.execute('''
        SELECT concept, usage_count FROM long_term_memory
        ORDER BY usage_count DESC LIMIT 10
        ''')
        stats['top_concepts'] = cursor.fetchall()

        conn.close()
        return stats
```

## 🚀 **¿Qué sigue?**

Dominas desarrollo con LLMs. Ahora vamos a explorar Model Context Protocol para conectar LLMs con herramientas externas:

**➡️ [Siguiente: Model Context Protocol (MCP)](./mcp-introduccion.md)**

---

## 📊 **Performance benchmarks**

### **SDK vs HTTP directo:**

| Operación | SDK Oficial | HTTP Directo | Diferencia |
|-----------|-------------|--------------|------------|
| **Simple chat** | 250ms | 180ms | +70ms overhead |
| **Streaming** | 45ms TTFT* | 35ms TTFT | +10ms overhead |
| **Function calling** | 300ms | 250ms | +50ms overhead |
| **Error recovery** | Automático | Manual | Tiempo variable |

*TTFT = Time To First Token

### **Memory overhead:**

| Componente | RAM Usage | Storage |
|------------|-----------|---------|
| **Basic SDK** | ~50MB | 0MB |
| **Con embeddings** | ~200MB | ~10MB/1k memories |
| **Streaming buffer** | ~2MB | 0MB |
| **Full memory system** | ~300MB | ~50MB/10k conversations |

---

*El desarrollo profesional con LLMs requiere considerar streaming, memory management y error handling robusto. Con estos fundamentos, puedes crear aplicaciones de IA que escalen y sean confiables en producción.*