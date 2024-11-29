# Monitoreo de Red Mediante Contenedores en Docker
Pedro Rafael Muñoz Barrero - Santiago Gonzalez Escobar

## Introducción

## Pasos de Instalación

El ejemplo demuestra cómo ejecutar el servidor Zabbix con soporte de base de datos MySQL, la interfaz web Zabbix basada en el servidor web Nginx y la puerta de enlace Java Zabbix.

1. Crear una red dedicada para los contenedores de componentes de Zabbix:
   
```bash
docker network create --subnet 172.20.0.0/16 --ip-range 172.20.240.0/20 zabbix-net
```

2. Iniciar una instancia de servidor MySQL vacía.
   
```bash
docker run --name mysql-server -t ^
             -e MYSQL_DATABASE="zabbix" ^
             -e MYSQL_USER="zabbix" ^
             -e MYSQL_PASSWORD="zabbix_pwd" ^
             -e MYSQL_ROOT_PASSWORD="root_pwd" ^
             --network=zabbix-net ^
             --restart unless-stopped ^
             -d mysql:8.0-oracle ^
             --character-set-server=utf8 --collation-server=utf8_bin ^
             --default-authentication-plugin=mysql_native_password
```

3. Iniciar la instancia de la puerta de enlace Java de Zabbix

```bash
docker run --name zabbix-java-gateway -t ^
             --network=zabbix-net ^
             --restart unless-stopped ^
             -d zabbix/zabbix-java-gateway:alpine-7.0-latest
```

4. Inicie la instancia del servidor Zabbix y vincule la instancia con la instancia del servidor MySQL creada

```bash
docker run --name zabbix-server-mysql -t ^
            -e DB_SERVER_HOST="mysql-server" ^
            -e MYSQL_DATABASE="zabbix" ^
            -e MYSQL_USER="zabbix" ^
            -e MYSQL_PASSWORD="zabbix_pwd" ^
            -e MYSQL_ROOT_PASSWORD="root_pwd" ^
            -e ZBX_JAVAGATEWAY="zabbix-java-gateway" ^
            --network=zabbix-net ^
            -p 10051:10051 ^
            --restart unless-stopped ^
            -d zabbix/zabbix-server-mysql:alpine-7.0-latest
```

5. Inicie la interfaz web de Zabbix y vincule la instancia con el servidor MySQL creado y las instancias del servidor Zabbix

```bash
docker run --name zabbix-web-nginx-mysql -t ^
             -e ZBX_SERVER_HOST="zabbix-server-mysql" ^
             -e DB_SERVER_HOST="mysql-server" ^
             -e MYSQL_DATABASE="zabbix" ^
             -e MYSQL_USER="zabbix" ^
             -e MYSQL_PASSWORD="zabbix_pwd" ^
             -e MYSQL_ROOT_PASSWORD="root_pwd" ^
             --network=zabbix-net ^
             -p 80:8080 ^
             --restart unless-stopped ^
             -d zabbix/zabbix-web-nginx-mysql:alpine-7.0-latest
```

