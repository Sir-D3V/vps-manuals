# Manuales VPS — Cottus AI

Manuales operativos para configurar un VPS Debian 13 desde cero hasta producción: hardening, Docker, dominio + Cloudflare Tunnel, y stack de aplicaciones.

## Índice

| # | Manual | Contenido |
|---|--------|-----------|
| 00 | [Configuración inicial](manual-00-configuracion-inicial.md) | Password, update, timezone, SSH keys, UFW, fail2ban, unattended-upgrades, swap |
| 01 | [Docker](manual-01-docker.md) | Engine + compose plugin + daemon.json (rotación de logs) |
| 02 | [Red y directorios stack](manual-02-red-y-directorios-stack.md) | Hostname, `/opt/apps`, red `proxy-net` |
| 03 | [Dominio y túnel](manual-03-dominio-y-tunnel.md) | Nameservers → Cloudflare, cloudflared, rutas públicas |
| 04 | [Postgres](manual-04-postgres.md) | pgvector/pg16, una instancia, bases y usuarios por app |
| 05 | [n8n](manual-05-n8n.md) | n8n sobre Postgres + URL pública vía túnel |
| + | [Fijar versiones](manual-plus-fijar-versiones.md) | Pinear tags de imágenes Docker post-instalación |

## Convenciones del stack

- Cada app vive en `/opt/apps/<stack>` con su `docker-compose.yml` + `.env`
- Toda comunicación interna por la red `proxy-net`; nada publica puertos al host
- Todo `.env` → `chmod 600`
- Imágenes pineadas por versión — nunca `latest` en producción
