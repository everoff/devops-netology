# Домашнее задание к занятию "6.5. Elasticsearch"

## Задание 1
Ответ ElasticSearch:
```
maksim@MacBook-Pro Docker % sudo docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -v ~/Docker/elasticsearch/custom_elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml  elasticsearch:7.17.3

maksim@MacBook-Pro Docker % curl -X GET "http://127.0.0.1:9200/"
{
  "name" : "netology_test",
  "cluster_name" : "netology",
  "cluster_uuid" : "ERrgxjxxRQanPnD3XR0Xfw",
  "version" : {
    "number" : "7.17.3",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "5ad023604c8d7416c9eb6c0eadb62b14e766caff",
    "build_date" : "2022-04-19T08:11:19.070913226Z",
    "build_snapshot" : false,
    "lucene_version" : "8.11.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```
Ссылка на dockerhub: https://hub.docker.com/repository/docker/tetenkinm/elastic_netology

## Задание 2
- Добавление индексов
```
root@9a8f1fe264d0:/usr/share/elasticsearch# curl -X PUT localhost:9200/ind-1 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 1,  "number_of_replicas": 0 }}'
{"acknowledged":true,"shards_acknowledged":true,"index":"ind-1"}
root@9a8f1fe264d0:/usr/share/elasticsearch#
root@9a8f1fe264d0:/usr/share/elasticsearch# curl -X PUT localhost:9200/ind-2 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 2,  "number_of_replicas": 1 }}'
{"acknowledged":true,"shards_acknowledged":true,"index":"ind-2"}
root@9a8f1fe264d0:/usr/share/elasticsearch#
root@9a8f1fe264d0:/usr/share/elasticsearch# curl -X PUT localhost:9200/ind-3 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 4,  "number_of_replicas": 2 }}'
{"acknowledged":true,"shards_acknowledged":true,"index":"ind-3"}
root@9a8f1fe264d0:/usr/share/elasticsearch#
```
- Состояние кластера
```
maksim@MacBook-Pro Docker % curl -X GET 'http://localhost:9200/_cluster/health?pretty'
{
  "cluster_name" : "netology",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 10,
  "active_shards" : 10,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 10,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 50.0
}
```
Т.к. мы имеем ряд unassigned шардов, поэтому статус кластера `yellow`.
- Удаление индексов
```
root@9a8f1fe264d0:/usr/share/elasticsearch# curl -X DELETE 'http://localhost:9200/ind-1?pretty'
{
  "acknowledged" : true
}
root@9a8f1fe264d0:/usr/share/elasticsearch# curl -X DELETE 'http://localhost:9200/ind-2?pretty'
{
  "acknowledged" : true
}
root@9a8f1fe264d0:/usr/share/elasticsearch# curl -X DELETE 'http://localhost:9200/ind-3?pretty'
{
  "acknowledged" : true
}
```

## Задание 3
Путь прописал в `elastisearch.yml` `path.repo: /usr/share/elasticsearch/snapshots`
- Запрос на регистрацию
```
sh-5.0# curl -XPOST localhost:9200/_snapshot/netology_backup?pretty -H 'Content-Type: application/json' -d'{"type": "fs", "settings": { "location":"/usr/share/elasticsearch/snapshots" }}'

```
- Создание индекса
```
h-5.0# curl -X PUT localhost:9200/test -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 1,  "number_of_replicas": 0 }}'
```
- Создание snapshot
```
sh-5.0# curl -X PUT localhost:9200/_snapshot/netology_backup/elasticsearch?wait_for_completion=true

sh-5.0# ls -lah /usr//share/elasticsearch/snapshots/
total 60K
drwxrwxr-x 3 elasticsearch root 4.0K Jun  6 15:57 .
drwxrwxr-x 1 root          root 4.0K Jun  6 15:41 ..
-rw-rw-r-- 1 elasticsearch root 1.4K Jun  6 15:57 index-0
-rw-rw-r-- 1 elasticsearch root    8 Jun  6 15:57 index.latest
drwxrwxr-x 6 elasticsearch root 4.0K Jun  6 15:57 indices
-rw-rw-r-- 1 elasticsearch root  29K Jun  6 15:57 meta-bpIu0AnTQ56CzBWNGauE2Q.dat
-rw-rw-r-- 1 elasticsearch root  713 Jun  6 15:57 snap-bpIu0AnTQ56CzBWNGauE2Q.dat
```
- Удаление/добавление индексов
```
sh-5.0# curl -X DELETE 'http://localhost:9200/test?pretty'
sh-5.0# curl -X PUT localhost:9200/test-2?pretty -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 1,  "number_of_replicas": 0 }}'

maksim@MacBook-Pro Docker % curl -X GET 'http://localhost:9200/_cat/indices?v'
health status index            uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   test-2           OIjYWiVVTXyy8Op4zIGRPg   1   0          0            0       226b           226b
```
- Восстановление из snapshot
```
sh-5.0# curl -X POST localhost:9200/_snapshot/netology_backup/elasticsearch/_restore?pretty -H 'Content-Type: application/json' -d'{"include_global_state":true}'
maksim@MacBook-Pro Docker % curl -X GET 'http://localhost:9200/_cat/indices?v'
green  open   test-2           OIjYWiVVTXyy8Op4zIGRPg   1   0          0            0       226b           226b
green  open   test             HzFsM8rgT1WPFxz0Ll0Uew   1   0          0            0       226b           226b
HzFsM8rgT1WPFxz0Ll0Uew
```
