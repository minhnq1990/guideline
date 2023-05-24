## Purpose
A general docker-compose template for deploy java app
## Setup
```
version: '3'
services:
  app:
    image: openjdk
    container_name: app
    user: 1001:1001
    restart: always
    volumes:
      - ./:/etc/app
      - "/etc/localtime:/etc/localtime:ro"
      - "/etc/timezone:/etc/timezone:ro"
    working_dir: /etc/app
    command: java -jar ./threecb-0.0.1-SNAPSHOT.jar
    network_mode: host
```
