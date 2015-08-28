# E3E ElasticSearch prototype

Extreme Energy Event Monitoring aims at making available on a public basis the technological means  to monitor explosions and other extreme energy events in real-time, objectively and through open-access.

This repository features an ElasticSearch prototype for E3E based on Docker and Kibana.

ElasticSearch can be used to collect data and query it through simple HTTP / REST calls. Data is automatically indexed, and can be templated (i.e. mapped to data types and data constraints) to make searching more efficient (e.g. declaring a given field is a number allows to perform simple mathematical aggregations such as MAX, MIN or SUM).

# Setup Procedure

## Setting up Docker

Docker is a virtualization container that allows to host lightweight operating system images and automate their maintenance.
Docker ships with most Linux distribution, please refer to the Docker setup procedures for your operating system.

### Docker on Linux CentOS 7

```bash
yum install docker
service docker start
```

### Setting up a quick ElasticSearch image with Docker
The official ElasticSearch docker image is based on Debian and already exports ports 9200 and 9300.

```bash
docker pull docker.io/elasticsearch
docker run -p 0.0.0.0:9200:9200 --name es -t elasticsearch

# Open an interactive console to poke around (if you want)
docker exec -t -i es /bin/bash
```
### Setting up a quick Kibana 4 image with Docker
Using [https://github.com/bobrik/docker-kibana], open a link between the Kibana and ElasticSearch containers.
```bash
docker pull bobrik/kibana
docker run -d -p 0.0.0.0:5601:5601 --name kibana -e KIBANA_ES_URL=http://cvl-e3e:9200 --link es:cvl-e3e -t bobrik/kibana
```
:warning: Kibana requires the hostname and exposes it - TO DO : Find a way to tell Kibana to expose a different hostname.

## Seeding data into ElasticSearch

Loading data into ElasticSearch requires :
* The creation of an INDEX - this is the storage area that will contain the data
* The creation of a MAPPING TEMPLATE - this is how ElasticSearch assigns data types (such as Number, String, Date) to your information, so as to facilitate data querying.
* The upload of the sample data in JSON format. Once uploaded, the data is immediately indexed, searchable and persisted. 

You can use a simple HTTP client tool like **curl** to perform the above steps.

```bash
# For example, our ElasticSearch server is deployed on cvl-e3e.cern.ch:9200
# (Port 9200 is the default ElasticSearch REST port)
export E3E_HOSTNAME=http://cvl-e3e.cern.ch:9200

wget http://raw.githubusercontent.com/bcopy/e3e-elasticsearch/master/sample-data/es-template-e3event.json
wget http://raw.githubusercontent.com/bcopy/e3e-elasticsearch/master/sample-data/sample.json
curl -XPUT http://$E3E_HOSTNAME/e3e/
curl -XPUT http://$E3E_HOSTNAME/e3e/_mapping/event --data-binary @es-template-e3event.json
curl -XPOST  http://$E3E_HOSTNAME/e3e/event/_bulk --data-binary @sample.json
```

You can then open your web browser to the Kibana 4 interface (the default port is 5601).

For example, we have deployed our Docker machines on **cvl-e3e.cern.ch** :

http://cvl-e3e.cern.ch:5601/

# Advocacy

* ElasticSearch supports geographical querying (e.g. "Show me the points located in that polygon area").
* ElasticSearch provides a built-in REST querying languages and JSON support.
* ElasticSearch provides an easy data integration point to cross-reference other data source (such as OCHA's HDX repository)

# Improvement suggestions

* Kibana provides a very simple dashboard prototyping tool, but does not integrate with other web technologies. Consider Webcomponents (http://webcomponents.org) for a standard way of sharing visualizations and integrating them in third-party websites, combined with the D3JS javascript API.
* As discussed during MozSprint 2015, E3E requires data policy enforcements (based on time, the context of the situation being reported upon, confidentiality levels) that ElasticSearch cannot handle out of the box - a layer on top, inspired by Elastic Shield, or an extension of open source project https://github.com/floragunncom/search-guard , would address this.
