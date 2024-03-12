# Practica-bitnami/wordpress Jose Francisco León López
## En esta práctica vamos a instalar wordpress/bitnami a través de docker-compose
### Para ello antes de empezar debemos instalar docker-compose y docker
#### El comando para instalar docker es:
~~~
sudo apt install docker.io
~~~
#### NOTA: Si no hacemos un apt update primero a la máquina no nos va a reconocer los comandos
#### El comando para instalar docker-compose es:
~~~
sudo apt install docker-compose
~~~
#### Una vez instalados los comandos creamos nuestro archivo de docker-compose:
~~~
version: '3'

services:
  wordpress:
    image: bitnami/wordpress:6.4.3
    environment: 
      - WORDPRESS_DATABASE_HOST=mysql
      - WORDPRESS_DATABASE_NAME=${WORDPRESS_DATABASE_NAME}
      - WORDPRESS_DATABASE_USER=${WORDPRESS_DATABASE_USER}
      - WORDPRESS_DATABASE_PASSWORD=${WORDPRESS_DATABASE_PASSWORD}
      - WORDPRESS_BLOG_NAME=${WORDPRESS_BLOG_NAME}
      - WORDPRESS_USERNAME=${WORDPRESS_USERNAME}
      - WORDPRESS_PASSWORD=${WORDPRESS_PASSWORD}
      - WORDPRESS_EMAIL=${WORDPRESS_EMAIL}
    volumes: 
      - wordpress_data:/bitnami/wordpress
    depends_on:
      - mysql
    restart: always
    networks:
      - frontend-network
      - backend-network

  mysql:
    image: mysql:8.0
    ports:
      - 3306:3306
    environment:
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
      - MYSQL_DATABASE=${WORDPRESS_DATABASE_NAME}
      - MYSQL_USER=${WORDPRESS_DATABASE_USER}
      - MYSQL_PASSWORD=${WORDPRESS_DATABASE_PASSWORD}
    volumes:
      - mysql_data:/var/lib/mysql
    networks: 
      - backend-network
    restart: always
    
  phpmyadmin:
    image: phpmyadmin
    ports:
      - 8080:80
    environment: 
      - PMA_HOST=mysql
    restart: always
    depends_on: 
      - mysql
    networks:
      - frontend-network
      - backend-network

  https-portal:
    image: steveltn/https-portal:1
    ports:
      - 80:80
      - 443:443
    restart: always
    environment:
      DOMAINS: '${DNS_DOMAIN_SECURE} -> http://wordpress:8080'
      STAGE: 'production' # Don't use production until staging works
      # FORCE_RENEW: 'true'
    networks:
      - frontend-network  
volumes: 
  mysql_data:
  wordpress_data:

networks: 
  frontend-network:
  backend-network:

~~~
### Cosas a recalcar del archivo es que hemos instalado: mysql,phpmyadmin,wordpress que para instalar el de bitnai tenemos que poner bitnami/wordpress y la version que queremos y https-portal que es para que tenga el certificado https.
### Después que las variables deben estar como podemos encontrarlas en docker hub porque si no, no va a funcionar.
### Y si no ponemos el STAGE en production en el https-portal no nos lo va a coger
### Una vez hecho todo esto ejecutamos el comando:
~~~
sudo docker-compose up -d
~~~
### Para levantar el contenedor y ya nos saldrá en nuestro dominio nuestro wordpress.
### El archivo .env se debe quedar así:
~~~
DNS_DOMAIN_SECURE=prestashop-bitnami.ddns.net

WORDPRESS_DATABASE_HOST=mysql
WORDPRESS_DATABASE_NAME=wordpress 
WORDPRESS_DATABASE_USER=wp_user
WORDPRESS_DATABASE_PASSWORD=wp_pass

MYSQL_ROOT_PASSWORD=root

WORDPRESS_BLOG_NAME="Sitio web de Jose Francisco"
WORDPRESS_USERNAME=admin
WORDPRESS_PASSWORD=admin
WORDPRESS_EMAIL=demo@demo.es
~~~
