# Módulo 10 — Imagens e Áudio com IA

**Fase 3 › Aplicações de IA** · Visão computacional e processamento de áudio

---

## Visão Computacional com Hugging Face

```python
from transformers import pipeline

# Classificação de imagem
classificador = pipeline("image-classification",
                         model="google/vit-base-patch16-224")
resultado = classificador("foto.jpg")
# [{'label': 'cat', 'score': 0.98}]

# Detecção de objetos
detector = pipeline("object-detection",
                    model="facebook/detr-resnet-50")
objetos = detector("rua.jpg")
# [{'label': 'car', 'score': 0.99, 'box': {'xmin': 10, ...}}]

# Image captioning (imagem → descrição)
captioner = pipeline("image-to-text",
                     model="Salesforce/blip-image-captioning-base")
descricao = captioner("foto.jpg")
```

---

## Geração de imagens com Stable Diffusion

```bash
pip install diffusers accelerate
```

```python
from diffusers import StableDiffusionPipeline
import torch

pipe = StableDiffusionPipeline.from_pretrained(
    "runwayml/stable-diffusion-v1-5",
    torch_dtype=torch.float16
)
pipe = pipe.to("cuda")  # ou "cpu" (mais lento)

imagem = pipe(
    prompt="um gato astronauta em Marte, arte digital, 4k",
    negative_prompt="baixa qualidade, desfocado",
    num_inference_steps=30
).images[0]

imagem.save("resultado.png")
```

---

## Processamento de Áudio

### Transcrição com Whisper

```python
# Whisper da OpenAI via Hugging Face
transcriber = pipeline(
    "automatic-speech-recognition",
    model="openai/whisper-large-v3"
)
resultado = transcriber("audio.mp3")
print(resultado["text"])

# Ou via API da OpenAI (mais simples)
from openai import OpenAI
client = OpenAI()

with open("audio.mp3", "rb") as f:
    transcricao = client.audio.transcriptions.create(
        model="whisper-1",
        file=f,
        language="pt"
    )
print(transcricao.text)
```

### Síntese de Voz (TTS)

```python
# Via API da OpenAI
from pathlib import Path

response = client.audio.speech.create(
    model="tts-1",
    voice="nova",  # alloy, echo, fable, onyx, nova, shimmer
    input="Olá, sou um assistente de IA."
)

Path("audio_gerado.mp3").write_bytes(response.content)
```

---

## Pipeline completo: Imagem + Áudio + NLP

```python
from transformers import pipeline
from openai import OpenAI

client = OpenAI()

def pipeline_analise_midia(caminho_imagem: str, caminho_audio: str) -> dict:
    # 1. Descrever imagem
    captioner = pipeline("image-to-text",
                         model="Salesforce/blip-image-captioning-base")
    descricao_imagem = captioner(caminho_imagem)[0]["generated_text"]

    # 2. Transcrever áudio
    with open(caminho_audio, "rb") as f:
        transcricao = client.audio.transcriptions.create(
            model="whisper-1", file=f, language="pt"
        )

    # 3. Analisar sentimento do texto transcrito
    analisador = pipeline("sentiment-analysis",
                          model="lxyuan/distilbert-base-multilingual-cased-sentiments-student")
    sentimento = analisador(transcricao.text)[0]

    return {
        "descricao_imagem": descricao_imagem,
        "transcricao_audio": transcricao.text,
        "sentimento": sentimento["label"],
        "confianca_sentimento": round(sentimento["score"], 3)
    }
```

---

## Projeto

!!! example "Analisador de mídia"
    1. Endpoint `POST /analisar-imagem` — recebe imagem, retorna descrição
    2. Endpoint `POST /transcrever` — recebe áudio, retorna texto
    3. Endpoint `POST /pipeline-completo` — recebe imagem + áudio, retorna análise completa
    4. Interface simples em HTML com upload de arquivos

---

## Vídeos recomendados

- **"Stable Diffusion em Python"** — Sandeco
- **"Whisper na prática"** — AI da Hora
- **"Hugging Face para imagens"** — Asimov Academy YouTube

---

[Próximo módulo: LangChain e RAG :material-arrow-right:](langchain.md){ .md-button .md-button--primary }
