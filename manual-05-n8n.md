1. Generar nueva llave encriptada

   **openssl rand \-hex 32**

   respuesta esperada: cadena de texto aleatoria.

2. Recuperar la contraseña asignada para n8n en la BD

   **grep N8N\_DB\_PASSWORD /opt/apps/postgres/.env**

   respuesta esperada: N8N\_DB\_PASSWORD

3. Crear archivo .env de n8n

   cd /opt/apps/n8n  
   nano .env

   respuesta esperada: se abrirá el editor  
   **N8N\_DB\_PASSWORD=la\_del\_paso\_2**  
   **N8N\_ENCRYPTION\_KEY=la\_del\_paso\_1**  
   **GENERIC\_TIMEZONE=America/Caracas**

   (ctrl+o , enter, ctrl+x)

4. asignar permisos al archivo

   **chmod 600 .env**

   respuesta esperada: ninguna.

5. curl para buscar versión reciente n8n

   **curl \-s https\://api.github.com/repos/n8n-io/n8n/releases/latest \| grep \-oP '"tag\_name":\\s\*"\\K\[^"\]+'**

   respuesta esperada: número version a pegar en paso 6

6. configurar docker composer n8n

    **nano docker-compose.yml**  
   (siempre procurar estar dentro del directorio n8n)

   respuesta esperada: se abrirá el editor  
   **services:**  
     **n8n:**  
       **image: [docker.n8n.io/n8nio/n8n:](http://docker.n8n.io/n8nio/n8n:latest)***~~paso 5 aqui~~*  
       **restart: unless-stopped**  
       **environment:**  
         **DB\_TYPE: postgresdb**  
         **DB\_POSTGRESDB\_HOST: postgres**  
         **DB\_POSTGRESDB\_PORT: 5432**  
         **DB\_POSTGRESDB\_DATABASE: n8n**  
         **DB\_POSTGRESDB\_USER: n8n**  
         **DB\_POSTGRESDB\_PASSWORD: \${N8N\_DB\_PASSWORD}**  
         **N8N\_ENCRYPTION\_KEY: \${N8N\_ENCRYPTION\_KEY}**  
         **N8N\_ENFORCE\_SETTINGS\_FILE\_PERMISSIONS: "true"**  
         **N8N\_HOST: [n8n.mc-suministros.com](http://n8n.mc-suministros.com)**  
         **N8N\_PROTOCOL: https**  
         **WEBHOOK\_URL: [https\://n8n.mc-suministros.com/](https://n8n.mc-suministros.com/)**  
         **GENERIC\_TIMEZONE: \${GENERIC\_TIMEZONE}**  
         **TZ: \${GENERIC\_TIMEZONE}**  
         **EXECUTIONS\_DATA\_PRUNE: "true"**  
         **EXECUTIONS\_DATA\_MAX\_AGE: "168"**  
       **volumes:**  
         **\- n8n\_data:/home/node/.n8n**  
       **networks:**  
         **\- proxy-net**

   **volumes:**  
     **n8n\_data:**

   **networks:**  
     **proxy-net:**  
       **external: true**

   (ctrl+o, enter, ctrl+x)

7. levantar el contenedor

   **cd /opt/apps/n8n && docker compose up \-d**  
   **docker compose ps**  
   **docker compose logs \--tail 15 n8n**

   respuesta esperada: barra de progreso \+ respectivos checks

8. Registrar el usuario 

   Abrir **https\://n8n.dominio.com** 

   llenar formulario con nuestro usuario **cottusai.work@g**

9. Fin del manual