# HOWTO: Nginx Reverse Proxy

## Abstract
An __Explanation for an University-Project__ for setting up a multi-domain Webservice Infrastructure with automated vhost configuration deployment and TLS-Cert generation in a dockerized environment.

## Problem
If you want to serve multiple domains (domains or even subdomains) on a system (host) with only one IP-Address, you need to introduce some kind of reverse proxy. Because you only have one single :443 Port on your system to listen at, there have to be a central instance which than routes the traffic to your endpoints.
This is the reason why nginx or webserver like apache2 lets you configure _vhosts_. Regarding to the Domain the Client will request, the server gets different documents or endpoints. The configuration of this _vhost_-Files was always not the best job to do, so its time to automate this configuration with existing (docker) Architectures.
When talking about automation it would we very useful to automate the Letsencrypt thing too. Take care of generating new certs every 90 days or generate new once when you deploy a new app, there would be no better time for automate this step too, because docker knows which systems are currently running and have a vhosts running, why we dont start a container (which has this information) and let him create the vhost- and letsencrypt files.
This is the part where we want to start our documentation/explanation.

## What we want
We want to implement:
- 2 Webapps with 2 seperated Domains on a Single host
  - Wordpress - from https://hub.docker.com/_/wordpress  (wordpress.example.com)
  - Whoami - from https://hub.docker.com/r/jennerwein/whoami (whoami.example.com)
with automated vhost deployment and letsencrypt-certificates with its HTTPS config automatically, with never touching this centralized Infrastructure for the next years (ahm weeks;)

# Architecture
Please see: https://raw.githubusercontent.com/JrCs/docker-letsencrypt-nginx-proxy-companion/master/schema.png

### Setup
There are two types of implementation of this docker-reverse proxy environment. On the one hand you can use the three container Architecture or the two container Architecture. In the following we will use the two node setup.

So we need:
- Database as container
- Webserver as container
- reverseproxy as container
- container manages TLS Certs

### Networking
- Network: Wordpress connected to database
- Internal Network: Wordpress connected to reverseproxy
- Internal Network: Whoami connected to reverseproxy
- External Network: reverseproxy connected to external interface eth0

## How to get started

### The Project Structure

This is the structure of our project.

    ├── app
    │   ├── whoami
    │   │   └── docker-compose.yml
    │   └── wordpress
    │           └── docker-compose.yml
    └── proxy
        └── docker-compose.yml

We split our repo in two subfolders. One is the ```app``` folder, where we place our applications in a subfolder, like Wordpress or whoami.
The other is the folder ```proxy``` where we store our Infrastructure compose file.

### Do it
Clone Repo

    git clone https://github.com/hbrspros/docker-nginx-reverseproxy.git

Setup Infrastructure Proxy

    cd proxy
    docker-compose -d up

Setup app: whoami

    cd ../app/whoami
    docker-compose -d up

Setup app: wordpress

    cd ../wordpress
    docker-compose -d up   

### Example Output

    root@ubuntu:~/docker-nginx-reverseproxy/proxy# docker-compose up
    WARNING: Some networks were defined but are not used by any service: nginx-proxy
    Creating network "proxy_internal" with the default driver
    Creating volume "proxy_conf" with default driver
    ...
    Creating volume "proxy_certs" with default driver
    Pulling nginx-proxy (jwilder/nginx-proxy:)...
    ...
    Status: Downloaded newer image for jwilder/nginx-proxy:latest
    Pulling letsencrypt (jrcs/letsencrypt-nginx-proxy-companion:)...
    …
    Status: Downloaded newer image for jrcs/letsencrypt-nginx-proxy-companion:latest
    Creating nginx-proxy ... done
    Creating nginx-proxy-le ... done
    …
    …
    Attaching to nginx-proxy, nginx-proxy-le
    nginx-proxy    | WARNING: /etc/nginx/dhparam/dhparam.pem was not found. A pre-generated dhparam.pem will be used for now while a new one
    nginx-proxy    | is being generated in the background.  Once the new dhparam.pem is in place, nginx will be reloaded.
    nginx-proxy    | forego     | starting dockergen.1 on port 5000
    nginx-proxy    | forego     | starting nginx.1 on port 5100
    nginx-proxy    | dockergen.1 | 2019/06/14 07:49:10 Generated '/etc/nginx/conf.d/default.conf' from 2 containers
    nginx-proxy    | dockergen.1 | 2019/06/14 07:49:10 Running 'nginx -s reload'
    nginx-proxy    | dockergen.1 | 2019/06/14 07:49:10 Watching docker events
    nginx-proxy    | dockergen.1 | 2019/06/14 07:49:10 Contents of /etc/nginx/conf.d/default.conf did not change. Skipping notification 'nginx -s reload'
    nginx-proxy-le | Generating a RSA private key
    nginx-proxy-le | .........................................++++
    nginx-proxy-le | ......++++
    nginx-proxy-le | writing new private key to '/etc/nginx/certs/default.key.new'
    nginx-proxy-le | -----
    nginx-proxy-le | Info: a default key and certificate have been created at /etc/nginx/certs/default.key and /etc/nginx/certs/default.crt.
    nginx-proxy-le | Info: Creating Diffie-Hellman group in the background.
    nginx-proxy-le | A pre-generated Diffie-Hellman group will be used for now while the new one
    nginx-proxy-le | is being created.
    nginx-proxy-le | Generating DH parameters, 2048 bit long safe prime, generator 2
    nginx-proxy-le | Reloading nginx proxy (nginx-proxy)...
    nginx-proxy-le | 2019/06/14 07:49:11 Generated '/etc/nginx/conf.d/default.conf' from 2 containers
    nginx-proxy-le | 2019/06/14 07:49:11 [notice] 59#59: signal process started
    nginx-proxy-le | 2019/06/14 07:49:12 Generated '/app/letsencrypt_service_data' from 2 containers
    nginx-proxy-le | 2019/06/14 07:49:12 Running '/app/signal_le_service'
    nginx-proxy-le | 2019/06/14 07:49:12 Watching docker events
    nginx-proxy-le | 2019/06/14 07:49:12 Contents of /app/letsencrypt_service_data did not change. Skipping notification '/app/signal_le_service'
    nginx-proxy-le | Sleep for 3600s
    nginx-proxy-le | This is going to take a long time
    nginx-proxy-le | Info: Diffie-Hellman group creation complete, reloading nginx.
    nginx-proxy-le | Reloading nginx proxy (nginx-proxy)...
    nginx-proxy-le | 2019/06/14 07:49:23 Contents of /etc/nginx/conf.d/default.conf did not change. Skipping notification ''
    nginx-proxy-le | 2019/06/14 07:49:23 [notice] 81#81: signal process started
    nginx-proxy    | 2019/06/14 07:51:25 [notice] 84#84: signal process started
    nginx-proxy    | Generating DH parameters, 2048 bit long safe prime, generator 2
    nginx-proxy    | This is going to take a long time
    nginx-proxy    | dhparam generation complete, reloading nginx

#### Explanation
The nginx and letsencrypt container both had the docker-gen implemented in the background. Then a new Container get ups following can be watched:
1. Nginx generates the new config for the vhost with the container IP
1. Letsencrypt gets the certs (with http challenge) and configure vhosts to get the HTTPS Server ready.

This ends up in this modification like this:

    root@ubuntu:~/docker-nginx-reverseproxy/proxy# diff /tmp/default.conf /var/lib/docker/volumes/proxy_conf/_data/default.conf
    61a62,95
    > # whoami.example.com
    > upstream whoami.example.com {
    >                               ## Can be connected with "proxy_internal" network
    >                       # whoami
    >                       server 172.23.0.4:8080;
    > }
    > server {
    >       server_name whoami.example.com;
    >       listen 80 ;
    >       access_log /var/log/nginx/access.log vhost;
    >       return 301 https://$host$request_uri;
    > }
    > server {
    >       server_name whoami.example.com;
    >       listen 443 ssl http2 ;
    >       access_log /var/log/nginx/access.log vhost;
    >       ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
    >       ssl_ciphers 'ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:!DSS';
    >       ssl_prefer_server_ciphers on;
    >       ssl_session_timeout 5m;
    >       ssl_session_cache shared:SSL:50m;
    >       ssl_session_tickets off;
    >       ssl_certificate /etc/nginx/certs/whoami.example.com.crt;
    >       ssl_certificate_key /etc/nginx/certs/whoami.example.com.key;
    >       ssl_dhparam /etc/nginx/certs/whoami.example.com.dhparam.pem;
    >       ssl_stapling on;
    >       ssl_stapling_verify on;
    >       ssl_trusted_certificate /etc/nginx/certs/whoami.example.com.chain.pem;
    >       add_header Strict-Transport-Security "max-age=31536000" always;
    >       include /etc/nginx/vhost.d/default;
    >       location / {
    >               proxy_pass http://whoami.example.com;
    >       }
    > }

As you can see a new upstream and two new vhosts are defined. Also the letsencrypt certs take their place.

## How to deploy new webapps (with TLS certs)
In this section we will describe all necessary options you have to set (in the docker-compose file for example) when starting a new app-container:

    ...
    environment:
      VIRTUAL_HOST: wordpress.example.com
      LETSENCRYPT_HOST: wordpress.example.com
      LETSENCRYPT_EMAIL: test@example.com

Please see our presentation for detailed information about Dockerfiles, docker-compose, networking, SSL-Labs result etc.
