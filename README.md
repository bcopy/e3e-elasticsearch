# e3e-elasticsearch
Extreme Energy Event Monitoring aims at making available on a public basis the technological means  to monitor explosions and other extreme energy events in real-time, objectively and through open-access.

This repository features an ElasticSearch prototype for E3E based on Docker and Kibana.

# Setup Procedure

## Setting up Docker

Docker is a virtualization container that allows to host lightweight operating system images and automate their maintenance.
Docker ships with most Linux distribution, please refer to the Docker setup procedures for your operating system.

### Docker on CentOS 7

```bash
yum install docker
adduser -g docker -m docker
```

### Setting up a quick ElasticSearch image with Docker
The official ElasticSearch docker image is based on Debian and already exports ports 9200 and 9300.

```bash
docker pull docker.io/elasticsearch
docker run -p 0.0.0.0:9200:9200 --name es -t elasticsearch

# Open an interactive console to poke around
docker exec -t -i es /bin/bash
```

# DockerFile


