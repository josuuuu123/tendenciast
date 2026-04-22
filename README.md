# Informe de Práctica de Laboratorio

---

## 1. Título

**Implementación y gestión de contenedores Docker con servidor web Nginx**

---

## 2. Tiempo de Duración

**Duración estimada:** 90 minutos

---

## 3. Fundamentos

Docker es una plataforma de código abierto que permite desarrollar, empaquetar y ejecutar aplicaciones dentro de **contenedores**. Un contenedor es una unidad estándar de software que agrupa el código de una aplicación junto con todas sus dependencias, de manera que la aplicación se ejecute de forma rápida y confiable en cualquier entorno.

A diferencia de las máquinas virtuales tradicionales, los contenedores no incluyen un sistema operativo completo, sino que comparten el kernel del sistema operativo anfitrión. Esto los hace mucho más ligeros, portables y eficientes en el uso de recursos. Docker utiliza el concepto de **imágenes**, que son plantillas de solo lectura usadas para crear contenedores. Una imagen puede basarse en otra imagen, añadiendo capas de personalización.

El flujo de trabajo básico de Docker gira en torno a tres conceptos fundamentales:

- **Dockerfile:** Archivo de texto que contiene las instrucciones para construir una imagen Docker.
- **Imagen:** Plantilla inmutable generada a partir del Dockerfile.
- **Contenedor:** Instancia en ejecución de una imagen.

![Arquitectura Docker](https://docs.docker.com/get-started/images/docker-architecture.webp)
*Figura 3-1. Arquitectura general de Docker (cliente, daemon y registro).*

**Nginx** es un servidor web de alto rendimiento, también utilizado como proxy inverso, balanceador de carga y servidor de caché HTTP. Su arquitectura basada en eventos le permite manejar miles de conexiones simultáneas con un consumo mínimo de memoria. En el contexto de Docker, Nginx es frecuentemente desplegado como un contenedor que sirve contenido estático o actúa como punto de entrada para microservicios.

La comunicación entre contenedores se gestiona mediante **redes Docker**. Por defecto, Docker crea una red tipo `bridge` que permite que los contenedores en el mismo host se comuniquen entre sí. Los **volúmenes** de Docker permiten persistir datos generados por los contenedores, independientemente del ciclo de vida de estos.

Docker Hub es el registro público oficial donde se almacenan y distribuyen imágenes de contenedores. A través de él es posible descargar imágenes oficiales como `nginx`, `ubuntu`, `mysql`, entre otras, sin necesidad de construirlas desde cero.

![Ciclo de vida de un contenedor](https://miro.medium.com/v2/resize:fit:1400/1*vca4e-SibPNB8WvTRfToiA.png)
*Figura 3-2. Ciclo de vida de un contenedor Docker.*

En conjunto, estas tecnologías permiten crear entornos reproducibles y escalables, ideales para el desarrollo moderno de software bajo metodologías DevOps y arquitecturas de microservicios.

---

## 4. Conocimientos Previos

Para realizar esta práctica el estudiante necesita tener claros los siguientes temas:

- Comandos básicos de Linux (navegación de directorios, gestión de archivos, permisos).
- Manejo del navegador web para verificar servicios HTTP.
- Conceptos básicos de redes (puertos, protocolo HTTP/HTTPS, dirección IP).
- Uso de terminal o consola de comandos.
- Nociones básicas sobre servidores web.
- Estructura de archivos de configuración (formato texto plano).

---

## 5. Objetivos a Alcanzar

- Implementar contenedores con la imagen oficial de Nginx en un entorno Docker.
- Manipular archivos de configuración de Nginx dentro de un contenedor en ejecución.
- Mapear puertos del contenedor al sistema anfitrión para exponer servicios web.
- Gestionar el ciclo de vida de contenedores (crear, iniciar, detener, eliminar).
- Montar volúmenes para servir contenido HTML estático personalizado desde el host.

---

## 6. Equipo Necesario

- Computador con sistema operativo Windows 10/11, Linux (Ubuntu 20.04+) o macOS 12+.
- Cuenta en [Play with Docker](https://labs.play-with-docker.com/) (alternativa online, no requiere instalación).
- Docker Engine v24.0 o superior / Docker Desktop v4.20 o superior.
- Navegador web (Google Chrome, Firefox o Edge).
- Conexión a internet estable.
- Terminal o PowerShell con permisos de administrador.

---

## 7. Material de Apoyo

- [Documentación oficial de Docker](https://docs.docker.com/)
- [Documentación oficial de Nginx](https://nginx.org/en/docs/)
- Guía de asignatura — Módulo 3: Contenedores y virtualización.
- [Cheat Sheet de comandos Linux](https://cheatography.com/davechild/cheat-sheets/linux-command-line/)
- [Docker Hub — Imagen oficial de Nginx](https://hub.docker.com/_/nginx)
- [Cheat Sheet de Docker](https://docs.docker.com/get-started/docker_cheatsheet.pdf)

---

## 8. Procedimiento

**Paso 1: Verificar la instalación de Docker**

Abrir la terminal y ejecutar el siguiente comando para comprobar que Docker está correctamente instalado:

```bash
docker --version
```

![Verificación de versión de Docker](https://i.imgur.com/placeholder1.png)
*Figura 8-1. Verificación de la versión de Docker instalada.*

---

**Paso 2: Descargar la imagen oficial de Nginx**

Ejecutar el siguiente comando para obtener la imagen de Nginx desde Docker Hub:

```bash
docker pull nginx
```

![Descarga de imagen Nginx](https://i.imgur.com/placeholder2.png)
*Figura 8-2. Descarga de la imagen Nginx desde Docker Hub.*

---

**Paso 3: Crear y ejecutar un contenedor Nginx**

Iniciar un contenedor exponiendo el puerto 8080 del host al puerto 80 del contenedor:

```bash
docker run -d --name mi-nginx -p 8080:80 nginx
```

| Parámetro | Descripción |
|-----------|-------------|
| `-d` | Ejecutar en segundo plano (detached) |
| `--name mi-nginx` | Asignar nombre al contenedor |
| `-p 8080:80` | Mapear puerto 8080 del host → 80 del contenedor |
| `nginx` | Imagen base a utilizar |

![Contenedor Nginx en ejecución](https://i.imgur.com/placeholder3.png)
*Figura 8-3. Contenedor Nginx creado y en ejecución.*

---

**Paso 4: Verificar el funcionamiento desde el navegador**

Abrir el navegador e ingresar la siguiente URL:

```
http://localhost:8080
```

Deberá visualizarse la página de bienvenida predeterminada de Nginx.

![Página de bienvenida Nginx](https://i.imgur.com/placeholder4.png)
*Figura 8-4. Página de bienvenida de Nginx visualizada en el navegador.*

---

**Paso 5: Crear contenido HTML personalizado**

En el sistema anfitrión, crear una carpeta y un archivo `index.html`:

```bash
mkdir ~/mi-sitio
echo "<h1>Hola desde Docker + Nginx</h1>" > ~/mi-sitio/index.html
```

---

**Paso 6: Montar un volumen con el contenido personalizado**

Detener y eliminar el contenedor anterior, luego crear uno nuevo con el volumen montado:

```bash
docker stop mi-nginx
docker rm mi-nginx
docker run -d --name mi-nginx -p 8080:80 \
  -v ~/mi-sitio:/usr/share/nginx/html nginx
```

![Contenedor con volumen montado](https://i.imgur.com/placeholder5.png)
*Figura 8-5. Contenedor Nginx con volumen de contenido personalizado montado.*

---

**Paso 7: Verificar el contenido personalizado en el navegador**

Recargar `http://localhost:8080` para visualizar el contenido HTML personalizado servido por Nginx.

![Contenido personalizado en el navegador](https://i.imgur.com/placeholder6.png)
*Figura 8-6. Página HTML personalizada servida correctamente por el contenedor.*

---

**Paso 8: Listar y gestionar contenedores activos**

```bash
docker ps           # Ver contenedores en ejecución
docker ps -a        # Ver todos los contenedores (incluidos detenidos)
docker stop mi-nginx    # Detener el contenedor
docker start mi-nginx   # Iniciar el contenedor nuevamente
docker rm mi-nginx      # Eliminar el contenedor
```

![Gestión de contenedores](https://i.imgur.com/placeholder7.png)
*Figura 8-7. Listado y gestión de contenedores desde la terminal.*

---

## 9. Resultados Esperados

Al finalizar la práctica, el estudiante habrá logrado:

- Desplegar exitosamente un contenedor Nginx accesible desde el navegador web en el puerto configurado.
- Servir contenido HTML estático personalizado mediante el uso de volúmenes Docker.
- Comprender el mapeo de puertos entre el contenedor y el sistema anfitrión.
- Dominar los comandos básicos del ciclo de vida de un contenedor.

**Captura del resultado final:**
<img width="580" height="366" alt="image" src="https://github.com/user-attachments/assets/28ab7e8f-360f-4d50-93ae-05da343a53c6" />
<img width="825" height="171" alt="image" src="https://github.com/user-attachments/assets/b75722e7-c7f3-4085-b0fa-d60af45e3aa6" />
<img width="1362" height="672" alt="image" src="https://github.com/user-attachments/assets/af662fff-b535-45a4-8de3-2cfef47ef42d" />




## 10. Bibliografía

Docker Inc. (2024). *Docker documentation: Get started*. Docker Docs. https://docs.docker.com/get-started/

Nginx Inc. (2024). *Nginx beginner's guide*. Nginx. https://nginx.org/en/docs/beginners_guide.html

Turnbull, J. (2014). *The Docker book: Containerization is the new virtualization*. James Turnbull.

Mouat, A. (2015). *Using Docker: Developing and deploying software with containers*. O'Reilly Media.

Poulton, N. (2023). *Docker deep dive* (2nd ed.). Independently published. https://nigelpoulton.com/books/

---

*Informe elaborado para la asignatura de Sistemas Distribuidos — Período 2024-2025*
