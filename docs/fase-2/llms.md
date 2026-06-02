# Módulo 5 — Por Dentro das LLMs

**Fase 2 › Fundamentos de IA** · Como modelos como o ChatGPT funcionam

---

!!! info "Este é o módulo mais importante para entender IA de verdade"
    A diferença entre um usuário de IA e um engenheiro de IA é entender o que acontece por baixo dos panos.

---

## O que é um LLM?

LLM (Large Language Model) é um modelo de machine learning treinado em bilhões de textos para **prever qual token vem a seguir**. O ChatGPT, Claude, Gemini e Llama são todos LLMs.

---

## A jornada de uma palavra dentro de um LLM

### 1. Tokenização

O texto não entra como palavras — entra como *tokens*. Tokens são pedaços de palavras com IDs numéricos. "Inteligência" pode virar 3 tokens. Cada modelo tem seu próprio vocabulário.

```
"Olá mundo" → [15, 284, 3500]
```

!!! tip "Por que tokens importam para engenheiros?"
    APIs de LLM cobram por token. Prompts verbosos custam mais. Entender tokenização ajuda a otimizar custos e o tamanho do contexto.

### 2. Embeddings

Cada token é convertido em um **vetor de números** (ex: 768 ou 4096 dimensões). Esses vetores capturam significado semântico: "rei" e "rainha" ficam próximos no espaço vetorial, enquanto "gato" e "democracia" ficam longe.

```python
# Visualizando a ideia de proximidade semântica
"rei" + "mulher" - "homem" ≈ "rainha"   # álgebra vetorial!
```

### 3. Transformer e Atenção (Attention)

O coração de todo LLM moderno. O mecanismo de **self-attention** permite que cada token "olhe" para todos os outros e decida quais são relevantes.

```
"O banco estava cheio"
→ "banco" presta atenção em "cheio" para entender
  que é banco de praça, não banco financeiro
```

A atenção opera em múltiplas "cabeças" (multi-head attention) em paralelo, cada uma capturando diferentes relações semânticas.

### 4. Camadas e parâmetros

Um LLM tem dezenas de camadas de transformers empilhadas. Cada camada aprende padrões progressivamente mais abstratos:

- Camadas iniciais: sintaxe, gramática
- Camadas do meio: semântica, relações entre conceitos
- Camadas finais: raciocínio, geração coerente

| Modelo | Parâmetros |
|---|---|
| GPT-2 | 1.5 bilhões |
| Llama 3.1 8B | 8 bilhões |
| GPT-3 | 175 bilhões |
| GPT-4 (estimativa) | ~1 trilhão |

### 5. Geração — sampling

O modelo não retorna uma palavra — retorna uma **distribuição de probabilidade** sobre todo o vocabulário. A palavra escolhida depende da **temperatura**:

```python
# Temperatura 0 → sempre escolhe o token mais provável (determinístico)
# Temperatura 1 → segue a distribuição natural
# Temperatura > 1 → mais criativo/aleatório

# Top-p (nucleus sampling) — usa apenas tokens que somam p% de probabilidade
# Top-k — usa apenas os k tokens mais prováveis
```

### 6. Context Window

O modelo só "vê" um número limitado de tokens por vez. Tudo fora dessa janela é esquecido — é por isso que memória e RAG existem.

| Modelo | Context Window |
|---|---|
| GPT-4o | 128k tokens |
| Claude 3.5 Sonnet | 200k tokens |
| Gemini 1.5 Pro | 1M tokens |
| Llama 3.1 8B | 128k tokens |

---

## Como os modelos são treinados

### 1. Pre-training
O modelo aprende a linguagem prevendo o próximo token em trilhões de tokens da web. É custoso: GPT-4 custou estimados $100M+ para treinar.

### 2. Fine-tuning supervisionado (SFT)
O modelo é ajustado em exemplos de perguntas + respostas ideais escritas por humanos. Ensina o modelo a seguir instruções.

### 3. RLHF (Reinforcement Learning from Human Feedback)
Humanos avaliam respostas e o modelo aprende a maximizar as avaliações positivas. É o que faz modelos como ChatGPT serem "úteis, inofensivos e honestos".

```
Pre-training → "Modelo que prevê texto"
SFT          → "Modelo que segue instruções"
RLHF         → "Modelo que é útil e seguro"
```

---

## O que LLMs realmente fazem (e o que não fazem)

!!! success "O que LLMs fazem bem"
    - Gerar texto coerente e fluente
    - Seguir instruções complexas
    - Traduzir, resumir, classificar
    - Escrever e explicar código
    - Raciocínio em múltiplos passos (com CoT)

!!! warning "Limitações fundamentais"
    - Não têm acesso à internet (por padrão)
    - Conhecimento tem data de corte
    - Podem "alucinar" — inventar fatos com confiança
    - Não têm memória entre conversas (por padrão)
    - Não fazem aritmética perfeitamente — usam ferramentas para isso

---

## Fine-tuning vs Prompt Engineering vs RAG

| Abordagem | Quando usar | Custo |
|---|---|---|
| **Prompt Engineering** | Sempre — tente primeiro | Gratuito |
| **RAG** | Precisar de dados externos/atualizados | Baixo/Médio |
| **Fine-tuning** | Formato de saída muito específico ou domínio muito especializado | Alto |
| **Pre-training** | Construir um modelo do zero | Altíssimo |

---

## Exercícios conceituais

!!! exercise "Exercício 1"
    Explique com suas palavras: por que aumentar a temperatura gera respostas mais criativas mas menos precisas?

!!! exercise "Exercício 2"
    Pesquise: qual é o custo por 1 milhão de tokens de entrada e saída no GPT-4o, Claude 3.5 Sonnet e Gemini 1.5 Flash? Qual é o mais barato para uma aplicação de alto volume?

!!! exercise "Exercício 3"
    Abra o [Tokenizer da OpenAI](https://platform.openai.com/tokenizer) e tokenize um texto em português de 100 palavras. Quantos tokens foram gerados? Por que é diferente de 100?

!!! exercise "Exercício 4"
    Experimente a mesma pergunta com temperatura 0, 0.5 e 1.5 usando a API da OpenAI. Documente as diferenças nas respostas.

---

## Vídeos recomendados

- **"Como funciona o ChatGPT por dentro"** — Sandeco
- **"Transformers explicados"** — IA do Zero
- **"But what is a GPT?"** — 3Blue1Brown (em inglês, legendado — visual excepcional)
- **"LLMs do zero"** — buscar "como LLM funciona português"

---

[Próximo módulo: Hugging Face :material-arrow-right:](huggingface.md){ .md-button .md-button--primary }
