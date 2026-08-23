# Домашнее задание Сартисона Евгения N9 #

Описание/Пошаговая инструкция выполнения домашнего задания:
Развернуть Instance ES – желательно в AWS
Создать в ES индекс, в нём должно быть обязательное поле text типа string
Создать для индекса pattern
Добавить в индекс как минимум 3 документа желательно со следующим содержанием:
«моя мама мыла посуду а кот жевал сосиски»
«рама была отмыта и вылизана котом»
«мама мыла раму»
Написать запрос нечеткого поиска к этой коллекции документов ко ключу «мама ела сосиски»
Расшарить коллекцию postman (желательно сдавать в таком формате)
Прислать ссылку на коллекцию


## Развернуть Instance ES – желательно в AWS ## 

Не нашел ElasticSeach в российских облаках, а в зарубежных есть трудности по известным причинам
```
Yandex Cloud больше не предоставляет полноценный управляемый сервис Managed Service for Elasticsearch, 
```

развернул в локальном докере: docker-compose.yml
```
version: '3.8'
services:
  elasticsearch:
    image: elasticsearch:8.13.0
    container_name: elasticsearch
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ports:
      - "9200:9200"
    volumes:
      - esdata:/usr/share/elasticsearch/data

volumes:
  esdata:
```

проверка работы
```
student:~/elastic$ docker ps
CONTAINER ID   IMAGE                  COMMAND                  CREATED       STATUS       PORTS                                                   NAMES
1a1d8af872f3   elasticsearch:8.13.0   "/bin/tini -- /usr/l…"   2 hours ago   Up 2 hours   0.0.0.0:9200->9200/tcp, [::]:9200->9200/tcp, 9300/tcp   elasticsearch

student:~/elastic$ curl localhost:9200
{
  "name" : "1a1d8af872f3",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "X2xXlYMwQxGr_AAHcLjCug",
  "version" : {
    "number" : "8.13.0",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "09df99393193b2c53d92899662a8b8b3c55b45cd",
    "build_date" : "2024-03-22T03:35:46.757803203Z",
    "build_snapshot" : false,
    "lucene_version" : "9.10.0",
    "minimum_wire_compatibility_version" : "7.17.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "You Know, for Search"
}
```



## Создать в ES индекс, в нём должно быть обязательное поле text типа string  ## 
Создать индекс
```
student:~/elastic$ curl -X PUT "localhost:9200/es_index" \y_index"}student:~/elastic$ curl -X PUT "localhost:9200/es_index" \
-H "Content-Type: application/json" \
-d '{
  "settings": { "number_of_shards": 1 },
  "mappings": { "properties": {
    "name": { "type": "text" },
    "id":   { "type": "keyword" },
    "score": { "type": "float" } }}
}
'
```

Проверить индекс
```
student:~/elastic$ curl -X GET "localhost:9200/es_index"
{"es_index":{"aliases":{},"mappings":{"properties":{"id":{"type":"keyword"},"name":{"type":"text"},"score":{"type":"float"}}},"settings":{"index":{"routing":{"allocation":{"include":{"_tier_preference":"data_content"}}},"number_of_shards":"1","provided_name":"es_index","creation_date":"1787472496065","number_of_replicas":"1","uuid":"ijHWVl4uSUmbRmLuGzaRow","version":{"created":"8503000"}}}}}
```
Просмотреть данные в индексе
```
student:~/elastic$ curl -X GET "localhost:9200/es_index/_search?pretty"
{
  "took" : 373,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 0,
      "relation" : "eq"
    },
    "max_score" : null,
    "hits" : [ ]
  }
}
```

## Создать для индекса pattern  ## 

Создаем pattern, чтобы новые индексы автоматически наследовали нужные настройки
```
curl -X PUT "http://localhost:9200/_index_template/es_temp_name" \
  -H "Content-Type: application/json" \
  -d '{
    "index_patterns": ["search-*"],
    "template": {
      "settings": {
        "number_of_shards": 1
      }
    }
  }'
```

проверка
```
student:~/elastic$ curl -X GET "http://localhost:9200/_index_template/es_temp_name"
{"index_templates":[{"name":"es_temp_name","index_template":{"index_patterns":["search-*"],"template":{"settings":{"index":{"number_of_shards":"1"}}},"composed_of":[]}}]}
```

## Добавить в индекс как минимум 3 документа желательно со следующим содержанием:
«моя мама мыла посуду а кот жевал сосиски»
«рама была отмыта и вылизана котом»
«мама мыла раму»
```
curl -X POST "http://localhost:9200/es_index/_doc" \
     -H "Content-Type: application/json" \
     -d '{
       "name": "моя мама мыла посуду а кот жевал сосиски",
       "id": 1,
       "score": "4"
     }'
	 
	 
curl -X POST "http://localhost:9200/es_index/_doc" \
     -H "Content-Type: application/json" \
     -d '{
       "name": "мама мыла раму",
       "id": 1,
       "score": "13"
     }'


curl -X POST "http://localhost:9200/es_index/_doc" \
     -H "Content-Type: application/json" \
     -d '{
       "name": "рама была отмыта и вылизана котом",
       "id": 2,
       "score": "9"
     }'	 
```

Проверить что данные были добавлены в индекс
```
student:~/elastic$ curl -X GET "localhost:9200/es_index/_search?pretty"
{
  "took" : 202,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 3,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "es_index",
        "_id" : "7L_HLaABXTdniXK1vIwc",
        "_score" : 1.0,
        "_source" : {
          "name" : "моя мама мыла посуду а кот жевал сосиски",
          "id" : 1,
          "score" : "4"
        }
      },
      {
        "_index" : "es_index",
        "_id" : "7b_JLaABXTdniXK1kIyf",
        "_score" : 1.0,
        "_source" : {
          "name" : "мама мыла раму",
          "id" : 1,
          "score" : "13"
        }
      },
      {
        "_index" : "es_index",
        "_id" : "7r_JLaABXTdniXK10Iwz",
        "_score" : 1.0,
        "_source" : {
          "name" : "рама была отмыта и вылизана котом",
          "id" : 2,
          "score" : "9"
        }
      }
    ]
  }
}

```

## Написать запрос нечеткого поиска к этой коллекции документов ко ключу «мама ела сосиски»


##  Расшарить коллекцию postman (желательно сдавать в таком формате)  ## 
не могу сделать, так-как делал локально

## Прислать ссылку на коллекцию ## 
не могу сделать, так-как делал локально

