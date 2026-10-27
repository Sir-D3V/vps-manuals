1. Generar nueva llave encriptada

   ```bash
   openssl rand -hex 32
   ```

   respuesta esperada: cadena de texto aleatoria.

2. Recuperar la contraseña asignada para n8n en la BD

   ```bash
   grep N8N_DB_PASSWORD /opt/apps/postgres/.env
   ```

   respuesta esperada: N8N_DB_PASSWORD

3. Crear archivo .env de n8n

   ```bash
   cd /opt/apps/n8n
   nano .env
   ```

   respuesta esperada: se abrirá el editor

   ```dotenv
   N8N_DB_PASSWORD=la_del_paso_2
   N8N_ENCRYPTION_KEY=la_del_paso_1
   GENERIC_TIMEZONE=America/Caracas
   ```

   (ctrl+o , enter, ctrl+x)

4. asignar permisos al archivo

   ```bash
   chmod 600 .env
   ```

   respuesta esperada: ninguna.

5. curl para buscar versión reciente n8n

   ```bash
   curl -s https://api.github.com/repos/n8n-io/n8n/releases/latest | grep -oP '"tag_name":\s*"\K[^"]+'
   ```

   respuesta esperada: número version a pegar en paso 6

6. configurar docker composer n8n

   ```bash
   nano docker-compose.yml
   ```

   (siempre procurar estar dentro del directorio n8n)

   respuesta esperada: se abrirá el editor

   ```yaml
   services:
     n8n:
       image: docker.n8n.io/n8nio/n8n:VERSION  # ← pegar versión del paso 5 (sin el prefijo n8n@)
       restart: unless-stopped
       environment:
         DB_TYPE: postgresdb
         DB_POSTGRESDB_HOST: postgres
         DB_POSTGRESDB_PORT: 5432
         DB_POSTGRESDB_DATABASE: n8n
         DB_POSTGRESDB_USER: n8n
         DB_POSTGRESDB_PASSWORD: ${N8N_DB_PASSWORD}
         N8N_ENCRYPTION_KEY: ${N8N_ENCRYPTION_KEY}
         N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS: "true"
         N8N_HOST: n8n.mc-suministros.com
         N8N_PROTOCOL: https
         WEBHOOK_URL: https://n8n.mc-suministros.com/
         GENERIC_TIMEZONE: ${GENERIC_TIMEZONE}
         TZ: ${GENERIC_TIMEZONE}
         EXECUTIONS_DATA_PRUNE: "true"
         EXECUTIONS_DATA_MAX_AGE: "168"
       volumes:
         - n8n_data:/home/node/.n8n
       networks:
         - proxy-net

   volumes:
     n8n_data:

   networks:
     proxy-net:
       external: true
   ```

   (ctrl+o, enter, ctrl+x)

7. levantar el contenedor

   ```bash
   cd /opt/apps/n8n && docker compose up -d
   docker compose ps
   docker compose logs --tail 15 n8n
   ```

   respuesta esperada: barra de progreso + respectivos checks

8. Registrar el usuario

   Abrir **https://n8n.dominio.com**

   llenar formulario con nuestro usuario **cottusai.work@g**

9. Fin del manual
