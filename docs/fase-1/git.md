# Módulo 3 — Git e GitHub

**Fase 1 › Fundamentos** · Controle de versão e colaboração

---

## Por que Git é indispensável para engenheiros de IA?

Git é um sistema que registra o histórico de todas as mudanças no seu código. GitHub é a plataforma onde esse histórico fica salvo na nuvem.

Para um engenheiro de IA, Git é indispensável: você vai versionar modelos, prompts, pipelines e colaborar em equipe. Além disso, todos os projetos open-source de IA (LangChain, Hugging Face, CrewAI) vivem no GitHub.

---

## Instalação e configuração inicial

```bash
# Verificar se já está instalado
git --version

# Configurar identidade (feita uma vez)
git config --global user.name "Seu Nome"
git config --global user.email "seu@email.com"
```

---

## Fluxo básico de trabalho

```
Código alterado → git add → git commit → git push → GitHub
```

### Comandos essenciais

```bash
# Inicializar repositório
git init

# Ver status atual
git status

# Adicionar arquivos para o próximo commit
git add arquivo.py          # arquivo específico
git add .                   # todos os arquivos alterados

# Salvar snapshot (commit)
git commit -m "feat: adiciona endpoint de chat"

# Enviar para o GitHub
git push origin main

# Baixar atualizações
git pull

# Ver histórico
git log --oneline
```

---

## Branches — linhas paralelas de desenvolvimento

```bash
# Criar e entrar em uma branch
git checkout -b feature/agente-rag

# Listar branches
git branch

# Voltar para main
git checkout main

# Unir branch na main
git merge feature/agente-rag

# Deletar branch após merge
git branch -d feature/agente-rag
```

!!! tip "Boas práticas de nomes de branch"
    - `feature/nome-da-feature` — nova funcionalidade
    - `fix/descricao-do-bug` — correção de bug
    - `docs/secao-atualizada` — documentação

---

## .gitignore — o que NÃO versionar

Crie um arquivo `.gitignore` na raiz do projeto:

```gitignore
# Chaves de API — NUNCA versione isso
.env
*.env

# Ambientes virtuais Python
venv/
.venv/
__pycache__/
*.pyc

# Modelos de IA (arquivos grandes)
*.bin
*.safetensors
models/

# Bancos de dados locais
*.db
*.sqlite

# Arquivos do sistema
.DS_Store
Thumbs.db
```

!!! danger "Regra de ouro"
    **Nunca versione chaves de API ou senhas.** Se você acidentalmente fizer isso, a chave já está comprometida — troque imediatamente, mesmo que você delete o commit.

---

## Trabalhando com GitHub

### Criar repositório e fazer o primeiro push

```bash
# 1. Crie o repositório no github.com (sem README)
# 2. No terminal, dentro da pasta do projeto:

git init
git add .
git commit -m "feat: projeto inicial"
git branch -M main
git remote add origin https://github.com/seu-usuario/seu-repo.git
git push -u origin main
```

### Clonar um repositório existente

```bash
git clone https://github.com/usuario/repositorio.git
cd repositorio
```

---

## Pull Requests — revisão antes do merge

Um Pull Request (PR) é uma proposta de mudança. Antes de fazer merge na main, outros colaboradores revisam o código.

```
1. Criar branch → fazer commits → push
2. Abrir PR no GitHub (comparando sua branch com main)
3. Revisar o código junto com a equipe
4. Fazer merge após aprovação
```

---

## Comandos para situações comuns

```bash
# Desfazer mudanças NÃO commitadas
git restore arquivo.py

# Desfazer o último commit (mantendo as mudanças)
git reset HEAD~1

# Ver diferença entre arquivos
git diff

# Guardar mudanças temporariamente (sem commit)
git stash
git stash pop  # recuperar

# Ver quem modificou cada linha de um arquivo
git blame arquivo.py
```

---

## Exercícios

!!! exercise "Exercício 1"
    Crie um repositório no GitHub chamado `meu-primeiro-repo`, faça 3 commits com mensagens descritivas seguindo o padrão `tipo: descrição`.

!!! exercise "Exercício 2"
    Crie uma branch `feature/calculadora`, implemente uma calculadora simples e faça merge na main via Pull Request.

!!! exercise "Exercício 3"
    Adicione um `.gitignore` adequado ao projeto Python com ambiente virtual e arquivo `.env`.

!!! exercise "Exercício 4"
    Clone um repositório open-source de IA do GitHub (sugestão: `langchain-ai/langchain`), explore a estrutura de pastas e leia o histórico de commits.

---

## Vídeos recomendados

- **"Git e GitHub para Iniciantes"** — Curso em Vídeo
- **"Git na prática"** — Código Fonte TV
- **"Git do zero ao avançado"** — Attekita Dev

---

[Próximo módulo: Engenharia de Prompts :material-arrow-right:](prompts.md){ .md-button .md-button--primary }
