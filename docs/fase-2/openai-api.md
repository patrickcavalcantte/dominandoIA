# Módulo 8 — API da OpenAI

**Fase 2 › Fundamentos de IA** · O padrão de fato para trabalhar com LLMs

---

## Instalação

```bash
pip install openai python-dotenv
```

```ini
# .env
OPENAI_API_KEY=sk-proj-...
```

---

## Chat Completions — o endpoint principal

```python
from openai import OpenAI
from dotenv import load_dotenv

load_dotenv()
client = OpenAI()

response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[
        {"role": "system", "content": "Você é um assistente de Python."},
        {"role": "user", "content": "Como fazer uma lista em Python?"}
    ],
    temperature=0.7,
    max_tokens=500
)
print(response.choices[0].message.content)
print(f"Tokens usados: {response.usage.total_tokens}")
```

## Papéis das mensagens

| Role | Função |
|---|---|
| `system` | Define o comportamento do modelo para toda a conversa |
| `user` | Mensagem do usuário |
| `assistant` | Resposta anterior do modelo (para manter histórico) |

## Histórico de conversa

```python
historico = [
    {"role": "system", "content": "Você é um assistente de culinária."}
]

def conversar(mensagem: str) -> str:
    historico.append({"role": "user", "content": mensagem})
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=historico
    )
    resposta = response.choices[0].message.content
    historico.append({"role": "assistant", "content": resposta})
    return resposta

print(conversar("Qual receita de bolo você sugere?"))
print(conversar("Quais ingredientes preciso?"))  # lembra da conversa anterior
```

---

## Streaming — resposta em tempo real

```python
stream = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[{"role": "user", "content": "Escreva um poema"}],
    stream=True
)
for chunk in stream:
    print(chunk.choices[0].delta.content or "", end="", flush=True)
```

---

## Embeddings

```python
response = client.embeddings.create(
    input="Texto para vetorizar",
    model="text-embedding-3-small"
)
vetor = response.data[0].embedding  # lista de 1536 floats
print(f"Dimensão: {len(vetor)}")
```

---

## Visão (GPT-4o)

```python
response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{
        "role": "user",
        "content": [
            {"type": "text", "text": "O que tem nessa imagem?"},
            {"type": "image_url", "image_url": {"url": "https://..."}}
        ]
    }]
)
```

---

## Function Calling (Tool Use)

A base de como agentes funcionam: o modelo "solicita" que seu código execute uma função.

```python
tools = [{
    "type": "function",
    "function": {
        "name": "buscar_clima",
        "description": "Retorna o clima atual de uma cidade",
        "parameters": {
            "type": "object",
            "properties": {
                "cidade": {"type": "string", "description": "Nome da cidade"}
            },
            "required": ["cidade"]
        }
    }
}]

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Qual o clima em São Paulo?"}],
    tools=tools
)

# Verificar se o modelo quer chamar uma função
if response.choices[0].message.tool_calls:
    tc = response.choices[0].message.tool_calls[0]
    print(f"Função: {tc.function.name}")
    print(f"Args: {tc.function.arguments}")
```

---

## Output estruturado com Pydantic

```python
from pydantic import BaseModel

class AnaliseSentimento(BaseModel):
    sentimento: str
    confianca: float
    justificativa: str

completion = client.beta.chat.completions.parse(
    model="gpt-4o-mini",
    messages=[{"role": "user", "content": "Analise: 'Produto ótimo!'"}],
    response_format=AnaliseSentimento,
)
resultado = completion.choices[0].message.parsed
print(resultado.sentimento)    # "positivo"
print(resultado.confianca)     # 0.98
```

---

## Projeto

!!! example "Chatbot de atendimento"
    1. System prompt que define a personalidade (ex: assistente de uma loja)
    2. Mantém histórico completo da conversa
    3. Streaming ativado para resposta em tempo real
    4. Salva cada sessão em arquivo JSON com timestamp
    5. Exibe uso de tokens ao final

---

## Vídeos recomendados

- **"API da OpenAI do zero"** — Sandeco
- **"Function Calling com OpenAI"** — AI da Hora
- **"GPT-4 Vision na prática"** — buscar no YouTube

---

[Próxima fase: FastAPI :material-arrow-right:](../fase-3/fastapi.md){ .md-button .md-button--primary }
