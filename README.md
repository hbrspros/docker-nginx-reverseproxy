# Nginx Reverse Proxy

## Purpose
Setup a mutlidomain Webservice Infrastructure with automated vhost configuration deployment and TLS-Cert generation in a dockerized environment.

## What the 'customer' wants
- 2 Webapps with 2 seperated Domains on a Single host
  - Wordpress (wordpress.example.com)
  - Whoami (whoami.example.com)

## The old way (LAMP)
Eg for Wordpress:
- create Subdomains
- create database
- create folder/place files 
  - configure mysql settings in config.php 
- manual vhost configuration
- create certificates and configure TLS 

## The new Way (Docker - Microservices)
- create Subdomains
- ~~create~~ __run__ database __container__
- ~~create~~ __run__ ~~folder/place files~~ __application container__
  - __before starting set mysql settings__ ~~configure mysql settings in config.php~~ __in container environment__ 
- ~~manual vhost configuration~~ __vhost as container environment__
- __let the infrastructure __create certificates and configure TLS __for you__

# Architecture 
## The old way (LAMP)
### Setup
- Database as Service on host system
- Webserver as Service on host system
- Webserver manages TLS Certs

### Networking
- No virtual interfaces/all on one system with a single eth0 interface

## The new Way (Docker - Microservices)
### Setup
- Database as container
- Webserver as container
- reverseproxy as container
- Webserver manages TLS Certs

### Networking
- Network: Webserver connected to database
- Internal Network: Webserver connected to reverseproxy
- External Network: reverseproxy connected to external interface eth0 

## How to get started

Clone Repo
`git clone https://github.com/hbrspros/docker-nginx-reverseproxy.git`

Setup Proxy
`cd nginx`
`docker-compose -d up`

Setup app
`cd ../app`
`docker-compose -d up`
