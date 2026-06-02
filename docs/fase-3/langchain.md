# Módulo 11 — LangChain e RAG

**Fase 3 › Aplicações de IA** · Agentes que consultam documentos

---

## O que é LangChain?

LangChain é o framework mais popular para construir aplicações com LLMs. Ele abstrai a complexidade de encadear chamadas de modelos, ferramentas, memória e recuperação de documentos.

```bash
pip install langchain langchain-openai langchain-community faiss-cpu pypdf
```

---

## Chains — encadeamento de operações

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

llm = ChatOpenAI(model="gpt-4o-mini")

prompt = ChatPromptTemplate.from_messages([
    ("system", "Você é um especialista em {assunto}."),
    ("human", "{pergunta}")
])

chain = prompt | llm | StrOutputParser()

resposta = chain.invoke({
    "assunto": "Python",
    "pergunta": "O que é uma list comprehension?"
})
```

---

## RAG — Retrieval Augmented Generation

RAG dá ao LLM acesso a documentos externos. Em vez de depender do treinamento, o modelo busca informações relevantes no momento da pergunta.

```
Pergunta → Busca chunks relevantes → Injeta no prompt → LLM responde
```

### Implementação completa

```python
from langchain_community.document_loaders import PyPDFLoader, DirectoryLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_openai import OpenAIEmbeddings
from langchain_community.vectorstores import FAISS
from langchain.chains import RetrievalQA

# 1. Carregar documentos
loader = DirectoryLoader("documentos/", glob="**/*.pdf", loader_cls=PyPDFLoader)
docs = loader.load()

# 2. Dividir em chunks
splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200,
    separators=["\n\n", "\n", ".", " "]
)
chunks = splitter.split_documents(docs)
print(f"Total de chunks: {len(chunks)}")

# 3. Criar embeddings e vector store
embeddings = OpenAIEmbeddings(model="text-embedding-3-small")
vectorstore = FAISS.from_documents(chunks, embeddings)
vectorstore.save_local("vectorstore")  # persistir em disco

# 4. Carregar vectorstore existente
vectorstore = FAISS.load_local("vectorstore", embeddings,
                                allow_dangerous_deserialization=True)

# 5. Criar chain de Q&A
qa_chain = RetrievalQA.from_chain_type(
    llm=ChatOpenAI(model="gpt-4o-mini"),
    retriever=vectorstore.as_retriever(search_kwargs={"k": 4}),
    return_source_documents=True
)

# 6. Perguntar
resultado = qa_chain.invoke("O que diz o documento sobre X?")
print(resultado["result"])
for doc in resultado["source_documents"]:
    print(f"Fonte: {doc.metadata['source']}")
```

---

## Agentes com LangChain

```python
from langchain.agents import create_react_agent, AgentExecutor
from langchain_community.tools import DuckDuckGoSearchRun
from langchain import hub

llm = ChatOpenAI(model="gpt-4o")
search = DuckDuckGoSearchRun()
tools = [search]

prompt = hub.pull("hwchase17/react")
agent = create_react_agent(llm, tools, prompt)
executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

resultado = executor.invoke({"input": "Qual o preço do Bitcoin hoje?"})
print(resultado["output"])
```

---

## RAG do zero (sem frameworks)

Entender o RAG sem abstrações torna você um engenheiro melhor.

```python
from openai import OpenAI
import numpy as np
import faiss
from dataclasses import dataclass

client = OpenAI()

@dataclass
class Chunk:
    texto: str
    fonte: str

class RAGSimples:
    def __init__(self):
        self.chunks: list[Chunk] = []
        self.index = faiss.IndexFlatIP(1536)

    def _embedding(self, texto: str) -> np.ndarray:
        r = client.embeddings.create(input=texto, model="text-embedding-3-small")
        v = np.array([r.data[0].embedding], dtype="float32")
        faiss.normalize_L2(v)
        return v

    def indexar(self, texto: str, fonte: str, chunk_size: int = 500):
        for i in range(0, len(texto), chunk_size - 100):
            trecho = texto[i:i + chunk_size].strip()
            if len(trecho) > 50:
                self.chunks.append(Chunk(trecho, fonte))
                self.index.add(self._embedding(trecho))

    def responder(self, pergunta: str) -> str:
        query = self._embedding(pergunta)
        _, indices = self.index.search(query, 4)
        contexto = "\n\n".join([
            f"[{self.chunks[i].fonte}]\n{self.chunks[i].texto}"
            for i in indices[0] if i >= 0
        ])
        r = client.chat.completions.create(
            model="gpt-4o-mini",
            messages=[
                {"role": "system", "content": f"Responda baseado no contexto:\n\n{contexto}"},
                {"role": "user", "content": pergunta}
            ]
        )
        return r.choices[0].message.content
```

---

## Projeto

!!! example "Assistente de documentos"
    1. Carrega múltiplos PDFs de uma pasta
    2. Cria vector store persistente em disco
    3. Interface de chat onde o usuário pergunta sobre os documentos
    4. Mostra quais trechos foram usados para responder (sources)
    5. Serve via FastAPI com endpoint `POST /perguntar`

---

## Vídeos recomendados

- **"LangChain do zero"** — Sandeco
- **"RAG com LangChain"** — AI da Hora
- **"Agentes com LangChain"** — buscar no YouTube

---

[Próximo módulo: Agentes com Agno :material-arrow-right:](agno.md){ .md-button .md-button--primary }
