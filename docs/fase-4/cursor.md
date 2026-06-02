# Módulo 15 — CursorAI

**Fase 4 › Ferramentas** · IDE com IA para 10x mais produtividade

---

## O que é Cursor?

Cursor é uma IDE baseada no VS Code com IA profundamente integrada. Diferente de copilots simples, o Cursor entende seu codebase inteiro, permite conversar sobre o código, refatorar arquivos inteiros e gerar código complexo com contexto completo.

**Download:** [cursor.sh](https://cursor.sh)

---

## Funcionalidades principais

| Atalho | Função |
|---|---|
| `Tab` | Aceitar sugestão inline |
| `Cmd/Ctrl + K` | Editar código selecionado com instrução |
| `Cmd/Ctrl + L` | Abrir chat com contexto do projeto |
| `Cmd/Ctrl + I` | Composer — gerar múltiplos arquivos |
| `@arquivo.py` | Referenciar arquivo no chat |
| `@codebase` | Referenciar o projeto inteiro |

---

## Usando o Chat (Cmd+L)

```
# Exemplos de prompts no chat:

"Explique o que esse arquivo faz"
"Por que esse teste está falhando?"
"Como posso otimizar essa função para lidar com 1 milhão de registros?"
"Adicione logging em todas as funções desse módulo"
"Escreva testes unitários para a classe RAGSimples"
```

## Usando o Composer (Cmd+I)

Para features completas que envolvem múltiplos arquivos:

```
"Crie uma API FastAPI completa com:
- Endpoint POST /chat com histórico de conversa
- Endpoint POST /resumir para textos longos
- Modelos Pydantic para request/response
- Middleware de CORS
- Arquivo .env.example
- README com instruções de uso"
```

---

## .cursorrules — padrões do projeto

Crie `.cursorrules` na raiz do projeto para o Cursor seguir seus padrões:

```
Você é um especialista em Python, FastAPI e IA.

Padrões de código:
- Use type hints em todos os parâmetros e retornos
- Siga PEP 8
- Use async/await para operações I/O
- Prefira dataclasses ou Pydantic a dicionários soltos
- Logging em vez de print
- Variáveis de ambiente via python-dotenv

Estrutura de projeto:
- src/ para código fonte
- tests/ para testes
- docs/ para documentação
- .env para variáveis (nunca versionar)

Não faça:
- Não use Exception genérico — use exceções específicas
- Não escreva comentários óbvios
- Não use variáveis com nomes de uma letra (exceto loops simples)
```

---

## Fluxo de trabalho com Cursor

```
1. Descreve a feature no Composer
2. Revisa o código gerado (sempre!)
3. Usa Cmd+K para ajustes pontuais
4. Usa o Chat para tirar dúvidas
5. Testa e itera
```

!!! warning "Sempre revise o código gerado"
    O Cursor pode gerar código funcionalmente correto mas com problemas sutis de segurança, performance ou que não segue os padrões do projeto. Revise sempre antes de aceitar.

---

## Exercício

!!! exercise "Cursor na prática"
    1. Instale o Cursor e abra o projeto de FastAPI que você criou
    2. Use o Composer para adicionar autenticação JWT completa
    3. Use Cmd+K para adicionar tratamento de erros em uma função
    4. Use o Chat para pedir "explique como o middleware de autenticação funciona"
    5. Crie o `.cursorrules` para o projeto

---

[Próximo módulo: Claude Code :material-arrow-right:](claude-code.md){ .md-button .md-button--primary }
