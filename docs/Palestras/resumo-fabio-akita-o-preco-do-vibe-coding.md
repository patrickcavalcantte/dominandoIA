# O Preço do Vibe Coding — Fábio Akita
*Web Summit Rio 2026*

> **Evento:** Web Summit Rio 2026
> **Autor:** Fábio Akita
> **Tema:** O impacto real do ecossistema de LLMs e agentes autônomos nas carreiras de software — e por que "vibe coding" precisa virar **Agile Vibe Coding**.

---

## Contexto / Tese Central

LLMs e agentes autônomos estão mudando como construímos software, tornando 2026 um ponto de virada para a engenharia orientada a agentes. Mas, além do marketing, Akita distingue a **"programação por impulso"** (vibe coding como retratada na mídia) da **engenharia de verdade**, defendendo uma abordagem disciplinada: **Agile Vibe Coding**. A mensagem é aproveitar a velocidade da IA **sem abandonar arquitetura, verificação e o verdadeiro trabalho artesanal de software**.

---

## 1. A História até Aqui

### A bolha era velha (Slide 3)
- **2020–2022**: a promessa de "qualquer um programa" já vinha sendo martelada.
- **Fim de 2022**: a correção do mercado começou **antes** da IA programar direito. O ChatGPT foi "o prego no caixão".

### A corrida era parâmetro + compute (2022–2024, Slide 4)
- **GPT-3** (175B) virou o marco público da era moderna.
- **PaLM / Llama / DeepSeek**: centenas de bilhões; MoE chega para ativar só parte do modelo.
- **Lei de escala**: mais dado + mais parâmetro + mais compute ainda ajuda, mas cobra caro.

### O gargalo mudou (Slide 5)
- **Treino ficou caro**: frontier training já passa de 100 MW; projeção de múltiplos GW até 2030.
- **Data center é lento**: energia, terreno, transformador, conexão não nascem em sprint.
- **Uso virou carga**: um agente troca 1 prompt por dezenas de chamadas, tool calls e retries.

> O ganho passou a vir de **usar melhor o modelo existente**: reasoning, tool calling, KV cache, prompt caching e harness maduro.

---

## 2. 2025 — O Ano dos Agentes (Slide 6)

| Data | Marco |
|---|---|
| **mar 2025** | Responses API, tools e Agents SDK viram produto |
| **mai 2025** | Claude 4 pensa entre tool calls; Claude Code vira GA |
| **ago 2025** | GPT-5 aguenta loop mais longo e erra menos no uso de ferramenta |
| **nov 2025** | GPT-5.1 e Opus 4.5 refinam uso diário |

> *Não foi chatbot mais esperto. Foi modelo, ferramenta, terminal e loop fechando juntos.*

### O que separa Tier A: não é mais só parâmetro (Slide 18)
- **Prompt caching** — sem cache, cada turno relê o contexto inteiro e o custo explode.
- **Tool calling** — chamar a ferramenta certa, com o argumento certo.
- **Reasoning** — budget extra para planejar antes de agir.

> *Tamanho virou commodity. Agente real precisa de infraestrutura de inferência, não só parâmetro no release note.*

---

## 3. A Prova Prática — Disciplina > "IA Melhor"

### Mesmo dev, mesmo agente, processo diferente (Slide 10)
| Projeto | Resultado |
|---|---|
| **FrankMD** | 212 commits em 19 dias, refactor pesado, teste correndo atrás |
| **M.Akita Chronicles** | 274 commits em 8 dias, **TDD, CI e refatoração contínua** |

> *A variável não foi "IA melhor". Foi **disciplina de engenharia** desde o primeiro commit.*

### Números que pesam — recorte Web Summit Rio 2026 (Slide 11)
| Métrica | Valor |
|---|---|
| Linhas úteis | **432.134** |
| Linhas de teste | **62.643** |
| Commits | **2.241** |
| Horas ativas estimadas | **~473 h** |

> Agregado de 26 projetos AI-assisted.

### Alcançamos o "Developer 10x"? (Slide 12)
- **5x a 10x** de velocidade
- **Mais tração** — menos bloqueio, menos procrastinação
- **Mais alcance** — stack inteira, ferramentas, deploy, documentação
- **Mais confiança** — testes, CI, refatoração, produção

---

## 4. O Nome Verdadeiro: Agile Vibe Coding (Slide 14)

> **É XP (Extreme Programming) com pareamento de máquina.**

- **TDD** — segura erro de modelo antes de virar lama
- **CI por commit** — pega drift e regressão cedo
- **Refatoração contínua** — evita cirurgia cara depois

### Prompt único é pra demo (Slide 13)
- **Produção é iteração** — bug, deploy, retorno, refatoração, ajuste de prompt
- **"Pronto" é mentira** — 125 commits de pós-produção em 4 projetos

---

## 5. A Virada Econômica (Slide 19)

> **IA nunca vai ser perfeita. Mas errar ficou barato.**

- **Roda** — agente executa, testa, quebra e mostra o stack trace
- **Corrige** — o loop volta em segundos, não em dias
- **Decide** — o humano ainda corta escopo, arquitetura e risco

> **Barato:** CRUD, landing page, painel interno, bot, ETL, cola entre APIs.
> **Caro:** julgamento, arquitetura, operação, dono do problema.

---

## 6. O Toolkit Open Source — Trocar de Harness sem Perder Controle (Slide 21)

| Ferramenta | Função |
|---|---|
| **ai-jail** | Autonomia do agente com cerca no filesystem |
| **ai-memory** | Contexto que sobrevive ao Claude, Codex e opencode |
| **ai-usagebar** | Limite de plano visível antes de travar no meio da refatoração |

### ai-jail: Autonomia com cerca (Slide 22)
- **Modo sem freio no harness** — Claude/Codex com menos confirmação a cada passo
- **Trava no SO** — projeto read-write; host, home, cache e dotfiles fora ou controlados
- **Política versionável** — `--dry-run`, `--mask .env`, `--private-home`, `--lockdown`
- Repo: **akitaonrails/ai-jail** (sandbox multi-OS — "not 100% secure, but enough")

> *Não é VM nem blindagem militar. É uma camada prática para deixar o agente trabalhar rápido sem dar a chave da casa.*

### ai-memory: Contexto que sobrevive ao harness (Slide 23)
- **Hooks capturam** — prompt, tool call, decisão e boundary de sessão
- **Markdown vira memória** — wiki versionável, FTS5, handoff e busca via MCP
- **Troca de agente sem reexplicar** — fecha Claude hoje, abre Codex amanhã no mesmo diretório

> *Não substitui `docs/` canônico. Cobre gotchas, decisões transitórias e handoff entre sessões.*

---

## 7. Transparência: o próprio deck (Slide 28)

> **Sim, este deck inteiro foi feito com IA.**

- **Pesquisa e estrutura** — fontes, ordem dos argumentos, cortes e rearranjos
- **Texto sincronizado** — slides, roteiro e presenter notes mantidos juntos
- **Mídia e acabamento** — frames, crops, builds e pós-processo do PPTX

> *Agente no terminal, Marp para gerar o deck, scripts para automatizar build e iteração curta até fechar.*

### Código e dados públicos (Slide 16)
> **github.com/akitaonrails/llm-coding-benchmark** — benchmark de runs autônomos de coding contra um brief fixo de Rails, comparando modelos locais (Ollama) e em nuvem sob o mesmo prompt.

---

## 8. Anotações Pessoais (insights da palestra)

> **A IA é um espelho de quem você é.**

- **Sênior bom ganha agilidade.** Quem já tem boa base de engenharia usa a IA como amplificador — produz mais código excelente, mais rápido.
- **Programador ruim erra mais rápido.** A IA não corrige a falta de fundamento; ela apenas acelera o resultado — bom ou ruim. Se você é ruim, vai errar mais e em maior velocidade.
- **Os modelos chegaram num platô.** As versões recentes de GPT e Claude não trouxeram melhorias absurdas — a evolução agora é incremental (refinamento de uso diário, não saltos de capacidade). Isso reforça a tese do Akita: **o diferencial está no processo e na disciplina, não em "esperar o próximo modelo mágico".**
- **Ainda precisamos de bons cientistas da computação.** Não dá para terceirizar tudo para a IA. **As decisões — escopo, arquitetura, risco, julgamento — precisam ser tomadas por humanos.** A IA executa; o humano continua dono do problema.

> **Conclusão pessoal:** A IA reflete quem você é. Ela multiplica sua competência (ou sua incompetência). Por isso, investir em fundamento de engenharia é mais importante do que nunca — não menos.

---

## Síntese Final

Fábio Akita entrega um contraponto pragmático ao hype do "vibe coding":

1. **O gargalo virou inferência, não treino.** O ganho competitivo agora está em *usar bem* o modelo — reasoning, tool calling, caching e um harness maduro — não em ter o maior número de parâmetros.

2. **Velocidade real existe (5–10x), mas tem preço.** "Prompt único" é teatro de demo; produção é iteração, pós-produção e manutenção. "Pronto" é mentira.

3. **A variável decisiva é disciplina de engenharia, não "IA melhor".** Dois projetos com o mesmo dev e o mesmo agente tiveram resultados opostos — o que mudou foi TDD, CI e refatoração contínua desde o primeiro commit.

4. **Errar ficou barato — decidir, não.** O agente roda e corrige em segundos; o humano permanece dono do julgamento, da arquitetura, do risco e do problema.

5. **Ferramentas para manter o controle:** ai-jail, ai-memory e ai-usagebar dão autonomia ao agente sem "dar a chave da casa".

> **Agile Vibe Coding = a velocidade da IA + a disciplina da engenharia. O preço do vibe coding é abrir mão dessa disciplina — e esse preço não vale a pena.**

---

## Bibliografia / Referências

- **Site do autor:** [akitaonrails.com](https://akitaonrails.com/)
- **Benchmark público:** [github.com/akitaonrails/llm-coding-benchmark](https://github.com/akitaonrails/llm-coding-benchmark)
- **Toolkit open source:** [github.com/akitaonrails/ai-jail](https://github.com/akitaonrails/ai-jail) (+ ai-memory, ai-usagebar)
