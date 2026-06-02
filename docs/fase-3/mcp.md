# Módulo 13 — MCPs na Prática

**Fase 3 › Aplicações de IA** · Model Context Protocol

---

## O que é MCP?

MCP (Model Context Protocol) é um protocolo criado pela Anthropic que padroniza como LLMs se conectam a ferramentas externas.

**Analogia:** MCP é para agentes de IA o que USB é para computadores — um padrão universal que qualquer ferramenta pode seguir.

```
LLM (Claude/GPT) ←→ MCP Client ←→ MCP Server ←→ Ferramenta
```

---

## Instalação

```bash
pip install mcp anthropic
```

---

## Criando um servidor MCP

```python
from mcp.server import Server
from mcp.server.stdio import stdio_server
from mcp.types import Tool, TextContent
import json
import asyncio

app = Server("meu-servidor-ia")

@app.list_tools()
async def listar_ferramentas() -> list[Tool]:
    return [
        Tool(
            name="buscar_clima",
            description="Retorna o clima atual de uma cidade",
            inputSchema={
                "type": "object",
                "properties": {
                    "cidade": {"type": "string", "description": "Nome da cidade"}
                },
                "required": ["cidade"]
            }
        ),
        Tool(
            name="salvar_nota",
            description="Salva uma nota em disco",
            inputSchema={
                "type": "object",
                "properties": {
                    "titulo": {"type": "string"},
                    "conteudo": {"type": "string"}
                },
                "required": ["titulo", "conteudo"]
            }
        )
    ]

@app.call_tool()
async def chamar_ferramenta(name: str, arguments: dict):
    if name == "buscar_clima":
        cidade = arguments["cidade"]
        # Em produção: chamar API real
        return [TextContent(type="text", text=f"Clima em {cidade}: 25°C, ensolarado")]

    if name == "salvar_nota":
        from pathlib import Path
        Path(f"notas/{arguments['titulo']}.txt").write_text(
            arguments["conteudo"], encoding="utf-8"
        )
        return [TextContent(type="text", text="Nota salva com sucesso!")]

async def main():
    async with stdio_server() as (read, write):
        await app.run(read, write, app.create_initialization_options())

if __name__ == "__main__":
    asyncio.run(main())
```

---

## MCPs prontos para usar

Instale via npm ou pip e configure no seu cliente MCP:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/caminho/pasta"]
    },
    "sqlite": {
      "command": "uvx",
      "args": ["mcp-server-sqlite", "--db-path", "banco.db"]
    },
    "brave-search": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-brave-search"],
      "env": {"BRAVE_API_KEY": "sua_chave"}
    }
  }
}
```

| MCP | Função |
|---|---|
| `server-filesystem` | Ler/escrever arquivos locais |
| `server-sqlite` | Banco de dados SQLite |
| `server-github` | Repositórios GitHub |
| `server-brave-search` | Busca na web |
| `server-google-maps` | Mapas e localização |

---

## Usando com Claude via SDK Python

```python
import anthropic

client = anthropic.Anthropic()

# Claude com ferramentas expostas pelo MCP
response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    tools=[
        {
            "name": "buscar_clima",
            "description": "Retorna o clima de uma cidade",
            "input_schema": {
                "type": "object",
                "properties": {
                    "cidade": {"type": "string"}
                },
                "required": ["cidade"]
            }
        }
    ],
    messages=[{"role": "user", "content": "Qual o clima em São Paulo?"}]
)

# Verificar se Claude quer usar uma ferramenta
if response.stop_reason == "tool_use":
    tool_use = next(b for b in response.content if b.type == "tool_use")
    print(f"Ferramenta: {tool_use.name}")
    print(f"Argumentos: {tool_use.input}")
```

---

## Projeto

!!! example "Servidor MCP de notas"
    Crie um servidor MCP com 4 ferramentas:

    1. `criar_nota(titulo, conteudo)` — salva nota em SQLite
    2. `listar_notas()` — lista todas as notas
    3. `ler_nota(titulo)` — lê uma nota específica
    4. `deletar_nota(titulo)` — deleta uma nota

    Configure no Claude Desktop e use como assistente pessoal de notas.

---

## Vídeos recomendados

- **"MCP explicado do zero"** — Sandeco
- **"Model Context Protocol na prática"** — AI da Hora

---

[Próximo módulo: Multi-Agentes com CrewAI :material-arrow-right:](crewai.md){ .md-button .md-button--primary }
