# Módulo 16 — Claude Code

**Fase 4 › Ferramentas** · IA diretamente no terminal

---

## O que é Claude Code?

Claude Code é a CLI da Anthropic que integra Claude diretamente no terminal. Diferente de chatbots, ele pode ler, editar e executar código no seu projeto — tornando-se um engenheiro de pair-programming real.

---

## Comandos essenciais

```bash
claude                          # inicia sessão interativa
claude "explique este projeto"  # pergunta rápida com contexto do diretório
claude -p "adicione testes"     # modo não-interativo (pipe)
```

---

## Casos de uso no dia a dia

### Debugging
```
"O teste test_rag.py está falhando com este erro:
IndexError: list index out of range na linha 45.
Investigue a causa e proponha a correção."
```

### Refatoração
```
"Refatore o arquivo agents/base.py para:
- Separar a lógica de tool execution em uma classe separada
- Adicionar type hints em todos os métodos
- Substituir os prints por logging"
```

### Geração de testes
```
"Gere testes unitários para a classe SistemaRAG em rag.py.
Cubra: indexar_documento, buscar, responder.
Use pytest e mock para a OpenAI API."
```

### Revisão de código
```
"Revise as mudanças no branch feature/auth e aponte:
- Bugs potenciais
- Problemas de segurança
- Melhorias de performance"
```

### Documentação
```
"Gere um README.md completo para este projeto incluindo:
- Descrição, pré-requisitos, instalação
- Variáveis de ambiente necessárias
- Exemplos de uso de cada endpoint
- Arquitetura do sistema em texto"
```

---

## Boas práticas

!!! tip "Seja específico"
    - **Ruim:** "melhore o código"
    - **Bom:** "adicione validação de entrada no endpoint /chat para rejeitar perguntas vazias ou acima de 10.000 caracteres"

!!! tip "Dê contexto"
    Antes de pedir mudanças, deixe o Claude ler os arquivos relevantes com `/read` ou referenciando os caminhos na sua mensagem.

!!! tip "Use para tarefas repetitivas"
    - "Adicione docstrings em todas as funções públicas de src/"
    - "Converta todos os prints para logging no projeto"
    - "Adicione `# type: ignore` onde houver erro de tipo no mypy"

---

## Integrando ao fluxo de desenvolvimento

```bash
# Antes de abrir um PR
claude "revise as mudanças no git diff e aponte problemas"

# Entendendo código legado
claude "explique a arquitetura deste projeto e como os módulos se relacionam"

# Migrações
claude "migre este código de LangChain 0.1 para LangChain 1.0"
```

---

## Exercício

!!! exercise "Claude Code na prática"
    1. No terminal, dentro de um projeto Python, peça ao Claude Code para:
    2. Adicionar type hints em todo o projeto
    3. Gerar testes para uma função específica
    4. Criar um `.gitignore` adequado para o projeto
    5. Explicar a arquitetura atual do projeto

---

[Próximo módulo: Automações com n8n :material-arrow-right:](n8n.md){ .md-button .md-button--primary }
