Kafka-connect-elasticsearch
=
Simple project for toying with Kafka, Kafka-Connect and using a mysql-instance as source and elasticsearch as sink.

Getting started
-
Start by starting up the containers
```bash
docker-compose -f <docker-compose-file> up -d
```
Give it a couple of seconds to start up. Pay attention to how the containers are getting named and 
change your config. accordingly if needed. Then create the kafka-jdbc-connector.
```bash
curl -X POST   -H "Content-Type: application/json"   --data '{ "name": "quickstart-jdbc-source", "config": { "connector.class": "io.confluent.connect.jdbc.JdbcSourceConnector", "tasks.max": 1, "connection.url": "jdbc:mysql://3_quickstart-mysql_1:3306/connect_test?user=root&password=confluent", "mode": "incrementing", "incrementing.column.name": "id", "timestamp.column.name": "modified", "topic.prefix": "quickstart-jdbc-", "poll.interval.ms": 1000 } }'   http://localhost:8083/connectors
```
then create the elasticsearch-connector
```bash
curl -X POST -H "Content-Type: application/json" --data '{ "name": "quickstart-es-source", "config": { "connector.class": "io.confluent.connect.elasticsearch.ElasticsearchSinkConnector", "tasks.max": 1, "topics": "quickstart-jdbc-test", "key.ignore": true, "connection.url": "http://elasticsearch1:9200", "type.name": "kafka-connect" } }' http://localhost:8083/connectors
```

if all goes well you should now be able to query elasticsearch for your data
```bash
curl http://127.0.0.1:9200/quickstart-jdbc-test/_search?pretty
```

