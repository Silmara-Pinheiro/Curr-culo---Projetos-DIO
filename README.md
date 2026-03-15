# Currículo - Projetos DIO
Projeto para desenvolvimento e versionamento do meu CV

## 📄 Projeto Currículo

Este repositório contém o desenvolvimento e versionamento do meu currículo (CV).  
O objetivo é manter uma versão atualizada, organizada e acessível, além de explorar diferentes formatos como PDF, DOCX e HTML.

---

### 🎯 Objetivos
- Centralizar todas as versões do currículo em um único lugar.
- Facilitar atualizações e controle de versão.
- Explorar diferentes formatos de apresentação (Word, PDF, HTML/CSS, LaTeX).
- Usar GitHub Projects para organizar tarefas e progresso.

---

#### 🔗  Desafio com a criação de um caderno temático no NotebookLM
Descrição: O projeto “Os Pilares e a Gestão das Finanças” foi implementado com foco em compreender e aplicar os fundamentos da administração financeira em diferentes contextos. Para sua execução, utilizei o notebook LM como recurso principal de organização e registro das atividades, o que permitiu estruturar conteúdos e análises de forma prática e acessível.
Link do projeto: https://notebooklm.google.com/notebook/7ea3dcea-be3f-4679-8439-5ff1849c9bf9?authuser=1


---


##### 🔗  Desafio: Conversando por Voz Com o ChatGPT Utilizando Whisper (OpenAI) e Python

📌 Resumo
Protótipo de conversa por voz multilíngue que integra:
- Whisper (OpenAI) para transcrição de áudio em texto.
- ChatGPT (OpenAI API) para geração de respostas inteligentes.
- gTTS (Google Text-to-Speech) para síntese de voz.
O repositório contém código executável, exemplos de entrada/saída e instruções passo a passo para reproduzir a demo em poucos minutos.

🚀 Objetivo
Demonstrar como combinar ASR (Automatic Speech Recognition) e LLMs (Large Language Models) para criar uma experiência de comunicação por voz em múltiplos idiomas.

🔧 Requisitos
Liste de forma clara as versões mínimas necessárias:
- Python (ex.: 3.9+)
- Bibliotecas (já no requirements.txt)
- Sistema operacional testado (Linux, Windows, Mac)

🛠️ Arquitetura
Fluxo principal:
🎤 Microfone → Whisper → ChatGPT → gTTS → 🔊 Saída de áudio

📂 Estrutura do Projeto 
├── README.md
├── requirements.txt
├── voice_chat_whisper_gpt_keypress.py
├── examples/
│   ├── input_example.wav
│   ├── transcript_example.txt
│   └── response_example.mp3
└── docs/
    ├── arquitetura.png
    ├── screenshot_execucao.png
    └── demo.gif


⚙️ Instalação
- Clone o repositório:
git clone https://github.com/seuusuario/voice-chat-whisper-gpt.git
cd voice-chat-whisper-gpt
- Instale dependências:
pip install -r requirements.txt
- Instale ffmpeg (necessário para pydub):
- Linux: sudo apt install ffmpeg
- Mac: brew install ffmpeg
- Windows: baixe em ffmpeg.org
- Configure a variável de ambiente:
export OPENAI_API_KEY="sua_chave_aqui"


▶️ Como Rodar
Modo tecla Enter
python voice_chat_whisper_gpt_keypress.py

- Pressione Enter para iniciar a gravação.
- Pressione Enter novamente para parar.
Modo batch (arquivo pronto)
python voice_chat_whisper_gpt_keypress.py input.wav


📊 Exemplos
- Entrada (áudio curto): examples/input_example.wav
- Transcrição (Whisper): examples/transcript_example.txt
- Resposta (ChatGPT): exibida no terminal.
- Saída em voz (gTTS): examples/response_example.mp3

📸 Evidências Visuais
- Screenshots da execução no terminal.
- GIF mostrando gravação e resposta.
- Logs de transcrição e resposta.

🔮 Próximos Passos
- Suporte a streaming em tempo real.
- Integração com interfaces gráficas (Tkinter, PyQt).
- Suporte a outras vozes além do gTTS.
- Exemplos em outros idiomas (inglês, espanhol, francês).


Abrir o arquivo para a escrita: 
    def stopper():
        nonlocal stop_thread
        input()  # aguarda Enter para parar
        stop_thread = True

    t = threading.Thread(target=stopper, daemon=True)
    t.start()

    while not stop_thread:
        try:
            data = q.get(timeout=0.1)
        except queue.Empty:
            continue
        file.write(data)


    print(f"Gravação finalizada e salva em {filename}") ---------- OpenAI Whisper transcrição ---------- def transcribe_with_whisper(nome do arquivo: str = AUDIO_FILENAME, traduzir: bool = False): com open (nome do arquivo, "rb") como audio_file: se traduzir: Transcrição = OpenAI. Audio.transcribe("whisper-1", audio_file, traduz=Verdadeiro) Caso contrário: Transcrição = OpenAI. Audio.transcribe ("whisper-1", audio_file) text = transcript.get("text") se isinstance(transcript, dict) else str(transcript) language = transcript.get("language") se isinstance(transcript, dict) else None Retorne texto, idioma
    
---------- ChatGPT ---------- def chat_with_gpt(user_text: força, detected_lang: força | Nenhum = Nenhum, system_prompt: força | Nenhum = Nenhum): system_msg = {"role": "system", "content": system_prompt or "Você é um assistente útil e educado."} Se detected_lang: system_msg["content"] += f" Responda no idioma detectado: {detected_lang}." Mensagens = [system_msg, {"Função": "Usuário", "Conteúdo": user_text}] RESP = OpenAI. ChatCompletion.create(model="gpt-3.5-turbo", messages=messages, temperature=0.7) resposta = resp["escolhas"][0]"mensagem"]["conteúdo"].strip() responder

---------- gTTS e reprodução ---------- def synthesize_with_gtts(texto: str, lang: str="pt", out_file:p=RESPONSE_AUDIO): tts = gTTS(texto=texto, lang=lang) tts.save(out_file) retorno out_file
def play_audio_file(caminho: força): áudio = AudioSegment.from_file(caminho, format="mp3") Play (áudio)
---------- Mapeamento simples de idioma para gTTS ---------- LANG_MAP = { "pt": "pt", "pt-BR": "pt", "en": "en", "es": "es", "fr": "fr", # adicione conforme necessário }
def map_lang_for_gtts(código: str  Nenhum): Se não for código: Retorno "PT" code = code.lower() return LANG_MAP.get(code, code.split("-")[0])
---------- Fluxo principal ---------- def main(): # 1) Gravar até Enter record_until_enter(AUDIO_FILENAME)

Transcrever com Whisper
print("Enviando áudio para transcrição (Whisper)...") text, lang = transcribe_with_whisper(AUDIO_FILENAME, translate=False) print("Transcrição:", text) if lang: print("Idioma detectado:", lang)

ChatGPT
print("Enviando texto ao ChatGPT...") system_prompt = "Você é um assistente útil. Responda de forma clara e objetiva." chat_response = chat_with_gpt(text, detected_lang=lang, system_prompt=system_prompt) print("Resposta do ChatGPT:", chat_response)

Sintetizar com gTTS
gtts_lang = map_lang_for_gtts(lang) print(f"Sintetizando resposta em '{gtts_lang}' com gTTS...") synthesize_with_gtts(chat_response, lang=gtts_lang, out_file=RESPONSE_AUDIO)

Reproduzir
print("Reproduzindo resposta...") play_audio_file(RESPONSE_AUDIO) print("Concluído.")







