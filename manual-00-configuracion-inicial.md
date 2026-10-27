1. Configurar huso horario

   **sudo timedatectl set-timezone America/Caracas**  
   **timedatectl | grep "Time zone"**

   respuesta esperada: (-04, -0400)

2. generar llave ssh - local (no host)

   **ssh-keygen -t ed25519 -C "nombre-vps"**  
   (en caso de querer generar segunda clave para otro equipo, solamente se podra iniciando sesion con la primera clave)     

   respuesta esperada: cajetin de símbolos

3. visualizar llave e ir a respaldar de inmediato

   **cat ~/.ssh/id_ed25519**

   respuesta esperada: Begin - - - End

4. copiar la llave al vps para iniciar sesión sin contraseña y evitar hackeos - local (no host)

   **ssh-copy-id debian@11.111.11.11**  
   (sustituir el numero por el ip real que corresponda)

   respuesta esperada: cantidad total de llaves añadidas. 

5. cerrar sesion e iniciar de nuevo para probar que funciona

   **ssh debian@11.111.11.11**  
   (sustituir el numero por el ip real que corresponda)

   respuesta esperada: inicia sesión de inmediato.

6. Creación de directivas de endurecimiento modular (exigir llaves)

   **sudo tee /etc/ssh/sshd_config.d/99-hardening.conf <<EOF PermitRootLogin no**  
   **PasswordAuthentication no**  
   **PubkeyAuthentication yes**  
   **EOF**

   respuesta esperada: misma impresión.

7. Recargar demonio SSH para que apliquen cambios

   **sudo sshd -t && sudo systemctl reload ssh**

   respuesta esperada: ninguna.

8. denegación de tráfico entrante y Habilitar acceso ssh

   **sudo apt install -y ufw**  
   **sudo ufw default deny incoming**  
   **sudo ufw default allow outgoing**  
   **sudo ufw allow 22/tcp**           
   **sudo ufw enable**                 
   **sudo ufw status verbose**

   respuesta esperada: pantalla confirmación “y”

9. protección contra fuerza bruta

   **sudo apt install -y fail2ban**  
   **sudo tee /etc/fail2ban/jail.local <<EOF**  
   **[DEFAULT]**  
   **bantime = 1h**  
   **findtime = 10m**  
   **maxretry = 5**  
   **[sshd]**  
   **enabled = true**  
   **EOF**  
   **sudo systemctl enable --now fail2ban**  
   **sudo fail2ban-client status sshd**

   respuesta esperada: listado status jail

10. actualizaciones de seguridad

    **sudo apt install -y unattended-upgrades**  
    **sudo dpkg-reconfigure -plow unattended-upgrades**

    respuesta esperada: pantalla confirmación “YES”

11. asignar swap

    **sudo fallocate -l 4G /swapfile && sudo chmod 600 /swapfile**  
    **sudo mkswap /swapfile && sudo swapon /swapfile**  
    **echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab**  
    **echo 'vm.swappiness=10' | sudo tee /etc/sysctl.d/99-swappiness.conf**  
    **sudo sysctl -p /etc/sysctl.d/99-swappiness.conf**  
    **free -h**

    respuesta esperada: swap 4.0Gi

12. Fin del manual

      
    

 

