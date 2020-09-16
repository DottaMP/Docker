# Docker <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcTTAzPMBlhHWrHahasAijS7gxPx3hKMrx9Qfw&usqp=CAU" width="100" height="40">&nbsp;

#### Índice de aprendizagem:
 01 Introdução a Docker  
 02 HandsOn - Docker instalação  
 03 HandsOn - Deploy DB e Spring Part I  
 04 HandsOn - Deploy DB e Spring Part II  
 05 HandsOn - Docker Angular  
 06 HandsOn - Comandos Docker  
 
#### Comandos cdm: 
*docker-compose up -d --build* > Inicializa um novo container;  
*docker ps* > Mostra os containers que existem;  
*docker logs ID* > Roda o container pelo ID;  
*docker stop ID* > Para parar de rodar o container;  
*docker rm ID* > Para remover um container.  

#### Configurações necessárias, criar os arquivos abaixo:

1. Dockerfile  
FROM mysql:8.0.21  
EXPOSE 3308  

2. Dockerfile (que fica dentro na pasta-api)  
FROM maven:3.6.1-jdk-11-slim  

3. docker-compose.yml  
```version: '3.4'  
services:
  db:
    command: mysqld --default-authentication-plugin=mysql_native_password
    restart: always
    build:
      context: .
      dockerfile: Dockerfile
    environment:
      TZ: America/Sao_Paulo
      MYSQL_ROOT_PASSWORD: #######
      MYSQL_USER: ####
      MYSQL_PASSWORD: #######
      MYSQL_DATABASE: nome_db
    ports:
      - "3308:3306"
    networks:
      - generation-network
  nome_db-api:
    restart: always
    build: ./nome_db
    working_dir: /nome_db
    environment:
      TZ: America/Sao_Paulo
      SPRING_BOOT_ENVIRONMENT: Production
    volumes:
      - ./nome_db:/nome_db
      - ~/.m2:/root/.m2
    ports:
      - "9000:8080"
    command: mvn clean spring-boot:run
    links:
      - db
    depends_on:
      - db
    networks:
      - generation-network
networks:
    generation-network:
        driver: bridge
```
