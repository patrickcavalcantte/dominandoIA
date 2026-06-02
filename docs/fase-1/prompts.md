# Módulo 4 — Engenharia de Prompts

**Fase 1 › Fundamentos** · Extrair o máximo de qualquer LLM

---

## O que é Engenharia de Prompts?

Prompt é tudo que você envia para um LLM. Engenharia de Prompts é a disciplina de estruturar esse texto de forma que o modelo entenda exatamente o que você quer e produza o melhor resultado possível.

Um engenheiro de IA passa mais tempo refinando prompts do que escrevendo código. A diferença entre um prompt medíocre e um excelente pode ser a diferença entre um produto que funciona e um que não funciona.

---

## Como LLMs leem seu prompt

Antes de escrever bons prompts, entenda como o modelo processa a entrada:

- O modelo lê da esquerda para a direita, token por token
- Ele não "pensa" antes — gera o próximo token com base em tudo que veio antes
- **Instruções no início** têm mais peso
- Contexto próximo ao final influencia mais a próxima geração
- O modelo tende a continuar padrões: se você começa bem, ele continua bem

```
System prompt → estabelece comportamento geral
Exemplos      → mostram o padrão esperado
Contexto      → dados relevantes para a tarefa
Instrução     → o que fazer com tudo isso
```

---

## Os 5 elementos de um prompt eficaz

### 1. Papel (Persona)

```
❌ "Explique machine learning"

✓ "Você é um professor universitário especialista em IA com 15 anos de
   experiência ensinando para iniciantes. Explique machine learning."
```

### 2. Contexto

```
❌ "Melhore esse email"

✓ "Você é um copywriter profissional. Estou enviando um email de
   follow-up para um cliente que não respondeu há 5 dias após uma
   proposta de R$15.000. Tom: profissional mas amigável, sem parecer
   desesperado. Melhore esse email: [email]"
```

### 3. Instrução clara

```
❌ "Analise esse código"

✓ "Analise esse código Python e liste:
   1) bugs que podem causar erros em produção
   2) problemas de performance
   3) violações de PEP 8
   Para cada item, explique o problema e sugira a correção."
```

### 4. Formato de saída

```
✓ "Responda em JSON com os campos: titulo, resumo (max 2 frases),
   tags (lista de strings), dificuldade (iniciante/intermediário/avançado)"

✓ "Responda em markdown com: título H2, lista de tópicos,
   bloco de código Python no final"

✓ "Responda em exatamente 3 parágrafos, sem bullet points"
```

### 5. Restrições

```
✓ "Não use jargão técnico. Não inclua introduções genéricas como
   'Ótima pergunta!'. Não repita informações já dadas.
   Limite a resposta a 200 palavras."
```

---

## Técnicas fundamentais

### Zero-shot
Sem exemplos. Para tarefas simples e bem definidas.

```
Classifique o sentimento do texto como positivo, negativo ou neutro.
Texto: "A entrega atrasou 3 dias mas o produto veio perfeito."
Sentimento:
```

### Few-shot
Mostre o padrão com exemplos. O modelo aprende o formato e o raciocínio.

```
Classifique o sentimento:

Texto: "Adorei o atendimento, voltarei sempre!"
Sentimento: positivo

Texto: "Produto chegou quebrado, péssimo."
Sentimento: negativo

Texto: "Entrega no prazo, produto como descrito."
Sentimento: neutro

Texto: "Demorou mais que o esperado, mas valeu a pena."
Sentimento:
```

!!! tip "Regra dos exemplos"
    Use pelo menos 3 exemplos. Cubra casos-limite, não só os óbvios.

### Chain-of-Thought (CoT)
Para tarefas de raciocínio, peça para "pensar em voz alta". Melhora drasticamente a precisão.

```
❌ "João tem 3 caixas com 8 laranjas cada. Dá 1/4 para Maria. Quantas ficou?"

✓ "Resolva passo a passo, mostrando cada cálculo:
   João tem 3 caixas com 8 laranjas cada. Ele dá 1/4 do total para
   Maria. Quantas laranjas João ficou?"
```

Saída esperada:
```
Passo 1: Total = 3 × 8 = 24
Passo 2: Deu para Maria = 24 × 1/4 = 6
Passo 3: João ficou = 24 - 6 = 18
Resposta: 18 laranjas.
```

### ReAct (Reason + Act)
Padrão para agentes: o modelo alterna entre raciocinar e agir.

```
Resolva seguindo o padrão:
Pensamento: [raciocine sobre o que fazer]
Ação: [ferramenta a usar]
Observação: [resultado da ação]
... (repete até ter a resposta)
Resposta Final: [resposta ao usuário]

Pergunta: Qual a capital do país com maior PIB do mundo em 2024?
```

### Self-consistency
Para respostas críticas: gere múltiplas respostas e escolha a mais frequente.

```python
from openai import OpenAI
from collections import Counter

client = OpenAI()

def resposta_consistente(pergunta: str, n: int = 5) -> str:
    respostas = []
    for _ in range(n):
        r = client.chat.completions.create(
            model="gpt-4o-mini",
            messages=[{"role": "user", "content": pergunta}],
            temperature=0.8
        )
        respostas.append(r.choices[0].message.content.strip())
    return Counter(respostas).most_common(1)[0][0]
```

---

## System Prompts profissionais

```python
SYSTEM_PROMPT = """
# Identidade
Você é a Aria, assistente de suporte da TechCorp.

# Comportamento
- Seja cordial, paciente e profissional
- Use linguagem simples, evite jargão com leigos
- Se o usuário parecer frustrado, reconheça o problema antes de dar soluções
- Nunca prometa prazos ou reembolsos sozinho

# Conhecimento
Produtos disponíveis: TechPro X1, TechHome S3, TechSound B2

# Limitações
- Não discuta concorrentes
- Não tome decisões de reembolso — escale para humano
- Se não souber: "Deixa eu verificar isso para você"

# Formato
- Respostas curtas para perguntas simples
- Use numeração para passos técnicos
- Finalize sempre perguntando se o problema foi resolvido
"""
```

### Injetando contexto dinâmico

```python
def montar_prompt(pergunta: str, historico: dict, kb: str) -> list:
    system = f"""Você é um assistente de suporte.

<conhecimento_base>
{kb}
</conhecimento_base>

<historico_cliente>
Nome: {historico['nome']}
Plano: {historico['plano']}
</historico_cliente>

Responda com base nas informações acima."""

    return [
        {"role": "system", "content": system},
        {"role": "user", "content": pergunta}
    ]
```

---

## Prompt Chaining — pipelines de prompts

Para tarefas complexas, divida em vários prompts. Cada um faz uma coisa bem.

```python
from openai import OpenAI

client = OpenAI()

def llm(prompt: str, system: str = "", temp: float = 0.3) -> str:
    msgs = []
    if system:
        msgs.append({"role": "system", "content": system})
    msgs.append({"role": "user", "content": prompt})
    r = client.chat.completions.create(
        model="gpt-4o-mini", messages=msgs, temperature=temp
    )
    return r.choices[0].message.content.strip()

def pipeline_analise_contrato(texto: str) -> dict:
    # Passo 1: Extrair cláusulas
    clausulas = llm(
        f"Extraia todas as cláusulas em JSON (lista de strings):\n\n{texto}",
        temp=0.1
    )
    # Passo 2: Identificar riscos
    riscos = llm(
        f"Para cada cláusula, identifique riscos legais. JSON: lista de objetos "
        f"com 'clausula' e 'riscos'.\n\nCláusulas: {clausulas}",
        system="Você é um advogado especialista em contratos comerciais.",
        temp=0.2
    )
    # Passo 3: Resumo executivo
    resumo = llm(
        f"Escreva um resumo executivo de 3 parágrafos para o CEO, "
        f"linguagem não-técnica.\n\nRiscos: {riscos}",
        temp=0.5
    )
    return {"clausulas": clausulas, "riscos": riscos, "resumo": resumo}
```

---

## Prompts para casos de uso de IA

### Extração de dados estruturados

```python
prompt = """Extraia as informações do currículo em JSON.

Campos:
- nome (string)
- email (string ou null)
- telefone (string ou null)
- experiencias (lista: empresa, cargo, periodo, descricao)
- habilidades (lista de strings)
- formacao (lista: instituicao, curso, ano_conclusao)

Se um campo não for encontrado, use null.
Retorne APENAS o JSON, sem texto adicional.

CURRÍCULO:
{curriculo}"""
```

### Classificação com múltiplas categorias

```python
prompt = """Classifique o ticket de suporte.

Categorias:
- BILLING: cobrança, faturas, pagamentos
- TECHNICAL: bugs, erros, problemas de funcionamento
- ACCOUNT: senha, acesso, permissões
- FEATURE: pedidos de novas funcionalidades
- GENERAL: dúvidas gerais

Urgência: ALTA (impede uso) / MEDIA (dificulta) / BAIXA (cosmético/dúvida)

Retorne JSON: {{"categoria": "...", "urgencia": "...", "resumo": "..."}}

Ticket: {ticket}"""
```

### Para RAG (injeção de contexto)

```
Responda à pergunta baseado EXCLUSIVAMENTE no contexto fornecido.

REGRAS:
- Se a resposta estiver no contexto, responda com base nele
- Se não estiver, diga: "Não encontrei essa informação na base de conhecimento"
- Nunca misture conhecimento próprio com o contexto
- Cite a fonte: (Fonte: [nome do documento])

<contexto>
{contexto_recuperado}
</contexto>

Pergunta: {pergunta}
```

### Para agentes

```
Você é um agente autônomo de pesquisa.

PROCESSO OBRIGATÓRIO:
1. Antes de qualquer ação, liste o que precisa descobrir
2. Execute uma ação por vez
3. Após cada ação, avalie se tem informação suficiente
4. Só responda ao usuário quando tiver certeza

FERRAMENTAS:
- buscar_web(query)
- calcular(expressao)
- salvar_nota(conteudo)

REGRAS:
- Nunca invente informações — se não sabe, busque
- Se uma busca falhar, reformule a query
- Máximo de 10 ações por resposta
```

---

## Erros comuns e como evitar

| Erro | Exemplo ruim | Exemplo correto |
|---|---|---|
| Instrução vaga | "Melhore isso" | "Reescreva 30% mais curto, mantendo todos os dados" |
| Sem formato | "Liste as vantagens" | "Liste exatamente 5 vantagens em bullet points de até 15 palavras" |
| Conflito interno | "Seja breve e explique tudo em detalhes" | Escolha um: "Máx 100 palavras" |
| Sem contexto | "Responda como especialista" | "Você é especialista em [área] com foco em [subárea]" |
| Negação sem alternativa | "Não use linguagem técnica" | "Use linguagem acessível para uma pessoa de 60 anos sem experiência em tecnologia" |

---

## Avaliando e iterando prompts

```python
from dataclasses import dataclass

@dataclass
class CasoTeste:
    entrada: str
    saida_esperada: str
    criterios: list[str]

def avaliar_prompt(template: str, casos: list[CasoTeste]) -> dict:
    resultados = []
    for caso in casos:
        saida = llm(template.format(entrada=caso.entrada))
        avaliacao = llm(
            f"""Avalie se a saída atende aos critérios. JSON:
{{"aprovado": true/false, "score": 0-10, "feedback": "..."}}

Esperado: {caso.saida_esperada}
Obtido: {saida}
Critérios: {caso.criterios}""",
            temp=0.1
        )
        resultados.append({"saida": saida, "avaliacao": avaliacao})

    return {"detalhes": resultados}
```

---

## Resumo das técnicas

| Técnica | Quando usar |
|---|---|
| **Zero-shot** | Tarefas simples e bem definidas |
| **Few-shot** | Quando o formato de saída é específico |
| **Chain-of-Thought** | Raciocínio, matemática, lógica |
| **ReAct** | Agentes com ferramentas |
| **Self-consistency** | Respostas críticas onde errar é caro |
| **Prompt Chaining** | Tarefas complexas que se beneficiam de decomposição |

---

## Exercícios

!!! exercise "Exercício 1 — Classificador de intenções"
    Crie um prompt que classifique mensagens em: `comprar`, `suporte`, `reclamação`, `elogio`, `outro`. Teste com 10 mensagens e meça a acurácia.

!!! exercise "Exercício 2 — Extrator de dados"
    Dado um texto de notícia, extraia em JSON: `titulo`, `data`, `pessoas_mencionadas`, `organizacoes`, `local`, `resumo_3_frases`. Teste com 5 notícias reais.

!!! exercise "Exercício 3 — Pipeline de conteúdo"
    Crie um pipeline de 4 prompts encadeados:

    1. Recebe um tema → gera 5 títulos
    2. Escolhe título → gera outline com 5 seções
    3. Para cada seção → gera parágrafo de 100 palavras
    4. Une tudo → gera introdução e conclusão

!!! exercise "Exercício 4 — A/B test"
    Crie dois prompts para a mesma tarefa, defina 5 critérios de qualidade e use o LLM como avaliador para decidir qual é melhor.

---

## Vídeos recomendados

- **"Engenharia de Prompts do zero"** — Sandeco
- **"Como escrever prompts profissionais"** — AI da Hora
- **"Prompt Engineering na prática"** — Asimov Academy YouTube

---

[Próxima fase: Por Dentro das LLMs :material-arrow-right:](../fase-2/llms.md){ .md-button .md-button--primary }
