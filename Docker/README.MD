

## List
```
docker container ls
docker image ls
docker ps -a
docker ps -as
```

## Registry stuff

###  pulling images from a self-signed docker registry

setup:
1. registry hosted on https://registry.example.com
2. setting remote Debian to pull images from https://registry.example.com

self signed registry HTTPS
ca.cert from https://registry.example.com

on Debian
```
$ openssl genrsa -out client.key 4096
$ openssl req -new -x509 -text -key client.key -out client.cert
```

https://registry.example.com can setup `http Basic Authentication`

- Folder structure:
```
    /etc/docker/certs.d/
    └── registry.example.com          <-- Hostname without port
       ├── client.cert
       ├── client.key
       └── ca.crt

```

pull image
```
docker login registry.example.com 
docker pull registry.example.com/test-image
```

https://docs.docker.com/registry/deploying/
https://docs.docker.com/engine/security/certificates/

## Remove, list

```
docker container ls
docker image ls
docker container rm <container_id>
docker image rm <image_id>
docker rmi -f training/webapp
```

Reference:</br>
https://stackoverflow.com/questions/33907835/docker-error-cannot-delete-docker-container-conflict-unable-to-remove-reposito