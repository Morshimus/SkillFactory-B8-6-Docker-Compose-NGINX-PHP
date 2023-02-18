# SkillFactory-B8-6-Docker-Compose-NGINX-PHP

## Задание:
* [x] - :one: ~~**Возьмите исходники простейшего PHP-приложения.**~~
  > Взяли, в разрезе задания это не так важно..

* [x] - :two: ~~**Добавьте docker-compose.yml конфигурацию с двумя сервисами: php и nginx.Nginx должен использовать готовый докер-образ, сервис php должен собираться из текущей директории. Для этого в репозитории есть Dockerfile.**~~
 > В задание не уточнено, но помимо сборки еще и монтировать должен www папку, ну это понятно интуитивно.

* [x] - :three: ~~**Добавьте healthcheck для php-сервиса, который ходил бы на http://nginx и проверял содержимое на наличие строки «works» (это можно сделать через curl и grep).**~~
 > Ок.
 
* [x] - :four: ~~**Запустить приложение через Docker Compose.**~~

## Поехали.

```
version: '3.8'

x-logging: &logging
 driver: "json-file"
 options:
   max-size: "100m"
   max-file: "1"

services:
 nginx:
   image: nginx:latest
   ports:
     - "127.0.0.1:${WEB_PORT:-80}:80"
   volumes:
     - ./nginx:/etc/nginx/conf.d:rw
     - ./www:/var/www:ro
   restart: unless-stopped
   networks:
    backend:
     aliases:
       - nginx
    frontend:
   logging: *logging
   deploy:
     resources:
       limits:
         cpus: "${DOCKER_nginx_CPUS:-1}"
         memory: "${DOCKER_nginx_MEMORY:-200MB}"

 php:
   build: .
   volumes:
     - ./www:/var/www:ro
   restart: unless-stopped
#   depends_on:
#     nginx:
#      condition: service_healthy
   networks:
    backend:
     aliases:
       - php
   logging: *logging
   healthcheck:
     test: ["CMD","curl http://nginx:80 | grep works"]
     interval: 5s
     timeout: 5s
     retries: 20
   deploy:
     resources:
       limits:
         cpus: "${DOCKER_php_CPUS:-1}"
         memory: "${DOCKER_php_MEMORY:-200MB}"
        
networks:
 frontend:
   driver: bridge
   driver_opts:
      com.docker.network.enable_ipv6: "false"
   ipam:
     driver: default
     config:
      - subnet: 10.111.21.0/28
 backend:
   internal: true
   driver: bridge
   driver_opts:
    com.docker.network.enable_ipv6: "false"
   ipam:
     driver: default
     config:
      - subnet: 10.111.22.0/28   
```
![image](https://am3pap003files.storage.live.com/y4mFfXxDX2uvAM93PqNkhggHcBigfB3yX3PW7I5-szpkOVhxCgQ0owiYODBc5_DbcIZ_CiSEfqrZDy8tZKL7ys13NMUcl5_-sqrOsCbFf06I29bOzWTWF6J-f699sKZmPr0_JK68UhyPdUfq32KgesHJ9mtybg0dCC6VrKe6UsG3j-FESYDuLjtOnflGOXir9-P?encodeFailures=1&width=1652&height=396)
