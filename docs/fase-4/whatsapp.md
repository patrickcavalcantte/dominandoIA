# Módulo 20 — Agentes de Atendimento no WhatsApp

**Fase 4 › Ferramentas** · Projeto final da formação

---

## Arquitetura completa

```
Usuário (WhatsApp)
→ API Oficial do WhatsApp (Meta)
→ Webhook → n8n
→ LLM (OpenAI/Claude) + RAG
→ Ferramentas (calendário, banco de dados)
→ Resposta → WhatsApp
```

---

## Pré-requisitos

- Conta Meta Business verificada
- Número de telefone dedicado (não pode ser número pessoal)
- App no Meta for Developers
- n8n hospedado com HTTPS (webhook público)

---

## Configuração do webhook no n8n

```javascript
// Nó Code — verificar webhook do WhatsApp
const mode = $input.first().json.query?.["hub.mode"];
const token = $input.first().json.query?.["hub.verify_token"];
const challenge = $input.first().json.query?.["hub.challenge"];

if (mode === "subscribe" && token === "SEU_TOKEN_SECRETO") {
  return [{ json: { challenge } }];
}

return [{ json: { error: "Token inválido" } }];
```

---

## Enviando mensagens

```python
import requests

def enviar_whatsapp(numero: str, mensagem: str, token: str, phone_id: str):
    url = f"https://graph.facebook.com/v18.0/{phone_id}/messages"
    response = requests.post(
        url,
        headers={
            "Authorization": f"Bearer {token}",
            "Content-Type": "application/json"
        },
        json={
            "messaging_product": "whatsapp",
            "to": numero,
            "type": "text",
            "text": {"body": mensagem}
        }
    )
    return response.json()
```

---

## Workflow n8n completo

```
Trigger: Webhook (mensagem recebida)
→ Code: Extrair número, nome e texto
→ Code: Buscar histórico da conversa (SQLite/Redis)
→ OpenAI: Classificar intenção
→ Switch:
   ├── suporte → Agente com RAG de documentos
   ├── agendamento → Google Calendar API
   ├── vendas → Agente com catálogo de produtos
   └── outros → Resposta genérica
→ Code: Salvar mensagem no histórico
→ HTTP Request: Enviar resposta pelo WhatsApp
```

---

## Memória da conversa

```javascript
// Nó Code — buscar histórico
const numero = $input.first().json.numero;
const historico = $node["SQLite"].json[numero] || [];

// Montar mensagens para OpenAI
const mensagens = [
  { role: "system", content: "Você é assistente da Clínica Saúde+" },
  ...historico.slice(-10),  // últimas 10 mensagens
  { role: "user", content: $input.first().json.texto }
];

return [{ json: { mensagens, numero } }];
```

---

## Projeto Final

!!! example "Agente completo para clínica"
    Construa um agente de atendimento para uma clínica fictícia:

    **Funcionalidades:**

    - [ ] Atende via WhatsApp com API oficial
    - [ ] Responde dúvidas sobre serviços (RAG com documentos da clínica)
    - [ ] Agenda consultas via Google Calendar
    - [ ] Envia confirmações automáticas 24h antes
    - [ ] Escala para humano com tag `#humano` se não souber responder
    - [ ] Dashboard de atendimentos em Google Sheets
    - [ ] Relatório semanal automático por email

    **Critérios de avaliação:**

    - [ ] Webhook configurado e recebendo mensagens
    - [ ] LLM respondendo com contexto da conversa
    - [ ] RAG funcionando com documentos da clínica
    - [ ] Agendamento integrado ao Google Calendar
    - [ ] Histórico persistido entre sessões

---

## Vídeos recomendados

- **"WhatsApp Bot com n8n e ChatGPT"** — Sandeco
- **"API do WhatsApp Business"** — AI da Hora
- **"Agente de WhatsApp completo"** — buscar no YouTube

---

## Parabéns por concluir a formação!

Você agora domina toda a stack de um **Engenheiro de Agentes de IA**:

- Fundamentos sólidos em Python e Git
- Entendimento profundo de como LLMs funcionam
- APIs de IA (OpenAI, Hugging Face, Groq, Anthropic)
- Construção de agentes com LangChain, Agno, CrewAI
- MCPs para conectar IA a qualquer ferramenta
- Deploy e automações em produção

**O próximo passo é construir.** Escolha um problema real que você quer resolver e aplique o que aprendeu.

---

[Voltar ao início :material-arrow-left:](../index.md){ .md-button }
