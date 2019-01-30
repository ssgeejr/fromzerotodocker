
https://docs.docker.com/engine/reference/commandline/dockerd/#/daemon-configuration-file

https://medium.freecodecamp.org/how-to-setup-log-rotation-for-a-docker-container-a508093912b2

 /etc/docker/daemon.json
 
 #Also, keep in mind, that options in /etc/docker/config.json must not be specified in
 #the daemon command line in the systemd unit for instance, otherwise the daemon won't
 #start up at all.
 #/etc/docker/config.json
 
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "5"
  }
}


#### The docker run command

```
docker run \
    --log-driver json-file \
    --log-opt max-size=10m \
    --log-opt max-file=10 \
    alpine echo hello world
```

#### Using docker-compose

```
version: '3.2'
services:
  nginx:
    image: 'nginx:latest'
    ports:
      - '80:80'
    logging:
      driver: "json-file"
      options:
        max-size: "1k"
        max-file: "3"
```


https://stackoverflow.com/questions/49111573/docker-compose-not-reading-logging-config-in-etc-docker-daemon-json



#### registries
```
"insecure-registries" : ["myregistrydomain.com:5000", "registry.example.com"]


```
