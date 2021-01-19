# Asistente Virtual con Rasa

## Instalación

### Instalación en Ubuntu Linux

Antes de comenzar con la instalación de **Rasa**, nos aseguramos de que el sistema operativo está actualizado y de que dispone de las librerías necesarias para su instalación.

Actualizando el sistema:

```bash
$ sudo apt-get update
$ sudo apt-get upgrade
```

Comprobamos que tenemos instaladas las últimas versiones de `python3` y `pip3`.

```bash
$ python3 --version
$ pip3 --version
# si no tenemos instalados los paquetes de desarrollo y pip de python3
$ sudo apt-get install python3-dev python3-pip 
```

Para trabajar con proyectos Python, se recomienda usar entornos de desarrollo aislados. `virtualenv` y `virtualenvwrapper` son dos herramientas que nos permiten crear estos tipos de entorno.

Si no tenemos `virtualenv` podemos instalarlo a través de apt	

```bash
$ sudo apt-get install python3-venv
```

Antes de instalar **Rasa** vamos a instalar algunas herramientas que nos ayudarán en el desarrollo del asistente.

Para llevar control de cambios de nuestro proyecto y compartir nuestro trabajo vamos a instalar el repositorio de código fuente `Git`.

```bash
$ git --version # comprobamos si ya lo tenemos instalado
$ sudo apt-get install git # instalamos Git si no lo tenemos
```

Una vez instalado, configuramos nuestro usuario y correo.

```bash
$ git config --global user.name "mi-nombre"
$ git config --global user.email "mi-correo@gmail.com"
$ git --list # nos muestra la configuración
$ vi ~/.gitconfig # vemos el fichero de configuración de Git
```

Podemos acceder a Git vía https o ssh, cualquiera de las dos formas es válida. En esta guía vamos a utilizar ssh y para ello vamos a necesitar generar un certificado, si no disponemos de él.

```bash
$ ssh-keygen -t ed25519 -C "mi-correo@gmail.com" # generamos el certificado.
$ ls ~/.ssh # en este directorio encontraremos las claves privada y publica que se han generado.
$ eval "(ssh-agent -s)" # arrancamos el agente ssh en segundo plano
$ ssh-add ~/.ssh/id_ed25519 # añadimos el certificado al agente
```

Ya tenemos instalado el certificado en el sistema, ahora tenemos que crear una clave SSH en GitHub utilizando la clave pública de nuestro certificado. Para copiarla podemos utilizar la herramienta `xclip`.

```bash
$ sudo apt-get install xclip # si no la tenemos instalada la instalamos con apt
$ sudo xclip -selection clipboard < ~/.ssh/id_ed25519.pub # copiamos al clipboard
```

Con la clave en el clipboard vamos a `GitHub>Profile>Settings>SSH & GPG keys>New SSH key` y añadimos nuestra clave pública. Desde este momento ya podemos trabajar con los repositorios de GitHub sin la necesidad de autenticar nos constantemente.

De forma opcional podemos instalar `VSCode` como IDE de desarrollo. Para poder hacerlo, nuestro sistema debe disponer de escritorio. Utilizaremos la herramienta de instalación de aplicaciones de `Gnome`. 

Llegamos al paso final, la instalación de **Rasa**.

```bash
$ python3 -m venv ./venv # creamos el entorno virtual de python
$ source ./venv/bin/activate # activamos el entorno
(venv) $ pip3 install -U pip # actualizamos pip
(venv) $ pip3 install rasa # instalamos rasa
```



## Anexo I

| Referencia                                   | URL                                                          |
| -------------------------------------------- | ------------------------------------------------------------ |
| Guía de instalación de Rasa.                 | [Step-by-step Installation Guide](https://rasa.com/docs/rasa/installation/)                     |
| Guía de instalación de GitHub. Conexión SSH. | [Connecting to GitHub with SSH](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh) |
|                                              |                                                              |
|                                              |                                                              |
|                                              |                                                              |
|                                              |                                                              |
|                                              |                                                              |
|                                              |                                                              |
|                                              |                                                              |

