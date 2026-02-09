<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8" />
    <title>sistemax.onion — Asistente virtual para Python básico</title>
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <style>
        /* SECTION: Design Tokens */
        :root {
            --color-bg-primary: #050816;
            --color-bg-secondary: #0b1020;
            --color-surface: #111827;
            --color-surface-soft: #0f172a;
            --color-text: #e5e7eb;
            --color-text-muted: #9ca3af;
            --color-accent: #38bdf8;
            --color-accent-soft: rgba(56, 189, 248, 0.14);
            --color-danger: #f97373;
            --color-border: rgba(148, 163, 184, 0.35);
            --accent-rgb: 56, 189, 248;
            --font-sans: "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            --radius-sm: 10px;
            --radius-md: 14px;
            --radius-lg: 20px;
            --shadow-soft: 0 1px 2px rgba(15, 23, 42, 0.4), 0 8px 20px rgba(15, 23, 42, 0.7);
            --transition-fast: 0.16s ease-out;
        }

        /* SECTION: Base Styles */
        * { box-sizing: border-box; }
        body {
            margin: 0;
            padding: 0;
            min-height: 100vh;
            font-family: var(--font-sans);
            color: var(--color-text);
            background: radial-gradient(at 10% 10%, rgba(59, 130, 246, 0.2) 0, transparent 45%),
                        radial-gradient(at 90% 0, rgba(236, 72, 153, 0.15) 0, transparent 45%),
                        #020617;
            -webkit-font-smoothing: antialiased;
            overflow-x: hidden;
        }

        /* SECTION: Layout */
        .app-shell {
            max-width: 900px;
            margin: 0 auto;
            padding: 24px;
            display: flex;
            flex-direction: column;
            gap: 24px;
        }

        .header {
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 16px 24px;
            border-radius: var(--radius-lg);
            background: rgba(15, 23, 42, 0.8);
            backdrop-filter: blur(10px);
            border: 1px solid var(--color-border);
            box-shadow: var(--shadow-soft);
        }

        .brand { display: flex; align-items: center; gap: 12px; }
        .brand-mark {
            width: 40px; height: 40px; border-radius: 12px;
            background: linear-gradient(135deg, #60a5fa, #38bdf8);
            color: #020617; font-weight: bold;
            display: flex; align-items: center; justify-content: center;
        }
        .brand-name { font-weight: 600; font-size: 1.1rem; }
        .header-badge { font-size: 0.75rem; color: var(--color-accent); border: 1px solid var(--color-accent); padding: 4px 12px; border-radius: 20px; }

        /* SECTION: Chat Interface */
        .chat-container {
            display: flex;
            flex-direction: column;
            background: var(--color-surface);
            border-radius: var(--radius-lg);
            border: 1px solid var(--color-border);
            height: 600px;
            box-shadow: var(--shadow-soft);
            overflow: hidden;
        }

        .chat-messages {
            flex: 1;
            padding: 20px;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: 16px;
            background: rgba(0,0,0,0.2);
        }

        .message {
            max-width: 85%;
            padding: 12px 16px;
            border-radius: var(--radius-md);
            font-size: 0.95rem;
            line-height: 1.5;
            position: relative;
            animation: fadeIn 0.3s ease-out;
        }

        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        .message.user {
            align-self: flex-end;
            background: var(--color-accent);
            color: #020617;
            border-bottom-right-radius: 4px;
        }

        .message.bot {
            align-self: flex-start;
            background: var(--color-surface-soft);
            border: 1px solid var(--color-border);
            color: var(--color-text);
            border-bottom-left-radius: 4px;
        }

        .message code {
            background: rgba(0,0,0,0.3);
            padding: 2px 5px;
            border-radius: 4px;
            font-family: monospace;
            color: var(--color-accent);
        }

        .message pre {
            background: #050816;
            padding: 12px;
            border-radius: 8px;
            overflow-x: auto;
            margin: 10px 0;
            border: 1px solid rgba(255,255,255,0.1);
        }

        /* Input Area */
        .chat-input-area {
            padding: 16px;
            background: var(--color-bg-secondary);
            border-top: 1px solid var(--color-border);
            display: flex;
            gap: 12px;
        }

        .chat-input {
            flex: 1;
            background: var(--color-bg-primary);
            border: 1px solid var(--color-border);
            border-radius: var(--radius-md);
            padding: 12px 16px;
            color: var(--color-text);
            outline: none;
            transition: border-color var(--transition-fast);
        }

        .chat-input:focus { border-color: var(--color-accent); }

        .send-btn {
            background: var(--color-accent);
            color: #020617;
            border: none;
            border-radius: var(--radius-md);
            padding: 0 20px;
            font-weight: 600;
            cursor: pointer;
            transition: transform 0.1s;
        }

        .send-btn:active { transform: scale(0.95); }
        .send-btn:disabled { opacity: 0.5; cursor: not-allowed; }

        /* Quick Suggestions */
        .suggestions {
            display: flex;
            gap: 8px;
            padding: 10px 20px;
            flex-wrap: wrap;
            background: rgba(0,0,0,0.1);
        }

        .chip {
            background: rgba(255,255,255,0.05);
            border: 1px solid var(--color-border);
            padding: 6px 14px;
            border-radius: 20px;
            font-size: 0.8rem;
            cursor: pointer;
            transition: all 0.2s;
        }

        .chip:hover { background: var(--color-accent-soft); border-color: var(--color-accent); }

        .loading-dots { display: inline-flex; gap: 4px; }
        .dot { width: 6px; height: 6px; background: var(--color-accent); border-radius: 50%; animation: bounce 1.4s infinite ease-in-out; }
        .dot:nth-child(2) { animation-delay: 0.2s; }
        .dot:nth-child(3) { animation-delay: 0.4s; }
        @keyframes bounce { 0%, 80%, 100% { transform: scale(0); } 40% { transform: scale(1); } }

        @media (max-width: 600px) {
            .app-shell { padding: 12px; }
            .chat-container { height: 75vh; }
        }
    </style>
</head>
<body>
    <div class="app-shell">
        <header class="header">
            <div class="brand">
                <div class="brand-mark">sy</div>
                <div class="brand-text">
                    <div class="brand-name">sistemax.onion</div>
                    <div style="font-size: 0.7rem; color: var(--color-text-muted);">Tutor de Python Básico</div>
                </div>
            </div>
            <div class="header-badge">Online</div>
        </header>

        <main class="chat-container">
            <div class="chat-messages" id="chatMessages">
                <div class="message bot">
                    ¡Hola! Soy <strong>sistemax.onion</strong>. Mi misión es ayudarte a dominar los fundamentos de Python de forma clara y sin complicaciones. <br><br>
                    ¿Qué concepto te gustaría explorar hoy? Podemos hablar de variables, listas, bucles o funciones.
                </div>
            </div>
            
            <div class="suggestions" id="suggestions">
                <div class="chip" onclick="sendSuggestion('¿Qué es una variable en Python?')">¿Qué es una variable?</div>
                <div class="chip" onclick="sendSuggestion('Dame un reto de bucles for')">Reto de bucles for</div>
                <div class="chip" onclick="sendSuggestion('Explica las funciones básicas')">Explicar funciones</div>
            </div>

            <form class="chat-input-area" id="chatForm">
                <input type="text" class="chat-input" id="userInput" placeholder="Escribe tu duda sobre Python aquí..." autocomplete="off">
                <button type="submit" class="send-btn" id="sendBtn">Enviar</button>
            </form>
        </main>

        <footer style="text-align: center; font-size: 0.75rem; color: var(--color-text-muted);">
            sistemax.onion — IA Educativa especializada en Python Básico
        </footer>
    </div>

    <script>
        const apiKey = ""; // La plataforma inyectará la clave automáticamente
        const chatMessages = document.getElementById('chatMessages');
        const chatForm = document.getElementById('chatForm');
        const userInput = document.getElementById('userInput');
        const sendBtn = document.getElementById('sendBtn');

        // Configuración de la Identidad de la IA
        const systemPrompt = `
            Eres un asistente virtual inteligente y amigable llamado sistemax.onion.
            TU OBJETIVO: Ayudar a estudiantes a aprender conceptos básicos de Python (variables, tipos de datos, bucles, funciones, listas, diccionarios, condicionales).
            
            TONO: Profesional pero casual. Mezcla precisión técnica con cercanía.
            
            CAPACIDADES:
            1. Explicar conceptos básicos de Python de forma sencilla.
            2. Proponer ejercicios prácticos y retos cortos de código.
            3. Dar pistas y guiar al usuario sin darle la respuesta completa de inmediato.
            
            LIMITACIONES (ESTRICTAS):
            1. No respondas sobre temas que no sean de Python Básico.
            2. No escribas tareas o proyectos completos para el usuario. Solo puedes guiar, dar conceptos clave y pequeños ejemplos de sintaxis.
            3. Si el usuario pide un proyecto entero, dile amablemente que tu función es enseñarle para que él pueda construirlo.
            
            FORMATO:
            - Respuestas de 2 a 4 párrafos para dudas simples.
            - Usa listas con viñetas para pasos o características.
            - Siempre incluye ejemplos de código breves usando bloques de Markdown.
        `;

        let chatHistory = [];

        async function callGemini(prompt) {
            const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`;
            
            const payload = {
                contents: [
                    { parts: [{ text: chatHistory.map(h => `${h.role}: ${h.text}`).join('\n') + `\nuser: ${prompt}` }] }
                ],
                systemInstruction: { parts: [{ text: systemPrompt }] }
            };

            for (let i = 0; i < 5; i++) {
                try {
                    const response = await fetch(url, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });
                    if (!response.ok) throw new Error('Network error');
                    const data = await response.json();
                    return data.candidates?.[0]?.content?.parts?.[0]?.text || "Lo siento, tuve un problema procesando eso.";
                } catch (error) {
                    const delay = Math.pow(2, i) * 1000;
                    await new Promise(resolve => setTimeout(resolve, delay));
                }
            }
            return "He tenido varios errores de conexión. Por favor, intenta de nuevo en un momento.";
        }

        function addMessage(role, text) {
            const msgDiv = document.createElement('div');
            msgDiv.className = `message ${role}`;
            
            // Renderizado básico de Markdown para código
            let formattedText = text
                .replace(/```python\n([\s\S]*?)```/g, '<pre><code>$1</code></pre>')
                .replace(/```([\s\S]*?)```/g, '<pre><code>$1</code></pre>')
                .replace(/`([^`]+)`/g, '<code>$1</code>')
                .replace(/\n/g, '<br>');

            msgDiv.innerHTML = formattedText;
            chatMessages.appendChild(msgDiv);
            chatMessages.scrollTop = chatMessages.scrollHeight;
            
            chatHistory.push({ role, text });
        }

        function showLoading() {
            const loadingDiv = document.createElement('div');
            loadingDiv.className = 'message bot';
            loadingDiv.id = 'loading';
            loadingDiv.innerHTML = '<div class="loading-dots"><div class="dot"></div><div class="dot"></div><div class="dot"></div></div>';
            chatMessages.appendChild(loadingDiv);
            chatMessages.scrollTop = chatMessages.scrollHeight;
        }

        function removeLoading() {
            const loader = document.getElementById('loading');
            if (loader) loader.remove();
        }

        async function handleChat(e) {
            e.preventDefault();
            const text = userInput.value.trim();
            if (!text) return;

            userInput.value = '';
            addMessage('user', text);
            
            sendBtn.disabled = true;
            showLoading();

            const aiResponse = await callGemini(text);
            
            removeLoading();
            addMessage('bot', aiResponse);
            sendBtn.disabled = false;
        }

        function sendSuggestion(text) {
            userInput.value = text;
            chatForm.dispatchEvent(new Event('submit'));
        }

        chatForm.addEventListener('submit', handleChat);
    </script>
</body>
</html>
