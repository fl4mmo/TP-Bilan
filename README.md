Voici les instructions pour ce tp

# Prérequis

Pour installer docker vous devrez importer le fichier install_docker.sh
-
Vous effectuerez ensuite un sh install_docker.sh


## Pour l'installation de Wordpress avec docker

Pour installer wordpress avec docker rien de plus simple, il vous suffit de lancer un docker run -p 8080:80 -d wordpress

Le -p sert à désigner le port que nous souhaitons attribuer à notre container avec le port d'accès à gauche (lorsque vous voudrez accéder à l'interface de votre container) et le port local utilisé à droite.
Vous êtes bien entendu libre de choisir le port que vous souhaitez.

Le -d quant à lui sert à réaliser le lancement du container de manière détachée.

## Pour l'installation de Zabbix avec docker

Pour installer Zabbix avec docker nous allons passer par un docker-compose contenant ceci: 
version: '3.5'

services:
  zabbix-db:
   image: zabbix/zabbix-server-mysql:latest
    container_name: zabbix-db
    environment:
      MYSQL_ROOT_PASSWORD: zabbix_password
      MYSQL_USER: zabbix
      MYSQL_PASSWORD: zabbix_password
      MYSQL_DATABASE: zabbix
    volumes:
      - zabbix-db-data:/var/lib/mysql
    networks:
      - zabbix-net

  zabbix-server:
    image: zabbix/zabbix-server-mysql:latest
    container_name: zabbix-server
    environment:
      DB_SERVER_HOST: zabbix-db
      MYSQL_USER: zabbix
      MYSQL_PASSWORD: zabbix_password
      MYSQL_DATABASE: zabbix
      ZBX_JAVA_GATEWAY: zabbix-java-gateway
    depends_on:
      - zabbix-db
    ports:
      - "10051:10051"
    networks:
      - zabbix-net

  zabbix-web:
    image: zabbix/zabbix-web-nginx-mysql:latest
    container_name: zabbix-web
    environment:
      ZBX_SERVER_HOST: zabbix-server
      DB_SERVER_HOST: zabbix-db
      MYSQL_USER: zabbix
      MYSQL_PASSWORD: zabbix_password
      MYSQL_DATABASE: zabbix
    ports:
      - "80:8081"
    depends_on:
      - zabbix-server
    networks:
      - zabbix-net

  zabbix-java-gateway:
    image: zabbix/zabbix-java-gateway:latest
    container_name: zabbix-java-gateway
    networks:
      - zabbix-net

networks:
  zabbix-net:(choix du réseau)

volumes:
  zabbix-db-data:(choix de l'espace utilisé)

Vous le retrouverez sur mon github sous le nom de docker-compose.yml vous devrez donc le télécharger avec un wget https://github.com/fl4mmo/TP-Bilan/blob/main/docker-compose.yml

Puis vous le lancerez avec la commande docker-compose up 
