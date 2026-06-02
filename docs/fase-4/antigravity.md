# Módulo 19 — Google Antigravity

**Fase 4 › Ferramentas** · Sites e automações sem escrever código

---

## O que é Google Antigravity?

Google Antigravity (parte do Google Labs / Firebase Studio) permite criar sites, apps e automações usando linguagem natural e IA, sem escrever código manualmente. Ideal para prototipar rapidamente ou criar interfaces sem saber frontend.

---

## Principais casos de uso

- Landing pages e sites institucionais
- Dashboards de dados
- Formulários conectados a bancos de dados
- Protótipos de aplicativos para validar ideias

---

## Prompts eficazes para Antigravity

```
✓ "Crie uma landing page para um curso de Python com:
   - Hero section com título, subtítulo e botão de CTA verde
   - Seção de benefícios com 3 cards com ícones
   - Seção de depoimentos com 2 cards
   - Formulário de captura de email
   - Rodapé com links de redes sociais
   - Design escuro com cores roxo e verde"

✗ "Faça um site bonito"
```

---

## Fluxo de trabalho

```
1. Descreva o que quer em linguagem natural
2. A IA gera o código (HTML/CSS/JS ou React)
3. Refine com prompts adicionais
4. Publique diretamente pelo Firebase
```

---

## Integrando com n8n e IA

Formulários do Antigravity podem enviar dados para n8n via webhook:

```javascript
// Código gerado pelo Antigravity para enviar formulário
async function enviarFormulario(dados) {
  const response = await fetch("https://n8n.seudominio.com/webhook/formulario", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(dados)
  });
  return response.json();
}
```

**Workflow n8n resultante:**
```
Webhook recebe dados
→ OpenAI classifica o lead
→ Salva no Google Sheets
→ Envia email de boas-vindas personalizado
→ Notifica equipe no Slack se lead qualificado
```

---

## Exercício

!!! exercise "Landing page com automação"
    1. Crie uma landing page para um produto fictício com Antigravity
    2. Adicione formulário de captura de email/WhatsApp
    3. Conecte ao n8n via webhook
    4. O n8n salva os dados no Google Sheets e envia email de confirmação automático

---

[Próximo módulo: Agentes no WhatsApp :material-arrow-right:](whatsapp.md){ .md-button .md-button--primary }
