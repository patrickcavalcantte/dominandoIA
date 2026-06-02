# Módulo 7 — LLMs Alternativas

**Fase 2 › Fundamentos de IA** · Modelos locais e outros provedores

---

## Por que usar modelos além do ChatGPT?

**Custo, privacidade e controle.** Rodar um LLM localmente significa que seus dados nunca saem da sua máquina — essencial para dados sensíveis. Além disso, modelos como Llama 3 e Mistral são competitivos com GPT-4 em muitas tarefas.

---

## Principais provedores

| Provedor | Modelos | Como acessar |
|---|---|---|
| OpenAI | GPT-4o, GPT-4o-mini | API (pago) |
| Anthropic | Claude 3.5 Sonnet/Opus | API (pago) |
| Google | Gemini 1.5 Pro/Flash | API (gratuito com limites) |
| Meta | Llama 3.1, Llama 3.3 | Open-source |
| Mistral | Mistral 7B, Mixtral | API + open-source |
| Groq | Llama, Mixtral (ultra rápido) | API gratuita |

---

## Rodando modelos localmente com Ollama

```bash
# 1. Instalar em ollama.com
# 2. Baixar e rodar um modelo
ollama pull llama3.2
ollama run llama3.2

# Usar via Python
pip install ollama
```

```python
import ollama

response = ollama.chat(
    model='llama3.2',
    messages=[{'role': 'user', 'content': 'Explique o que é um LLM'}]
)
print(response['message']['content'])
```

## Groq — API gratuita e ultra rápida

```bash
pip install groq
```

```python
from groq import Groq

client = Groq(api_key="sua_chave")  # groq.com → API Keys
chat = client.chat.completions.create(
    messages=[{"role": "user", "content": "Olá!"}],
    model="llama-3.3-70b-versatile"
)
print(chat.choices[0].message.content)
```

## Google Gemini

```bash
pip install google-generativeai
```

```python
import google.generativeai as genai

genai.configure(api_key="sua_chave")  # aistudio.google.com
model = genai.GenerativeModel("gemini-1.5-flash")
response = model.generate_content("Explique RAG")
print(response.text)
```

---

## Como escolher o modelo certo

| Situação | Modelo recomendado |
|---|---|
| Tarefas simples e baratas | Gemini Flash, GPT-4o-mini |
| Tarefas complexas | Claude 3.5 Sonnet, GPT-4o |
| Privacidade total | Ollama local (Llama 3.2) |
| Custo zero, alta velocidade | Groq + Llama 3.3 70B |
| Multimodal (imagem + texto) | GPT-4o, Gemini 1.5 Pro |

---

## Projeto

!!! example "Comparador de modelos"
    Crie um script que:

    1. Faz a mesma pergunta para 3 modelos (GPT-4o-mini, Llama via Groq, Gemini Flash)
    2. Registra o tempo de resposta de cada um
    3. Salva as respostas em JSON para comparação manual

---

## Vídeos recomendados

- **"Rodando LLMs localmente com Ollama"** — Sandeco
- **"Groq — LLM gratuito e ultra rápido"** — AI da Hora
- **"Comparando modelos de IA"** — buscar no YouTube

---

[Próximo módulo: API da OpenAI :material-arrow-right:](openai-api.md){ .md-button .md-button--primary }
