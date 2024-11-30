# Monitoreo de Red Mediante Contenedores en Docker
Pedro Rafael Muñoz Barrero - Santiago Gonzalez Escobar

## Introducción

En el presente informe se documenta de manera detallada el proceso de contenerización de la plataforma de monitoreo Zabbix utilizando Docker, una herramienta de virtualización ligera que facilita la creación, implementación y gestión de aplicaciones en contenedores. El objetivo principal de este proyecto es implementar un entorno funcional y escalable de Zabbix, compuesto por sus diferentes componentes (servidor, base de datos, gateway Java e interfaz web), asegurando una integración eficiente y simplificando la administración del sistema mediante la tecnología de contenedores.

A lo largo del informe, se presentan de forma estructurada los pasos seguidos para configurar cada contenedor, explicando los comandos utilizados, las variables de entorno definidas y las configuraciones aplicadas. Asimismo, se incluyen evidencias visuales (como capturas de pantalla y salidas de comandos) que respaldan el correcto funcionamiento del sistema implementado. Este documento no solo sirve como guía técnica para la implementación de Zabbix en un entorno contenerizado, sino que también destaca los beneficios de utilizar Docker en la gestión de aplicaciones complejas como esta.

## Pasos de Instalación

El ejemplo demuestra cómo ejecutar el servidor Zabbix con soporte de base de datos MySQL, la interfaz web Zabbix basada en el servidor web Nginx y la puerta de enlace Java Zabbix.

1. Crear una red dedicada para los contenedores de componentes de Zabbix:
   
```bash
docker network create --subnet 172.20.0.0/16 --ip-range 172.20.240.0/20 zabbix-net
```
1.1 Crea una nueva red en Docker
```bash
docker network create
```
1.2 Define la subred completa que será utilizada por la red zabbix-net.
```bash
--subnet 172.20.0.0/16
```
172.20.0.0/16: Especifica un rango de direcciones IP, donde:
    
   - El prefijo 172.20.0.0 es el inicio del rango.
   - /16 indica que se usarán los primeros 16 bits como la parte de la red, permitiendo hasta 65,536 direcciones IP (de 172.20.0.1 a 172.20.255.254).
     
1.3 Restringe el rango de direcciones IP que se asignarán dinámicamente a los contenedores en esta red.
```bash
--ip-range 172.20.240.0/20
```

Restringe el rango de direcciones IP que se asignarán dinámicamente a los contenedores en esta red.

172.20.240.0/20: Este rango es más pequeño que la subred principal y permite 4,096 direcciones IP (de 172.20.240.1 a 172.20.255.254).

Esto significa que la red puede manejar una subred más grande, pero las IPs asignadas a los contenedores estarán limitadas al rango definido por --ip-range.

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
2.1 Ejecuta un nuevo contenedor basado en la imagen especificada (en este caso, MySQL 8.0 de Oracle).
```bash
docker run
```
2.2 Asigna un nombre al contenedor: mysql-server. Esto facilita su identificación y gestión, por ejemplo, con comandos como docker stop mysql-server.
```bash
--name mysql-server
```
2.3 Asocia un pseudo-TTY al contenedor. Aunque este parámetro no es esencial para una base de datos, puede ser útil para depuración o ejecución interactiva.
```bash
--name mysql-server
```
2.4 Definición de variables de entorno

Se utilizan para configurar el servidor MySQL:

- MYSQL_DATABASE="zabbix": Crea automáticamente una base de datos llamada zabbix al iniciar el contenedor.

- MYSQL_USER="zabbix": Crea un usuario con el nombre zabbix.

- MYSQL_PASSWORD="zabbix_pwd": Establece la contraseña del usuario zabbix como zabbix_pwd.

- MYSQL_ROOT_PASSWORD="root_pwd": Establece la contraseña del usuario root (administrador) como root_pwd.

2.5 Conecta este contenedor a la red zabbix-net. Esto permite que otros contenedores en la misma red (como el servidor de Zabbix) puedan comunicarse directamente con este servidor MySQL.
```bash
--network=zabbix-net
```
2.6 Configura la política de reinicio del contenedor. Este ajuste asegura que:
```bash
--network=zabbix-net
```
- Si el contenedor falla o el servidor Docker se reinicia, el contenedor se reiniciará automáticamente.

- No se reiniciará si lo detienes manualmente (con docker stop).

3. Iniciar la instancia de la puerta de enlace Java de Zabbix

```bash
docker run --name zabbix-java-gateway -t ^
             --network=zabbix-net ^
             --restart unless-stopped ^
             -d zabbix/zabbix-java-gateway:alpine-7.0-latest
```
3.1 Inicia un nuevo contenedor basado en una imagen de Docker especificada.
```bash
docker run
```
3.2 Asigna el nombre zabbix-java-gateway al contenedor, lo que facilita su identificación y gestión con comandos como docker logs zabbix-java-gateway.
```bash
--name zabbix-java-gateway
```
3.3 Asocia un pseudo-TTY al contenedor. Aunque no es crítico en este caso, puede ser útil para tareas interactivas o depuración.
```bash
-t
```
3.4 Conecta este contenedor a la red Docker llamada zabbix-net. Esto permite que otros contenedores en la misma red (como el servidor Zabbix y la base de datos) se comuniquen fácilmente con este Java Gateway.
```bash
--network=zabbix-net
```
3.5 Establece la política de reinicio del contenedor. Esto asegura que:

- El contenedor se reiniciará automáticamente si falla o si el servidor Docker se reinicia.
  
- No se reiniciará automáticamente si lo detienes manualmente (con docker stop).

```bash
--restart unless-stopped
```
3.6 Ejecuta el contenedor en segundo plano (modo "detached"), dejando la terminal libre para otros comandos.
```bash
-d
```
3.7 Especifica la imagen de Docker que se usará para crear el contenedor:

```bash
zabbix/zabbix-java-gateway:alpine-7.0-latest
```
- zabbix/zabbix-java-gateway: Es la imagen oficial de Zabbix para el Java Gateway.

- alpine-7.0-latest: Es una versión optimizada y ligera de la imagen basada en Alpine Linux, diseñada para Zabbix 7.0.

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
4.1 Inicia un nuevo contenedor basado en la imagen especificada (Zabbix Server con soporte para MySQL).
```bash
docker run
```
4.2 Asigna el nombre zabbix-server-mysql al contenedor, facilitando su identificación y administración.
```bash
--name zabbix-server-mysql
```
4.3 Asocia un pseudo-TTY al contenedor, lo que puede ser útil para tareas interactivas o de depuración, aunque no es esencial en este caso.
```bash
-t
```
4.4 Definición de variables de entorno
```bash
-e
```
- DB_SERVER_HOST="mysql-server": Especifica el nombre o dirección del servidor MySQL. En este caso, apunta al contenedor mysql-server en la red zabbix-net.

- MYSQL_DATABASE="zabbix": Define el nombre de la base de datos MySQL a la que se conectará Zabbix. Debe coincidir con la configuración del contenedor MySQL.

- MYSQL_USER="zabbix": Usuario de MySQL utilizado por Zabbix para conectarse a la base de datos.

- MYSQL_PASSWORD="zabbix_pwd": Contraseña para el usuario zabbix.

- MYSQL_ROOT_PASSWORD="root_pwd": Contraseña del usuario administrador (root) de MySQL.

- ZBX_JAVAGATEWAY="zabbix-java-gateway": Especifica el nombre del contenedor donde se ejecuta el Zabbix Java Gateway, que permite monitorear aplicaciones Java vía JMX.

4.5 Conecta este contenedor a la red zabbix-net, permitiendo que el servidor Zabbix se comunique con:

- El servidor MySQL (mysql-server).
  
- El Java Gateway (zabbix-java-gateway).
```bash
--network=zabbix-net
```
4.6 Publica el puerto 10051 del contenedor en el host, permitiendo que los agentes Zabbix y otros sistemas accedan al servidor Zabbix desde fuera del contenedor.
```bash
-p 10051:10051
```
4.7 Ejecuta el contenedor en segundo plano (modo "detached"), liberando la terminal para otros comandos.
```bash
-d
```
4.8 Especifica la imagen de Docker que se usará para crear el contenedor:
```bash
zabbix/zabbix-server-mysql:alpine-7.0-latest
```
- zabbix/zabbix-server-mysql: Es la imagen oficial de Zabbix Server con soporte para bases de datos MySQL.

- alpine-7.0-latest: Es una versión ligera de la imagen basada en Alpine Linux, optimizada para Zabbix 7.0.

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
5.1 Asigna el nombre zabbix-web-nginx-mysql al contenedor, lo que facilita su identificación y gestión.
```bash
--name zabbix-web-nginx-mysql
```
5.2 Estas variables configuran la interfaz web de Zabbix y su conexión con la base de datos y el servidor Zabbix:

- ZBX_SERVER_HOST="zabbix-server-mysql": Especifica la dirección del servidor Zabbix al que se conectará la interfaz web. En este caso, apunta al contenedor zabbix-server-mysql dentro de la red Docker.

- DB_SERVER_HOST="mysql-server": Especifica la dirección del servidor MySQL que almacena la base de datos de Zabbix. Aquí apunta al contenedor mysql-server.

- MYSQL_DATABASE="zabbix": Define el nombre de la base de datos de Zabbix en MySQL.

- MYSQL_USER="zabbix": Especifica el usuario de MySQL que será utilizado por la interfaz web para conectarse a la base de datos.

- MYSQL_PASSWORD="zabbix_pwd": Define la contraseña del usuario zabbix en MySQL.

- MYSQL_ROOT_PASSWORD="root_pwd": Contraseña del usuario administrador (root) de MySQL.

5.3 Publica el puerto 8080 del contenedor en el puerto 80 del host. Esto permite que la interfaz web de Zabbix sea accesible desde un navegador en el puerto 80 del servidor (usualmente utilizado para HTTP).

Puertos involucrados:

- 8080: Puerto interno del contenedor donde Nginx sirve la interfaz web.

- 80: Puerto en el host donde los usuarios accederán a la interfaz web (por ejemplo, http://localhost).
```bash
-p 80:8080
```
5.4 Configura la política de reinicio del contenedor para que:
```bash
--restart unless-stopped
```
- Se reinicie automáticamente si falla o si el servidor Docker se reinicia.

- No se reinicie automáticamente si se detiene manualmente (docker stop).

5.5 Especifica la imagen de Docker que se usará para crear el contenedor:

```bash
zabbix/zabbix-web-nginx-mysql:alpine-7.0-latest
```

- zabbix/zabbix-web-nginx-mysql: Es la imagen oficial de la interfaz web de Zabbix que utiliza

- Nginx como servidor web y MySQL como base de datos.
alpine-7.0-latest: Es una versión ligera basada en Alpine Linux, optimizada para Zabbix 7.0.

## Contenedores creados 
![image](https://github.com/user-attachments/assets/ffcfc9ec-54d8-4513-b729-85274cd262a1)

## Zabbix 
![image](https://github.com/user-attachments/assets/eeefb6b7-65f9-4584-b876-87bde7570cd7)

## Conclusiones
La contenerización de Zabbix utilizando Docker demostró ser un enfoque eficiente para desplegar y gestionar esta herramienta de monitoreo. En conclusión, Docker se posiciona como una herramienta clave para desplegar aplicaciones como Zabbix, permitiendo a los administradores de sistemas aprovechar las ventajas de la contenerización para simplificar el manejo de infraestructuras críticas de monitoreo. Este enfoque no solo optimiza los recursos disponibles, sino que también asegura una mayor portabilidad y consistencia en los despliegues.
