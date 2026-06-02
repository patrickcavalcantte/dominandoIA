# Módulo 6 — Hugging Face

**Fase 2 › Fundamentos de IA** · O GitHub dos modelos de IA

---

## O que é o Hugging Face?

Hugging Face é uma plataforma com mais de 500 mil modelos open-source prontos para uso: linguagem, visão computacional, áudio, tradução e muito mais. A biblioteca `transformers` é a principal forma de usar esses modelos em Python.

---

## Instalação

```bash
pip install transformers torch datasets
pip install accelerate  # para GPU
```

---

## Pipeline — a forma mais simples

```python
from transformers import pipeline

# Análise de sentimento
classificador = pipeline("sentiment-analysis")
resultado = classificador("Esse produto é incrível!")
# [{'label': 'POSITIVE', 'score': 0.9998}]

# Geração de texto
gerador = pipeline("text-generation", model="gpt2")
texto = gerador("Era uma vez", max_length=50)

# Tradução PT → EN
tradutor = pipeline("translation", model="Helsinki-NLP/opus-mt-pt-en")
resultado = tradutor("Olá, como vai você?")
```

## Tarefas disponíveis

| Tarefa | Pipeline |
|---|---|
| Análise de sentimento | `sentiment-analysis` |
| Geração de texto | `text-generation` |
| Resumo | `summarization` |
| Pergunta e resposta | `question-answering` |
| Classificação | `text-classification` |
| Embeddings | `feature-extraction` |
| Imagem → texto | `image-to-text` |
| Áudio → texto | `automatic-speech-recognition` |
| Detecção de objetos | `object-detection` |

---

## Modelos em português

```python
# BERT treinado em português
from transformers import AutoTokenizer, AutoModel

tokenizer = AutoTokenizer.from_pretrained("neuralmind/bert-base-portuguese-cased")
model = AutoModel.from_pretrained("neuralmind/bert-base-portuguese-cased")

# Tokenizar texto
inputs = tokenizer("Texto de exemplo", return_tensors="pt")
outputs = model(**inputs)
```

---

## Datasets

```python
from datasets import load_dataset

# Carregar dataset público
dataset = load_dataset("imdb")           # reviews de filmes
dataset = load_dataset("squad")          # perguntas e respostas
dataset = load_dataset("common_voice", "pt")  # áudio em português

# Explorar
print(dataset["train"][0])
print(dataset["train"].features)
```

---

## API de Inferência (sem GPU local)

```python
import requests

API_URL = "https://api-inference.huggingface.co/models/neuralmind/bert-base-portuguese-cased"
headers = {"Authorization": f"Bearer {HF_TOKEN}"}

response = requests.post(
    API_URL,
    headers=headers,
    json={"inputs": "Texto para processar"}
)
print(response.json())
```

---

## Projeto

!!! example "Analisador de reviews de produtos"
    1. Use pipeline de `sentiment-analysis` com modelo em português
    2. Leia um CSV com 100 reviews
    3. Classifique cada uma como positiva/negativa
    4. Gere relatório JSON com percentuais e exemplos de cada categoria

---

## Vídeos recomendados

- **"Hugging Face do zero"** — Asimov Academy YouTube
- **"Transformers com Python"** — Sandeco
- **"NLP com Hugging Face"** — Mario Filho

---

[Próximo módulo: LLMs Alternativas :material-arrow-right:](llms-alternativas.md){ .md-button .md-button--primary }
