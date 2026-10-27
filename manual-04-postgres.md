1. crear contraseña aleatoria con un generador

   ```bash
   openssl rand -hex 24
   ```

   respuesta esperada: contraseña nueva

2. crear archivo .env

   ```bash
   cd /opt/apps/postgres
   nano .env
   ```

   respuesta esperada: se abrirá el editor

   ```dotenv
   POSTGRES_USER=postgres
   POSTGRES_PASSWORD=pegar_generado_1
   N8N_DB_PASSWORD=pegar_generado_2
   CHATWOOT_DB_PASSWORD=pegar_generado_3
   METABASE_DB_PASSWORD=pegar_generado_4
   ```

   (contraseña de cualquier otro aplicativo tambien iria aqui )

3. asignar permisos al archivo recién creado

   ```bash
   chmod 600 .env
   ```

   respuesta esperada: ninguna

4. crear docker compose en postgres

   ```bash
   nano docker-compose.yml
   ```

   respuesta esperada: se abrira el editor

   ```yaml
   services:
     postgres:
       image: pgvector/pgvector:pg16
       restart: unless-stopped
       environment:
         POSTGRES_USER: ${POSTGRES_USER}
         POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
       volumes:
         - postgres_data:/var/lib/postgresql/data
       healthcheck:
         test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER}"]
         interval: 10s
         timeout: 5s
         retries: 5
       networks:
         - proxy-net

   volumes:
     postgres_data:

   networks:
     proxy-net:
       external: true
   ```

5. levantar servicio

   ```bash
   docker compose up -d
   docker compose ps
   ```

   respuesta esperada: barra de progreso con checks

6. crear base de datos, usuarios y extensiones

   ```bash
   cd /opt/apps/postgres
   set -a
   source .env
   set +a
   ```

   ```bash
   docker compose exec -T postgres psql -U postgres <<EOF
   CREATE USER n8n WITH PASSWORD '$N8N_DB_PASSWORD';
   CREATE DATABASE n8n OWNER n8n;
   CREATE USER chatwoot WITH PASSWORD '$CHATWOOT_DB_PASSWORD';
   CREATE DATABASE chatwoot OWNER chatwoot;
   CREATE USER metabase WITH PASSWORD '$METABASE_DB_PASSWORD';
   CREATE DATABASE metabase OWNER metabase;
   \c chatwoot
   CREATE EXTENSION IF NOT EXISTS vector;
   CREATE EXTENSION IF NOT EXISTS pg_trgm;
   EOF
   ```

   respuesta esperada: listado de creaciones.

7. verificar bases de datos

   ```bash
   docker compose exec postgres psql -U postgres -c "\l"
   docker compose exec postgres psql -U postgres -c "\du"
   ```

   respuesta esperada: paginador con tabla de bases de datos.
   (se sale con la letra “q”)

8. verificar dns interno para postgres

   ```bash
   docker run --rm --network proxy-net postgres:16-alpine pg_isready -h postgres -p 5432
   ```

   respuesta esperada: confirmación de conexiones aceptadas.

9. Fin del manual
