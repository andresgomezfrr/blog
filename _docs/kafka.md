---
layout: documentation
title: Kafka
permalink: /kafka/
---

* Do not remove this line (it will not be displayed)
{:toc}

# Arquitectura

## ZooKeeper y Kafka

Una vez vista la arquitectura básica de ZooKeeper y Kafka, vamos a iniciar ambos servicios y ver como Kafka se registra en ZooKeeper.

1. Ahora vamos a descargar la distribución de Kafka y la descomprimimos.
```
wget http://apache.rediris.es/kafka/2.1.0/kafka_2.12-2.1.0.tgz
```
```
tar -xvf kafka_2.12-2.1.0.tgz ; cd kafka_2.12-2.1.0
```

2. En primer lugar vamos a ejecutar un servidor de Zookeeper:
```
bin/zookeeper-server-start.sh config/zookeeper.properties
```

3. Para ejecutar Kafka utilizaremos uno de los scripts que proporciona:
```
bin/kafka-server-start.sh config/server.properties
```
Si se desea ejecutar en segundo plano se puede añadir la opción -daemon:
```
bin/kafka-server-start.sh -daemon config/server.properties
```
  * **Nota:** Por defecto la configuración de Kafka busca el servidor de ZooKeper en localhost:2181, si hemos levantado el servicio de Kafka y ZooKeeper en máquinas separadas deberemos editar el fichero ```config/server.properties```

4. Para verificar que el servidor esta corriendo y se ha registrado correctamente en ZooKeeper podemos utilizar la utilidad **zookeeper-shell.sh**. Veremos que efectivamente existe un Broker con identificador 0, es el valor que viene en el fichero por defecto.
```
[zk: localhost:2181(CONNECTED) 5] ls /brokers/ids
[0]
```
Si hacemos un **get** del zNode podemos ver el contenido que el broker ha almacenado dentro.
```
[zk: localhost:2181(CONNECTED) 7] get /brokers/ids/0
{"jmx_port":-1,"timestamp":"1472318261728","endpoints":["PLAINTEXT://192.168.99.1:9092"],"host":"192.168.99.1","version":3,"port":9092}
```

* **Nota:** Si quisiéramos levantar varios Brokers en la misma maquina para realizar pruebas deberíamos de cambiar el identificador del fichero **broker.id**, el directorio donde se almacena la información de Kafka **log.dirs** y el puerto donde escucha el servicio **listeners**.

## Topics
Ahora vamos a crear un nuevo topic con 1 partición y factor de replicación igual a 1. En primer lugar tendremos que estar en la carpeta donde se encuentra la distribución de Kafka que nos descargamos anteriormente, y ejecutar lo siguiente:
```
bin/kafka-topics.sh --zookeeper localhost:2181 --create --topic nuevo-topic --partitions 1 --replication-factor 1
```

Una ves hemos creado el topic podemos verificar su creación utilizando:
```
bin/kafka-topics.sh --zookeeper localhost:2181 --list
```

También podemos ver una descripción más detallada utilizando:
```
bin/kafka-topics.sh --zookeeper localhost:2181 --describe --topic nuevo-topic
```
Obtendremos una salida similar a la siguiente:
```
Topic:nuevo-topic	PartitionCount:1	ReplicationFactor:1	Configs:
	Topic: nuevo-topic	Partition: 0	Leader: 0	Replicas: 0	Isr: 0
```
Analizando la salida podemos ver que nos dice que el topic *nuevo-topic* tiene una partición que su líder se encuentra en el broker con id 0 y que tiene una replica en el broker 0 y se encuentra en sincronía con la partición líder. Esto último es obvio ya que solamente tenemos una partición.

## Productor y Consumidor

Una vez ya tenemos nuestros topics creados, es hora de producir algún mensaje en él y comprobar que el sistema funciona completamente. Para enviar mensajes vamos a utilizar las herrameientas que vienen con el paquete de Kafka que porporciona un consumidor y producor de consola.

### Productor
El productor de consola deja abierta una shell, donde podremos escribir mensajes para que los envie a Kafka.
```
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic nuevo-topic  --property parse.key=true --property key.separator=,
```
Este comando nos permite enviar mensajes con clave a Kafka. Usando el símbolo indicado en **--property key.seperator** se puede separar la clave del mensaje. Probemos a enviar varios mensajes:
```
1,hola
2,mundo
clave,mensaje
```
Los mensajes se dan por terminados y se envían cuando se pulsa enter.

* **Nota:** Si intentamos enviar un mensaje sin la ',' el productor fallará al no poder distinguir la clave del valor.

### Consumidor
Igual que el productor usaremos un script que viene proporcionado con Kafka:
```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --property print.key=true --topic nuevo-topic  --consumer.config config/consumer.properties
```

Una vez ejecutado, la shell se quedará abierta y empezaremos a ver los mensajes que enviamos por el productor. Hemos utilizado la opción **--consumer.config config/consumer.properties** para poder indicar un grupo de consumidor. Si mostramos el contenido del fichero:

```
cat config/consumer.properties
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
# see kafka.consumer.ConsumerConfig for more details

# Zookeeper connection string
# comma separated host:port pairs, each corresponding to a zk
# server. e.g. "127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002"
zookeeper.connect=127.0.0.1:2181

# timeout in ms for connecting to zookeeper
zookeeper.connection.timeout.ms=6000

#consumer group id
group.id=test-consumer-group

#consumer timeout
#consumer.timeout.ms=5000
```

Podemos ver que se indica un group.id con valor de **test-consumer-group**.

* **Nota:** Hemos utilizado la opción **--new-consumer** ya que hemos utilizado la nueva API de Kafka introducida en la versión 0.9.0. El consumidor aun permite utilizar la API antigua.

# Configuración

## Configuración Kafka Broker

En esta lección vamos a hablar de los distintos parámetros de configuración que se pueden aplicar al Broker de Kafka. El fichero utilizado para configurar el servicio de Kafka es ```server.properties```

```
################################ Server Basics ################################
broker.id=0

################################# Log Basics ##################################
log.dirs=/tmp/kafka-logs
num.partitions=1

############################# Log Retention Policy ############################
log.retention.hours=168
log.retention.bytes=1073741824
log.segment.bytes=536870912
log.retention.check.interval.ms=300000

################################## Zookeeper ##################################
zookeeper.connect=localhost:2181
```

Este es un ejemplo de fichero de configuración básico.

* **broker.id** : Identificador único por cada Broker. También existe la posibilidad de generarlo de manera automática para ello debemos: eliminar esta propiedad y configurar las siguientes propiedades. Con esto conseguimos que el Broker obtenga un identificador único utilizando Zookeeper.

```
broker.id.generation.enable=true
reserved.broker.max.id=1000
```

* **log.dirs** : En esta propiedad indicamos el directorio donde queremos que se almacenen los distintos ficheros de log de Kafka, es decir, los datos que son almacenados en las distintas particiones de los topics.

* **num.partitions** : Número de particiones por defecto al crear un topic de manera automática.

* **log.retention.hours** : Número de horas que se mantienen los datos de un log, antes de eliminarlos. Existen otras dos propiedades que pueden suplantar esta propiedad.

```properties
log.retention.minutes
log.retention.ms
```

* **log.retention.bytes** : Número total de bytes que Kafka almacena por cada log, antes de eliminarlos.

**Nota:**  *log.retention.hours* y *log.retention.bytes* son complementarias una con otra. Los datos se borran cuando se cumpla la primera de ellas.

* **log.segment.bytes** : Tamaño máximo que puede ocupar los ficheros por lo que están compuestos el log.

* **log.retention.check.interval.ms** : Define el intervalo de tiempo que configura cada cuanto tiempo se verifica si existen logs para ser borrados.

* **zookeeper.connect** : La dirección y puerto donde se encuentra el o los nodos de ZooKeeper. Si existiera un chroot se podría indicar detrás de cada host. Ejemplo: ```zookeeper1:2181/kafka-test,zookeeper2:2181/kafka-test,zookeeper3:2181/kafka-test```

La lista completa de configuraciones del Broker se pueden encontrar en el siguiente enlace:
[Configuración Broker Kafka](http://kafka.apache.org/documentation.html#brokerconfigs).

## Configuración de Topic

### Configuración particiones y réplicas
A la hora de crear un topic nuevo podemos indicar el número de particiones y el factor de replicación.

```
bin/kafka-topics.sh --zookeeper localhost:2181 --create --topic my-topic --partitions 1 --replication-factor 1
```
 * En la creación Kafka asigna la replicas automáticamente a los brokers.

Una vez ya tenemos creado el topic es facil incrementar el número de particiones:
```
bin/kafka-topics.sh --zookeeper localhost:2181 --alter --topic my-topic --partitions 10
```

Aunque no es tan sencillo incrementar el factor de replicación, para hacerlo debemos hacer lo siguiente:

1. En primer lugar debemos definir un fichero, que vamos a nombrar *incremento-replicas.json*, en formato JSON donde indicamos en que broker queremos asignar las distintas particiones (incluidas sus replicas):
```json
{"version":1, "partitions":[{"topic":"my-topic", "partition":0, "replicas":[1,2,3]}]}
```

Mediante este fichero indicamos que para el topic *my-topic* queremos que su partición 0 se encuentre en los brokers con ID: 1,2,3; es decir conseguiríamos un factor de replicación de 3. Kafka se encarga automáticamente de decidir que partición es la líder.

2. Ejecutar el siguiente comando para aplicar la configuración deseada:
```
bin/kafka-reassign-partitions.sh --zookeeper localhost:2181 --reassignment-json-file incremento-replicas.json --execute
```

### Configuración a nivel de topic

Como hemos visto en la lección también se pueden aplicar configuraciones especificas por topic. Si esta configuración no es aplicada se utilizan los valores utilizados en el fichero del broker, o los por defecto si no están especificados.

La configuración por topic se puede aplicar a la hora de creación del topic:

```bash
bin/kafka-topics.sh --zookeeper localhost:2181 --create --topic my-topic --partitions 1 --replication-factor 1 --config max.message.bytes=64000 --config flush.messages=1
```

* Mediante este comando crearemos un topic denominado **my-topic** que tendrá 1 partición y factor de replicación de 1, el tamaño máximo de los mensajes de este topic sera 64000 bytes y se realizara un flush al log que se encuentra en el disco duro por cada mensaje.

Si quisiéramos cambiar la configuración especifica de un topic utilizaríamos un comando como el siguiente:

```bash
bin/kafka-topics.sh --zookeeper localhost:2181 --alter --topic my-topic --config max.message.bytes=128000
```

* Este comando cambia el valor para la configuración **max.message.bytes** ahora el topic soporta mensajes con un tamaño máximo de 128000 bytes en lugar de 64000 bytes.

Finalmente, también existe la posibilidad de borrar una configuración aplicada a un topic, en ese caso tendremos que usar el comando con la opción **--delete-config**:

```bash
bin/kafka-topics.sh --zookeeper localhost:2181 --alter --topic my-topic --delete-config max.message.bytes
```

* Una vez ejecutado el valor el topic utilizará el valor definido en el fichero de configuración del broker ```server.properties``` o de no estar definida utilizará el valor por defecto.

Algunas configuraciones más interesantes que se pueden aplicar por topic son las siguientes:

| Propiedad     | Descripción     |
| :------------- | :------------- |
| cleanup.policy      | Política de borrado aplicada al topic: **delete** o **compact**.  |
| max.message.bytes| Tamaño máximo de los mensajes almacenados en este topic.|
|retention.bytes| Tamaño máximo que un log puede crecer antes de que se aplique una política de borrado.|
|min.insync.replicas| Número de replicas que deben asentir antes de devolver el ACK al productor.|

La lista completa de configuración por topic se pueden encontrar en el siguiente enlace:
[Configuración a nivel de topic](http://kafka.apache.org/documentation.html#topic-config).

cleanup.policy=compact

## Configuración Productor
Las principales propiedades para el productor son las siguientes:

| Propiedad     | Descripción     |
| :------------- | :------------- |
| bootstrap.servers  | Lista de brokers de Kafka separados por coma e indicando el puerto: ```server01:9092,server02:9092,server03:9092```|
| key.serializer      | Clase utilizada para serializar las keys.     |
| value.serializer       | Clase utilizada para serializar los mensajes.  |

Existen varios serializadores ya implementados:

| Deserializador     | Clase    |
| :------------- | :------------- |
| String       | org.apache.kafka.common.serialization.StringSerializer |
| Long       | org.apache.kafka.common.serialization.LongSerializer |
| Integer       | org.apache.kafka.common.serialization.IntegerSerializer |
| Double       | org.apache.kafka.common.serialization.DoubleSerializer |
| Bytes       | org.apache.kafka.common.serialization.BytesSerializer |
| ByteArray       | org.apache.kafka.common.serialization.ByteArraySerializer |
| ByteBuffer      | org.apache.kafka.common.serialization.ByteBufferSerializer |

Si se desea implementar uno propio se debe utilizar la interfaz:[org.apache.kafka.common.serialization.Serializer](https://github.com/apache/kafka/blob/trunk/clients/src/main/java/org/apache/kafka/common/serialization/Serializer.java)

Hay varias propiedades que resultan de interés en el productor y son las siguientes:

| Propiedad     | Descripción     |
| :------------- | :------------- |
| batch.size      |  Número de mensajes que el productor almacenara antes de enviarlos en lotes al broker.|
| linger.ms      |   Número máximo de espera a que se acumulen los mensajes especificados en **batch.size**, antes de enviar los mensajes.|
| acks      | Nivel de asentimiento definido por el productor.|

Lista completa de las configuraciones del productor: [Configuración Productor](http://kafka.apache.org/documentation.html#producerconfigs).

## Configuración Consumidor
Las principales propiedades para el consumidor son las siguientes:

| Propiedad     | Descripción     |
| :------------- | :------------- |
| bootstrap.servers  | Lista de brokers de Kafka separados por coma e indicando el puerto: ```server01:9092,server02:9092,server03:9092```|
| key.deserializer      | Clase utilizada para deserializar las keys.     |
| value.deserializer       | Clase utilizada para deserializar los mensajes.  |
| group.id | String que identifica al consumidor dentro de un grupo de consumidores.|


Existen varios deserializadores ya implementados:

| Deserializador     | Clase    |
| :------------- | :------------- |
| String       | org.apache.kafka.common.serialization.StringDeserializer |
| Long       | org.apache.kafka.common.serialization.LongDeserializer |
| Integer       | org.apache.kafka.common.serialization.IntegerDeserializer |
| Double       | org.apache.kafka.common.serialization.DoubleDeserializer |
| Bytes       | org.apache.kafka.common.serialization.BytesDeserializer |
| ByteArray       | org.apache.kafka.common.serialization.ByteArrayDeserializer |
| BytesBuffer      | org.apache.kafka.common.serialization.ByteBufferDeserializer |

Si se desea implementar uno propio se debe utilizar la interfaz: [org.apache.kafka.common.serialization.Deserializer](https://github.com/apache/kafka/blob/trunk/clients/src/main/java/org/apache/kafka/common/serialization/Deserializer.java)

Lista completa de las configuraciones del consumidor: [Configuración Consumidor](http://kafka.apache.org/documentation.html#newconsumerconfigs).

# Operación

## Crecimiento del Cluster

En esta sección vamos a tratar cómo expandir un cluster de Kafka ya formado para distribuir la carga y de esta forma conseguir un mayor rendimiento.

Para hacer crecer un cluster solamente tenemos que añadir un nuevo broker con un nuevo identificador único. Este nuevo broker se unirá al cluster utilizando ZooKeeper, pero no empezara a recibir datos hasta que le asignemos particiones. Utilizaremos la herramienta ```kafka-reassign-partitions.sh```para esta operación.

Mediante esta herramienta podemos incrementar el número de réplicas que queremos para una partición y la distribución de en qué broker queremos asignar cada réplica. Esta operación vimos cómo funcionaba anteriormente en la sección de *Configuración* en la sesión de *Topics*, y mediante un fichero en formato JSON podíamos indicar donde asignabamos cada replica.

```json
{"version":1, "partitions":[{"topic":"my-topic", "partition":0, "replicas":[1,2,3]}]}
```

La herramienta *kafka-reassign-partitions.sh* nos permite generar este fichero de manera automática:

  1. Indicamos en un fichero JSON los topics que deseamos mover, creando un fichero *topics-move.json* :

  ```json
  {"topics":[{"topic":"topic1"},{"topic":"topic2"}], "version":1}
  ```
  2. Una vez tenemos el fichero, utilizamos el comando indicando el identificador de los brokers donde queremos mover los topics.

  ```
  bin/kafka-reassign-partitions.sh --zookeeper localhost:2181 --topics-to-move-json-file topics-move.json --broker-list "5,6" --generate
  ```

  Este comando genera una salida como la que utilizamos en la sesión anteriormente, copiamos la salida y creamos un fichero *mover-replicas.json*.

  ```json
  {"version":1, "partitions":[
        {"topic":"topic1", "partition":0, "replicas":[5,6]},
        {"topic":"topic1", "partition":1, "replicas":[5,6]},
        {"topic":"topic2", "partition":0, "replicas":[5,6]},
        {"topic":"topic2", "partition":1, "replicas":[5,6]}
  ]}
  ```
  3. Finalmente, tenemos que ejecutar el comando que ya conocemos para aplicar la nueva configuración.

  ```
  bin/kafka-reassign-partitions.sh --zookeeper localhost:2181   --reassignment-json-file mover-replicas.json --execute
  ```

**Nota:** En ocasiones al mover replicas con datos este proceso puede tardar un poco mientras se migran los logs de un broker al otro. Es importante asegurarse cuando el proceso a finalizado para realizar acciones como eliminar un broker, para ello el comando proporciona la opción **--verify**

## LinkedIn Kafka Tool

En primer lugar, vamos a descargarnos el gestor de paquetes de python.

```
yum install epel-release
```
```
yum install -y python-pip
```

Una vez hemos instalado el gestor ya podemos instalar el paquete de utilidades de LinkedIn:

```
pip install kafka-tools
```

Una vez instalado ya podríamos utilizar la herramienta **kafka-assigner**, aunque puede ser necesario tener que exportar la variable de entorno JAVA_HOME si no esta exportada.

```
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.102-1.b14.el7_2.x86_64/jre/
```

```
kafka-assigner -h
usage: kafka-assigner [-h] -z ZOOKEEPER [-l] [-g] [-e] [-m MOVES]
                     [--sizer {ssh}] [-s] [-d DATADIR] [--skip-ple]
                     [--ple-size PLE_SIZE] [--ple-wait PLE_WAIT]
                     [--tools-path TOOLS_PATH]

                     {trim,elect,set-replication-factor,clone,remove,balance,reorder}
                     ...

Rejigger Kafka cluster partitions

positional arguments:
 {trim,elect,set-replication-factor,clone,remove,balance,reorder}
                       Select manipulation module to use
   trim                Remove partitions from some brokers (reducing RF)
   elect               Reelect partition leaders using preferred replica
                       election
   set-replication-factor
                       Increase the replication factor of the specified
                       topics
   clone               Copy partitions from some brokers to a new broker
                       (increasing RF)
   remove              Move partitions from one broker to one or more other
                       brokers (maintaining RF)
   balance             Rebalance partitions across the cluster
   reorder             Reelect partition leaders using replica reordering

optional arguments:
 -h, --help            show this help message and exit
 -z ZOOKEEPER, --zookeeper ZOOKEEPER
                       Zookeeper path to the cluster (i.e. zk-
                       eat1-kafka.corp:12913/kafka-data-deployment)
 -l, --leadership      Show cluster leadership balance
 -g, --generate        Generate partition reassignment file
 -e, --execute         Execute partition reassignment
 -m MOVES, --moves MOVES
                       Max number of moves per step
 --sizer {ssh}         Select module to use to get partition sizes
 -s, --size            Show partition sizes
 -d DATADIR, --datadir DATADIR
                       Path to the data directory on the broker
 --skip-ple            Skip preferred replica election after finishing moves
 --ple-size PLE_SIZE   Max number of partitions in a single preferred leader
                       election
 --ple-wait PLE_WAIT   Time in seconds to wait between preferred leader
                       elections
 --tools-path TOOLS_PATH
                       Path to Kafka admin utilities, overriding PATH env var
```

Vamos a crear un topic con 30 particiones:

```
bin/kafka-topics.sh --create --partitions 30 --topic test01 --replication-factor 1 --zookeeper localhost
```

Si ejecutamos un *describe* del topic veremos que las particiones están distribuidas entre el broker0 y el broker1.

```
bin/kafka-topics.sh --describe --topic test01 --zookeeper localhost
Topic:test01	PartitionCount:30	ReplicationFactor:1	Configs:
  Topic: test01	Partition: 0	Leader: 1	Replicas: 1	Isr: 1
  Topic: test01	Partition: 1	Leader: 0	Replicas: 0	Isr: 0
  Topic: test01	Partition: 2	Leader: 1	Replicas: 1	Isr: 1
  Topic: test01	Partition: 3	Leader: 0	Replicas: 0	Isr: 0
  Topic: test01	Partition: 4	Leader: 1	Replicas: 1	Isr: 1
  Topic: test01	Partition: 5	Leader: 0	Replicas: 0	Isr: 0
  Topic: test01	Partition: 6	Leader: 1	Replicas: 1	Isr: 1
  Topic: test01	Partition: 7	Leader: 0	Replicas: 0	Isr: 0
  Topic: test01	Partition: 8	Leader: 1	Replicas: 1	Isr: 1
  Topic: test01	Partition: 9	Leader: 0	Replicas: 0	Isr: 0
  Topic: test01	Partition: 10	Leader: 1	Replicas: 1	Isr: 1
  Topic: test01	Partition: 11	Leader: 0	Replicas: 0	Isr: 0
  Topic: test01	Partition: 12	Leader: 1	Replicas: 1	Isr: 1
  Topic: test01	Partition: 13	Leader: 0	Replicas: 0	Isr: 0
  Topic: test01	Partition: 14	Leader: 1	Replicas: 1	Isr: 1
  Topic: test01	Partition: 15	Leader: 0	Replicas: 0	Isr: 0
  Topic: test01	Partition: 16	Leader: 1	Replicas: 1	Isr: 1
  Topic: test01	Partition: 17	Leader: 0	Replicas: 0	Isr: 0
  Topic: test01	Partition: 18	Leader: 1	Replicas: 1	Isr: 1
  Topic: test01	Partition: 19	Leader: 0	Replicas: 0	Isr: 0
  Topic: test01	Partition: 20	Leader: 1	Replicas: 1	Isr: 1
  Topic: test01	Partition: 21	Leader: 0	Replicas: 0	Isr: 0
  Topic: test01	Partition: 22	Leader: 1	Replicas: 1	Isr: 1
  Topic: test01	Partition: 23	Leader: 0	Replicas: 0	Isr: 0
  Topic: test01	Partition: 24	Leader: 1	Replicas: 1	Isr: 1
  Topic: test01	Partition: 25	Leader: 0	Replicas: 0	Isr: 0
  Topic: test01	Partition: 26	Leader: 1	Replicas: 1	Isr: 1
  Topic: test01	Partition: 27	Leader: 0	Replicas: 0	Isr: 0
  Topic: test01	Partition: 28	Leader: 1	Replicas: 1	Isr: 1
  Topic: test01	Partition: 29	Leader: 0	Replicas: 0	Isr: 0
```

Si ejecutamos el siguiente comando:

```
kafka-assigner -z localhost:2181 --tools-path /root/kafka_2.11-0.10.0.1/bin/ --generate -e balance --types even
```

Nos genera un DRY-RUN de la ejecución que se va a realizar. Si estamos conformes con el nuevo particionado tenemos que cambiar la opción **--generate** por **--execute**, y el comando comenzara a ejecutar el reparticionado poco a poco, para que el sistema no se vea muy afectado.

```
kafka-assigner -z localhost:2181 --tools-path /root/kafka_2.11-0.10.0.1/bin/ --execute -e balance --types even
```

```
bin/kafka-topics.sh --describe --topic test01 --zookeeper localhost
Topic:test01	PartitionCount:30	ReplicationFactor:1	Configs:
	Topic: test01	Partition: 0	Leader: 2	Replicas: 2	Isr: 2
	Topic: test01	Partition: 1	Leader: 2	Replicas: 2	Isr: 2
	Topic: test01	Partition: 2	Leader: 2	Replicas: 2	Isr: 2
	Topic: test01	Partition: 3	Leader: 2	Replicas: 2	Isr: 2
	Topic: test01	Partition: 4	Leader: 2	Replicas: 2	Isr: 2
	Topic: test01	Partition: 5	Leader: 2	Replicas: 2	Isr: 2
	Topic: test01	Partition: 6	Leader: 2	Replicas: 2	Isr: 2
	Topic: test01	Partition: 7	Leader: 2	Replicas: 2	Isr: 2
	Topic: test01	Partition: 8	Leader: 2	Replicas: 2	Isr: 2
	Topic: test01	Partition: 9	Leader: 2	Replicas: 2	Isr: 2
	Topic: test01	Partition: 10	Leader: 1	Replicas: 1	Isr: 1
	Topic: test01	Partition: 11	Leader: 0	Replicas: 0	Isr: 0
	Topic: test01	Partition: 12	Leader: 1	Replicas: 1	Isr: 1
	Topic: test01	Partition: 13	Leader: 0	Replicas: 0	Isr: 0
	Topic: test01	Partition: 14	Leader: 1	Replicas: 1	Isr: 1
	Topic: test01	Partition: 15	Leader: 0	Replicas: 0	Isr: 0
	Topic: test01	Partition: 16	Leader: 1	Replicas: 1	Isr: 1
	Topic: test01	Partition: 17	Leader: 0	Replicas: 0	Isr: 0
	Topic: test01	Partition: 18	Leader: 1	Replicas: 1	Isr: 1
	Topic: test01	Partition: 19	Leader: 0	Replicas: 0	Isr: 0
	Topic: test01	Partition: 20	Leader: 1	Replicas: 1	Isr: 1
	Topic: test01	Partition: 21	Leader: 0	Replicas: 0	Isr: 0
	Topic: test01	Partition: 22	Leader: 1	Replicas: 1	Isr: 1
	Topic: test01	Partition: 23	Leader: 0	Replicas: 0	Isr: 0
	Topic: test01	Partition: 24	Leader: 1	Replicas: 1	Isr: 1
	Topic: test01	Partition: 25	Leader: 0	Replicas: 0	Isr: 0
	Topic: test01	Partition: 26	Leader: 1	Replicas: 1	Isr: 1
	Topic: test01	Partition: 27	Leader: 0	Replicas: 0	Isr: 0
	Topic: test01	Partition: 28	Leader: 1	Replicas: 1	Isr: 1
	Topic: test01	Partition: 29	Leader: 0	Replicas: 0	Isr: 0
```

# Monitorización

## Monitorización Herramientas

#### Monitorización CentOS/Ubuntu: Script Ruby
En primer lugar vamos a descargar la última versión de CentOS 7.
```
http://centos.mirror.xtratelecom.es/7/isos/x86_64/CentOS-7-x86_64-Minimal-1511.iso
```

Una vez instalado iniciamos sesión con el usuario root y hacemos un update del sistema.

```
yum update
```

Cuando hayamos actualizado el sistema vamos a instalar Ruby, utilizando para ello RVM, antes de instalar el software debemos de descargar las claves GPG.

```
gpg2 --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
```

```
\curl -sSL https://get.rvm.io | bash -s stable --ruby
```
Finalmente para tener el entorno de ruby cargado deberemos ejecutar:

```
source /usr/local/rvm/scripts/rvm
```

Una vez tenemos ruby instalado y configurado, debemos instalar algunas gemas que necesita nuestro script para su correcto funcionamiento:

```
gem install mincore
```

```
gem install poseidon
```

```
gem install getopt
```

```
gem install json
```

Cuando hayamos instalados estas gemas, únicamente nos quedará instalar un herramienta conocida como **pcstat** que nos es de utilidad para analizar las páginas en cache.

```
curl -L -o pcstat https://github.com/tobert/pcstat/raw/2014-05-02-01/pcstat.x86_64
```

```
chmod +x pcstat
```

```
mv pcstat /usr/bin/
```

Ahora ya! Podemos probar el script, que podemos encontrar al final de este documento.

```
ruby rb_kmonitor.rb  -h
Usage: rb_kmonitor.rb -i interval [-t topic] [-b broker list] [-q query]
		-i interval	-> [60] 		Interval of monitorization
		-t topic	-> [rb_monitor]		Topic to produce
		-b brokers	-> [localhost:9092]	Kafka broker list separated values
		-q query	-> [all]		Query for specific metric
```
**Nota:** El script buscará los logs de Kafka en los directorios dentro de **/tmp** que cumpla la siguiente expresión regular:

```regex
\/tmp\/kafka-logs(?:-\d)?
```

Finalmente, para ejecutar el script utilizaremos:

```
ruby rb_kmonitor.rb -i 5 -t monitor -b localhost:9092
```

Si, consumimos del topic que hemos indicado veremos mensajes como los siguientes:
```json
{"timestamp":1474139509,"sensor_name":"localhost.localdomain","monitor":"cache_pages_size","value":30,"type":"system","unit":"MB"}
{"timestamp":1474139509,"sensor_name":"localhost.localdomain","monitor":"cache_pages_usage","value":13.457287738915616,"type":"system","unit":"%"}
{"timestamp":1474139509,"sensor_name":"localhost.localdomain","monitor":"cache_hits","value":100.0,"type":"system","unit":"%"}
{"timestamp":1474139509,"sensor_name":"localhost.localdomain","monitor":"disk_read_hits","value":0.0,"type":"system","unit":"%"}
{"timestamp":1474139509,"sensor_name":"localhost.localdomain","monitor":"disk_write_hits","value":100.0,"type":"system","unit":"%"}
{"timestamp":1474139509,"sensor_name":"localhost.localdomain","monitor":"disk_usage","value":23.91457479508197,"type":"system","unit":"%"}
{"timestamp":1474139509,"sensor_name":"localhost.localdomain","monitor":"network_eno16777736_tx","value":0,"type":"system","unit":"bps"}
{"timestamp":1474139509,"sensor_name":"localhost.localdomain","monitor":"network_eno16777736_rx","value":0,"type":"system","unit":"bps"}
```

* **rb_kmonitor.rb**

```ruby
require 'mincore'
require 'json'
require 'poseidon'
require 'socket'
require 'getopt/std'

@sensor_name = Socket.gethostname

opt = Getopt::Std.getopts("i:t:b:hq:")

# Print message
def logit(message)
	printf("%s\n", message)
end

# Print usage
def usage
	logit "Usage: rb_kmonitor.rb -i interval [-t topic] [-b broker list] [-q query]"
	logit "		-i interval	-> [60] 		Interval of monitorization"
	logit "		-t topic	-> [rb_monitor]		Topic to produce"
	logit "		-b brokers	-> [localhost:9092]	Kafka broker list separated values"
	logit "		-q query	-> [all]		Query for specific metric"
	exit 0
end

usage if opt["h"]

interval = if opt["i"].nil? then 60 elsif /\d+/.match(opt["i"].to_s) then opt["i"].to_i else logit "ERROR : '#{opt["i"].to_s}' NaN"; exit 0 end - 2

topic = if opt["t"].nil? then "rb_monitor" else opt["t"].to_s.strip end

broker_list = if opt["b"].nil? then "localhost:9092" else opt["b"].to_s.strip.split(/\s/) end
# Get kafka services PIDs
stats = {}

# Swap usage
def get_swap_usage ()
	swap_usage = {}

	swap_data = `cat /proc/swaps | sed '1d' | cut -f2-3`.split(/\s/)

	swap_usage["timestamp"] = @timestamp
	swap_usage["sensor_name"] = @sensor_name
	swap_usage["monitor"] = "swap_usage"
	swap_usage["value"] = (swap_data[1].to_f/swap_data[0].to_f)*100
	swap_usage["type"] = "system"
	swap_usage["unit"] = "%"

	return swap_usage
end

def get_io_operations()

	io_operations = []

	stats = `cat /proc/diskstats|grep 'sd[a-z]'`.split(/\n/)
	sleep(1)
	stats_delta = `cat /proc/diskstats|grep 'sd[a-z]'`.split(/\n/)

	total = 0
	delta_rms = 0
	delta_wms = 0

	stats.each do |line|
		if m = /\s+\d+\s+\d+\s+(?<dev>sd[a-z])\s+\d+\s+\d+\s+\d+\s+(?<rms>\d+)\s+\d+\s+\d+\s+\d+\s+(?<wms>\d+)\s+\d+\s+\d+\s+(?<tms>\d+)/.match(line)
			total = m["rms"].to_i + m["wms"].to_i
			delta_rms = m["rms"].to_i
			delta_wms = m["wms"].to_i
		end
	end

	stats_delta.each do |line|
		if m = /\s+\d+\s+\d+\s+(?<dev>sd[a-z])\s+\d+\s+\d+\s+\d+\s+(?<rms>\d+)\s+\d+\s+\d+\s+\d+\s+(?<wms>\d+)\s+\d+\s+\d+\s+(?<tms>\d+)/.match(line)
			total_delta = (m["rms"].to_i + m["wms"].to_i) - total
			total =  if total_delta == 0 then total else total_delta end

			stats_r = {}
			stats_r["timestamp"] = @timestamp
			stats_r["sensor_name"] = @sensor_name
			stats_r["monitor"] = "disk_read_hits"
			stats_r["value"] = ((m["rms"].to_i - delta_rms)/total.to_f)*100
			stats_r["type"] = "system"
			stats_r["unit"] = "%"

			io_operations << stats_r

			stats_w = {}
			stats_w["timestamp"] = @timestamp
			stats_w["sensor_name"] = @sensor_name
			stats_w["monitor"] = "disk_write_hits"
			stats_w["value"] = ((m["wms"].to_i - delta_wms)/total.to_f)*100
			stats_w["type"] = "system"
			stats_w["unit"] = "%"

			io_operations << stats_w
		end
	end

	return io_operations
end

# Disk usage
def get_disk_usage ()
	# Get disk usage
	#usage = `df . | awk '{print $5}' | sed -ne 2p |cut -d"%" -f1`
	disk_stat = `df . | awk '{print $2" "$3}' | sed -ne 2p`.split(/\s/)

	disk_usage = {}
	disk_usage["timestamp"] = @timestamp
	disk_usage["sensor_name"] = @sensor_name
	disk_usage["monitor"] = "disk_usage"
	disk_usage["value"] = (disk_stat[1].to_f/disk_stat[0].to_f) * 100
	disk_usage["type"] = "system"
	disk_usage["unit"] = "%"

	return disk_usage
end

# Network usage
def get_network_stats ()

	network_usage = []

	# Get networks statistics
	networks = `ls /sys/class/net`.split(/\n/)

	network_stats = {}

	networks.each do |network|
		if not /lo/.match(network)
			network_stats[network] = {}
			network_stats[network]["txbps"] = `cat /sys/class/net/#{network}/statistics/tx_bytes`.strip.to_i
			network_stats[network]["rxbps"] = `cat /sys/class/net/#{network}/statistics/rx_bytes`.strip.to_i
		end
	end

	sleep(1)

	networks.each do |network|

		if not /lo/.match(network)
			stats_tx = {}
			stats_tx["timestamp"] = @timestamp
			stats_tx["sensor_name"] = @sensor_name
			stats_tx["monitor"] = "network_" + network + "_tx"
			stats_tx["value"] = `cat /sys/class/net/#{network}/statistics/tx_bytes`.strip.to_i - network_stats[network]["txbps"]
			stats_tx["type"] = "system"
			stats_tx["unit"] = "bps"

			network_usage << stats_tx

			stats_rx = {}
			stats_rx["timestamp"] = @timestamp
			stats_rx["sensor_name"] = @sensor_name
			stats_rx["monitor"] = "network_" + network + "_rx"
			stats_rx["value"] = `cat /sys/class/net/#{network}/statistics/rx_bytes`.strip.to_i - network_stats[network]["rxbps"]
			stats_rx["type"] = "system"
			stats_rx["unit"] = "bps"

			network_usage << stats_rx
		end
	end

	return network_usage
end

# Cache pages usage
def get_cache_pages_stats ()

	cache_pages_stats = []

	total = 0
	cached = 0

	filter = ["recovery-point-offset-checkpoint", "cleaner-offset-checkpoint", "replication-offset-checkpoint", "meta.properties", "__consumer_offsets"]

	# TODO Change kafka directory!!
	Dir.glob("/tmp/*") do |directory|
		if File.directory?(directory) and /\/tmp\/kafka-logs(?:-\d)?/.match(directory)

			Dir.glob(directory + "/*") do |kafka_dir|
				if not filter.any? { |s| kafka_dir.include? s}

					Dir.glob(kafka_dir + "/*") do |topic_dir|

						if not File.stat(topic_dir).size == 0
							data = JSON.parse(`pcstat --json #{topic_dir}`)
							total += data[0]["pages"]
							cached += data[0]["cached"]
						end
					end
				end
			end
		end
	end

	cache_pages_size = {}
	cache_pages_size["timestamp"] = @timestamp
	cache_pages_size["sensor_name"] = @sensor_name
	cache_pages_size["monitor"] = "cache_pages_size"
	cache_pages_size["value"] = (total*File.PAGESIZE) >> 20
	cache_pages_size["type"] = "system"
	cache_pages_size["unit"] = "MB"

	cache_pages_stats << cache_pages_size

	cache_pages_usage = {}
	cache_pages_usage["timestamp"] = @timestamp
	cache_pages_usage["sensor_name"] = @sensor_name
	cache_pages_usage["monitor"] = "cache_pages_usage"
	cache_pages_usage["value"] = if total > 0 then (cached/total.to_f)*100  else 0 end
	cache_pages_usage["type"] = "system"
	cache_pages_usage["unit"] = "%"

	cache_pages_stats << cache_pages_usage

	return cache_pages_stats
end

# Cache hits
def get_cache_hits_usage ()
	# Enable kernel functions trace
	File.open('/proc/sys/kernel/ftrace_enabled','w') { |f| f.write("1") }

	kernel_functions = ["mark_page_accessed", "mark_buffer_dirty", "add_to_page_cache_lru", "account_page_dirtied"]

	`printf "mark_page_accessed\nmark_buffer_dirty\nadd_to_page_cache_lru\naccount_page_dirtied\n" > /sys/kernel/debug/tracing/set_ftrace_filter
	if ! echo 1 > /sys/kernel/debug/tracing/function_profile_enabled; then
	    echo > /sys/kernel/debug/tracing/set_ftrace_filter
	    die "ERROR: enabling function profiling. Have CONFIG_FUNCTION_PROFILER? Exiting."
	fi`

	mem_stats = {}

	File.open('/proc/meminfo', 'r') do |f|
		meminfo = f.read.split(/\n/).each do |a|
			if (m = /^(?<name>Cached|Buffers):\s+(?<value>\d{1,})\skB/.match(a))
				mem_stats[m["name"]] = (m["value"].to_i >> 10).to_s + " MB"
			end
		end
	end

	total = 0
	misses = 0

	Dir.glob("/sys/kernel/debug/tracing/trace_stat/*") do |file|

		File.open(file, 'r') do |f|
			cache_stats = {}

			f.read.split(/\n/).each do |a|

				if (m = /(?<function>mark_page_accessed|mark_buffer_dirty|add_to_page_cache_lru|account_page_dirtied)\s+(?<value>\d+).+/.match(a))
					cache_stats[m["function"]] = m["value"].to_i
				end
			end

			misses = (cache_stats["add_to_page_cache_lru"].to_i - cache_stats["account_page_dirtied"].to_i)
			total = (cache_stats["mark_page_accessed"].to_i - cache_stats["mark_buffer_dirty"].to_i)

			mem_stats["cache_misses"] = if misses < 0 then 0 else misses end
			mem_stats["cache_hits"] = total - mem_stats["cache_misses"]
			mem_stats["cache_ratio"] = if total > 0 then 100*(mem_stats["cache_hits"]/total.to_f) else 0 end
		end
	end

	ratio = 100*(mem_stats["cache_hits"]/total.to_f)

	cache_hits = {}
	cache_hits["timestamp"] = @timestamp
	cache_hits["sensor_name"] = @sensor_name
	cache_hits["monitor"] = "cache_hits"
	cache_hits["value"] = if ratio > 0 then ratio else 0 end
	cache_hits["type"] = "system"
	cache_hits["unit"] = "%"

	`echo 0 > /sys/kernel/debug/tracing/function_profile_enabled`
	`echo 1 > /sys/kernel/debug/tracing/function_profile_enabled`

	return cache_hits
end

if opt["q"]

	@timestamp = Time.now.getutc().to_i

	query = opt["q"]

	case query
		when "swap_usage"
			p get_swap_usage.to_json.to_s
		when "cache_pages_usage"
			p get_cache_pages_stats.to_json.to_s
		when "cache_hits"
			p get_cache_hits_usage.to_json.to_s
		when "disk_usage"
			p get_disk_usage.to_json.to_s
		when "io_operations"
			p get_io_operations.to_json.to_s
		when "network_stats"
			p get_network_stats.to_json.to_s
	end
else
	# Main
	producer = Poseidon::Producer.new(broker_list, "monitor_producer")

	while true

		@timestamp = Time.now.getutc().to_i

		messages = []
		messages << Poseidon::MessageToSend.new(topic, get_swap_usage.to_json)

		get_cache_pages_stats.each do |data|
			messages << Poseidon::MessageToSend.new(topic, data.to_json)
		end

		messages << Poseidon::MessageToSend.new(topic, get_cache_hits_usage.to_json)

		get_io_operations.each do |data|
			messages << Poseidon::MessageToSend.new(topic, data.to_json)
		end

		messages << Poseidon::MessageToSend.new(topic, get_disk_usage.to_json)

		get_network_stats.each do |data|
			messages << Poseidon::MessageToSend.new(topic, data.to_json)
		end

		producer.send_messages(messages)

		sleep(interval)

	end
end
```

#### Consultando LAG
Kafka nos proporciona una herramienta para consultar el LAG que tienen los distintos consumidores. Para calcular el LAG únicamente se necesita saber el último offset de una partición y el offset actual de un grupo de consumidores para esa partición.

Utilizando la herramienta **kafka-consumer-groups.sh** podemos listar los grupos de consumidores actuales y solicitar de que particiones y por que offset están cada uno de ellos.

```
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --list
```
Este comando nos muestra los grupos de consumidores, dando una salida como la siguiente:

```
test-consumer-group-2
console-consumer-69989
```
Para preguntar por un grupo de consumidor en concreto deberíamos ejecutar lo siguiente:

```
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group console-consumer-69989
```

Y nos mostrará una salida con el siguiente formato:

```
GROUP                          TOPIC                          PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG             OWNER
test-consumer-group-2          output                         0          5032656         14075668        9043012         consumer-1_/127.0.0.1
```

Cual es el offset de la partición del topic del que se encuentra leyendo el consumidor, también nos muestra el último offset y la diferencia entre ellos, que sería el LAG.

## JMX & JConsole

Si queremos activar el puerto de JMX remoto en una aplicación JAVA tenemos que utilizar los distintos flags:

```
 -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.port=9595
 ```

**Nota:** JMX tiene la opción de utilizar autenticación pero en nuestro caso no será necesario.

Kafka verifica si existe la variable de entorno JMX_PORT, si esta definida el Broker incorpora los flags mencionados automáticamente en su ejecución. Para definir la variable de entorno podemos ejecutar lo siguiente:

```
export JMX_PORT=9595
```

Si queremos que el Broker exponga el puerto en una dirección IP concreta debemos añadir el flag:

```
-Djava.rmi.server.hostname=192.168.1.40
```

Una vez tenemos el Broker configurado con las opciones de JMX, podemos utilizar la herramienta **jconsole** para conectarnos al proceso y monitorizarlo.

```
jconsole
```

**Nota:** La herramienta jconsole viene en la distribución de Java.

## Yahoo Kafka Manager UI

En primer lugar vamos a descargar la última versión del software desde el enlace que se encuentra en el repositorio oficial [Kafka Manager Github](https://github.com/yahoo/kafka-manager).

```
wget https://github.com/yahoo/kafka-manager/archive/1.3.3.18.tar.gz
```

Una vez descargado tenemos que descomprimir el fichero y nos creada una carpeta con el siguiente nombre *kafka-manager-1.3.1.8*

```
tar -xvf 1.3.3.18.tar.gz
```

Cuando tengamos la carpeta únicamente tenemos que construir la distribución utilizando el siguiente comando:

```
./sbt clean dist
```

**Nota:** Si no se suele usar sbt puede que la construcción dure unos minutos.

Al terminar el comando nos habra construido un fichero ZIP, que vamos a descomprimir, y entrar en la carpeta que se genera.

```
unzip target/universal/kafka-manager-1.3.3.18.zip
```
```
cd kafka-manager-1.3.3.18/
```

Tenemos que editar el fichero de configuración en la ruta *conf/application.conf* y editar la linea donde se indica el servidor de ZooKeeper.

```
kafka-manager.zkhosts="kafka-manager-zookeeper:2181"
```

Una vez ya modificado el fichero de configuración podemos ejecutar el servicio.

```
bin/kafka-manager
```

Para acceder a la interfaz web debemos usar la URL: http://localhost:9000

# Seguridad

## Encriptación SSL

##### 1. Generación de una clave SSL y certificado por cada Kafka Broker

El primer paso para desplegar HTTPS es generar una clave y certificado por cada máquina donde tengamos instalado un broker. Para realizar esta tarea podemos usar la herramienta de java *keytool*, generaremos la clave en un keystore temporal del cual podemos exportar y firmar posteriormente.


```
 keytool -keystore server.keystore.jks -alias localhost -validity 360 -genkey
```

 * keystore: El keystore donde se almacenará la clave privada del certificado.
 * validity: La validez del certificado en días.

La herramienta nos solicitará la siguiente información:

```
Introduzca la contraseña del almacén de claves:
Volver a escribir la contraseña nueva:
¿Cuáles son su nombre y su apellido?
  [Unknown]:  Andres
¿Cuál es el nombre de su unidad de organización?
  [Unknown]:  openwebinars
¿Cuál es el nombre de su organización?
  [Unknown]:  openwebinars
¿Cuál es el nombre de su ciudad o localidad?
  [Unknown]:  Sevilla
¿Cuál es el nombre de su estado o provincia?
  [Unknown]:  Andalucia
¿Cuál es el código de país de dos letras de la unidad?
  [Unknown]:  ES
¿Es correcto CN=Andres, OU=openwebinars, O=openwebinars, L=Sevilla, ST=Andalucia, C=ES?
  [no]:  si

Introduzca la contraseña de clave para <localhost>
	(INTRO si es la misma contraseña que la del almacén de claves):
```

**Nota:** Como contraseña se ha usado *openwebinars*.

Si queremos verificar el certificado podemos usar:

```
keytool -list -v -keystore server.keystore.jks
```

##### 2. Creación de nuestra propia autoridad certificadora (CA)

La CA es la responsable de firmar los certificados, la firma del certificado debe realizarse por cada maquina del cluster.

Utilizando el siguiente comando generamos un CA que simplemente es una par de clave publica/privada y un certificado que nos permitirán firmar otros certificados.

```
openssl req -new -x509 -keyout ca-key -out ca-cert -days 365
```

La herramienta nos solicitará la siguiente información:

```
Generating a 1024 bit RSA private key
..........++++++
.........................................++++++
writing new private key to 'ca-key'
Enter PEM pass phrase:
Verifying - Enter PEM pass phrase:
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:ES
State or Province Name (full name) [Some-State]:Sevilla
Locality Name (eg, city) []:Sevilla
Organization Name (eg, company) [Internet Widgits Pty Ltd]:openwebinars
Organizational Unit Name (eg, section) []:openwebinars
Common Name (e.g. server FQDN or YOUR name) []:openwebinars
Email Address []:openwebinars@openwebinars.org
```

**Nota:** La contraseña usada para la contraseña de paso es *openwebinars*.

Este comando nos generara dos ficheros *ca-cert* y *ca-key* que utilizaremos seguidamente.

El siguiente paso es añadir el certificar al almacén de confianza de los clientes para que estos confíen en ese certificado.

```
keytool -keystore server.truststore.jks -alias CARoot -import -file ca-cert
```

**Nota:** En el comando usamos de contraseña nuevamente *openwebinars* y contestamos que confiamos en el certificado.

##### 3. Firmado del certificado
En este punto vamos a firmar todos los certificaos generados en el primer punto, con la CA generada en el punto 2.

En primer lugar debemos exportar el certificado que tenemos almacenado en el keystore.

```
keytool -keystore server.keystore.jks -alias localhost -certreq -file cert-file
```

Después, firmamos con la CA:
```
openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 360 -CAcreateserial -passin pass:openwebinars
```

Finalmente, importamos de nuevo el certificado del CA y el certificado firmado dentro del keystore.

```
keytool -keystore server.keystore.jks -alias CARoot -import -file ca-cert
```

```
keytool -keystore server.keystore.jks -alias localhost -import -file cert-signed
```

* keystore: Localización del keystore.
* ca-cert: El certificado del CA.
* ca-key: La clave privada del CA.
* ca-password: Clave de paso del CA (*openwebinars*)
* cert-file: El certificado del servido sin firmar.
* cert-signed: El certificado del servidor firmado.

##### 4. Configuración de los Brokers

Kafka proporciona la posibilidad para crear conexiones con multiples puertos utilizando la propiedad **listeners**.

```
listeners=SSL://:9102
```

**Nota:** Se pueden proporcionar varios listeners de distinto tipo, podemos decir que escuche en SSL en el puerto 9102 y en claro en el puerto 9092:
```
listeners=SSL://:9102,PLAINTEXT://:9092
```

También debemos configurar el fichero de cada broker para que utilice los almacenes de claves, añadiendo las siguientes propiedades en el fichero *server.properties*.

```
ssl.keystore.location=server.keystore.jks
ssl.keystore.password=openwebinars
ssl.key.password=openwebinars

ssl.truststore.location=server.truststore.jks
ssl.truststore.password=openwebinars

security.inter.broker.protocol=SSL
```

Una forma rapida de verificar si funciona correctamente es utilizar el comando:

```
openssl s_client -debug -connect localhost:9102 -tls1
```

Que nos muestrará el certificado del servidor.

##### 5. Configuración clientes de Kafka

Una vez configurado los brokers es hora de configurar los clientes para que cifren la información, para ello debemos crear un truststore para los clientes donde importaremos el certificado de la CA.

```
keytool -keystore client.truststore.jks -alias CARoot -import -file ca-cert
```

También, añadiremos la configuración en los ficheros de configuración del consumidor y del productor.

```
security.protocol=SSL
ssl.truststore.location=client.truststore.jks
ssl.truststore.password=openwebinars
```

## Autenticación usando SSL

Si queremos habilitar la autenticación entre los clientes y los brokers debemos configurar la propiedad **ssl.client.auth=required** y seguir los siguientes pasos:

1. Debemos generar un keystore en el cliente y generar un certificado propio, como hicimos en el primer punto.
```
keytool -keystore client.keystore.jks -alias localhost -validity 360 -genkey
```
2. Posteriormente debemos exportar su certificado y firmarlo con el certificado del CA.
```
keytool -keystore client.keystore.jks  -alias localhost -certreq -file cert-file-client
```
```
openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file-client  -out cert-signed-client -days 360 -CAcreateserial -passin pass:openwebinars
```
3. Finalmente, debemos importar el certificado del cliente firmado en el truststore de cada broker.
```
keytool -keystore server.truststore.jks  -alias localhost -import -file cert-signed-client
```

# Kafka Docker

Para trabajar con Kafka en docker vamos a utilizar la imagen de *wurstmeister*.

[Kafka Docker Hub](https://hub.docker.com/r/wurstmeister/kafka/)

```
docker pull wurstmeister/kafka:0.10.2.0
```

El parametro que hay que tener en cuenta para configurar un broker de dentro de un docker, es el *listeners* que anunciamos cuando levantamos el docker. Este docker esta preparado para poder configurar las opciones de configuración de Kafka utilizando las variables de entorno añadiendole el sufijo **KAFKA_**.

KAFKA_ADVERTISED_LISTENERS

Lo importante que tenemos que tener en cuenta es que el listener que debemos anunciar es el de la maquina fisica donde se ejecuta el docker, es decir la interfaz principal de nuestro ordenador. Otro parametro que debemos configurar es la dirección de nuestro servidor ZooKeeper.

KAFKA_ZOOKEEPER_CONNECT

```
docker run -it -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://${MAIN_IP_ADDRESS}:9092 -e KAFKA_ZOOKEEPER_CONNECT=${ZOOKEEPER_SERVER} -p 9092:9092 wurstmeister/kafka:0.10.2.0
```

# Connectors

## MQTT Kafka Source Connector

En primer lugar vamos a descargar el proyecto del MQTT connector y haremos la build.

```
git clone https://github.com/evokly/kafka-connect-mqtt.git
```

```
cd kafka-connect-mqtt; ./gradlew clean jar copyRuntimeLibs
```

Una vez generado el jar lo copiamos dentro de la carpeta `libs` de la distribución de Kafka.

```
cp build/libs/kafka-connect-mqtt-1.1-SNAPSHOT.jar ~/kafka_2.12-2.1.0/libs/
cp build/output/lib/org.eclipse.paho.client.mqttv3-1.0.2.jar ~/kafka_2.12-2.1.0/libs/
```

Una vez añadida las dependencias tenemos que [iniciar un servidor ZooKeeper y un broker de Kafka](https://github.com/andresgomezfrr/big-data-md/blob/master/kafka/1.arquitectura/zookeeper-kafka.md).

Creamos el fichero que usaremos para configurar nuestro conector mqtt:
**mqtt.properties**

```properties
name=mqtt
connector.class=com.evokly.kafka.connect.mqtt.MqttSourceConnector
tasks.max=1

#Settings
kafka.topic=mqtt
mqtt.client_id=my-id
mqtt.clean_session=true
mqtt.connection_timeout=30
mqtt.keep_alive_interval=60
mqtt.server_uris=tcp://broker.hivemq.com:1883
mqtt.topic=testtopic/1
mqtt.qos=1
message_processor_class=com.evokly.kafka.connect.mqtt.sample.DumbProcessor
```

Vamos a utilizar un broker mqtt público que podemos encontar en el siguiente enlace:

[Broker MQTT](http://www.hivemq.com/try-out/)

Ejecutamos el conector en este caso vamos a ejecutar el modo standalone para testing:

```
kafka_2.12-2.1.0/bin/connect-standalone.sh kafka_2.12-2.1.0/config/connect-standalone.properties mqtt.properties
```

Para enviar mensajes podemos usar la siguiente web, debemos enviar los mensajes al mqtt topic `testtopic/1` .

[Productor online MQTT](http://www.hivemq.com/demos/websocket-client/)

Podemos veriricar su funcionamiento consumiendo del kafka topic `mqtt` utilizando el consumidor de consola.
