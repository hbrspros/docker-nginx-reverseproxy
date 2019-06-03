# Nginx Reverse Proxy

## Purpose
Setup a mutlidomain Webservice Infrastructure with automated vhost configuration deployment and TLS-Cert generation in a dockerized environment.

## What the 'customer' want
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



## How to get started

Clone Repo
`git clone https://github.com/hbrspros/docker-nginx-reverseproxy.git`

Setup Proxy
`cd nginx`
`docker-compose -d up`

Setup app
`cd ../app`
`docker-compose -d up`
