1. Cambiar contraseña

2. Actualizar

   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

   respuesta esperada: lineas de descarga y actualización.

3. instalar dependencias

   ```bash
   sudo apt install -y ca-certificates curl gnupg lsb-release
   ```

   respuesta esperada: lineas de descarga e instalación.

4. Registro de la clave de seguridad (GPG) de Docker

   ```bash
   sudo install -m 0755 -d /etc/apt/keyrings
   sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
   sudo chmod a+r /etc/apt/keyrings/docker.asc
   ```

   respuesta esperada: ninguna.

5. Agregar el repositorio  Docker a las fuentes de APT

   ```bash
   echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

   respuesta esperada: ninguna.

6. Instalación de docker y sus componentes

   ```bash
   sudo apt update && sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   ```

   respuesta esperada: lineas de descarga e instalación.

7. Ejecutar docker sin necesidad de sudo, agregando user

   ```bash
   sudo usermod -aG docker $USER
   ```

   respuesta esperada: ninguna.

8. aplicar cambios USER sin cerrar sesion actual

   ```bash
   newgrp docker
   ```

   respuesta esperada: ninguna.

9. Comprobar que todo esté bien configurado

   ```bash
   docker run --rm hello-world
   ```

   respuesta esperada: Hello from Docker!

10. consultar estado del servicio después de la limpieza

    ```bash
    docker info | grep -E 'Containers:|Images:|Server Version:'
    ```

    respuesta esperada: 0, 1, numero version.

11. rotación de logs y prevención de desbordamiento

    ```bash
    sudo tee /etc/docker/daemon.json <<EOF
    {
      "log-driver": "json-file",
      "log-opts": { "max-size": "10m", "max-file": "3" },
      "live-restore": true
    }
    EOF
    sudo systemctl restart docker
    docker info | grep "Logging Driver"
    ```

    respuesta esperada: Logging Driver: json-file

12. Fin del manual
