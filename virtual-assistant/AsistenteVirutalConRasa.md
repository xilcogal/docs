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
# opcionalmente podmeos instalar git flow
$ sudo apt-get install git-flow
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
(venv) $ pip3 list # paquetes instalados en el enviroment
```

Esta es la forma de instalar la última versión de Rasa. En esta guía utilizaremos una versión anterior; más adelante veremos como instalarla y como configurar un entorno virtual de python para esta versión.

### Gestión de versiones diferentes de Python

Como vimos en el punto anterior, es recomendable crear un entorno virtual que para el desarrollos de proyectos Python; esto nos permite instalar las dependencias de cada proyecto aisladas sin que puedan generar conflictos con otros proyectos. Pero además de las dependencias, nos puede generar conflicto la versión de Python que estamos usando. Una herramienta que nos va a permitir tener distintas versiones instaladas y además utilizar `virtualenv` como como virtualizador de entornos es `penv`.

La forma más sencilla de instalar `pyenv` es con su instalador automático. Para utilizarlo necesitaremos tener `curl` instalado.

```bash
$ sudo apt-get install -y build-essential libssl-dev zlib1g-dev libbz2-dev \
	libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
	xz-utils tk-dev libffi-dev liblzma-dev python-openssl git # comprobamos que tenemos todas las dependencias de instalación
$ sudo apt-get install curl # si no tenemos instalado curl en nuestro equipo
$ curl https://pyenv.run | bash # decargamos el instalable y lo ejecutamos.
# o
# $ curl -L https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer | bash # es equivalente al anterior
$ exec $SHELL # reiniciamos el shell
```

Una vez instalado tan sólo nos queda ver como utilizarlo. Lo primero es instalar una nueva versión de Python, por ejemplo la 3.7.6.

```bash
$ pyenv install 3.7.6 # instalamos la versión 3.7.6 de python
$ pyenv virtualenv 3.7.6 venv376 # creamos el entorno venv376 para python 3.7.6
$ pyenv virtualenvs # muestra la lista de entornos disponibles
$ pyenv activate venv376 # activa el entorno
$ pyenv deactivate # desactiva el entorno
$ pyenv uninstall venv376 # elimina el entorno
```



## Crear un nuevo proyecto

### Linea de comandos

Antes de crear un proyecto nuevo, es imprescindible conocer los comandos básico del CLI de Rasa; empecemos, son pocos.

| Comando             | Descripción                                                  |
| ------------------- | ------------------------------------------------------------ |
| rasa init           | Crea un nuevo proyecto de Rasa con un bot emocional como ejemplo. |
| rasa train          | Entrena el modelo de nuestro asistente utilizando nuestros datos de entrenamiento NLU y nuestras historias. |
| rasa interactive    | Inicia una sesión interactiva de aprendizaje  con nuestro asistente que podremos exportar como datos de entrenamiento. |
| rasa shell          | Arranca una linea de comando para que podamos hablar con nuestro asistente. Utiliza el último modelo generado. |
| rasa run            | Arranca el servidor de Rasa con el último modelo entrenado.  |
| rasa run actions    | Arranca el Action server de Rasa.                            |
| rasa visualize      | Realiza una representación visual de nuestra historias y las muestra en un navegador. |
| rasa test           | Prueba el último modelo entrenado contra los archivos que empiecen por test_ de nuestro proyecto. |
| rasa data split nlu | Realiza una partición 80/20 de nuestros datos de entrenamiento. |
| rasa data convert   | Convierte los datos de entrenamiento a distintos formatos.   |
| rasa data validate  | Valida la configuración y los datos de entrenamiento de nuestro proyecto para comprobar inconsistencias. |
| rasa export         | Exporta conversaciones del almacenamiento del Tracker a un event broker. |
| rasa x              | Arranca rasa X en modo local.                                |
| rasa -h             | Muestra la ayuda de los comando disponibles.                 |

A menos que se selecciones mediante opciones de cada comando el modelo a utilizar, siempre se utiliza el último modelo entrenado.

### Metodología

Podríamos construir un asistente persona improvisando, pero como con cualquier otro tipo de proyecto de desarrollo software necesitamos una metodología que nos guíe en este proceso.

Conversation-Driven Development (CDD) es un proceso que pone el foco en escuchar al usuario y trasladar estas percepciones a mejoras en nuestro asistente.

CDD es un proceso cíclico e iterativo donde se realizan seis actividades.

- Comparte (Share) el asistente con los usuario lo antes posible.
- Revisa (Review) las conversaciones.
  - Céntrate en los diálogos fuera de contexto y en los chitchats.
  - Busca la frustración del usuario al intentar comunicarse con el asistente.
- Etiqueta (Annotate) los diálogos y utilízalos como datos de entrenamiento.
- Prueba (Test) que el asistente se comporta como esperas.
- Sigue (Track) los fallos y mide su rendimiento.
- Fix (Corrige) corrige la conversaciones que fallan.

Una vez tentemos el proceso debemos completar la metodología, aplicando las mismas técnicas que en el desarrollo ágil de aplicaciones.

- Utiliza las mejores prácticas para el desarrollo de los  datos de entrenamiento.
- Entrega lo antes posible para que los usuario pueda probar el asistente.
- Desarrollo un pipeline CI/CD.

### Buenas practicas

#### Generando datos NLU

NLU (Natural Language Understanding) es el componente de Rasa que realiza:

- Clasificación de intenciones (¿Qué quiere hacer el usuario?).
- Extracción de entidades (¿Qué información nos da para hacerlo?).
- Recuperación de respuestas (¿Cuál es la respuesta a su petición?).

Un ejemplo de procesado de intención es:

```json
usuario > 
{ 
    text: "Búscame un restaurante chino cerca del centro."
}
# resultado del procesado NLU
NLU >
{
  "intent": "search_restaurant",
  "entities": {
    "cuisine": "chino",
    "location": "centro"
  }
}
```

Construir modelos NLU es difícil por lo que debemos seguir una serie de buenas prácticas que nos guíen en el desarrollo.

- Conversation-Driven Development para NLU. 
  - Recopila datos reales y eso significa utilizar los diálogos de los usuarios como datos de entrenamiento.
  - Comparte el asistente con los usuario de Test lo antes posible
  
- Evita la confusión entre intenciones. Prioriza el uso de entidades e intenciones genéricas sobre intenciones específicas.

- Mejora el reconocimiento de entidades.
  - Usa extractores de entidades pre-entrenados como:
    - SpacyEntityExtractor.
    - DucklingEntityExtractor.
   - Usa extractores de expresiones regulares para datos estructurados.
   - Usa tablas de búsquedas para entidades con valores finitos.
   - Usa sinónimos para normalizar entidades que pueden expresarse con distintas palabras.
  
- Gestiona los casos excepcionales.

  - Gestiona los errores de escritura:

    - Añádelos a los datos de entrenamiento.
    - Utiliza la opción char_wb de CountVectorsFeaturizer, que crea características a nivel de carácter, lo que permite identificar palabras sin todos sus caracteres.

    - ```markdown
      pipeline:
      # <other components>
      - name: CountVectorsFeaturizer
        analyze: char_wb
        min_ngram: 1
        max_ngram: 4
      # <other components>
      ```

    - Define una intención fuera de alcance **out_of_scope**, que utilizará el asistente cuando no pueda identificar una intención de usuario y te dará la oportunidad de redirigir el diálogo "No he entendido la pregunta".

#### Diálogos de entrenamiento

Escribir buenos dialogo a través de historias y reglas permiten al asistente seguir correctamente caminos esperados para la conversación y utilizar la generalización para resolver aquellos que nos has podido predecir.

Cuando diseñas un asistente debes manejar dos grupos de dialogos:

- Diálogos de camino feliz (happy path): son los diálogos usuario-asistente esperados.
- Diálogos de camino infeliz (unhappy path): son los diálogos usuario-asistente que divergen de los happy path.

La recomendación pasa por utilizar los diálogos reales para ambos caminos como entrenamiento del modelo del asistente.

#### Cuando escribir historias o reglas 



Las reglas son un tipo de datos de entrenamiento que utiliza el gestor del dialogo para manejar partes del dialogo que deben seguir siempre el mismo path. Por ejemplo: los saludos o las despedidas.

Por lo general las usaremos para:

- Interacciones de un turno: son preguntas y respuestas fuera de contexto.
- Fallbacks: se usan en combinación con FallbackClassifier para gestionar clasificaciones de intenciones con valores de confiabilidad por debajo del umbral establecido.
- Formularios: la activación de formularios y su entrega con frecuencia siguen un path fijo. Podemos utilizar reglas para manejar entradas inesperadas.

```yaml
rules:
- rule: Greeting Rule
  steps:
  - intent: greet
  - action: utter_greet
```

Para conversación con más de un turno utilizaremos siempre historias, puesto que las reglan no sufrirán un proceso de generalización en el entrenamiento.

#### Gestión del flujo de la conversación

##### Cuando usar la presencia de Slots en al conversación

Un `slot` no es mas que una variable en memoria donde guardamos información relevante para el dialogo. Podemos hacer que la presencia de un determinado valor en un slot tengo influencia en el camino que sigue la conversación. Para hacer esto podemos utilizar los slots de tipo boolean.

```yaml
stories:
- story: Welcome message, premium user
  steps:
   - intent: greet
   - action: action_check_profile
   - slot_was_set:
     - premium_account: true
   - action: utter_welcome_premium

- story: Welcome message, basic user
  steps:
   - intent: greet
   - action: action_check_profile
   - slot_was_set:
     - premium_account: false
   - action: utter_welcome_basic
   - action: utter_ask_upgrade
```

Como vemos en el ejemplo, creamos dos caminos en función de si el usuario es premium o no.

Para definir si un slot influye o no en la conversación usamos la propiedad `influence_conversation` del slot, estableciendo su valor a true o false respectivamente. 

##### Implementación de lógica de ramas

Cuando usemos un slot para influenciar el dialogo, pero esta influencia solo afecta a la respuesta que se le va a dar al usuario, podemos encapsular esta lógica en una `custom action` y definir un único camino.Por ejemplo:

```yaml
stories:
- story: check for rain
  steps:
  - intent: check_for_rain
  - action: action_check_for_rain
```


```python
def run(self, dispatcher, tracker, domain):
    is_raining = check_rain()
    if is_raining:
        dispatcher.utter_message(template="utter_is_raining")
        dispatcher.utter_message(template="utter_bring_umbrella")
    else:
        dispatcher.utter_message(template="utter_not_raining")
        dispatcher.utter_message(template="utter_no_umbrella_needed")
    return []
```

##### Uso de expresiones OR y Checkpoints

Son elementos útiles para reducir el número de historias pero a su vez su uso abusivo puede hacer que los datos de entrenamiento se confusos y difíciles de entender.

###### Expresiones OR

Usamos expresiones `OR` cuando dos historias siguen el mismo camino a partir de un punto. Por ejemplo:

```yaml
stories:
- story: newsletter signup with OR
  steps:
  - intent: signup_newsletter
  - action: utter_ask_confirm_signup
  - or:
    - intent: affirm
    - intent: thanks
  - action: action_signup_newsletter
```

###### Checkpoints

Los checkpoints se utilizan para modularizar historias en bloques separados que se repiten frecuentemente. Por ejemplo, si queremos consultar el feedback al cliente en cada dialogo.

```yaml
stories:
- story: beginning of conversation
  steps:
  - intent: greet
  - action: utter_greet
  - intent: goodbye
  - action: utter_goodbye
  - checkpoint: ask_feedback

- story: user provides feedback
  steps:
  - checkpoint: ask_feedback
  - action: utter_ask_feedback
  - intent: inform
  - action: utter_thank_you
  - action: utter_anything_else

- story: user doesn't have feedback
  steps:
  - checkpoint: ask_feedback
  - action: utter_ask_feedback
  - intent: deny
  - action: utter_no_problem
  - action: utter_anything_else
```

##### Crear rupturas lógica en las historias

Cuando en una misma historia tengamos dialogos separados, por ejemplo, un cliente pierde la tarjeta de credito, revisa las últimas transacciones para detectar fraudes y finalmente solicita el reemplazo, es conveniente dejarlas como historias separadas, no es necesario crear una única historia que refleje el uso conjunto de las tres.

#### Gestionar cambios de contexto

En ocasiones los usuarios rompen los dialogos no respondiendo a las preguntas que le realiza el asistente, lo que provoca que se desvien del `happy path`. Estas alteraciones del diálogo deben ser gestionadas por nuestro asistente mediante cambios de contexto.

##### Cambio de contexto mediante reglas

Como vimos antes, las reglas nos permiten responder dialogos unidireccionales como són los saludos y las despedidas. Cuando tengamos otros dialogos de respuesta única, por ejemplo "cúanto dienero tengo disponible en mi cuenta", utilizaremos reglas para definirlos, de esta forma el usuario podrá usarlos dentro de cualquier otro dialogo sin roper la conversación. Por ejemplo:

```bash
usuario> Quiero pagar con mi tarjeta de credito
bot> Qué tarjeta quiere utilizar?
	Tarjeta A, Tarjeata B
usuario> Cuánto dinero tengo en la Tarjeta B? # Sale del contexto
bot> 10.000 €
usuario> La tarjeta B. # vuelve al contexto
bot> Que cantidad quiere trasferir? 
...
```

##### Cambio de contexto mediante historias

En ocasiones necesitaremos realizar un cambio de contexto a una historia mulidireccional. En estos casos tendremos que diseñar una historia que desactive el formulario actual y active el nuevo formulario. Está historia finalizará desactivando el nuevo formulario e indicando al usuario si debe continuar con el contexto anterior.

#### Gestionar ficheros de conversación

Es una buena práctica separar las los datos nlu, las historias y las reglas en ficheros distintos en función de los tipos de dialogos que soporta nuestro asistente. Podemos ver ejemplos de esta práctica en [rasa-demo](https://github.com/RasaHQ/rasa-demo).

#### Usar el aprendizaje interactivo

El aprendizaje interactivo permite a Rasa hablar con nuestro asistente construyendo nuevo dialos que pueden ser exportados en forma de historias y que serviran para entrenar y mejora el modelo.

Podemos utilizar Rasa X o la herramienta de linea de comandos rasa `interactive`, para realizar este tipo de entrenamiento.

## Patrones de conversación

### Chitchat y  FAQs

Lo chitchats (diálogos fuera de contexto) y los FAQs (Frecuent Ask Questions) son casos de uso en los que la respuesta al usuario es siempre la misma, no variando en función del estado del dialogo.

#### Definiendo el pipeline NLU

Para la gestión de Chitchats y FAQs necesitamos una política de gestión del dialogo basada en reglas. Esta política se llama `RulePolicy` y debemos incluirla en nuestro fichero de configuración.

```markdown
## config.yml
## ----------
policies:
# other policies
- name: RulePolicy
```

El siguiente paso es incluir `ResponseSelector` en nuestro pipeline NLU, este componente será el responsable de devolver la respuesta correcta. Además debe situarse al final del pipeline porque necesita `featurizers` para la clasificación intenciones y extracción de entidades.

```yaml
## config.yml
-------------
pipeline:
  - name: WhitespaceTokenizer
  - name: RegexFeaturizer
  - name: LexicalSyntacticFeaturizer
  - name: CountVectorsFeaturizer
  - name: CountVectorsFeaturizer
    analyzer: char_wb
    min_ngram: 1
    max_ngram: 4
  - name: DIETClassifier
    epochs: 100
  - name: EntitySynonymMapper
  - name: ResponseSelector
    epochs: 100
    retrieval_intent: faq
  - name: ResponseSelector
    epochs: 100
    retrieval_intent: chitchat
```

Si queremos tener modelos de respuesta separados para FAQs y Chitchats utilizaremos el componente por duplicado, indicando para que tipo de modelo se va a utilizar.

#### Definiendo las reglas

Ya tenemos configurada las politicas y el pipeline adecuados para nuestro FAQs o chitchat, ahora tenemos que definir las reglas. Si tenemos un FAQs de 100 preguntas, podríamos definir una regla para cada una de ellas, pero en realidad la regla es siempre la misma. Para evitar hacer esto podemos definir una regla genérica tanto para los FAQs como para los Chitchats.

```yaml
## rules.yml
rules:
  - rule: respond to FAQs
    steps:
    - intent: faq
    - action: utter_faq
  - rule: respond to chitchat
    steps:
    - intent: chitchat
    - action: utter_chitchat
```

Las acciones utter_faq y utter_chitchat serán utilizadas por ResponseSelector para predecir el mensaje de respuesta.

#### Datos de entrenamiento de la NLU

Para cada una de las preguntas de nuestro FAQs o Chitchat tenemos que generar ejemplos de entrenamiento.

```yaml
## nlu.yml
## -------
nlu:
  - intent: chitchat/ask_name
    examples: |
      - What is your name?
      - May I know your name?
      - What do people call you?
      - Do you have a name for yourself?
  - intent: chitchat/ask_weather
    examples: |
      - What's the weather like today?
      - Does it look sunny outside today?
      - Oh, do you mind checking the weather for me please?
      - I like sunny days in Berlin.
```

no debemos olvidarnos de incluir los intentos que hemos creado en el dominio.

```yaml
## domain.yml
## ----------
intents:
- chitchat
- faq
```

#### Definición de las respuestas

Aquí tendremos que incluir todas las respuestas a las intenciones declaradas.

```yaml
## domain.yml
## ----------
responses:
  utter_chitchat/ask_name:
  - image: "https://i.imgur.com/zTvA58i.jpeg"
    text: Hello, my name is Retrieval Bot.
  - text: I am called Retrieval Bot!
  utter_chitchat/ask_weather:
  - text: Oh, it does look sunny right now in Berlin.
    image: "https://i.imgur.com/vwv7aHN.png"
  - text: I am not sure of the whole week but I can see the sun is out today.
```

### Lógica de negocio

Otra aplicación de los asistentes virtuales es gestionar peticiones de lógica de negocio. El mecanismo utilizado son los formularios. Un ejemplo sencillo es la consulta de un un restaurante. En este dialogo el asistente debe recuerar la información necesaria del usuario para realizar la búsqueda. Podemos encontrar un bot sobre este ejemplo en [frombot](https://github.com/RasaHQ/rasa/tree/main/examples/formbot).

Los formularios funcionan requiriendo al usuario la información que necesitan y almacenandola en slots. Una vez los `slots` requeridos están presentes, el asistente cumple la pentición original del usuario.

#### Definición del formulario

El primer paso es definir el formulario, donde debemos definir el mapeo de slots y las repuestas que el asistente debe dar al usuario para requerir la información.

##### Mapeo de slots

En el ejemplo que comentamos al inicio del capítulo, la información que el asistente necesita para cumplir la petición es:

- Tipo de cocina.
- Numero de comensales.
- Dónde comeran: en el interior o en el exterior del restaurante.

```yaml
## domain.yml
## ----------
forms:
  restaurant_form:
    cuisine:
      - type: from_entity
        entity: cuisine
    num_people:
      - type: from_entity
        entity: number
    outdoor_seating:
      - type: from_intent
        intent: affirm
        value: true
      - type: from_intent
        intent: deny
        value	: false
```

En el mapeo definimos que slots son requeridos y cuales no y como deben ser cubiertos.

Cada slot de tipo `from _entity` debemos añadir el `entity` al dominio.

```yaml
## domain.yml
## ----------
entities:
  - cuisine
  - number
```

Entidades como número y fecha pueden extraerse con DucklingEntityExtractor. Para usarlo tenemos que añadirlo al pipeline NLU.

```yaml
## config.yml
## ----------
language: en
pipeline:
# other components
- DucklingEntityExtractor:
  dimensions: ["number"]
```

El `slot` `outdoor_seating` se cubre en base a un intento , que puede ser `affirm` (true) o `deny` (false).

Debemos añadir los slots a nuestro dominio teniendo en cuenta que los slots asociados a un formulario no deberían tenerse en cuenta en la conversación, por lo que deberemos establecer la propiedad `influence_conversation` a `false`.

```yaml
## domain.yml
## ----------
slots:
  cuisine:
    type: text
    auto_fill: false
    influence_conversation: false
  num_people:
    type: float
    auto_fill: false
    influence_conversation: false
  outdoor_seating:
    type: text
    auto_fill: false
    influence_conversation: false
```

##### Validación de slots 

La validación de slots se realiza en las `custom actions`.

// TODO

##### Solicitud de Slots

Para definir las preguntas que el asistente utilizará para requerir la información al usuario, definimos en la sección `reponses` respuestas del tipo `utter_ask_{slotname}`.

```yaml
## domain.yml
## ----------
responses:
  utter_ask_cuisine:
    - text: "What cuisine?"
  utter_ask_num_people:
    - text: "How many people?"
  utter_ask_outdoor_seating:
    - text: "Do you want to sit outside?"
```

#### Diseño del las reglas

##### Configuración

Dado que el happy path de un formulario se debe definir como una regla, necesitamos activar en la configuración la política de reglas.

```yaml
## config.yml
## ----------
policies:
  - name: RulePolicy
```

##### Diseño de las reglas

Un formulario sólo necesita dos reglas:

* regla de activación: determina cuando se inicia el formulario.
* regla de entrega: determina cuando el formulario ha sido completado.

```yaml
## reules.yml
## ----------
rules:
  - rule: activate restaurant form
    steps:
      - intent: request_restaurant   # intent that triggers form activation
      - action: restaurant_form      # run the form
      - active_loop: restaurant_form # this form is active

  - rule: submit form
    condition:
    - active_loop: restaurant_form   # this form must be active
    steps:
      - action: restaurant_form      # run the form
      - active_loop: null            # the form is no longer active because it has been filled
      - action: utter_submit         # action to take after the form is complete
      - action: utter_slots_values   # action to take after the form is complete
```

Separando las reglas, el formulario funcionará incluso cuando el usuario lo interrumpa con un comportamiento inesperado o `chitchat`.

#### Entrenamiento NLU

Debemos proporcionar eventos tanto para la activación como para la provisión de las entidades.

##### Activación del formulario

En las reglas definimos la intención `request_restaurant` para la activación del `formulario`. Debemos proveer los ejemplos para su entrenamiento.

```yaml
## nlu.yml
## -------
nlu:
- intent: request_restaurant
  examples: |
    - im looking for a restaurant
    - can i get [swedish](cuisine) food in any area
    - a restaurant that serves [caribbean](cuisine) food
    - id like a restaurant
    - im looking for a restaurant that serves [mediterranean](cuisine) food
    - can i find a restaurant that serves [chinese](cuisine)
```

Los slots de tipo `from_entitty` podemos proveerlos en el propio intento de activación. En nuestro caso `cuisine` es uno de ellos.

##### Intenciones para cubrir el formulario

En nuestro ejemplo tenemos tres slots:

* cuisine.
* number.
* outdoor_seating.

El slot `outdoor_seating` lo hemos mapeado a dos intenciones por lo que necesitaremos proveer ejemplos para ellas.

Para el slot `number` no hemos especificado una intención porque estamos usando el extractor de entidades `Duckling`, así que podemos añadir ejemplo genéricos a la intención `inform`.

Para la entidad `cuisine` tendremos que añadir ejemplos anotadas dentro de la intención `inform`.

```yaml
## nlu.yml
## -------
nlu:
- intent: affirm
  examples: |
    - Yes
    - yes, please
    - yup
- intent: deny
  examples: |
    - no don't
    - no
    - no I don't want that

- intent: inform
  examples: |
    - [afghan](cuisine) food
    - how bout [asian oriental](cuisine)
    - what about [indian](cuisine) food
    - uh how about [turkish](cuisine) type of food
    - um [english](cuisine)
    - im looking for [tuscan](cuisine) food
    - id like [moroccan](cuisine) food
    - for ten people
    - 2 people
    - for three people
    - just one person
    - book for seven people
    - 2 please
    - nine people
```

Una vez hemos generado los datos de entrenmiento NLU, añadimos los intentos al dominio.

```yaml
## domain.yml
## ----------
intents:
  - request_restaurant
  - affirm
  - deny
  - inform
```

#### Definiendo las respuestas

El último paso es definir las repuestas que dará nuestro asistente al resultado de la entrega del formulario.

```yaml
## domain.yml
## ----------
responses:
  utter_submit:
  - text: "All done!"
  utter_slots_values:
  - text: "I am going to run a restaurant search using the following parameters:\n
            - cuisine: {cuisine}\n
            - num_people: {num_people}\n
            - outdoor_seating: {outdoor_seating}"
```

### Gestión de fallos del asistente

#### Gestión de mensajes fuera de alcance

Son mensajes inesperados dentro del alcance del asistente. Para gestionarlos creamos el intento `out_of_scope`:

```yaml
## nlu.yml
## -------
nlu:
- intent: out_of_scope
  examples: |
    - Quiero pedir comida.
    - ¿Cuánto es dos más dos?
    - ¿Quien es el presidente del Real Madrid?
```

Es una buena práctica utilizar como fuente de ejemplo las conversaciones reales de tu asistente.

El siguiente paso es definir en el dominio la respuesta utilizando la declaración `utter_out_of_scope`.

```yaml
## domain.yml
## ----------
responses:
  utter_out_of_scope:
  - text: Lo siente, aún no puedo hacer eso.
```

Por último debemos crear la regla que definirá el comportamiento del asistente.

```yaml
## rules.yml
## ---------
rules:
- rule: out-of-scope
  steps:
  - intent: out_of_scope
  - action: utter_out_of_scope
```

Si queremos diferenciar entre mensajes que están fuera de alcance y mensajes que estando fuera de alcance es posible que en algún momento esa funcionalidad esté disponible, podemos crear exactamente la misma estructura con un intento personalizado, por ejemplo `not_available_yet`.

#### Fallbacks

Los fallbacks se producen cuando un mensaje se ha clasificado con una confianza por debajo de umbral establecido.

##### NLU fallbacks

Los NLU fallbaks se producen cuando un mensaje se ha clasificado con una confianza por debajo de umbral establecido.

Para responder estos mensajes utilizaremos la intención `nlu_fallback`. También tendremos que añadir a nuestro pipeline NLU un componente que gestione estos mensajes, como es `FallbackClassifier`.

Entonces, configuramos el pipeline:

```yaml
## config.yml
## ----------
pipeline:
# other components
- name: FallbackClassifier
  threshold: 0.7
```

`threshold` indica que umbral mínimo de confianza para una intención.

Ahora definimos la respuesta en el dominio.

```yaml
## domain.yml
## ----------
responses:
  utter_please_rephrase:
  - text: I'm sorry, I didn't quite understand that. Could you rephrase?
```

El último paso es definir la regla que se ejecutará cada vez que se produzca un fallback.

```yaml
## rules.yml
## ---------
rules:
- rule: Ask the user to rephrase whenever they send a message with low NLU confidence
  steps:
  - intent: nlu_fallback
  - action: utter_please_rephrase
```

##### Umbral de confianza baja

En ocasiones el usuario puede lanzar un mensaje inesperado, dentro de una conversación, para el cual Rasa no podrá predecir la siguiente acción. En estos casos podemos utilizar una política de reglas.

Primero configuramos las políticas:

```yaml
## config.yml
## ----------
policies:
- name: RulePolicy
  # Confidence threshold for the `core_fallback_action_name` to apply.
  # The action will apply if no other action was predicted with
  # a confidence >= core_fallback_threshold
  core_fallback_threshold: 0.4
  core_fallback_action_name: "action_default_fallback"
  enable_fallback_prediction: True
```

El siguiente paso es definir la respuesta:

```yaml
## domain.yml
## ----------
responses:
  utter_default:
  - text: Sorry I didn't get that. Can you rephrase?
```

Lo que hace esta regla es lanzar la acción `action_default_fallback` que devolverá la respuesta al usuario y dejara el dialogo en el estado anterior al mensaje que ha provocado el `fallback`. Este mensaje no se tendrá en cuenta para la predicción de la siguiente acción.

Por último, y de forma opcional, podemos personalizar la acción `action_default_fallback` con nuestra propia`custom action`.

```python
## actions.py
## ----------
from typing import Any, Text, Dict, List

from rasa_sdk import Action, Tracker
from rasa_sdk.events import UserUtteranceReverted
from rasa_sdk.executor import CollectingDispatcher

class ActionDefaultFallback(Action):
    """Executes the fallback action and goes back to the previous state
    of the dialogue"""

    def name(self) -> Text:
        return ACTION_DEFAULT_FALLBACK_NAME

    async def run(
        self,
        dispatcher: CollectingDispatcher,
        tracker: Tracker,
        domain: Dict[Text, Any],
    ) -> List[Dict[Text, Any]]:
        dispatcher.utter_message(template="my_custom_fallback_template")

        # Revert user message which led to fallback.
        return [UserUtteranceReverted()]
```



##### Two-Stage Fallbacks

`Two-Stage Fallback` es un algoritmo que permite gestionar clasificaciones de intenciones con baja confianza. El algoritmo es el siguiente:

1. Un mensaje de usuario se clasifica con baja confianza.
2. Se pide la confirmación del usuario.
3. Si confirma, el dialogo continúa.
4. Si  deniega, se pide al usuario re-formular la petición.
5. Si el mensaje re-formulado tiene alta confianza se continua con el diálogo.
6. Si el mensaje re-formulado tiene baja confianza se solicita la confirmación del usuario.
7. Si el usuario confirma, se continua con el dialogo.
8. Si el usuario deniega, se lanza un acción fallback.

Para activar este comportamiento, primero tenemos que configurar las políticas de reglas y la configuración del pipeline.

```yaml
## config.yml
## ----------
pipeline:
# other components
- name: FallbackClassifier
  threshold: 0.7

policies:
# other policies
- RulePolicy
```

El siguiente paso es definir la respuesta de re-formulación de la petición.

```yaml
## domain.yml
## ----------
responses:
  utter_ask_rephrase:
  - text: I'm sorry, I didn't quite understand that. Could you rephrase?
```

Al igual que en el apartado anterior podemos personalizar la acción de re-frase.

Por último definimos la regla de `two-stage fallback`.

```yaml
## rules.yml
## ---------
rules:
- rule: Implementation of the Two-Stage-Fallback
  steps:
  - intent: nlu_fallback
  - action: action_two_stage_fallback
  - active_loop: action_two_stage_fallback
```


#### Human Hand off

`Human hand off` es el proceso por el que el asistente deja en manos de un humano el diálogo con el usuario. Esto ocurre cuando el diálogo termina en un fallback. Un método sencillo para realizarlo es cargar con un `payload` la acción de fallback. Esto le indicará al frontend que debe cambiar el canal de comunicación. Podemos ver un ejemplo en el [Helpdesk Assistant](https://github.com/RasaHQ/helpdesk-assistant).

### Gestión de entradas de datos inesperadas

Cuando nos encontramos dentro de un diálogo, el usuario puede exponer cuestiones que no esperamos. Estas cuestiones pueden ser de dos tipos:

- Fuera de contexto: en apartados anteriores ya hemos visto como gestionar este tipo de peticiones.
- Dentro de contexto: son cuestiones que el usuario suele utilizar dentro de los diálogos para obtener una explicación de que se le está pidiendo. A este tipo de cuestiones se le conoce como `interjecciones de usuario` o `interjecciones de contexto`. 

Veamos algún ejemplo de este último tipo.

```bash
usuario$ hola
bot$ Hola, ¿en qué puedo ayudarte?
usuario$ Busco un restaurante de cocina japones.
bot$ ¿Para cuántas personas?
usario$ ¿Para qué lo quieres saber?   ## interjección
bot$ Para poder acomodar a los comensales. ## explicación
bot$ ¿Para cuántas personas?
usuario$ 5 personas.
...

```

Para utilizar este comportamiento tenemos que activar la influencia de los slots en el dialogo. 

El primer paso es configurar el dominio.

```yaml
## domain.yml
## ----------
slots:
  requested_slot:
    type: categorical
    values:
      - cuisine
      - num_people
      - outdoor_seating
      - preferences
      - feedback
    influence_conversation: true
```

Por ultimo tenemos que entrenar nuestro modelo con historias que reflejen la interjección.

```yaml
## stories.yml
## -----------
stories:
- story: cuisine interjection
  steps:
  - intent: request_restaurant
  - action: restaurant_form
  - active_loop: restaurant_form
  - slot_was_set:
    - requested_slot: cuisine
  - intent: explain
  - action: utter_explain_cuisine
  - action: restaurant_form

- story: number of people interjection
  steps:
  - intent: request_restaurant
  - action: restaurant_form
  - active_loop: restaurant_form
  - slot_was_set:
    - requested_slot: num_people
  - intent: explain
  - action: utter_explain_num_people
  - action: restaurant_form
```

### Conversaciones contextuales

// TODO

## Conceptos

### Acciones personalizadas

Las acciones personalizadas o `custom actions` nos permiten gestionar la respuesta al usuario y dentro de esa gestión tenemos la posibilidad de integración con elementos de backend como bases de datos o APIs.

Las acciones personalizadas tienen que extender la clase del SDK Action. Esta clase tiene dos métodos predefinidos que podremos sobrescribir.

```python
class MyCustomAction(Action):

    def name(self) -> Text:

        return "action_name"

    async def run(
        self, dispatcher, tracker: Tracker, domain: Dict[Text, Any],
    ) -> List[Dict[Text, Any]]:

        return []
```

El método `name()` devuelve el nombre de la acción utilizada en la configuración del dominio y el método asíncrono `run()` es el responsable de ejecutar la acción que retornará la respuesta al usuario. Este método recibe tres parámetros:

- dispatcher: se utiliza para devolver la respuesta al usuario.
- tracker: nos da acceso al seguimiento del dialogo. A través de él podemos acceder a los slots y a los últimos mensajes, entre otros.
- domain: representa el dominio del robot.

Por último el método `run()` devuelve una lista de eventos.

Veamos un ejemplo aplicado al asistente de restaurantes.

```python
from typing import Text, Dict, Any, List
from rasa_sdk import Action
from rasa_sdk.events import SlotSet

class ActionCheckRestaurants(Action):
   def name(self) -> Text:
      return "action_check_restaurants"

   def run(self,
           dispatcher: CollectingDispatcher,
           tracker: Tracker,
           domain: Dict[Text, Any]) -> List[Dict[Text, Any]]:

      cuisine = tracker.get_slot('cuisine')
      q = "select * from restaurants where cuisine='{0}' limit 1".format(cuisine)
      result = db.query(q)

      return [SlotSet("matches", result if result is not None else [])]
```

#### Tracker

El objeto trace nos permite obtener los siguientes atributos de la conversación.

- `sender_id` - El ID unico del usuario.
- `slots` - la lista de slots.
- `latest_message` - el último mensaje del usuario: `intent`, `entities` y `text`.
- `events` - La lista de eventos previos.
- `active_loop` - El nombre del formulario activo.
- `latest_action_name` - La última acción ejecutada.

#### Dispatcher

El componente `Dispatcher` permite devolver respuestas al usuario, sin utilizar eventos, como se hace en el siguiente caso. 

```python
class ActionGreetUser(Action):
    def name(self) -> Text:
        return "action_greet_user"

    async def run(
        self,
        dispatcher: CollectingDispatcher,
        tracker: Tracker,
        domain: Dict[Text, Any],
    ) -> List[EventType]:

        dispatcher.utter_message(text = "Hi, User!")

        return []
```

Además de texto podemos enviar otros tipos de datos, veamos los uno a uno:

* `text`: un mensaje de texto.

* `image`: una imagen o URL cuyo resultado lo sea.

* `json_message`: un objeto json entendible por el canal al que va dirigido.

* `template`: una respuesta definida en el dominio.

* `attachment`: una URL.

* `buttons`: una lista de botones para respuesta del usuario con payloads.

  ```python
  dispatcher.utter_messsage(buttons = [
                  {"payload": "/affirm", "title": "Yes"},
                  {"payload": "/deny", "title": "No"},
              ]
  ```

* `elements`: sólo para Facebook.

* `**kwargs`: argumentos para interpolar en una plantilla.

  ```yaml
  ## domain.yml
  ## ----------
  responses:
    utter_greet_name:
    - text: Hi {name}!
  ```

  ```python
  dispatcher.utter_message(template = "utter_greet_name", name = "Aimee")
  ```

#### Eventos

// TODO

Las conversaciones en Rasa se representan con una lista de eventos. Rasa define todos los tipos de eventos necesarios en su SDK. Veamos los en detalle.

* SlotSet: permite establecer el valor de un slot.
* AllSlotReset: re-establece todos los valores.
* ReminderScheduled: 
* ConversationPaused
* ConversationResumed
* FollowupAction
* UserUtteranceReverted
* ActionReverted
* Restarted
* SessionStarted
* UsserUttered
* BotUttered
* ActionExecuted

#### Knowledge Base Actions

Esta funcionalidad permite a Rasa acceder a información de la Knowledge Base y utilizarla en los diálogos con los usuarios. 

Un ejemplo de este tipo de conversación podría ser:

```pseudocode
usuario>  Hola
bot> hola, cómo estás?
usuario$ bien. ¿Podrías darme la lista de restaurantes de 3 estrellas michelin de Madrid?
bot> Si, esta es la lista:
1. Tortilla Alegre.
2. Mojito Feliz.
3. Cachopo Perez.
usuario> ¿Que tipo de cocina hace Mojito Feliz?
bot> Cocina madrileña
...
```

Es imposible crear un modelo que maneje este tipo de conversación a través de ejemplos e historias. Además esta información puede ser dinámica y variar con el tiempo. Podemos ver un ejemplo de uso de este tipo de asistentes en [knowledgebasebot](https://github.com/RasaHQ/rasa/tree/main/examples/knowledgebasebot).

Para la Implementación de estos tipos de dialogo utilizaremos `ActionQueryKnowledgeBase`. El primer paso es configurar la fuente de nuestro conocimiento. Una forma sencilla de hacerlo es utilizar `InMemoryNowledgeBase`. Este componente nos permite utilizar un fichero `data.json` como `knowledge base`. Cada objeto debe tener al menos un atributo `id` y otro `name`.

```json
{
    "restaurant": [
        {
            "id": 0,
            "name": "Donath",
            "cuisine": "Italian",
            "outside-seating": true,
            "price-range": "mid-range"
        },
        {
            "id": 1,
            "name": "Berlin Burrito Company",
            "cuisine": "Mexican",
            "outside-seating": false,
            "price-range": "cheap"
        },
        {
            "id": 2,
            "name": "I due forni",
            "cuisine": "Italian",
            "outside-seating": true,
            "price-range": "mid-range"
        }
    ],
    "hotel": [
        {
            "id": 0,
            "name": "Hilton",
            "price-range": "expensive",
            "breakfast-included": true,
            "city": "Berlin",
            "free-wifi": true,
            "star-rating": 5,
            "swimming-pool": true
        },
        {
            "id": 1,
            "name": "Hilton",
            "price-range": "expensive",
            "breakfast-included": true,
            "city": "Frankfurt am Main",
            "free-wifi": true,
            "star-rating": 4,
            "swimming-pool": false
        },
        {
            "id": 2,
            "name": "B&B",
            "price-range": "mid-range",
            "breakfast-included": false,
            "city": "Berlin",
            "free-wifi": false,
            "star-rating": 1,
            "swimming-pool": false
        },
    ]
}
```

El siguiente paso es definir los ejemplos para la NLU. Crearemos un nuevo intento `query_knowledge_bas`.

```yaml
nlu:
- intent: query_knowledge_base
  examples: |
    - what [restaurants]{"entity": "object_type", "value": "restaurant"} can you recommend?
    - list some [restaurants]{"entity": "object_type", "value": "restaurant"}
    - can you name some [restaurants]{"entity": "object_type", "value": "restaurant"} please?
    - can you show me some [restaurants]{"entity": "object_type", "value": "restaurant"} options
    - list [German](cuisine) [restaurants]{"entity": "sobject_type", "value": "restaurant"}
    - do you have any [mexican](cuisine) [restaurants]{"entity": "object_type", "value": "restaurant"}?
    - do you know the [price range]{"entity": "attribute", "value": "price-range"} of [that one](mention)?
    - what [cuisine](attribute) is [it](mention)?
    - do you know what [cuisine](attribute) the [last one]{"entity": "mention", "value": "LAST"} has?
    - does the [first one]{"entity": "mention", "value": "1"} have [outside seating]{"entity": "attribute", "value": "outside-seating"}?
    - what is the [price range]{"entity": "attribute", "value": "price-range"} of [Berlin Burrito Company](restaurant)?
    - what about [I due forni](restaurant)?
    - can you tell me the [price range](attribute) of [that restaurant](mention)?
    - what [cuisine](attribute) do [they](mention) have?
```

En los datos de ejemplo anotaremos con:

- `object_type`: cada uno de los tipos de los datos de ejemplo: restaurante, hotel, ...
- `mention`: las referencias a `entities` previas.
- `attribute`: cada uno de los atributos de los objetos.

Cada uno de estos elementos tiene consideración de `entity` con un `slot` asociado  y por lo tanto los tenemos que definir en el domino. 

```yaml
## domain.yml
## ----------
entities:
  - object_type
  - mention
  - attribute

slots:
  object_type:
    type: unfeaturized
  mention:
    type: unfeaturized
  attribute:
    type: unfeaturized
```

Nos queda crear la acción que consultará nuestra base de conocimiento. Extendemos la clase 

`ActionQueryKnowledgeBase`.

```python
from rasa_sdk.knowledge_base.storage import InMemoryKnowledgeBase
from rasa_sdk.knowledge_base.actions import ActionQueryKnowledgeBase

class MyKnowledgeBaseAction(ActionQueryKnowledgeBase):
    def __init__(self):
        knowledge_base = InMemoryKnowledgeBase("data.json")
        super().__init__(knowledge_base)
```

Y como siempre la añadimos al dominio.

```yaml
## domain.yml
## ----------
actions:
- action_query_knowledge_base
```

Creamos una historia para el entrenamiento.

```yml
## strories.yml
## ------------
stories:
- story: knowledge base happy path
  steps:
  - intent: greet
  - action: utter_greet
  - intent: utter_greet
  - action: action_query_knowledge_base
  - intent: goodbye
  - action: utter_goodbye
```

Por último añadimos una respuesta de re-formulación de preguntas.

```yml
## domain.yml
## ----------
responses:
  utter_ask_rephrase:
  - text: "Sorry, I'm not sure I understand. Could you rephrase it?"
  - text: "Could you please rephrase your message? I didn't quite get that."
```

// TODO: ¿Cómo usarlo?

## Proyecto de ejemplo

Para crear un proyecto desde cero en Rasa podemos utilizaremos el comando

```bash
(venv) $ pip3 install rasa[full] # instalacion de rasa con todas las dependencias
(venv) $ rasa init # inicializa un bot emocional de rasa
```

Debemos asegurarnos de que hemos activado el entorno virtualizado antes de iniciar el proyecto. La estructura resultante es la siguiente:

![Estructura del proyecto Rasa](/home/xose/Proxectos/docs/virtual-assistant/images/project-structure.png)

Veremos que contienen cada uno de los directorios y archivos del proyecto a lo largo de este manual.



## Anexo I

| Referencia                                   | URL                                                          |
| -------------------------------------------- | ------------------------------------------------------------ |
| Guía de instalación de Rasa.                 | [Step-by-step Installation Guide](https://rasa.com/docs/rasa/installation/) |
| Guía de instalación de GitHub. Conexión SSH. | [Connecting to GitHub with SSH](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh) |
| Repositorio GitHub pyenv                     | https://github.com/pyenv/pyenv/                              |
| Repositorio GitHub pyenv-virtualenv          | https://github.com/pyenv/pyenv-virtualenv                    |
| Dependencias de instalación de pyenv         | https://github.com/pyenv/pyenv/wiki/Common-build-problems    |
|                                              |                                                              |
|                                              |                                                              |
|                                              |                                                              |
|                                              |                                                              |

