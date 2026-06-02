# Módulo 14 — Multi-Agentes com CrewAI

**Fase 3 › Aplicações de IA** · Sistemas de múltiplos agentes colaborando

---

## O que é CrewAI?

CrewAI é um framework para orquestrar múltiplos agentes de IA trabalhando em equipe. Cada agente tem um papel, objetivo e ferramentas específicos. Juntos, completam tarefas complexas que um único agente não conseguiria.

```bash
pip install crewai crewai-tools
```

---

## Conceitos fundamentais

| Conceito | Descrição |
|---|---|
| **Agent** | Agente com papel, objetivo e backstory |
| **Task** | Tarefa com descrição e resultado esperado |
| **Crew** | Equipe de agentes + lista de tarefas |
| **Process** | `sequential` (um após o outro) ou `hierarchical` (manager delega) |

---

## Crew básica

```python
from crewai import Agent, Task, Crew, Process
from crewai_tools import SerperDevTool

search_tool = SerperDevTool()

# Agentes
pesquisador = Agent(
    role="Pesquisador Sênior",
    goal="Descobrir tendências de IA em 2025",
    backstory="Especialista em pesquisa com 10 anos de experiência",
    tools=[search_tool],
    verbose=True,
    llm="gpt-4o-mini"
)

escritor = Agent(
    role="Escritor de Conteúdo",
    goal="Escrever artigos claros e envolventes",
    backstory="Jornalista especializado em tecnologia",
    verbose=True,
    llm="gpt-4o-mini"
)

# Tarefas
tarefa_pesquisa = Task(
    description="Pesquise as 5 maiores tendências de IA em 2025",
    expected_output="Lista detalhada com descrição de cada tendência",
    agent=pesquisador
)

tarefa_escrita = Task(
    description="Escreva um artigo de blog sobre as tendências pesquisadas",
    expected_output="Artigo de 500 palavras em markdown",
    agent=escritor,
    context=[tarefa_pesquisa]  # usa output da tarefa anterior
)

# Executar
crew = Crew(
    agents=[pesquisador, escritor],
    tasks=[tarefa_pesquisa, tarefa_escrita],
    process=Process.sequential,
    verbose=True
)

resultado = crew.kickoff()
print(resultado.raw)
```

---

## Processo Hierárquico

```python
revisor = Agent(
    role="Editor Chefe",
    goal="Garantir qualidade e precisão do conteúdo",
    backstory="Editor com 20 anos de experiência",
    llm="gpt-4o"
)

crew = Crew(
    agents=[pesquisador, escritor, revisor],
    tasks=[...],
    process=Process.hierarchical,
    manager_llm="gpt-4o"  # manager delega tarefas automaticamente
)
```

---

## Crew com output estruturado

```python
from pydantic import BaseModel

class RelatorioMercado(BaseModel):
    empresa: str
    resumo: str
    pontos_fortes: list[str]
    riscos: list[str]
    recomendacao: str

tarefa_analise = Task(
    description="Analise a empresa {empresa} e gere relatório",
    expected_output="Relatório executivo completo",
    output_pydantic=RelatorioMercado,
    agent=analista
)

resultado = crew.kickoff(inputs={"empresa": "Petrobras"})
relatorio: RelatorioMercado = resultado.pydantic
print(relatorio.recomendacao)
```

---

## Ferramentas disponíveis no CrewAI

```python
from crewai_tools import (
    SerperDevTool,       # busca no Google
    WebsiteSearchTool,   # RAG em websites
    FileReadTool,        # leitura de arquivos
    CodeInterpreterTool, # execução de Python
    PDFSearchTool,       # RAG em PDFs
    YoutubeVideoSearchTool,
)
```

---

## Projeto

!!! example "Crew de análise de mercado"
    1. **Agente Pesquisador** — busca dados sobre uma empresa na web
    2. **Agente Analista Financeiro** — analisa métricas com YFinance
    3. **Agente Redator** — escreve relatório executivo
    4. Saída: `RelatorioEmpresa` (Pydantic) com resumo, pontos fortes, riscos e recomendação
    5. Serve via FastAPI: `POST /analisar/{empresa}`

---

## Vídeos recomendados

- **"CrewAI do zero"** — Sandeco
- **"Multi-agentes com CrewAI"** — AI da Hora
- **"CrewAI na prática"** — buscar no YouTube

---

[Próxima fase: CursorAI :material-arrow-right:](../fase-4/cursor.md){ .md-button .md-button--primary }
