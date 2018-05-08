## read-only-cerebro

Sample nginx + [cerebro](https://github.com/lmenezes/cerebro) config to prevent destructive Elasticsearch actions by allowing all GET and POST requests to cerebro and _only_ GET requests to Elasticsearch. POST requests are required for form data submission to cerebro, but are not necessary for reading data from ES.

![read_only_cerebro](https://github.com/yuriybash/read-only-cerebro/blob/master/assets/read-only-cerebro.png "read_only_cerebro")


- GET request to cerebro (OK):

```
curl localhost:8080

<!-- cerebro index body below-->
<!DOCTYPE html>
<html lang="en" ng-app="cerebro" ng-init="appVersion = 'v0.8.1'">
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        ...
```

- GET request to cerebro, results in GET request to ES (OK):

```
curl --H \
  --request POST \
  --data-binary 'http://localhost:9000/rest/request' \
  http://localhost:9000/rest/request


{
  "status": 200,
  "body": {
    "twitter": {
      "aliases": {},
      "mappings": {},
      "settings": {
        "index": {
          "creation_date": "1549264814452",
          "number_of_shards": "5",
          "number_of_replicas": "1",
          "uuid": "RN50HetGQEqDx2llPJqNuA",
          "version": {
            "created": "6050499"
          },
          "provided_name": "twitter"
        }
      }
    }
  }
}
```



POST request to cerebro, does not result in a POST request to ES (OK):

```
curl 'http://localhost:8080/connect' -H --request POST --data-binary '{"host":"http://localhost:9200"}' --compressed

{
  "status": 200,
  "body": {
    "cluster_name": "elasticsearch_yuriy",
    "status": "yellow",
    "timed_out": false,
    "number_of_nodes": 1,
    "number_of_data_nodes": 1,
    "active_primary_shards": 5,
    "active_shards": 5,
    "relocating_shards": 0,
    "initializing_shards": 0,
    "unassigned_shards": 5,
    "delayed_unassigned_shards": 0,
    "number_of_pending_tasks": 0,
    "number_of_in_flight_fetch": 0,
    "task_max_waiting_in_queue_millis": 0,
    "active_shards_percent_as_number": 50
  }
}
```

POST request to cerebro, does result in a POST request to ES (**potentially destructive**):

```
curl 'http://localhost:8080/rest/request' --data-binary '{"method":"POST","data":{"name":"john doe"},"path":"/twitter/doc","host":"http://localhost:9200"}' --compressed

<html>
<head><title>403 Forbidden</title></head>
<body>
```

`PUT` and `DELETE` requests are always blocked.
