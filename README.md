# CommitNube
Integrantes: Álvaro Quiroga, Luis Quiroga, Alexander Medina, Alexander Valdes.
Introducción:
El presente documento describe la infraestructura tecnológica utilizada para ejecutar y desplegar la aplicación. Se detallan los componentes principales de la solución, incluyendo Dockerfile, Docker-Compose, GitHub Actions, los Secrets de GitHub, la base de datos y la máquina virtual alojada en Microsoft Azure.

# Dockerfile
El archivo Dockerfile contiene las instrucciones necesarias para construir una imagen Docker de la aplicación.
Una imagen Docker es una plantilla que incluye los elementos necesarios para ejecutar un software, como el entorno de ejecución, las dependencias y la configuración inicial.
Su principal objetivo es estandarizar el entorno de ejecución, evitando diferencias entre los equipos de desarrollo y el servidor de producción.
Entre sus principales funciones se encuentran:
Definir la imagen base del entorno.
Instalar las dependencias necesarias.
Copiar los archivos del proyecto.
Configurar el directorio de trabajo.
Exponer el puerto utilizado por la aplicación.
Definir el comando de inicio del servicio.
El archivo permite construir una imagen reproducible que posteriormente puede ser ejecutada como un contenedor.

# docker-compose
el archivo docker-compose.yml permite definir y administrar los diferentes servicios que necesita la aplicación mediante una configuración centralizada.
En lugar de ejecutar cada contenedor de manera independiente, Docker Compose permite iniciar, detener y administrar varios servicios de forma coordinada.
Sus principales responsabilidades son:
Definir los servicios de la aplicación.
Configurar las imágenes o instrucciones de construcción.
Establecer los puertos de comunicación.
Configurar las variables de entorno.
Definir redes de comunicación entre contenedores.
Configurar volúmenes para mantener información persistente.
Por ejemplo, una aplicación puede utilizar un contenedor para el backend y otro para la base de datos.
Para iniciar los servicios se utiliza:
docker compose up -d
Para reconstruir las imágenes y actualizar los servicios:
docker compose up -d --build
Para detener los servicios:
docker compose down

# maquina virtual
La infraestructura de ejecución se encuentra alojada en Microsoft Azure, utilizando una máquina virtual con sistema operativo Ubuntu Linux.
Esta máquina funciona como servidor de la aplicación y proporciona los recursos necesarios para ejecutar Docker y administrar los servicios desplegados.

La administración remota de la máquina virtual se realiza mediante SSH (Secure Shell), un protocolo que permite establecer conexiones cifradas entre equipos.
La autenticación se realiza utilizando una clave privada con extensión .pem.
El comando de conexión utilizado desde Windows es:
ssh -i "ruta/azure_key.pem" usuario@IP_PUBLICA
Este mecanismo permite administrar el servidor sin necesidad de acceder directamente a su interfaz gráfica.


# deploy/secrets
El workflow utiliza un conjunto de instrucciones YAML que definen:
El evento que activa el despliegue.
El entorno donde se ejecuta el proceso.
Las credenciales necesarias para conectarse al servidor.
Los comandos que se ejecutan remotamente mediante SSH.
La actualización y reconstrucción de los servicios Docker.
El despliegue se ejecuta cuando un Pull Request es fusionado en la rama principal main, siempre que se cumplan las condiciones de aprobación establecidas en las reglas de protección del repositorio.

GitHub Secrets permite almacenar información confidencial utilizada durante los procesos automatizados.
Su finalidad es evitar que credenciales, claves privadas y datos sensibles queden expuestos directamente en el código fuente del proyecto.
Para este sistema se utilizan los siguientes Secrets:

AZURE_HOST Dirección IP pública de la máquina virtual.

AZURE_USER Usuario de acceso SSH al servidor.

AZURE_SSH_KEY Clave privada utilizada para autenticar la conexión SSH.

# Flujo general
El proceso completo de actualización funciona de la siguiente manera:
El desarrollador modifica el código fuente.
Los cambios se suben a GitHub.
Se genera un Pull Request.
El equipo revisa y aprueba los cambios.
Se fusiona el Pull Request en main.
GitHub Actions inicia el proceso de despliegue.
Se establece una conexión SSH con Azure.
Se actualiza el repositorio en Ubuntu.
Docker Compose reconstruye y ejecuta los servicios.
La aplicación queda actualizada en el servidor.
