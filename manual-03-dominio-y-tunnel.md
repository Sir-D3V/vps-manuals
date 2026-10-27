1. loguearse en cloudflare y agregar dominio cliente en nuestra cuenta madre

   domains \> add domain

   continuar y seguir pasos por defecto. finalmente esperar propagación.

2. instalar utilidades dns

   **sudo apt update && sudo apt install \-y dnsutils**

   respuesta esperada: lineas de descarga e instalación.

3. consultar nameservers

   **dig NS mc-suministros.com \+short**

   respuesta esperada: dos líneas de cloudflare y no de otro server.

4. navegar hasta el antiguo host entonces navegar hasta:

   dashboard \> domains \> DNS \> Records

   localizar los records tipo “A” y eliminarlos.

   localizar el registro cname de godaddy y eliminarlo también.

5. mientras esperamos propagación, proseguimos con la creación del túnel en el dashboard de cloudflare

   volver al home del dashboard y encontrar la seccion de  **Networking** 

   crear e ingresar nombre del túnel “nomenclatura vps \- sin servicio al final”

   elegir la opción **docker** 

   copiar únicamente token de la cadena  **Run tunnel with Docker**

   navegar hasta el el directorio  **cd /opt/apps/cloudflared/**  en caso que no exista entonces crearlo con mkdir

   **nano .env**           

   respuesta esperada: se abre editor y se debe agregar el parámetro “TUNNEL\_TOKEN=” seguido por el token.   
   **chmod 600 .env**

   respuesta esperada: ninguna.

6. curl para buscar versión reciente nginx

   **curl \-s https\://api.github.com/repos/cloudflare/cloudflared/releases/latest \| grep \-oP '"tag\_name":\\s\*"\\K\[^"\]+'**

   respuesta esperada: número version a pegar en paso 7

7. Orquestación Docker Compose en cloudflare

   **nano docker-compose.yml**  
   (siempre procurar estar dentro del directorio cloudflare)

   respuesta esperada: se abre editor para el manifiesto yml.  
   **services:**  
     **cloudflared:**  
       **image: cloudflare/cloudflared:***~~paso 6 aqui~~*  
       **restart: unless-stopped**  
       **command: tunnel \--no-autoupdate run**  
       **environment:**  
         **\- TUNNEL\_TOKEN=\${TUNNEL\_TOKEN}**  
       **networks:**  
         **\- proxy-net**

   **networks:**  
     **proxy-net:**  
       **external: true**

   guardar con ctrl+o , seguido de “enter” y salir con ctrl+x

8. levantar el túnel

   **docker compose up \-d**

   respuesta esperada: lista de contenedores con **checks**

9. visualizar logs

   **docker compose logs \--tail 10 cloudflared**

   respuesta esperada: listado de indicadores de conectividad.

10. ir al sitio cloudflare y luego a los menu:   
    networking \> tunnels \>   
    buscar túnel previamente creado \>   
    add route \> Add published application 

    del formulario en cuestión solamente llenar:  
    \* **subdomain** (dejar vacío si corresponde)  
    \* **service url** (puerto que corresponda)  

    nota: con respecto al site comprobar en el record   
    dashboard \> domains \> DNS \> Records  
    que ya existe el registro **CNAME** el cual redirige www hacia el dominio corto.

11. crear directorio para la landing \- resolver el error de host timed out

    **mkdir \-p /opt/apps/web/site**

    respuesta esperada: ninguna.

12. curl para buscar versión reciente nginx

    **curl \-s https\://api.github.com/repos/nginx/nginx/releases/latest \| grep \-oP '"tag\_name":\\s\*"\\K\[^"\]+'**  
    (pegar “**\- alpine**” al final de la versión)

    respuesta esperada: número version a pegar en paso 12

13. Orquestación Docker Compose en web

    **nano /opt/apps/web/docker-compose.yml**  
    (siempre procurar estar dentro del directorio web)

    respuesta esperada: se abre editor para el manifiesto yml.  
    **services:**  
      **web:**  
        **image: nginx:***~~paso 11 aquí~~*  
        **restart: unless-stopped**  
        **volumes:**  
          **\- ./site:/usr/share/nginx/html:ro**  
        **networks:**  
          **\- proxy-net**

    **networks:**  
      **proxy-net:**  
        **external: true**

    guardar con ctrl+o , seguido de “enter” y salir con ctrl+x 

14. crear html de prueba

    **echo '\<h1\>MC Suministros \- En construccion\</h1\>' \> /opt/apps/web/site/index.html**

    respuesta esperada: ninguna.

15. levantar el servicio

     **cd /opt/apps/web && docker compose up \-d**

    respuesta esperada: lista de contenedores con **checks**  
    (finalmente consultar directo en la web el sitio funcionando)

16. Redirect www a la raíz, navegar:

    Rules \> Redirect Rules \> Create from template  
    marcar unicamente casilla **“Preserve query string”** y finalmente botón **Deploy**.   
    (ignorar advertencia “rule may not apply to your traffic”)

    Comprobar que el sitio web este funcionando anteponiendo “www” al dominio.

17. Fin del manual  
