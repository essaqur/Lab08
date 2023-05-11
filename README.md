# Lab08
### Docker-compose
### 1
```
mkdir Lab08
cd Lab08
```
### 2
```
nano Dockerfile-client

FROM ubuntu:22.10

RUN apt update && apt install curl --no-install-recommends -y

ENV SERVER_URL http://google.com

ENTRYPOINT bash -c "while true ; do sleep 5 && curl -qq $SERVER_URL; done" 
```
### 3
```
nano Dockerfile-server

FROM ubuntu:22.10

RUN apt update && apt install npm --no-install-recommends -y && \
    mkdir -p /opt/server

WORKDIR /opt/server

RUN npm init -y && \
    npm install json-server

COPY package.json /opt/server/package.json
COPY db.json /opt/server/db.json

EXPOSE 3000

ENTRYPOINT ["npm", "start"]
```
### 4
```
nano db.json

{
  "users": [
    {
      "id": 1,
      "name": "Ivan",
      "age": 18
    },
    {
      "name": "Maksim",
      "id": 2,
      "age": 19
     }
   ]
}
```
### 5
```
nano package.json

{
  "name": "server",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "json-server": "^0.17.3"
  },
  "scripts": {
    "start": "json-server -H 0.0.0.0 -p 3000 db.json"
  }
}
```
### 6
```
nano docker-compose.yml

version: '3.9'

services:
  server:
    image: lab8:server
    container_name: server
    networks:
      - lab8-network
  client:
    image: lab8:client
    container_name: client
    networks:
      - lab8-network
    environment:
      - SERVER_URL=http://server:3000/users

networks:
  lab8-network:
    driver: "bridge"
    ipam:
      driver: default
      config:
        - subnet: 172.20.0.0/16
```
### 7
```
docker build -f Dockerfile-server -t lab8:server .
docker build -f Dockerfile-client -t lab8:client .
docer-compose up -d
docker logs client
docker logs server
docker-compose down
```
![image](https://user-images.githubusercontent.com/109910115/234850269-1c5d059d-4bc9-43e2-95eb-89364212c31a.png)
![image](https://user-images.githubusercontent.com/109910115/234850590-a054485c-0b42-41d9-a1f6-62c56bb77691.png)
![image](https://user-images.githubusercontent.com/109910115/234850845-2974960a-82df-4478-85e6-af266ac21492.png)
![image](https://user-images.githubusercontent.com/109910115/234851264-5da0f32b-cb2f-4ebf-ba20-a3997e7366e5.png)
![image](https://user-images.githubusercontent.com/109910115/234851623-1fa8149c-0ded-4c09-a8e7-8b98643f6efd.png)
