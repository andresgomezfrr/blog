---
layout: documentation
title: Druid
permalink: /druid/
---

* Do not remove this line (it will not be displayed)
{:toc}

## Instalación

1. Descargamos y descomprimimos Druid:
  ```
  wget https://ftp.cixug.es/apache/druid/0.20.1/apache-druid-0.20.1-bin.tar.gz && tar -xzf apache-druid-0.20.1-bin.tar.gz&& cd apache-druid-0.20.1
  ```
2. Arrancamos el setup para desarrollo:
  ```
  env DRUID_SKIP_JAVA_CHECK=1 ./bin/start-micro-quickstart  
  ```
3. Accedemos a la interfaz unificada, para comprobar que todo esta funcionando correctamente. [http://localhost:8888/unified-console.html](http://localhost:8888/unified-console.html)

```json
{"sensor_id":3,"temperature":30,"humidity":80,"timestamp":1599758629}
{"sensor_id":1,"temperature":25,"humidity":80,"timestamp":1599758629}
{"sensor_id":2,"temperature":26,"humidity":59,"timestamp":1599758569}
{"sensor_id":2,"temperature":30,"humidity":72,"timestamp":1599758569}
{"sensor_id":2,"temperature":27,"humidity":73,"timestamp":1599758629}
{"sensor_id":2,"temperature":31,"humidity":62,"timestamp":1599758569}
{"sensor_id":3,"temperature":30,"humidity":80,"timestamp":1599758629}
{"sensor_id":2,"temperature":30,"humidity":75,"timestamp":1599758629}
{"sensor_id":3,"temperature":30,"humidity":68,"timestamp":1599758689}
{"sensor_id":1,"temperature":28,"humidity":51,"timestamp":1599758569}
{"sensor_id":1,"temperature":31,"humidity":65,"timestamp":1599758569}
{"sensor_id":3,"temperature":25,"humidity":75,"timestamp":1599758689}
{"sensor_id":1,"temperature":31,"humidity":54,"timestamp":1599758689}
{"sensor_id":1,"temperature":27,"humidity":73,"timestamp":1599758629}
{"sensor_id":1,"temperature":26,"humidity":74,"timestamp":1599758629}
{"sensor_id":1,"temperature":29,"humidity":67,"timestamp":1599758569}
{"sensor_id":1,"temperature":25,"humidity":62,"timestamp":1599758569}
{"sensor_id":2,"temperature":27,"humidity":75,"timestamp":1599758689}
{"sensor_id":3,"temperature":31,"humidity":69,"timestamp":1599758689}
{"sensor_id":2,"temperature":26,"humidity":51,"timestamp":1599758629}
{"sensor_id":1,"temperature":27,"humidity":64,"timestamp":1599758569}
{"sensor_id":2,"temperature":31,"humidity":58,"timestamp":1599758629}
{"sensor_id":1,"temperature":25,"humidity":51,"timestamp":1599758689}
{"sensor_id":2,"temperature":31,"humidity":59,"timestamp":1599758569}
{"sensor_id":3,"temperature":30,"humidity":59,"timestamp":1599758689}
{"sensor_id":2,"temperature":26,"humidity":74,"timestamp":1599758689}
{"sensor_id":3,"temperature":32,"humidity":72,"timestamp":1599758689}
{"sensor_id":3,"temperature":30,"humidity":65,"timestamp":1599758569}
{"sensor_id":3,"temperature":26,"humidity":60,"timestamp":1599758629}
{"sensor_id":1,"temperature":25,"humidity":54,"timestamp":1599758689}
{"sensor_id":2,"temperature":29,"humidity":50,"timestamp":1599758569}
{"sensor_id":1,"temperature":30,"humidity":77,"timestamp":1599758569}
{"sensor_id":3,"temperature":26,"humidity":80,"timestamp":1599758689}
{"sensor_id":1,"temperature":30,"humidity":67,"timestamp":1599758689}
{"sensor_id":2,"temperature":30,"humidity":73,"timestamp":1599758569}
{"sensor_id":3,"temperature":29,"humidity":70,"timestamp":1599758629}
{"sensor_id":2,"temperature":29,"humidity":71,"timestamp":1599758569}
{"sensor_id":3,"temperature":30,"humidity":67,"timestamp":1599758689}
{"sensor_id":2,"temperature":32,"humidity":66,"timestamp":1599758629}
{"sensor_id":3,"temperature":32,"humidity":51,"timestamp":1599758629}
{"sensor_id":3,"temperature":31,"humidity":65,"timestamp":1599758689}
{"sensor_id":3,"temperature":27,"humidity":72,"timestamp":1599758689}
{"sensor_id":3,"temperature":25,"humidity":65,"timestamp":1599758629}
{"sensor_id":3,"temperature":30,"humidity":70,"timestamp":1599758629}
{"sensor_id":3,"temperature":28,"humidity":56,"timestamp":1599758689}
{"sensor_id":1,"temperature":28,"humidity":53,"timestamp":1599758629}
{"sensor_id":3,"temperature":28,"humidity":67,"timestamp":1599758689}
{"sensor_id":2,"temperature":32,"humidity":75,"timestamp":1599758569}
{"sensor_id":1,"temperature":31,"humidity":69,"timestamp":1599758629}
{"sensor_id":1,"temperature":30,"humidity":63,"timestamp":1599758569}
{"sensor_id":2,"temperature":29,"humidity":65,"timestamp":1599758689}
{"sensor_id":1,"temperature":26,"humidity":66,"timestamp":1599758629}
{"sensor_id":2,"temperature":27,"humidity":70,"timestamp":1599758689}
{"sensor_id":1,"temperature":26,"humidity":54,"timestamp":1599758689}
{"sensor_id":1,"temperature":28,"humidity":65,"timestamp":1599758629}
{"sensor_id":3,"temperature":27,"humidity":52,"timestamp":1599758569}
{"sensor_id":1,"temperature":31,"humidity":61,"timestamp":1599758569}
{"sensor_id":1,"temperature":28,"humidity":72,"timestamp":1599758689}
{"sensor_id":2,"temperature":28,"humidity":72,"timestamp":1599758629}
{"sensor_id":3,"temperature":27,"humidity":80,"timestamp":1599758569}
{"sensor_id":2,"temperature":29,"humidity":75,"timestamp":1599758689}
{"sensor_id":2,"temperature":25,"humidity":60,"timestamp":1599758569}
{"sensor_id":3,"temperature":31,"humidity":79,"timestamp":1599758569}
{"sensor_id":2,"temperature":26,"humidity":68,"timestamp":1599758689}
{"sensor_id":1,"temperature":26,"humidity":59,"timestamp":1599758569}
{"sensor_id":3,"temperature":29,"humidity":77,"timestamp":1599758569}
{"sensor_id":2,"temperature":30,"humidity":60,"timestamp":1599758569}
{"sensor_id":1,"temperature":25,"humidity":58,"timestamp":1599758569}
{"sensor_id":3,"temperature":29,"humidity":71,"timestamp":1599758569}
{"sensor_id":1,"temperature":26,"humidity":61,"timestamp":1599758689}
{"sensor_id":2,"temperature":26,"humidity":66,"timestamp":1599758629}
{"sensor_id":3,"temperature":26,"humidity":51,"timestamp":1599758689}
{"sensor_id":1,"temperature":30,"humidity":74,"timestamp":1599758569}
{"sensor_id":2,"temperature":26,"humidity":56,"timestamp":1599758689}
{"sensor_id":1,"temperature":26,"humidity":74,"timestamp":1599758629}
{"sensor_id":3,"temperature":27,"humidity":64,"timestamp":1599758569}
{"sensor_id":2,"temperature":28,"humidity":66,"timestamp":1599758629}
{"sensor_id":2,"temperature":30,"humidity":51,"timestamp":1599758569}
{"sensor_id":1,"temperature":31,"humidity":72,"timestamp":1599758629}
{"sensor_id":3,"temperature":25,"humidity":78,"timestamp":1599758689}
{"sensor_id":2,"temperature":30,"humidity":65,"timestamp":1599758569}
{"sensor_id":3,"temperature":25,"humidity":68,"timestamp":1599758569}
{"sensor_id":2,"temperature":31,"humidity":63,"timestamp":1599758689}
{"sensor_id":2,"temperature":32,"humidity":78,"timestamp":1599758689}
{"sensor_id":3,"temperature":27,"humidity":55,"timestamp":1599758629}
{"sensor_id":3,"temperature":25,"humidity":73,"timestamp":1599758569}
{"sensor_id":3,"temperature":32,"humidity":62,"timestamp":1599758629}
{"sensor_id":2,"temperature":26,"humidity":68,"timestamp":1599758569}
{"sensor_id":1,"temperature":32,"humidity":73,"timestamp":1599758689}
{"sensor_id":1,"temperature":32,"humidity":63,"timestamp":1599758569}
{"sensor_id":2,"temperature":28,"humidity":78,"timestamp":1599758629}
{"sensor_id":2,"temperature":32,"humidity":52,"timestamp":1599758569}
{"sensor_id":2,"temperature":26,"humidity":55,"timestamp":1599758569}
{"sensor_id":1,"temperature":32,"humidity":67,"timestamp":1599758689}
{"sensor_id":3,"temperature":32,"humidity":61,"timestamp":1599758629}
{"sensor_id":2,"temperature":26,"humidity":80,"timestamp":1599758629}
{"sensor_id":1,"temperature":27,"humidity":67,"timestamp":1599758689}
{"sensor_id":3,"temperature":32,"humidity":66,"timestamp":1599758629}
{"sensor_id":3,"temperature":32,"humidity":75,"timestamp":1599758689}
{"sensor_id":1,"temperature":31,"humidity":51,"timestamp":1599758629}
```


## [Creando una tarea de indexación](https://druid.apache.org/docs/latest/tutorials/tutorial-ingestion-spec.html)

# Cargando datos

* [Cargando datos usando fichero local](https://druid.apache.org/docs/latest/tutorials/tutorial-batch.html#loading-data-with-the-data-loader)

* [Cargando datos usando la extension de s3](https://druid.apache.org/docs/0.20.1/development/extensions-core/s3.html)

* [Cargando datos usando Kafka](https://druid.apache.org/docs/latest/tutorials/tutorial-kafka.html)

# Consulta de datos

* [Consultando mendiate la consola](https://druid.apache.org/docs/latest/tutorials/tutorial-query.html#query-sql-via-the-console)

* [Consultando mendiate la dsql](https://druid.apache.org/docs/latest/tutorials/tutorial-query.html#query-sql-via-dsql)

* [Consultando mendiate la SQL over HTTP](https://druid.apache.org/docs/latest/tutorials/tutorial-query.html#query-sql-over-http)

* [Consultando usando query nativas](https://druid.apache.org/docs/latest/tutorials/tutorial-query.html#native-json-queries)

# Operación

* [Agregando datos (roll-up)](https://druid.apache.org/docs/latest/tutorials/tutorial-rollup.html)

* [Retencion de datos](https://druid.apache.org/docs/latest/tutorials/tutorial-retention.html)

* [Cambiando granularidad de los datos](https://druid.apache.org/docs/latest/tutorials/tutorial-compaction.html)

* [Borrando datos](https://druid.apache.org/docs/latest/tutorials/tutorial-delete-data.html)

* [Transformando datos en la ingesta](https://druid.apache.org/docs/latest/tutorials/tutorial-transform-spec.html)

# Visualización

## Grafana + Druid

1. Ejecutamos el docker de grafana, montando un volumen para los plugins.
```
docker run -it -v "$PWD/data:/var/lib/grafana" -p 3000:3000 grafana/grafana:6.6.2-ubuntu
```

2. Entramos dentro del docker, instalamos el plugin de druid y reiniciamos el docker:
```
grafana-cli plugins install abhisant-druid-datasource
```

## Metabase + Druid

```
docker rm metabase; docker run -it -p 3000:3000 --name metabase metabase/metabase
```
