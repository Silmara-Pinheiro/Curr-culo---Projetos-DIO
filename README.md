# Currículo---Projetos-DIO
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



##### 🔗  Desafio: Conversando por Voz Com o ChatGPT Utilizando Whisper (OpenAI) e Python

Resumo do projeto
Protótipo de conversa por voz multilíngue que integra Whisper para transcrição, ChatGPT para geração de respostas e gTTS para síntese. O repositório contém código executável, exemplos de entrada/saída e instruções passo a passo para reproduzir a demo em poucos minutos.

Checklist: 
- README principal com objetivo do projeto, arquitetura e instruções de uso.
- Guia de instalação (dependências, versão do Python, instalação do ffmpeg, variáveis de ambiente como OPENAI_API_KEY).
- Como rodar (comandos exatos para executar em modo batch e modo tecla-Enter).
- Estrutura do código (descrição das pastas e arquivos principais).
- Exemplos: arquivos de áudio de entrada (curtos), transcrições geradas e respostas do ChatGPT em texto.
- Evidências visuais: screenshots da execução, GIFs mostrando gravação/execução, trechos do terminal com logs.
- Arquitetura: diagrama simples (PNG) mostrando fluxo: microfone → Whisper → ChatGPT → gTTS.

Bash: pip install openai sounddevice soundfile gtts pydub e instale ffmpeg no sistema para pydub
Script: (voice_chat_whisper_gpt_keypress.py)

Phyton: 
import os
import threading
import queue
import openai
import sounddevice as sd
import soundfile as sf
from gtts import gTTS
from pydub import AudioSegment
from pydub.playback import play

# ---------- Configurações ----------
OPENAI_API_KEY = os.getenv("OPENAI_API_KEY")
if not OPENAI_API_KEY:
    raise RuntimeError("Defina a variável de ambiente OPENAI_API_KEY com sua chave da OpenAI.")
openai.api_key = OPENAI_API_KEY

AUDIO_FILENAME = "input.wav"
RESPONSE_AUDIO = "response.mp3"
SAMPLE_RATE = 16000
CHANNELS = 1
BLOCKSIZE = 1024

# ---------- Gravação até tecla ----------
def record_until_enter(filename: str = AUDIO_FILENAME, samplerate: int = SAMPLE_RATE, channels: int = CHANNELS):
    """Grava do microfone até o usuário pressionar Enter e salva em filename (WAV)."""
    q = queue.Queue()

    def callback(indata, frames, time_info, status):
        if status:
            print("Aviso de áudio:", status)
        q.put(indata.copy())

    print("Pressione Enter para iniciar a gravação.")
    input("Aperte Enter para começar a gravar...")
    print("Gravando. Pressione Enter novamente para parar.")

    # Abrir arquivo para escrita
    with sf.SoundFile(filename, mode='w', samplerate=samplerate, channels=channels, subtype='PCM_16') as file:
        with sd.InputStream(samplerate=samplerate, channels=channels, blocksize=BLOCKSIZE, callback=callback):
            stop_thread = False

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

    print(f"Gravação finalizada e salva em {filename}")

# ---------- OpenAI Whisper transcrição ----------
def transcribe_with_whisper(filename: str = AUDIO_FILENAME, translate: bool = False):
    with open(filename, "rb") as audio_file:
        if translate:
            transcript = openai.Audio.transcribe("whisper-1", audio_file, translate=True)
        else:
            transcript = openai.Audio.transcribe("whisper-1", audio_file)
    text = transcript.get("text") if isinstance(transcript, dict) else str(transcript)
    language = transcript.get("language") if isinstance(transcript, dict) else None
    return text, language

# ---------- ChatGPT ----------
def chat_with_gpt(user_text: str, detected_lang: str | None = None, system_prompt: str | None = None):
    system_msg = {"role": "system", "content": system_prompt or "Você é um assistente útil e educado."}
    if detected_lang:
        system_msg["content"] += f" Responda no idioma detectado: {detected_lang}."
    messages = [system_msg, {"role": "user", "content": user_text}]
    resp = openai.ChatCompletion.create(model="gpt-3.5-turbo", messages=messages, temperature=0.7)
    answer = resp["choices"][0]["message"]["content"].strip()
    return answer

# ---------- gTTS e reprodução ----------
def synthesize_with_gtts(text: str, lang: str = "pt", out_file: str = RESPONSE_AUDIO):
    tts = gTTS(text=text, lang=lang)
    tts.save(out_file)
    return out_file

def play_audio_file(path: str):
    audio = AudioSegment.from_file(path, format="mp3")
    play(audio)

# ---------- Mapeamento simples de idioma para gTTS ----------
LANG_MAP = {
    "pt": "pt",
    "pt-BR": "pt",
    "en": "en",
    "es": "es",
    "fr": "fr",
    # adicione conforme necessário
}

def map_lang_for_gtts(code: str | None):
    if not code:
        return "pt"
    code = code.lower()
    return LANG_MAP.get(code, code.split("-")[0])

# ---------- Fluxo principal ----------
def main():
    # 1) Gravar até Enter
    record_until_enter(AUDIO_FILENAME)

    # 2) Transcrever com Whisper
    print("Enviando áudio para transcrição (Whisper)...")
    text, lang = transcribe_with_whisper(AUDIO_FILENAME, translate=False)
    print("Transcrição:", text)
    if lang:
        print("Idioma detectado:", lang)

    # 3) ChatGPT
    print("Enviando texto ao ChatGPT...")
    system_prompt = "Você é um assistente útil. Responda de forma clara e objetiva."
    chat_response = chat_with_gpt(text, detected_lang=lang, system_prompt=system_prompt)
    print("Resposta do ChatGPT:", chat_response)

    # 4) Sintetizar com gTTS
    gtts_lang = map_lang_for_gtts(lang)
    print(f"Sintetizando resposta em '{gtts_lang}' com gTTS...")
    synthesize_with_gtts(chat_response, lang=gtts_lang, out_file=RESPONSE_AUDIO)

    # 5) Reproduzir
    print("Reproduzindo resposta...")
    play_audio_file(RESPONSE_AUDIO)
    print("Concluído.")







