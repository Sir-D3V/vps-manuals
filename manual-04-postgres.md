1. crear contraseña aleatoria con un generador

   **openssl rand \-hex 24**  
     
   respuesta esperada: contraseña nueva

2. crear archivo .env

   **cd /opt/apps/postgres**  
   **nano .env**

   respuesta esperada: se abrirá el editor  
   **POSTGRES\_USER=postgres**  
   **POSTGRES\_PASSWORD=pegar\_generado\_1**  
   **N8N\_DB\_PASSWORD=pegar\_generado\_2**  
   **CHATWOOT\_DB\_PASSWORD=pegar\_generado\_3**  
   **METABASE\_DB\_PASSWORD=pegar\_generado\_4**

   (contraseña de cualquier otro aplicativo tambien iria aqui )

3. asignar permisos al archivo recién creado

   **chmod 600 .env**

   respuesta esperada: ninguna

4. crear docker compose en postgres

   **nano docker-compose.yml**

   respuesta esperada: se abrira el editor  
   **services:**  
     **postgres:**  
       **image: pgvector/pgvector:pg16**  
       **restart: unless-stopped**  
       **environment:**  
         **POSTGRES\_USER: \${POSTGRES\_USER}**  
         **POSTGRES\_PASSWORD: \${POSTGRES\_PASSWORD}**  
       **volumes:**  
         **\- postgres\_data:/var/lib/postgresql/data**  
       **healthcheck:**  
         **test: \["CMD-SHELL", "pg\_isready \-U \${POSTGRES\_USER}"\]**  
         **interval: 10s**  
         **timeout: 5s**  
         **retries: 5**  
       **networks:**  
         **\- proxy-net**

   **volumes:**  
     **postgres\_data:**

   **networks:**  
     **proxy-net:**  
       **external: true**

5. levantar servicio

   **docker compose up \-d**  
   **docker compose ps**

   respuesta esperada: barra de progreso con checks

6. crear base de datos, usuarios y extensiones

   **cd /opt/apps/postgres**  
   **set \-a**  
   **source .env**  
   **set \+a**

   **docker compose exec \-T postgres psql \-U postgres \<\<EOF**  
   **CREATE USER n8n      WITH PASSWORD '\$N8N\_DB\_PASSWORD';**  
   **CREATE DATABASE n8n      OWNER n8n;**  
   **CREATE USER chatwoot WITH PASSWORD '\$CHATWOOT\_DB\_PASSWORD';**  
   **CREATE DATABASE chatwoot OWNER chatwoot;**  
   **CREATE USER metabase WITH PASSWORD '\$METABASE\_DB\_PASSWORD';**  
   **CREATE DATABASE metabase OWNER metabase;**  
   **\\c chatwoot**  
   **CREATE EXTENSION IF NOT EXISTS vector;**  
   **CREATE EXTENSION IF NOT EXISTS pg\_trgm;**  
   **EOF**

   respuesta esperada: listado de creaciones.

7. verificar bases de datos

   **docker compose exec postgres psql \-U postgres \-c "\\l"**  
   **docker compose exec postgres psql \-U postgres \-c "\\du"**

   respuesta esperada: paginador con tabla de bases de datos.  
   (se sale con la letra “q”)

8. verificar dns interno para postgres

   **docker run \--rm \--network proxy-net postgres:16-alpine pg\_isready \-h postgres \-p 5432**

   respuesta esperada: confirmación de conexiones aceptadas.

9. Fin del manual

   

   

   

   

   

   

   

   

   

   

   

