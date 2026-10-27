```bash
# cloudflared
curl -s https://api.github.com/repos/cloudflare/cloudflared/releases/latest | grep -oP '"tag_name":\s*"\K[^"]+'

# nginx
curl -s https://api.github.com/repos/nginx/nginx/releases/latest | grep -oP '"tag_name":\s*"\K[^"]+'

# n8n
curl -s https://api.github.com/repos/n8n-io/n8n/releases/latest | grep -oP '"tag_name":\s*"\K[^"]+'

# chatwoot
curl -s https://api.github.com/repos/chatwoot/chatwoot/releases/latest | grep -oP '"tag_name":\s*"\K[^"]+'

# metabase
curl -s https://api.github.com/repos/metabase/metabase/releases/latest | grep -oP '"tag_name":\s*"\K[^"]+'
```

* n8n: Limpiar prefijo → De n8n@2.44.7 a 2.44.7
* Cloudflared: Directo → 2026.10.2
* Chatwoot: Conservar la "v" → v4.x.y
* Metabase: Fijar LTS → Usar etiqueta de rama estable como v0.64-lts o versión cerrada
* Nginx: Cambiar formato → De release-1.31.8 a 1.31.8-alpine
