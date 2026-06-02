# Módulo 9 — FastAPI

**Fase 3 › Aplicações de IA** · Servir modelos de IA como APIs profissionais

---

## Por que FastAPI?

FastAPI é o framework Python mais moderno para criar APIs web. É ultrarrápido, tem validação automática, documentação gerada automaticamente e suporte nativo a operações assíncronas — perfeito para servir modelos de IA.

```bash
pip install fastapi uvicorn pydantic
```

---

## API mínima

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def raiz():
    return {"mensagem": "API de IA funcionando!"}
```

```bash
uvicorn main:app --reload
# Documentação automática em: http://localhost:8000/docs
```

---

## Validação com Pydantic

```python
from pydantic import BaseModel

class PerguntaRequest(BaseModel):
    pergunta: str
    temperatura: float = 0.7
    max_tokens: int = 500

class RespostaResponse(BaseModel):
    resposta: str
    tokens_usados: int

@app.post("/perguntar", response_model=RespostaResponse)
async def perguntar(request: PerguntaRequest):
    # chama LLM aqui
    return RespostaResponse(resposta="...", tokens_usados=100)
```

---

## Async com OpenAI

```python
from openai import AsyncOpenAI

client = AsyncOpenAI()

@app.post("/chat")
async def chat(request: PerguntaRequest):
    response = await client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[{"role": "user", "content": request.pergunta}],
        temperature=request.temperatura
    )
    return {
        "resposta": response.choices[0].message.content,
        "tokens_usados": response.usage.total_tokens
    }
```

---

## Streaming via SSE

```python
from fastapi.responses import StreamingResponse

@app.post("/chat/stream")
async def chat_stream(request: PerguntaRequest):
    async def gerar():
        stream = await client.chat.completions.create(
            model="gpt-4o-mini",
            messages=[{"role": "user", "content": request.pergunta}],
            stream=True
        )
        async for chunk in stream:
            content = chunk.choices[0].delta.content
            if content:
                yield f"data: {content}\n\n"

    return StreamingResponse(gerar(), media_type="text/event-stream")
```

---

## CORS (para uso com frontend)

```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],
    allow_methods=["*"],
    allow_headers=["*"],
)
```

---

## Tratamento de erros

```python
from fastapi import HTTPException

@app.post("/chat")
async def chat(request: PerguntaRequest):
    if not request.pergunta.strip():
        raise HTTPException(status_code=400, detail="Pergunta não pode ser vazia")
    try:
        # chamada ao LLM
        pass
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))
```

---

## Projeto

!!! example "API de IA completa"
    1. `POST /chat` — recebe pergunta, retorna resposta do LLM
    2. `POST /resumir` — recebe texto longo, retorna resumo
    3. `POST /traduzir` — traduz para o idioma solicitado
    4. `POST /analisar` — análise de sentimento com Pydantic (output estruturado)
    5. Documentação automática funcional em `/docs`
    6. Validação de entrada em todos os endpoints

---

## Vídeos recomendados

- **"FastAPI do zero"** — Dunossauro (Eduardo Mendes)
- **"APIs com Python e FastAPI"** — Hashtag Programação
- **"FastAPI + OpenAI"** — AI da Hora

---

[Próximo módulo: Imagens e Áudio :material-arrow-right:](imagens-audio.md){ .md-button .md-button--primary }
