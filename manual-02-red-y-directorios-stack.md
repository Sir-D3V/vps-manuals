1. renombrar servidor, nomenclatura:
   nombre cliente (6) + tipo entorno + servicio

   ```bash
   sudo hostnamectl set-hostname nuevo-nombre
   ```

   respuesta esperada: ninguna.

   ```bash
   sudo nano /etc/hosts
   ```

   (renombrar ultima columna del 127.0.1.1)

2. crear directorios del stack

   ```bash
   sudo mkdir -p /opt/apps/{cloudflared,web,n8n,chatwoot,postgres,metabase} && sudo chown -R $USER:$USER /opt/apps
   ```

   (cantidad directorios necesarios para la producción)

   respuesta esperada: ninguna.

3. crear red virtual (proxy) para enrutar servicios

   ```bash
   docker network create proxy-net
   ```

   respuesta esperada: cadena id red.
