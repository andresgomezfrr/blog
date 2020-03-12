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
  wget http://apache.uvigo.es/druid/0.17.0/apache-druid-0.17.0-bin.tar.gz && tar -xzf apache-druid-0.17.0-bin.tar.gz && cd apache-druid-0.17.0
  ```
2. Arrancamos el setup para desarrollo:
  ```
  env DRUID_SKIP_JAVA_CHECK=1 ./bin/start-micro-quickstart  
  ```
3. Accedemos a la interfaz unificada, para comprobar que todo esta funcionando correctamente. [http://localhost:8888/unified-console.html](http://localhost:8888/unified-console.html)

## [Creando una tarea de indexación](https://druid.apache.org/docs/latest/tutorials/tutorial-ingestion-spec.html)

# Cargando datos

* [Cargando datos usando fichero local](https://druid.apache.org/docs/latest/tutorials/tutorial-batch.html#loading-data-with-the-data-loader)

* [Cargando datos usando la extension de s3](https://druid.apache.org/docs/0.17.0/development/extensions-core/s3.html)

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

## Swiv + Druid

https://github.com/YahooArchive/swiv

## Metabase + Druid

```
docker rm metabase; docker run -it -p 3000:3000 --name metabase metabase/metabase
```
