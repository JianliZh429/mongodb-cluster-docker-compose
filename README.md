# MongoDB Cluster docker compose

## Setup config server
You can comment other services in docker-compose file, only left the mongodb-config
    
1. start config server
    ```shell
    docker-compose up
    ```
     
2. initialize config server replica set
    
- connect to config server
    ```shell
    mongo 'mongodb://127.0.0.1:27117,127.0.0.1:27118,127.0.0.1:27119/?compressors=disabled&gssapiServiceName=mongodb'
    ```
        
- init replicaset
    ```shell
    rs.initiate(
      {
        _id : 'mongodb-config',
        configsvr: true,
        members: [
          { _id : 0, host : "mongodb-config-1:27117" },
          { _id : 1, host : "mongodb-config-2:27117" },
          { _id : 2, host : "mongodb-config-3:27117" }
        ]
      }
    )
    ```

## Setup sharding
You can mongos services in docker-compose file, only left the mongodb-config, mongodb-shard
    
1. start config server
    ```shell
    docker-compose up
    ```
    
2. initialize shards

- connect to shards server
    ```shell
    mongo 'mongodb://127.0.0.1:27217,127.0.0.1:27218,127.0.0.1:27219/?compressors=disabled&gssapiServiceName=mongodb'
    ```
    
- init replicaset
    ```shell
    rs.initiate(
      {
        _id : 'mongodb-shard',
        members: [
          { _id : 0, host : "mongodb-shard-1:27117" },
          { _id : 1, host : "mongodb-shard-2:27117" },
          { _id : 2, host : "mongodb-shard-3:27117" }
        ]
      }
    )
    ```
    
## Setup mongos
Uncomment all in docker-compose file
1. start docker-compose
    ```shell
    docker-compose up
    ```
    
2. add shard to mongos-1
- connect to mongos-1
    ```shell
    mongo --port 27317
    ```
- add shards
    ```shell
    sh.addShard("mongodb-shard/mongodb-shard-1:27117,mongodb-shard-2:27117,mongodb-shard-3:27117")
    ```
    
- enable sharding for database
    ```shell
    sh.enableSharding("test_db")
    ```
    
3. add shard to mongos-2

- connect to mongos-2
    ```shell
    mongo --port 27318
    ```
- add shards
    ```shell
    sh.addShard( "mongodb-shard/mongodb-shard-1:27117,mongodb-shard-2:27117,mongodb-shard-3:27117")
    ```
    
- enable sharding for database
    ```shell
    sh.enableSharding("test_db")
    ```
