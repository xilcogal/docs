# Docker Quick Start

por Xose Eijo 02.12.2020

## Instalación de Docker

Disponemos de distintas distribuciones de Docker en función del sistema operativo. En este manual nos centraremos en Linux, ya que la distribución para Windows, conocida como Docker Desktop, sólo sirve para desarrollo, para despliegues complejos tendríamos que irnos a las versiones Enterprise. Además la versión de Windows requiere el doble de recursos hardware para su funcionamiento. En cualquier caso los comando que veremos en este manual, son los mismo que se utilizan en Docker Desktop.

El primer paso es conectarnos a la máquina Linux en la que vamos a realizar la instalación de Docker Community. En el manual utilizaremos una distribución Redhat (Fedora, CentOS) Minimal, lo que nos permitirá centrar los recursos de la máquina en la ejecución del servicio Docker.

Siempre que vayamos a realizar una instalación, lo primero es actualizar el sistema.

```bash
$ yum -y update
```

Nos aseguramos de que tenemos el paquete de utilidades de red.

```bash
$ yum intall -y net-tools
```

Paramos el firewall y SELinux para evitar problemas de red con Docker. **Evidentemente, esto no debemos hacerlo nunca en un entorno de producción**.

```bash
$ systemctl stop firewalld # paramos el firewall
$ systemctl disable firewalld # lo eliminamos del inicio automático
```

Ahora SELinux:

```bash
$ sestatus # comprobamos el estado de SELinux
$ setenforce 0 # lo paramos
$ vi /etc/selinux/config # editamos el fichero y los ponemos Disabled

# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=disabled
# SELINUXTYPE= can take one of these three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

Ya podemos proceder con la instalación.

Eliminamos los paquetes que pudieran existir de una instalación previa o errónea.

```bash
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

Instalamos el paquete de utilidades de yum.

```bash
$ sudo yum install -y yum-utils
```

Configuramos el repositorio oficial de Docker Community.

```bash
$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

Instalamos Docker,

```bash
$ sudo yum install docker-ce docker-ce-cli containerd.io
```

Una vez finalizada la instalación  arrancamos el servicio de Docker:

```bash
$ systemctl start docker
```

Ahora lo activamos para que se inicie de forma automática en el proceso de arranque de la máquina.

```bash
$ systemctl enable docker
```

Cuando queramos parar o reiniciar el servicio podemos utilizar los comandos:

```bash
$ systemctl stop docker # para el servicio
$ systemctl restart docker # reinicia el servicio
$ systemctl status # info. del estado actual del servicio
```

Por último comprobamos la versión instalada.

```bash
$ docker --version # version de docker instalada
$ docker --info # información adicional sobre docker
```

## Consola de comando de Docker

Antes de adentrarnos en las funcionalidades de Docker, es imprescindible conocer sus consola de comandos y también el comando que utilizaremos con más frecuencia en nuestros primeros pasos, que no es otro que `--help`.

Lo primero que tenemos que saber de la CLI de Docker, es que tiene dos niéveles de comandos. En el primer nivel, nos encontraremos con comandos de servicio y alias, que tienen la sintaxis:

```bash
$ docker <command> [options]
```

en el segundo nivel, nos encontraremos con los comandos de componente: *image*, *volume*, *network*, *container*. Estos comandos tienen la sintaxis:

```bash
$ docker <component> <command> [options]
```

Y ya por último la ayuda; si tenemos dos niveles de comandos tendremos cuatro niveles de ayuda.

```bash
$ docker --help # lista de comandos de servicio
$ docker <command> --help # ayuda comando de servicio
$ docker <component> --help # lista de comandos de componente
$ docker <component> <command> --help # ayuda comando de componente
```

## Gestión de imágenes

Una imagen de Docker es la definición de cada una de las capas que forman un contenedor. Esta estructura de capas, en forma de cebolla, parte de una capa básica formada por las librerías necesarias para arrancar un contenedor sobre un sistema operativo Linux o Windows, lo que determinará el tipo de contenedor. A partir de aquí, se van incluyendo sucesivas capas que añaden funcionalidad a la imagen. Cada capa es única, es decir, si dos imágenes comparten las mismas capas, para Docker serán la misma.

¿Y cómo podemos conseguir una imagen? Podemos obtener imágenes para distintas aplicaciones del repositorio oficial de Docker, [DockerHub](https://hub.docker.com/). ¿Y cómo lo hacemos? Las descargamos a través del comando `pull`.  Una imagen muy utilizada para el testeo es `busybox`. Veremos más sobre esta imagen a lo largo del  manual. 

Además de la imágenes disponibles en DockerHub, podemos crear nuevas imágenes personalizadas, adaptándolas a nuestras necesidades. Volveremos sobre este tema en el apartado de creación de imágenes.

El primer paso para trabajar con imágenes es descargar la primera.

```bash
$ docker pull busybox
Using default tag: latest
```

cuando descargamos una imagen, lo primero que hace Docker es comprobar si ya está disponible y si coincide con la versión remota en DockerHub, si no cumple una de estas dos condiciones, descargará la imagen de DockerHub con la etiqueta `latest`.  Cuando no indicamos una etiqueta, ésta será la etiqueta por defecto. La etiqueta `latest` se asocia con la última versión de la imagen que estamos descargando. Si queremos una versión en concreto debemos indicar de forma explicita la etiqueta.

```bash
$ docker pull busybox:1.2.0
```

Para ver los comandos sobre imágenes disponibles en Docker, utilizamos el comando `--help`.

```bash
$ docker image --help
```

Veamos ahora algunos de los comando más relevantes de la gestión de imágenes:

| Comando | Alias         | Descripción                                       |
| ------- | ------------- | ------------------------------------------------- |
| build   | -             | Crea una imagen a partir de Dockerfile.           |
| inspect | -             | Muestra información detallada sobre la imagen.    |
| ls      | docker images | Muestra las imágenes disponibles.                 |
| prune   | -             | Elimina las imágenes que no se están usando.      |
| pull    | -             | Baja una imagen al repositorio local.             |
| push    | -             | Sube una imagen a un repositorio remoto.          |
| rm      | docker rmi    | Elimina una o más imágenes.                       |
| tag     | -             | Crea una imagen etiquetada a partir de otra dada. |

## Gestión de contenedores

Ya hemos visto que es una imagen, y en este punto definimos lo que es un contenedor como la instancia en ejecución de una imagen. Mientras el contenedor se encuentre en ejecución podremos interactuar con el a través de la CLI de Docker o de lo puertos que hayamos mapeado entre el contenedor y el host anfitrión.

Docker nos permite arrancar un contenedor en dos modos:

- **Modo interactivo**: en este modo el contenedor se mantiene activo mientras se ejecuta el comando que le incidamos en el arranque.
- **Modo desatendido**: en este modo el contenedor se ejecuta en segundo plano.

Primero veamos el modo iterativo, y para hacerlo vamos a utilizar la imagen busybox. Las sintaxis general del comando es:

```bash
$ docker run -it <image> <image_command>
```

Aplicado a busybox

```bash
$ docker run -it busybox bash
```

## Gestión de volúmenes



## Gestión de redes

## Creación de imágenes

## Docker Compose

## Docker Swarm

