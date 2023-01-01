# docker mongodb-sharded-cluste

dockerhub : https://hub.docker.com/_/mongo

### Config Servers 초기화(Initialize Config Servers)
```agsl
docker-compose exec config1 bash
```
```agsl
mongo
```
```agsl
rs.initiate( {
	_id: "configReplica",
	configsvr: true,
	members: [
		{ "_id": 0, "host": "config1:27017" },
		{ "_id": 1, "host": "config2:27017" },
		{ "_id": 2, "host": "config3:27017" }]
	} )
```
```agsl
rs.config()
```
```agsl
rs.status()
```
```agsl
use admin
```
```agsl
db.createUser({user:"root",pwd:"changeme",roles:[{role:"root",db:"admin"}]});
```
```agsl
mongo --port 27017 -u "root" -p "changeme" --authenticationDatabase "admin"
```
### Shard Servers 초기화(Initialize Shard Servers)
```agsl
docker-compose exec shard11 bash
```
```agsl
mongo
```
```agsl
rs.initiate({
    _id: "rs1",
    members: [
        { _id: 0, host : "shard11:27017" },
        { _id: 1, host : "shard12:27017" },
        { _id: 2, host : "shard13:27017", arbiterOnly:true }
    ]
})
```
```agsl
rs.config()
```
```agsl
rs.status()
```
### Mongos(router) Servers 초기화(Initialize Mongos Servers)
```agsl
docker-compose exec mongos1 bash
```
```agsl
mongo
```
```agsl
sh.addShard("rs1/shard11:27017,shard12:27017,shard13:27017")
```
```agsl
sh.status()
```