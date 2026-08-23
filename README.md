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

## Создать для индекса pattern  ## 

## Добавить в индекс как минимум 3 документа желательно со следующим содержанием:
«моя мама мыла посуду а кот жевал сосиски»
«рама была отмыта и вылизана котом»
«мама мыла раму»


## Написать запрос нечеткого поиска к этой коллекции документов ко ключу «мама ела сосиски»


##  Расшарить коллекцию postman (желательно сдавать в таком формате)  ## 


## Прислать ссылку на коллекцию ## 


