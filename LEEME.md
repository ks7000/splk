# ¿Qué es Splunk y cómo funciona?

Este es un resumen de las capacidades de monitorización de registros o mejor conocidos como _logs_. Probablemente haya usted oído hablar de Splunk, pero ¿puede describir lo que hace a un colega en unas pocas palabras? No es fácil. Splunk no pertenece a ninguna categoría tradicional, pero se distingue de la multitud. Eso lo hace interesante, pero también hace que la explicación sea más difícil. Este documento pretende orientar acerca del tema pero de una forma sencilla y sin mayor tecnicismo.

# DuckDuckGo para archivos de registros

¿Qué haceemos cuando necesitamos alguna información sobre el estado de una máquina -su sistema operativo- o algún software instalado en ella? Miramos sus archivos de registro (en idioma inglés _logfiles_). Estos indican en qué estado se encuentra y qué sucedió recientemente. Hasta acá todo muy bien.

¿Qué hacemos cuando necesitamos información sobre el estado de todos los dispositivos en nuestro centro de datos? Mirar todos los archivos de registro sería la respuesta correcta si fuera posible en cualquier período de tiempo práctico. **Aquí es donde entra Splunk**. Splunk comenzó como una especie de "[DuckDuckGo](https://duckduckgo.com/) para archivos de registros". Hoy realiza mayores tareas, pero el procesamiento de registros sigue siendo el punto fuerte del producto. Almacena todos sus registros y proporciona capacidades de búsqueda muy rápidas aproximadamente de la misma manera que DuckDuckGo lo hace para Internet.

# Splunk: Lenguaje para Procesos de Búsqueda

La mayoría de las personas están familiarizadas o utilizan el Lenguaje de Consulta Estructurada (en idioma inglés _Structured Query Language_ y mejor conocida como SQL) para obtener información a partir de datos almacenados en diferentes base de datos. **Piense en el Lenguaje para Procesos de Búsqueda de Spark** (_Search Processing Language_ o SPL) como SQL mejorado. Splunk SPL ofrece aún más. SPL es una herramienta extremadamente poderosa para examinar grandes cantidades de datos y realizar operaciones estadísticas sobre lo que es relevante en un contexto específico.

Sin ir muy lejos: pongamos por caso que tenemos un servidor web con Apache sirviendo páginas llamado "ks7000-servidor" y deseamos visualizar únicamente las visitas desde la dirección IPv4 específica 192.168.1.47, pues agregamos lo siguiente:

source="apachelogs:*" host="ks7000-servidor" | search IP=192.168.1.47

Nótese el uso del comando [_tubería_](https://es.wikipedia.org/wiki/Tuber%C3%ADa_(inform%C3%A1tica)) representado por la pleca o barra vertical para pasar información al siguiente comando que la filtra, tal como se estila en BASH en el ambiente GNU/Linux.

# Aplicaciones, complementos y fuentes de datos

Al leer lo anterior, usted puede preguntarse cómo Splunk sabe sobre el trabajo de nuestro servidor web. Y tiene ud. razón: por sí mismo, no sabe nada. Pero puede recibir datos de una variedad de fuentes: todo tipo de archivos de registros, Syslog, SNMP, registros de eventos de Windows, por nombrar algunos. Si los datos que necesita no se pueden encontrar en ningún registro, puede escribir una secuencia de comandos y dirigir a Splunk para que procese su salida al destino adecuado. Si eso todavía no es suficiente, debe consultar el Directorio de aplicaciones de Splunk (conocida com [_Splunkbase_](https://splunkbase.splunk.com/)) para obtener un complemento específico que recopile los datos necesarios. En el ejemplo anterior, los datos fueron generados por [uberAgent](https://uberagent.com/), el cual se ejecuta en los puntos finales monitorizados, independientemente de Splunk. uberAgent envía los datos que recopila a Splunk para su almacenamiento y procesamiento posterior (aunque no necesariamente en ese orden).

uberAgent marca el inicio de la recolección de datos (y de manera opcional los convierte en información) para luego ser enviado(s) al servidor splunk. Incluso puede presentar datos a el usuario mediante la aplicación uberAgent, conectada esta al servidor splunk. De igual forma funcionan los agentes especiales y aplicaciones de terceros. En todo caso es necesario el uberAgent ya sea que funcione solo, con otros componentes de terceros/propios o con un componente especial: el _Universal Forwarder_ de splunk («Splunk’s Universal Forwarder»).

# ¿Eventos, esquemas, indexado?

Con todo lo hasta ahora descrito, cualquiera puede pensar en Splunk como sinónimo de "base de datos" convencional. Pero eso no es exactamente así. Mientras que una base de datos requiere que definamos tablas y campos antes de poder almacenar datos, Splunk acepta casi cualquier cosa inmediatamente después de la instalación. En otras palabras, Splunk no tiene un esquema fijo. En cambio, realiza la extracción del campo en el momento de la búsqueda. Muchos formatos de registro se reconocen automáticamente y muchos otros más se puede especificar ya sea en archivos de configuración o directamente en la expresión de búsqueda.

Este enfoque permite manejar varias opciones. Al igual que DuckDuckGo va como una araña por la red leyendo cualquier página web sin saber nada sobre el diseño o contenido del sitio visitado, Splunk indexa cualquier dato de máquina que se pueda representar como texto.

Durante la fase de indexado, cuando Splunk procesa los datos entrantes y los prepara para el almacenamiento, el indexador realiza una modificación importante: divide la secuencia de caracteres en eventos individuales. Los eventos generalmente corresponden a líneas en el archivo de registro que se está procesando. Cada evento recibe una marca de tiempo y algunas otras propiedades predeterminadas como el dispositivo de origen, por ejemplo. Luego, las palabras clave de eventos se agregan a un archivo de índice para acelerar las búsquedas posteriores y el texto del evento se almacena en un archivo comprimido ubicado directamente en el sistema de archivos.
