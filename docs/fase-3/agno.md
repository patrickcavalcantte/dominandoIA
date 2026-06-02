# Módulo 12 — Agentes com Agno

**Fase 3 › Aplicações de IA** · Framework moderno para agentes de IA

---

## O que é Agno?

Agno (anteriormente Phidata) é um framework moderno e minimalista para criar agentes. É mais simples que LangChain e focado em criar agentes com ferramentas, memória e conhecimento de forma limpa.

```bash
pip install agno openai
```

---

## Agente básico

```python
from agno.agent import Agent
from agno.models.openai import OpenAIChat

agent = Agent(
    model=OpenAIChat(id="gpt-4o-mini"),
    description="Você é um assistente especialista em Python.",
    instructions=["Sempre forneça exemplos de código", "Seja conciso"],
    markdown=True
)

agent.print_response("Como usar decorators em Python?")
```

---

## Agente com ferramentas

```python
from agno.tools.duckduckgo import DuckDuckGoTools
from agno.tools.yfinance import YFinanceTools

agente_financeiro = Agent(
    model=OpenAIChat(id="gpt-4o"),
    tools=[YFinanceTools(stock_price=True, analyst_recommendations=True)],
    description="Analista financeiro com acesso a dados de mercado",
    show_tool_calls=True
)

agente_financeiro.print_response("Analise as ações da Apple (AAPL)")
```

---

## Agente com memória

```python
from agno.memory.db.sqlite import SqliteMemoryDb
from agno.storage.agent.sqlite import SqliteAgentStorage

agent = Agent(
    model=OpenAIChat(id="gpt-4o-mini"),
    memory=SqliteMemoryDb(db_file="memoria.db"),
    storage=SqliteAgentStorage(db_file="sessoes.db"),
    add_history_to_messages=True,
    num_history_responses=5,
    user_id="patrick"
)

agent.print_response("Meu nome é Patrick e gosto de Python")
# Em outra sessão...
agent.print_response("Qual é meu nome?")  # lembra!
```

---

## Multi-agentes

```python
agente_web = Agent(
    name="Buscador",
    tools=[DuckDuckGoTools()],
    description="Busca informações na internet"
)

agente_analista = Agent(
    name="Analista",
    description="Analisa e sumariza informações de forma clara"
)

time = Agent(
    team=[agente_web, agente_analista],
    instructions=["Busque informações e depois analise de forma clara"],
    show_tool_calls=True
)

time.print_response("Resuma as últimas notícias sobre IA generativa")
```

---

## Agente com RAG (conhecimento)

```python
from agno.knowledge.pdf import PDFKnowledgeBase
from agno.vectordb.pgvector import PgVector

knowledge_base = PDFKnowledgeBase(
    path="documentos/",
    vector_db=PgVector(table_name="documentos", db_url="..."),
)
knowledge_base.load(recreate=False)

agent = Agent(
    model=OpenAIChat(id="gpt-4o"),
    knowledge=knowledge_base,
    search_knowledge=True,
    description="Assistente que responde com base nos documentos"
)
```

---

## Projeto

!!! example "Agente de pesquisa"
    1. Ferramentas: busca na web + cálculo matemático
    2. Memória persistente em SQLite entre sessões
    3. Responde perguntas buscando na web quando necessário
    4. Modo verboso mostrando o raciocínio passo a passo
    5. Integrado com FastAPI como endpoint

---

## Vídeos recomendados

- **"Agno (Phidata) na prática"** — Sandeco
- **"Criando agentes com Agno"** — AI da Hora

---

[Próximo módulo: MCPs na Prática :material-arrow-right:](mcp.md){ .md-button .md-button--primary }
