# Install Couchbase via Docker Image

### ToDo:  Add mongo install as well 

#### Reference docs:
https://blog.couchbase.com/couchbase-docker-container/
https://hub.docker.com/r/couchbase/server/
https://docs.couchbase.com/server/6.0/install/getting-started-docker.html
https://blog.couchbase.com/couchbase-docker-container/
https://docs.couchbase.com/server/4.1/install/deployment-docker.html

#best practices (or not-good ones)
https://hub.docker.com/r/couchbase/server/

#### Clustering:
https://github.com/couchbase/docker/blob/master/compose/couchbase-server-sync-gateway/docker-compose.yml
https://github.com/wheniwork/couchbase-provisioner/blob/master/docker-compose.yml


#### Logging:
http://docs.couchdb.org/en/stable/config/logging.html


#### Installation:

Create your volume folders if you are not going to use actual docker volumes  
See: [LVM Mapping instructions](lvm_drive_mappings.md) to use drives other than /   

```
mkdir -p /opt/couchdb/var
```

####  notes ... 
```

couchbase/server:sandbox



```

Start your instance from the command line
```
docker run -d --ti --name cbase -p 8091-8096:8091-8096 -p 11210-11211:11210-11211 -v /opt/couchbase/var:/opt/couchbase/var couchbase/server:sandbox
```

run your instance with docker-compose:
docker-compose.yml  
```ruby
version: '3'
services:
  cbase:
    image: couchbase/server:sandbox
    container_name: cbase
    tty: true
    stdin_open: true
    restart: always
#   image: couchbase/server
    ports:
      - 8091:8091
      - 8092:8092 
      - 8093:8093 
      - 11210:11210
    volumes:
      - /opt/couchbase/var:/opt/couchbase/var

```

start it 
```
docker-compose up -d
```

Using a volume instead of a directory

```ruby
...
	volumes:
		- cb-var:/opt/couchbase/var

volumes:
  cb-var:
```


adding a network

```ruby
...
		networks:
			cb_net:
				ipv4_address: 192.168.50.10
				aliases:
					- couchbase




networks:
    cb_net:
        driver: bridge
        ipam:
            driver: default
            config: [{subnet: 192.168.50.0/24}]
```

From the couchbase documentation  
* CouchDB uses var to store its data, and is exposed as a volume.

#### Web-AdminUI  You can now hit your server from:
http://<your-ip>:8091



# Redis
docker pull redis


Create your volume folders if you are not going to use actual docker volumes  
See: [LVM Mapping instructions](lvm_drive_mappings.md) to use drives other than /   
```
mkdir -p /opt/redis/data
chown -R devops:devops /opt/redis
```

#### Command line
```
docker run -v /opt/redis/data:/data -ti -p 6379:6379 --name myredis redis
```

run your instance with docker-compose:
docker-compose.yml  
```ruby
# added to your original docker-compose.yml
  redis:
    image: redis
    container_name: redis
    tty: true
    stdin_open: true
    restart: always
    ports:
      - 6379:6379
    volumes:
      - /opt/redis/data:/data

```


-v 


#### providing your own configuration

Simply add your redis.conf and link it as a volume to ```/myredis/conf/redis.conf:/usr/local/etc/redis/redis.conf```

#### Command line
```
docker run -v /myredis/conf/redis.conf:/usr/local/etc/redis/redis.conf -v /opt/redis/data:/data --name myredis redis redis-server /usr/local/etc/redis/redis.conf	
```



# Kafka

Reference:
https://github.com/simplesteph/kafka-stack-docker-compose
http://www.michael-noll.com/blog/2013/03/13/running-a-multi-broker-apache-kafka-cluster-on-a-single-node/
https://docs.confluent.io/current/installation/docker/docs/installation/single-node-client.html


Start via docker-compose
```
version: '2.1'

services:
  zoo1:
    image: zookeeper:3.4.9
    hostname: zoo1
    ports:
      - "2181:2181"
    environment:
        ZOO_MY_ID: 1
        ZOO_PORT: 2181
        ZOO_SERVERS: server.1=zoo1:2888:3888
    volumes:
      - ./zk-single-kafka-single/zoo1/data:/data
      - ./zk-single-kafka-single/zoo1/datalog:/datalog

  kafka1:
    image: confluentinc/cp-kafka:5.0.0
    hostname: kafka1
    ports:
      - "9092:9092"
    environment:
      KAFKA_ADVERTISED_LISTENERS: LISTENER_DOCKER_INTERNAL://kafka1:19092,LISTENER_DOCKER_EXTERNAL://${DOCKER_HOST_IP:-127.0.0.1}:9092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: LISTENER_DOCKER_INTERNAL:PLAINTEXT,LISTENER_DOCKER_EXTERNAL:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: LISTENER_DOCKER_INTERNAL
      KAFKA_ZOOKEEPER_CONNECT: "zoo1:2181"
      KAFKA_BROKER_ID: 1
      KAFKA_LOG4J_LOGGERS: "kafka.controller=INFO,kafka.producer.async.DefaultEventHandler=INFO,state.change.logger=INFO"
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
    volumes:
      - ./zk-single-kafka-single/kafka1/data:/var/lib/kafka/data
    depends_on:
      - zoo1
```



