---
layout: post
title: "Big Data Zoo"
---
**En la actualidad, existen un gran cantidad de empresas que basan sus cimientos o desarrollan proyectos en torno al  Big Data. Estos proyectos están constituidos mayoritariamente por tecnologías de código libre, creadas y desarrolladas para ingestar, procesar, almacenar o servir grandes cantidades de información. Aunque podemos agrupar las tecnologías en distintos grupos dependiendo de su funcionamiento y especificaciones no existe ninguna regla para saber cuál es la adecuada para nuestro proyecto. No obstante, podemos contestar una serie de cuestiones que nos ayudarán a tomar una decisión:
¿Cómo y dónde obtengo los datos? ¿Con qué tecnología realizo el procesamiento? ¿Dónde almaceno la información? ¿Cómo consulto la información? ¿Qué representación de los datos es la adecuada?**

### ¿Qué tecnología Big Data es la adecuada para mi proyecto?

Las tecnologías  Big Data se pueden clasificar dependiendo de su función principal y del tratamiento al que se somete la información. Si seguimos el flujo de la información para agrupar las diferentes tecnologías, encontraríamos los siguientes grupos: **ingestión, procesamiento, almacenamiento y servicio**. Pero la pregunta ahora sería: ¿Qué tecnología es usada en cada uno de los grupos?

### Ingestión

La ingestión de datos es el proceso por el cual **la información que se obtiene en tiempo real** va siendo capturada o almacenada temporalmente para un posterior procesamiento. Un ejemplo de esta información, que es muy común en la actualidad, viene dado por los eventos reportados desde sensores distribuidos a un punto centralizado donde se procesan. Estos escenarios son los que nos encontramos en proyectos de IoT (Internet of Things), como puede ser los nuevos contadores de luz electrónicos, que reportan de forma periódica el consumo eléctrico de cada hogar a la central eléctrica. Otro entorno puede ser el envío de archivos de logs de diversas aplicaciones que se desean monitorizar.

Algunas de las tecnologías que podemos encontrar  en este grupo son:

* **Sistemas distribuidos de colas**: El más conocido actualmente es Apache Kafka, pero existen otros como RabbitMQ, y soluciones en la cloud como AWS Kinesis.
* **Sistemas de procesamiento de logs**: Donde podemos encontrar tecnologías como Logstash y Fluentd.

![](../images/post-data-zoo/lluvia.jpeg)

### Procesamiento

El procesamiento de la información es la fase en la que se realizan transformaciones a los datos en bruto para adaptar y obtener datos procesados y enriquecidos, que aporten el valor real de la información. El procesamiento se puede dividir en dos subgrupos dependiendo del volumen de datos y tiempo de procesamiento de estos: streaming processing y batch processing.

#### Streaming Processing

El streaming processing es un **procesamiento que se realiza en tiempo real**, tan pronto como los datos son capturados por la fase de ingestión son transferidos a la fase de procesado en un flujo continuo, de esta forma los datos van siendo procesados en pequeñas cantidades. Las principales tecnologías clasificadas en su modo de procesamiento en este grupo son:

* **Full-Streaming**: Apache Storm, Apache Samza, Apache Flink.
* **Microbatch**: Spark Streaming, Storm Trident.

![](../images/post-data-zoo/cascada.jpg)

Estas tecnologías procesan eventos en **cuestión de milisegundos** y aseguran distintas garantías ante fallos de sistemas o de la red. Las garantías se suelen clasificar en tres grupos, que se pueden observar en la tabla a continuación:

|                 | Storm          | Samza            | Flink           | Spark Streaming  | Storm Trident     |
| :-------------: | :-------------: | :-------------: | :-------------: | :-------------:  | :-------------:   |
| Máximo una vez | Si             |No              | No             |No               |Si                  |
| Mínimo una vez | Si             |Si              | No             |No               |Si                  |
| Exactamente una vez | No             |No              | Si             |Si               |Si                  |


#### Batch Processing

Por otro lado, el batch processing se realiza de manera periódica y son procesos que trabajan con **grandes volúmenes de información** (Gigabytes, Terabytes…). Principalmente, estos procesos se basan en el paradigma de MapReduce, que consta de dos fases: la primera es encargada de leer y adaptar los datos y en la segunda etapa es donde se realiza una agrupación de estos, normalmente en base a un clave común en todos. El ejemplo más común es el conocido “WordCount”, encargado de contar el número de repeticiones de cada palabra en un texto.

Al igual que en los distintos grupos, existen múltiples tecnologías para realizar procesos de batch processing. La más conocida es **Hadoop MapReduce** que funciona dentro del ecosistema de Hadoop apoyándose por su planificador YARN, pero la programación de estos procesos es algo costoso por lo que a lo largo de los años han ido surgiendo tecnologías que proporcionan abstracciones a más alto nivel para desarrollar estos procesos, algunos de estas tecnologías son: Apache Pig (Scripting), Cascading (Java), Cascalog (Clojure).

![](../images/post-data-zoo/mar.jpg)

Aunque Hadoop MapReduce trabaja bastante bien con grandes cantidades de datos, los procesos de Map y Reduce pueden ser lentos ya que necesitan escribir a disco entre distintas fases, por este motivo surgen nuevas tecnologías que solucionan este problema, algunas de ellas son: Apache Tez, Apache Spark o nuevamente Apache Flink, que trabaja tanto en streaming como en batch.

### Almacenamiento

Toda **la información** que se consume y se procesa **tiene que ser almacenada** para poder trabajar sobre ella en las fases de batch processing y para poder ser consultada y representada. El almacenamiento de la información se puede realizar de muchas formas, almacenándola completamente, lo que conoceríamos como almacenamiento en RAW, o realizando distintas indexaciones o compactaciones que incrementan la rapidez y facilitan el acceso. Existen dos sistemas de almacenamiento: sistemas de ficheros distribuidos y sistemas de almacenamiento de datos

#### Sistemas de ficheros distribuidos

Los sistemas de ficheros suelen utilizarse para almacenar el RAW de la información o resultados de procesos MapReduce. Algunas de las tecnologías que existen hoy en día son:

* Hadoop HDFS
* Tachyon
* Riak-CS
* Soluciones en la cloud como AWS S3

![](../images/post-data-zoo/presa.jpeg)

#### Sistemas de almacenamiento de datos

Por otro lado, estos sistemas de almacenamiento, son **sistemas especializados y desarrollados para trabajar con estructuras de datos en concreto** y se pueden clasificar en distintos tipos:

* **Relacionales**: En este grupo se encuentran las base de datos más comunes y conocidas como son: PostgreSQL, MySQL o soluciones cloud como Amazon RDS.
* **Clave / Valor**: Estas tecnologías están optimizadas para almacenar información estructurada por clave/valor, es decir en el sistema las claves son únicas y una clave podrá tener asignada un único valor a su vez. Existen soluciones que trabajan en disco y otras que trabajan en memoria y su funcionamiento es similar al de una caché distribuida. Algunas tecnologías son: Redis, GridGain, Riak, Voldemort, Aerospike, Memcached, entre otras muchas.
* **Columnar**: Estos sistemas de almacenamiento están caracterizados por el almacenamiento columnar de la información, normalmente se realizan indexaciones en una o varias columnas para aumentar la velocidad de búsqueda de información en ellas. La mayoría de las columnas se usan para trabajar con series temporales. Algunas de las tecnologías más utilizadas son: Cassandra, Druid, HBase y AWS Redshift.
* **Documentales**: Las bases de datos documentales son usadas para almacenar y acceder a datos semi-estructurados con formatos como XML o JSON. Aunque  permiten más libertad a la hora de almacenar los datos: esto puede perjudicar a la eficiencia o facilidad con la que se acceden a ellos. Algunos ejemplos de estas tecnologías, también conocidas como NoSQL, son: MongoDB, ElasticSearch o Couchbase.
* **Grafos**: Algunos conjuntos de datos tienen una representación en grafos como puede ser la información de las redes sociales, o la información del tráfico de una red de datos. Estas bases de datos sacrifican el acceso a tablas de datos para proporcionar mayor flexibilidad a la hora de datos orientados a grafos. Algunas de estas bases de datos son: Neo4j o OrientDB.

### Servicio

El último grupo es el encargado de **representar la información almacenada** en la fase de almacenamiento. Las tecnologías de este grupo suelen ser frameworks para crear aplicaciones WEB, algunos de ellos son:

* Ruby on Rails
* Node.js
* Django
* AngularJS

![](../images/post-data-zoo/vaso.jpeg)

Estos frameworks se apoyan en librerías de visualización de datos como: D3, Chart.js, Highcharts. Las librerias a su véz son usadas por herramientas de visualización como: Kibana, Grafana, Superset, Metabase, etc.

### Evalua tus necesidades

En conclusión hemos podido ver que existen una gran variedad de tecnologías relacionadas con el mundo del Big Data y que si la clasificamos en distintos grupos, aun así encontramos una gran variedad dentro de cada grupo. Es por ello que la respuesta a la pregunta “¿Qué tecnología es la adecuada?”, depende de la situación.

**Cada proyecto tiene unas necesidades diferentes** y aunque algunos proyectos tengan cierta similitud puede que unas tecnologías se adapten mejor que otras. El conjunto de estas tecnologías cooperando entre sí da lugar a distintas arquitecturas, algunas de ellas son **Arquitecturas Lambda** y **Arquitecturas Kappa**, en este artículo no entraremos al detalle de dichas arquitecturas.
