# Módulo — IA Local

**Fase 2 › Fundamentos de IA** · Rode modelos de linguagem no seu próprio computador

---

## Por que rodar IA localmente?

Antes de instalar qualquer coisa, entenda o que você ganha — e o que você abre mão.

| | IA na Nuvem (OpenAI, Claude) | IA Local |
|---|---|---|
| **Privacidade** | Dados enviados para servidores externos | Dados ficam 100% na sua máquina |
| **Custo** | Pago por token usado | Gratuito após configurar |
| **Qualidade** | GPT-4o, Claude Sonnet — estado da arte | Boa, mas inferior aos maiores modelos |
| **Velocidade** | Depende da internet | Depende do seu hardware |
| **Offline** | Não funciona sem internet | Funciona sem internet |
| **Limites de uso** | Rate limits por plano | Sem limites |

**Quando usar IA local:**

- Você trabalha com dados sensíveis (saúde, jurídico, financeiro)
- Quer experimentar sem gastar por token
- Precisa de acesso offline
- Quer entender como modelos funcionam mais a fundo
- Tem uma GPU e quer aproveitar o hardware

---

## Como um modelo roda localmente?

Para rodar um LLM no seu computador, três coisas acontecem:

**1. O modelo é carregado na memória**
O arquivo do modelo (geralmente `.gguf` ou `.safetensors`) é carregado na RAM ou na VRAM da GPU. Um modelo de 7 bilhões de parâmetros em precisão normal ocupa ~14GB. Com quantização (compressão), o mesmo modelo pode ocupar ~4GB.

**2. Quantização — o segredo para rodar em hardware comum**
Quantização reduz a precisão dos números dentro do modelo:

```
FP32 (full precision)  → 4 bytes por parâmetro → qualidade máxima
FP16 (half precision)  → 2 bytes por parâmetro → quase igual, metade do tamanho
Q8_0 (8-bit quant)     → 1 byte por parâmetro  → leve perda de qualidade
Q4_K_M (4-bit quant)   → 0.5 bytes/parâmetro  → perda notável, mas usável
Q2_K (2-bit quant)     → 0.25 bytes/parâmetro → degradação significativa
```

Na prática, **Q4_K_M é o ponto ideal** para uso cotidiano: boa qualidade com tamanho gerenciável.

**3. Inferência na CPU ou GPU**
- **GPU (NVIDIA/AMD/Apple Silicon):** muito mais rápido — tokens gerados em milissegundos
- **CPU:** mais lento — pode levar vários segundos por resposta, mas funciona em qualquer máquina

---

## Requisitos de hardware

### Por modelo

| Modelo | Parâmetros | VRAM mínima (Q4) | RAM mínima (CPU) |
|---|---|---|---|
| Llama 3.2 3B | 3B | 2 GB | 4 GB |
| Llama 3.2 1B | 1B | 1 GB | 2 GB |
| Llama 3.1 8B | 8B | 5 GB | 8 GB |
| Mistral 7B | 7B | 5 GB | 8 GB |
| Llama 3.3 70B | 70B | 40 GB | 48 GB |
| DeepSeek R1 7B | 7B | 5 GB | 8 GB |
| Gemma 3 4B | 4B | 3 GB | 6 GB |
| Phi-4 Mini | 3.8B | 3 GB | 5 GB |

### Verificar seu hardware

```bash
# Windows — ver GPU e VRAM
nvidia-smi   # para NVIDIA
# ou abrir: Gerenciador de tarefas → Desempenho → GPU

# Ver RAM disponível
# Gerenciador de tarefas → Desempenho → Memória
```

!!! tip "Regra prática"
    - **4GB de RAM:** modelos de 1B a 3B com Q4
    - **8GB de RAM:** modelos de 7B/8B com Q4 (lento na CPU)
    - **16GB de RAM:** modelos de 7B confortáveis + alguns de 13B
    - **GPU 8GB VRAM:** modelos de 7B/8B muito rápidos
    - **GPU 24GB VRAM:** modelos de 13B a 34B

---

## Ferramentas para rodar IA local

Existem três opções principais. Cada uma serve um propósito diferente.

---

### Opção 1 — Ollama (recomendado para começar)

Ollama é a forma mais simples de rodar LLMs localmente. Um comando baixa e roda o modelo. Tem API compatível com OpenAI.

**Instalação:** [ollama.com](https://ollama.com) → Download para Windows/Mac/Linux

```bash
# Verificar instalação
ollama --version

# Baixar e rodar Llama 3.2 (3B — leve, rápido)
ollama run llama3.2

# Outros modelos populares
ollama run mistral          # Mistral 7B — ótimo para código
ollama run deepseek-r1      # DeepSeek R1 — raciocínio avançado
ollama run gemma3           # Google Gemma 3
ollama run phi4-mini        # Microsoft Phi-4 Mini — eficiente
ollama run qwen2.5-coder    # Qwen — especialista em código
ollama run nomic-embed-text # modelo de embeddings local

# Listar modelos instalados
ollama list

# Remover modelo
ollama rm llama3.2
```

**Conversa interativa no terminal:**
```
$ ollama run llama3.2
>>> Explique o que é RAG em 3 frases
RAG (Retrieval Augmented Generation) é uma técnica que combina...
>>> /bye
```

**API REST automática (porta 11434):**

Quando o Ollama está rodando, ele expõe automaticamente uma API:

```python
import requests

response = requests.post(
    "http://localhost:11434/api/chat",
    json={
        "model": "llama3.2",
        "messages": [
            {"role": "user", "content": "O que é um transformer?"}
        ],
        "stream": False
    }
)
print(response.json()["message"]["content"])
```

**SDK Python do Ollama:**

```bash
pip install ollama
```

```python
import ollama

# Chat simples
response = ollama.chat(
    model="llama3.2",
    messages=[{"role": "user", "content": "Explique embeddings"}]
)
print(response["message"]["content"])

# Chat com histórico
historico = []

def conversar(mensagem: str) -> str:
    historico.append({"role": "user", "content": mensagem})
    response = ollama.chat(model="llama3.2", messages=historico)
    resposta = response["message"]["content"]
    historico.append({"role": "assistant", "content": resposta})
    return resposta

print(conversar("Meu nome é Patrick"))
print(conversar("Qual é o meu nome?"))  # lembra!

# Streaming
for chunk in ollama.chat(
    model="llama3.2",
    messages=[{"role": "user", "content": "Escreva um poema"}],
    stream=True
):
    print(chunk["message"]["content"], end="", flush=True)

# Embeddings locais
embedding = ollama.embeddings(
    model="nomic-embed-text",
    prompt="Texto para vetorizar"
)
vetor = embedding["embedding"]  # lista de floats
print(f"Dimensão: {len(vetor)}")  # 768
```

**API compatível com OpenAI SDK:**

O Ollama implementa a mesma interface da OpenAI — você pode usar o SDK da OpenAI apontando para o servidor local:

```python
from openai import OpenAI

# Aponta para o Ollama local em vez da OpenAI
client = OpenAI(
    base_url="http://localhost:11434/v1",
    api_key="ollama"  # qualquer string, não é validada
)

response = client.chat.completions.create(
    model="llama3.2",
    messages=[{"role": "user", "content": "Olá!"}]
)
print(response.choices[0].message.content)
```

!!! tip "Por que isso é poderoso?"
    Com a API compatível com OpenAI, você pode trocar `gpt-4o-mini` por `llama3.2` em qualquer projeto existente mudando apenas 2 linhas. Teste localmente de graça, suba em produção com a API paga.

---

### Opção 2 — LM Studio (interface gráfica)

LM Studio é uma aplicação desktop com interface visual. Ideal para quem quer experimentar modelos sem usar terminal.

**Download:** [lmstudio.ai](https://lmstudio.ai)

**O que você pode fazer:**
- Buscar e baixar modelos diretamente do Hugging Face pela interface
- Conversar com modelos em um chat visual
- Comparar dois modelos lado a lado
- Carregar e testar arquivos GGUF
- Expor servidor local compatível com OpenAI

**Quando usar LM Studio:**
- Você quer explorar modelos visualmente
- Quer testar rapidamente sem escrever código
- Precisa comparar modelos diferentes facilmente

---

### Opção 3 — llama.cpp (máximo controle)

llama.cpp é a biblioteca C++ que roda por baixo do Ollama e LM Studio. Usar diretamente dá mais controle sobre quantização, threads e parâmetros de geração.

```bash
# Clonar e compilar
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp
make -j4

# Baixar modelo GGUF do Hugging Face
# Buscar "llama-3.2-3b-instruct-q4_k_m.gguf" em huggingface.co/bartowski

# Rodar
./llama-cli -m models/llama-3.2-3b.gguf -p "Explique RAG:" -n 200
```

---

## Modelfile — criando modelos personalizados no Ollama

Um Modelfile permite criar um modelo com system prompt fixo, temperatura e parâmetros pré-configurados — como um "produto" construído sobre um modelo base.

```dockerfile
# Modelfile
FROM llama3.2

# System prompt fixo
SYSTEM """
Você é o assistente de suporte da TechCorp.
Responda apenas sobre nossos produtos: X1, S3 e B2.
Seja sempre cordial e conciso.
Se não souber a resposta, diga: "Vou verificar isso para você."
"""

# Parâmetros de geração
PARAMETER temperature 0.3
PARAMETER top_p 0.9
PARAMETER num_ctx 4096
PARAMETER stop "Usuário:"
PARAMETER stop "Assistente:"
```

```bash
# Criar o modelo personalizado
ollama create assistente-techcorp -f Modelfile

# Usar
ollama run assistente-techcorp
```

---

## RAG 100% local — sem nenhuma API externa

Combinando Ollama + embeddings locais + FAISS, você tem um sistema RAG completamente offline:

```python
import ollama
import faiss
import numpy as np
from pathlib import Path
from dataclasses import dataclass

@dataclass
class Chunk:
    texto: str
    fonte: str

class RAGLocal:
    """RAG completamente local — zero chamadas para APIs externas."""

    MODELO_CHAT = "llama3.1"          # modelo de geração
    MODELO_EMBED = "nomic-embed-text"  # modelo de embeddings
    DIM_EMBED = 768

    def __init__(self):
        self.chunks: list[Chunk] = []
        self.index = faiss.IndexFlatIP(self.DIM_EMBED)

    def _embedding(self, texto: str) -> np.ndarray:
        response = ollama.embeddings(model=self.MODELO_EMBED, prompt=texto)
        vetor = np.array([response["embedding"]], dtype="float32")
        faiss.normalize_L2(vetor)
        return vetor

    def _dividir(self, texto: str, tamanho: int = 500, overlap: int = 100) -> list[str]:
        chunks = []
        inicio = 0
        while inicio < len(texto):
            fim = min(inicio + tamanho, len(texto))
            if fim < len(texto):
                ultimo_ponto = texto.rfind(".", inicio, fim)
                if ultimo_ponto > inicio:
                    fim = ultimo_ponto + 1
            trecho = texto[inicio:fim].strip()
            if len(trecho) > 50:
                chunks.append(trecho)
            inicio = fim - overlap
        return chunks

    def indexar_arquivo(self, caminho: str):
        texto = Path(caminho).read_text(encoding="utf-8")
        for trecho in self._dividir(texto):
            self.chunks.append(Chunk(trecho, caminho))
            self.index.add(self._embedding(trecho))
        print(f"Indexado: {caminho} ({len(self.chunks)} chunks)")

    def buscar(self, pergunta: str, k: int = 3) -> list[Chunk]:
        if not self.chunks:
            return []
        query = self._embedding(pergunta)
        _, indices = self.index.search(query, min(k, len(self.chunks)))
        return [self.chunks[i] for i in indices[0] if i >= 0]

    def responder(self, pergunta: str) -> str:
        trechos = self.buscar(pergunta)
        contexto = "\n\n---\n\n".join(
            f"[{c.fonte}]\n{c.texto}" for c in trechos
        )
        response = ollama.chat(
            model=self.MODELO_CHAT,
            messages=[
                {
                    "role": "system",
                    "content": (
                        "Responda baseado EXCLUSIVAMENTE no contexto abaixo. "
                        "Se não encontrar a resposta, diga claramente.\n\n"
                        f"CONTEXTO:\n{contexto}"
                    )
                },
                {"role": "user", "content": pergunta}
            ]
        )
        return response["message"]["content"]


# Uso
rag = RAGLocal()
rag.indexar_arquivo("manual.txt")
rag.indexar_arquivo("politicas.txt")

print(rag.responder("Como faço para cancelar minha assinatura?"))
```

---

## Comparando modelos locais

Cada modelo tem pontos fortes. Use a tabela abaixo como guia:

| Modelo | Tamanho | Pontos fortes | Quando usar |
|---|---|---|---|
| **Llama 3.2 3B** | ~2GB | Rápido, leve, bom para texto | Dispositivos limitados, testes rápidos |
| **Llama 3.1 8B** | ~5GB | Balanceado, bom raciocínio | Uso geral, desenvolvimento |
| **Mistral 7B** | ~4GB | Código, instrução, inglês | Assistente de código |
| **Gemma 3 4B** | ~3GB | Multilingual, Google | Português, múltiplos idiomas |
| **Phi-4 Mini 3.8B** | ~2.5GB | Raciocínio matemático | Problemas lógicos e matemáticos |
| **DeepSeek R1 7B** | ~5GB | Raciocínio passo a passo | Tarefas que exigem "pensar alto" |
| **Qwen2.5-Coder 7B** | ~5GB | Código especializado | Geração e revisão de código |
| **nomic-embed-text** | ~270MB | Embeddings locais | RAG local, busca semântica |

---

## Integrando Ollama com LangChain

```python
from langchain_ollama import ChatOllama, OllamaEmbeddings
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_community.vectorstores import FAISS

# Chat local
llm = ChatOllama(model="llama3.1", temperature=0.3)

prompt = ChatPromptTemplate.from_messages([
    ("system", "Você é um especialista em {area}."),
    ("human", "{pergunta}")
])

chain = prompt | llm | StrOutputParser()
print(chain.invoke({"area": "Python", "pergunta": "O que é um decorator?"}))

# Embeddings locais para RAG
embeddings = OllamaEmbeddings(model="nomic-embed-text")

# Criar vectorstore com embeddings locais
from langchain_community.document_loaders import TextLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter

docs = TextLoader("documento.txt").load()
chunks = RecursiveCharacterTextSplitter(chunk_size=500).split_documents(docs)
vectorstore = FAISS.from_documents(chunks, embeddings)

# RAG completamente local
from langchain.chains import RetrievalQA

qa = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=vectorstore.as_retriever()
)
print(qa.invoke("Qual o tema principal do documento?"))
```

---

## Servidor local multi-modelo com FastAPI

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
import ollama

app = FastAPI(title="API de IA Local")

MODELOS_DISPONIVEIS = ["llama3.2", "mistral", "gemma3"]

class ChatRequest(BaseModel):
    modelo: str = "llama3.2"
    mensagem: str
    system_prompt: str = "Você é um assistente útil."
    temperatura: float = 0.7

class ChatResponse(BaseModel):
    resposta: str
    modelo_usado: str

@app.get("/modelos")
def listar_modelos():
    modelos = ollama.list()
    return {"modelos": [m["name"] for m in modelos["models"]]}

@app.post("/chat", response_model=ChatResponse)
def chat(request: ChatRequest):
    if request.modelo not in MODELOS_DISPONIVEIS:
        raise HTTPException(
            status_code=400,
            detail=f"Modelo deve ser um de: {MODELOS_DISPONIVEIS}"
        )
    response = ollama.chat(
        model=request.modelo,
        messages=[
            {"role": "system", "content": request.system_prompt},
            {"role": "user", "content": request.mensagem}
        ],
        options={"temperature": request.temperatura}
    )
    return ChatResponse(
        resposta=response["message"]["content"],
        modelo_usado=request.modelo
    )

@app.post("/embeddings")
def gerar_embeddings(texto: str):
    response = ollama.embeddings(model="nomic-embed-text", prompt=texto)
    return {
        "embedding": response["embedding"],
        "dimensao": len(response["embedding"])
    }
```

---

## Boas práticas

!!! success "Faça isso"
    - Comece com modelos pequenos (3B) e aumente conforme necessidade
    - Use Q4_K_M como quantização padrão — melhor equilíbrio qualidade/tamanho
    - Para produção local, prefira a API compatível com OpenAI (mais portável)
    - Use `nomic-embed-text` para embeddings locais — rápido e preciso

!!! warning "Evite isso"
    - Não tente rodar modelos 70B em hardware sem GPU adequada
    - Não use modelos locais para tarefas críticas que exigem qualidade máxima
    - Não ignore a quantização — modelos FP32 localmente são impraticáveis

!!! danger "Limitações reais"
    Modelos locais de 7B são bons, mas ainda ficam atrás de GPT-4o e Claude 3.5 Sonnet em tarefas complexas de raciocínio. Para produção com usuários reais, avalie se a qualidade atende.

---

## Exercícios

!!! exercise "Exercício 1 — Primeiros passos"
    1. Instale o Ollama
    2. Baixe dois modelos: `llama3.2` e `mistral`
    3. Faça a mesma pergunta para os dois no terminal e compare as respostas
    4. Use `ollama list` para ver os modelos instalados e seus tamanhos

!!! exercise "Exercício 2 — API local"
    Crie um script Python que:

    1. Conecta ao Ollama usando o SDK Python
    2. Implementa um chatbot com histórico persistente em memória
    3. Permite trocar de modelo com um comando especial: `!modelo mistral`
    4. Exibe o tempo de resposta de cada mensagem

!!! exercise "Exercício 3 — Modelfile personalizado"
    Crie um Modelfile para um assistente especialista em Python:

    1. Baseado no `llama3.2`
    2. System prompt que instrui o modelo a sempre fornecer exemplos de código
    3. Temperatura 0.2 (mais preciso para código)
    4. Teste com: "Como implementar um decorator de retry?"

!!! exercise "Exercício 4 — RAG local completo"
    Construa um sistema RAG 100% offline:

    1. Use os arquivos `.txt` ou `.md` desta documentação como base de conhecimento
    2. Indexe com `nomic-embed-text` via Ollama
    3. Responda perguntas com `llama3.1`
    4. Compare a qualidade das respostas com e sem contexto

!!! exercise "Exercício 5 — Benchmark"
    Compare desempenho de modelos locais vs API:

    1. Crie um conjunto de 10 perguntas de teste
    2. Execute em `llama3.2` (local), `mistral` (local) e `gpt-4o-mini` (API)
    3. Meça tempo de resposta e qualidade percebida (1–5)
    4. Calcule o custo estimado de usar a API para as 10 perguntas

---

## Vídeos recomendados

- **"Ollama do zero — rode LLMs no seu PC"** — Sandeco
- **"LM Studio na prática"** — AI da Hora
- **"RAG local com Ollama e LangChain"** — buscar no YouTube
- **"Llama 3 vs GPT-4 — comparação real"** — buscar no YouTube

---

[Próximo módulo: LLMs Alternativas :material-arrow-right:](llms-alternativas.md){ .md-button .md-button--primary }
