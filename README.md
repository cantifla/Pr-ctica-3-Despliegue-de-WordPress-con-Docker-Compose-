Práctica 3: Despliegue de WordPress con Docker Compose en Kali Linux 🐉
Autor: Edwind Isaías
Matrícula: 20240318
Entorno: Kali Linux (Rolling Release)

📌 Objetivo
Instalar Docker y Docker Compose en un entorno Kali Linux, crear un archivo declarativo YAML con variables de entorno y desplegar simultáneamente dos contenedores conectados: Base de Datos (MySQL) y Aplicativo (WordPress).

🚀 Pasos de Implementación
1. Instalación de Docker y Docker Compose en Kali
Kali Linux a veces requiere instalar Docker desde sus propios repositorios de forma específica. Ejecutaremos la instalación completa y limpia.

Bash
# 1. Actualizar repositorios de Kali
sudo apt update -y

# 2. Instalar Docker y Docker Compose (paquete unificado en versiones recientes)
sudo apt install -y docker.io docker-compose

# 3. Habilitar y arrancar el servicio de Docker
sudo systemctl enable --now docker

# 4. Verificar instalaciones
docker --version
docker-compose --version
2. Crear el archivo docker-compose.yml
Creamos un directorio exclusivo para el proyecto. Este archivo YAML contiene las Variables de Entorno (environment) que permitirán que WordPress se conecte automáticamente a la base de datos MySQL.

Bash
# 1. Crear la carpeta del proyecto y entrar en ella
mkdir -p ~/wordpress-kali && cd ~/wordpress-kali

# 2. Inyectar el código en el archivo docker-compose.yml
cat <<EOF > docker-compose.yml
version: '3.8'

services:
  db:
    image: mysql:5.7
    container_name: wp_database
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root_super_seguro
      MYSQL_DATABASE: wordpress_db
      MYSQL_USER: wp_user
      MYSQL_PASSWORD: wp_password
    volumes:
      - db_data:/var/lib/mysql

  wordpress:
    image: wordpress:latest
    container_name: wp_app
    restart: always
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wp_user
      WORDPRESS_DB_PASSWORD: wp_password
      WORDPRESS_DB_NAME: wordpress_db
    depends_on:
      - db

volumes:
  db_data:
EOF
3. Realizar el Despliegue de los Contenedores
Con el archivo ya creado en tu Kali, le damos la orden a Docker Compose para que lea las instrucciones, descargue las imágenes y levante todo en segundo plano (-d).

Bash
# Ejecutar el despliegue en segundo plano
sudo docker-compose up -d
(Nota: Verás en la pantalla cómo empieza a descargar MySQL y WordPress. Al final debe mostrar Creating wp_database ... done y Creating wp_app ... done).

4. Ingresar y Configurar WordPress
Abre Firefox (el navegador por defecto en Kali).

Ingresa a la siguiente dirección usando el puerto 8080 que configuramos:
http://127.0.0.1:8080

Te aparecerá la pantalla de instalación de WordPress.

Selecciona tu idioma (Español).

Llena los datos del sitio: Título (ej. El Blog de Edwind en Kali), crea tu usuario administrador y tu contraseña.

Haz clic en "Instalar WordPress".

¡Toma una captura de pantalla de tu navegador Firefox en Kali mostrando la instalación exitosa para tu práctica!

🛠️ Plan de Contingencia (Troubleshooting en Kali)
🛡️ Si la página dice "Error establishing a database connection":

Causa: El contenedor de WordPress es más rápido arrancando que el de MySQL.

Solución: Solo espera 1 minuto y recarga la página (F5).

🛡️ Si el comando docker-compose da error de "command not found":

Causa: En las versiones más nuevas de Kali (2023+), Docker cambió la forma en que integra Compose.

Solución: En lugar de escribir docker-compose up -d (con guion), prueba escribiendo docker compose up -d (con espacio).

🛡️ Si te da un error de "Port 8080 is already in use":

Causa: Kali suele tener muchas herramientas de seguridad corriendo (como Burp Suite) que a veces ocupan el puerto 8080.

Solución: Abre el archivo con nano docker-compose.yml, busca la línea ports: - "8080:80" y cámbiala por otro puerto, por ejemplo - "8085:80". Guarda (Ctrl+O, Enter, Ctrl+X) y vuelve a desplegar.
