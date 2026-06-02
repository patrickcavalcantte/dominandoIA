# Módulo 17 — Automações com n8n

**Fase 4 › Ferramentas** · Automatizar tarefas sem código

---

## O que é n8n?

n8n é uma plataforma de automação de workflows open-source. Você conecta apps e serviços (Gmail, Slack, Google Sheets, APIs de IA) visualmente — sem escrever código para a maioria dos casos.

**Iniciar localmente:**
```bash
docker run -it --rm \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
# Acesse: http://localhost:5678
```

---

## Conceitos fundamentais

| Conceito | Descrição |
|---|---|
| **Workflow** | Fluxo de automação com início, meio e fim |
| **Node** | Cada bloco no workflow (ex: "receber email", "chamar GPT") |
| **Trigger** | O que inicia o workflow (webhook, agendamento, evento) |
| **Credenciais** | Onde você armazena chaves de API de forma segura |

---

## Workflows essenciais para IA

### Atendimento automático por email

```
Trigger: Email recebido (Gmail)
→ Node: Extrair texto e remetente
→ Node: OpenAI — classificar assunto e gerar resposta
→ Node: If — se urgente, notificar no Slack
→ Node: Gmail — enviar resposta automática
→ Node: Google Sheets — registrar atendimento
```

### Pipeline de conteúdo automatizado

```
Trigger: Agendado (diariamente às 9h)
→ Node: HTTP Request — buscar trending topics
→ Node: OpenAI — gerar artigo completo
→ Node: HTTP Request — publicar no Wordpress
→ Node: HTTP Request — postar no LinkedIn via API
```

### Webhook + IA

```
Trigger: Webhook (POST /processar)
→ Node: Code — processar JSON recebido
→ Node: OpenAI — analisar dados
→ Node: Respond to Webhook — retornar resultado
```

---

## Nós mais úteis

| Nó | Função |
|---|---|
| **HTTP Request** | Chamar qualquer API REST |
| **Code** | Executar JavaScript custom |
| **OpenAI** | Integração nativa com GPT |
| **Google Sheets** | Ler/escrever planilhas |
| **Webhook** | Receber dados externos |
| **Schedule Trigger** | Executar em horários definidos |
| **If / Switch** | Lógica condicional |
| **Loop Over Items** | Iterar sobre listas |
| **Set** | Transformar dados |
| **Merge** | Combinar fluxos |

---

## Nó Code — para lógica avançada

```javascript
// Exemplo: processar mensagem do WhatsApp e extrair dados
const mensagem = $input.first().json.body;
const numero = mensagem.from;
const texto = mensagem.text?.body || "";

const resultado = {
  numero: numero.replace("@c.us", ""),
  texto: texto.trim(),
  timestamp: new Date().toISOString(),
  comprimento: texto.length
};

return [{ json: resultado }];
```

---

## Projeto

!!! example "Pipeline de análise de reviews"
    Crie um workflow que:

    1. Recebe reviews via webhook `POST /review`
    2. Usa OpenAI para classificar sentimento e extrair pontos positivos/negativos
    3. Se negativo: envia alerta no Slack
    4. Salva todos os resultados em Google Sheets
    5. Toda segunda às 9h: envia relatório semanal por email

---

## Vídeos recomendados

- **"n8n do zero"** — Sandeco
- **"Automações com n8n e IA"** — AI da Hora
- **"n8n completo em português"** — buscar no YouTube

---

[Próximo módulo: n8n Auto-Hospedagem :material-arrow-right:](n8n-deploy.md){ .md-button .md-button--primary }
