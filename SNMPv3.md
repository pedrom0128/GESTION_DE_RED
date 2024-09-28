# SNMPv3

## Pedro Muñoz - Santiago Gonzalez UMNG - TEC - Gestión de Red

## Introducción a SNMPv3:

  SNMPv3 es un protocolo de la capa de aplicación que facilita el intercambio de información de gestión entre dispositivos de red. Es parte del conjunto de protocolos TCP/IP y se utiliza ampliamente en redes empresariales.
  Está basado en peticiones y respuestas sobre UDP.
  
### Componentes Básicos:

•   Agentes: Son componentes de software que se ejecutan en los dispositivos a gestionar.

•   Gestores: Son componentes de software que se ejecutan en los sistemas de gestión de red.

•   MIB (Management Information Base): Es una base de datos que contiene la información de gestión de la red.

### Paquete Net-SNMP

SNMP: Comandos propios del gestor

•   Se debe instalar en el equipo gestor.

•   Archivo de configuración en /etc/snmp/snmp.conf

SNMPD: Sofware del Agente

•   Se debe instalar en los equipos a gestonar.

•   Su configuración se realiza en el fichero /etc/snmp/snmpd.conf.

•   Escucha peticiones el el puerto UDP 161.

## Pasos de Instalación y Configuración

### Primer paso

![image](https://github.com/user-attachments/assets/b71525eb-3769-43fd-9552-179f04cf805b)

El comando dnf install net-snmp net-snmp-utils net-snmp-devel en CentOS se utiliza para instalar paquetes relacionados con el Protocolo Simple de Gestión de Red (SNMP). 

•   net-snmp: Este paquete contiene el demonio SNMP (snmpd) y las herramientas básicas necesarias para la gestión y monitoreo de redes mediante SNMP.

•   net-snmp-utils: Incluye utilidades adicionales para interactuar con el demonio SNMP, como snmpwalk, snmpget, y otras herramientas útiles para consultar y gestionar dispositivos de red.

•   net-snmp-devel: Proporciona archivos de desarrollo necesarios para compilar aplicaciones que utilizan la biblioteca SNMP.

### Segundo paso

![image](https://github.com/user-attachments/assets/de9c767f-9c6f-4745-b7d6-49243283949e)

Con este comando paramos el servicio de agente snmpd para realizar su configuración.

### Tercer paso

![image](https://github.com/user-attachments/assets/2678191f-8213-440d-9af4-257e04921003)

Este comando crea un usuario SNMPv3 llamado admin con acceso de solo lectura.
net-snmp-config --create-snmpv3-user -ro -A SHAmunoz -X AESmunoz -a sha -x AES admin 

Parámetros:

-ro: Acceso de solo lectura.

-A SHAmunoz: Protocolo de autenticación SHA con la frase de paso “SHAmunoz”.

-X AESmunoz: Protocolo de privacidad AES con la frase de paso “AESmunoz”.

-a sha: Especifica el uso de SHA para autenticación.

-x AES: Especifica el uso de AES para privacidad.

### Cuarto paso 

![image](https://github.com/user-attachments/assets/0c845a0a-8f33-48c6-90a1-1904b1e0f56d)

Se ejecuta el servicio SNMP para gestionar y monitorear dispositivos de red.

Se aegura que el servicio esté siempre activo después de un reinicio del sistema.

Se verifica el estado actual.

### Quinto paso

![image](https://github.com/user-attachments/assets/d5ca4305-9083-4296-a199-597084178224)

snmpwalk:
Función: Es una herramienta que se utiliza para consultar un agente SNMP y obtener una lista de información basada en el OID (Object Identifier).

-v3:
Función: Especifica que se utilizará la versión 3 de SNMP, que incluye mejoras de seguridad como autenticación y privacidad.

-u admin:
Función: Define el nombre de usuario SNMP, en este caso, admin.

-l authPriv:
Función: Establece el nivel de seguridad en authPriv, lo que significa que se requiere autenticación y privacidad (cifrado).

-a SHA:
Función: Especifica que se utilizará el protocolo de autenticación SHA (Secure Hash Algorithm).

-A SHAmunoz:
Función: Proporciona la frase de paso para la autenticación con SHA, que en este caso es SHAmunoz.

-x AES:
Función: Indica que se utilizará el protocolo de privacidad AES (Advanced Encryption Standard) para cifrar los datos.

-X AESmunoz:
Función: Proporciona la frase de paso para el cifrado con AES, que en este caso es AESmunoz.
