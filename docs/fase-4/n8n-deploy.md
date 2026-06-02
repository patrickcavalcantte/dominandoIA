# Módulo 18 — n8n Auto-Hospedagem

**Fase 4 › Ferramentas** · Servidor próprio com domínio e SSL

---

## Por que hospedar seu próprio n8n?

O n8n cloud tem limites de execuções. Hospedando no seu servidor você tem execuções ilimitadas, controle total dos dados e custo fixo (apenas o VPS).

---

## Stack necessária

- VPS (Hostinger, DigitalOcean, AWS EC2)
- Docker + Docker Compose
- Domínio próprio
- Nginx como reverse proxy
- SSL via Let's Encrypt

---

## Docker Compose

```yaml
# docker-compose.yml
version: "3.8"

services:
  n8n:
    image: n8nio/n8n
    restart: always
    ports:
      - "5678:5678"
    environment:
      - N8N_HOST=n8n.seudominio.com
      - N8N_PORT=5678
      - N8N_PROTOCOL=https
      - NODE_ENV=production
      - WEBHOOK_URL=https://n8n.seudominio.com/
      - GENERIC_TIMEZONE=America/Sao_Paulo
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=senha_forte_aqui
    volumes:
      - n8n_data:/home/node/.n8n

volumes:
  n8n_data:
```

```bash
docker compose up -d
```

---

## Configuração Nginx

```nginx
# /etc/nginx/sites-available/n8n
server {
    server_name n8n.seudominio.com;

    location / {
        proxy_pass http://localhost:5678;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_read_timeout 86400;
    }
}
```

```bash
sudo ln -s /etc/nginx/sites-available/n8n /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

## SSL com Certbot

```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d n8n.seudominio.com
# Certificado renova automaticamente via cron
```

---

## Checklist de deploy

- [ ] VPS Ubuntu 22.04 com pelo menos 2GB RAM
- [ ] Docker e Docker Compose instalados
- [ ] Domínio apontando para o IP do servidor (DNS propagado)
- [ ] n8n rodando via Docker Compose
- [ ] Nginx configurado como reverse proxy
- [ ] SSL ativo (HTTPS funcionando)
- [ ] Autenticação básica ativada
- [ ] Backup automático do volume `n8n_data`

---

## Backup automático

```bash
# Adicionar ao crontab: crontab -e
0 3 * * * docker run --rm -v n8n_data:/data -v /backups:/backup alpine tar czf /backup/n8n-$(date +%Y%m%d).tar.gz /data
```

---

## Projeto

!!! example "Deploy completo"
    1. Crie uma VPS na Hostinger (plano básico)
    2. Configure Docker + n8n + Nginx + SSL seguindo o checklist
    3. Migre um workflow local para o servidor
    4. Configure backup automático diário
    5. Teste que webhooks externos chegam corretamente

---

## Vídeos recomendados

- **"Deploy n8n no servidor próprio"** — Sandeco
- **"Docker do zero para deploy"** — Código Fonte TV
- **"VPS com Hostinger + Docker"** — buscar no YouTube

---

[Próximo módulo: Google Antigravity :material-arrow-right:](antigravity.md){ .md-button .md-button--primary }
