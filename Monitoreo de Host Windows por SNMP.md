# Monitoreo de Host Windows por SNMP desde Zabbix

### Pedro Muñoz - Santiago Gonzalez UMNG - TEC - Gestión de Red

## Introducción:

La monitorización de sistemas es fundamental para garantizar el rendimiento, la disponibilidad y la seguridad de los equipos dentro de una infraestructura de TI. En este reporte, se documenta la implementación del protocolo SNMP (Simple Network Management Protocol) en hosts con sistemas operativos Windows 10 y 11, integrados en un entorno de monitoreo utilizando el servidor Zabbix. SNMP permite la recolección y monitoreo de parámetros críticos como el uso de CPU, memoria, almacenamiento y otros recursos esenciales para la operatividad del sistema. El uso de Zabbix, una plataforma ampliamente reconocida para la monitorización de redes, proporciona una solución centralizada y eficiente para la gestión de los recursos. El propósito de este estudio es evaluar la efectividad de SNMP en entornos Windows 10/11 y su integración con Zabbix, brindando una visión detallada de su rendimiento en tiempo real.

## Objetivo General

Implementar y evaluar la monitorización de hosts con sistemas operativos Windows 10 y 11 mediante el protocolo SNMP, utilizando Zabbix como plataforma de monitoreo, para mejorar la gestión de recursos y el control del rendimiento del sistema.

## Objetivos Específicos

Configurar el protocolo SNMP en los sistemas operativos Windows 10 y 11 para permitir la recolección de datos en tiempo real.

Integrar los hosts de Windows con el servidor Zabbix para el monitoreo.

Establecer plantillas y métricas personalizadas en Zabbix que permitan la visualización y análisis del rendimiento de los hosts monitoreados.

Evaluar la precisión de los datos recolectados y la capacidad de SNMP para detectar anomalías o problemas de rendimiento en los sistemas.

Generar reportes que reflejen el estado de los hosts monitoreados, destacando posibles mejoras o ajustes necesarios en la infraestructura.

# ÍTEMS

## Información Previa

Dirección IP del servidor Zabbix: 192.168.159.84.

Dirección IP de Windows: 192.168.159.235.

## a. Habilitación de SNMP sobre Windows

### Primer Paso

Accede al buscador de Windows y busca "Servicios"

![image](https://github.com/user-attachments/assets/7c7181d7-4502-40f2-a6a9-cd48c84971d3)

### Segundo Paso 

Localiza el Servicio SNMP y selecciona "Propiedades"

![image](https://github.com/user-attachments/assets/376212dc-0047-4568-ab21-2e7219112cb9)

### Tercer Paso

En la pestaña "Agente", introduce tu correo electrónico y la ubicación del host. En "Servicios", selecciona todas las opciones: "Físico", "Aplicaciones", "Enlace de datos y subred", "Internet" y "Punto a punto".
Haz clic en "Aplicar".

![image](https://github.com/user-attachments/assets/88ca5057-09e3-4275-804b-17e404ee104b)

### Cuarto Paso

Dirígete a la pestaña "Seguridad". Añade una nueva comunidad: Introduce el nombre de la comunidad que desees. Configura la opción para aceptar paquetes SNMP de cualquier host.
Haz clic en "Aplicar". Reiniciar el servicio SNMP.

![image](https://github.com/user-attachments/assets/fcedfbba-5c0d-4137-b7bf-8309b329257f)

## b. Registro de Host Monitoreado por SNMP a Zabbix

### Primer Paso 

Se crea el Host en Zabbix con los siguientes parámetros.

1. Escribe un nombre adecuado para el host.
2. Escoge la "Plantilla" a utilizar en este caso Windows by SNMP.
3. Selecciona el grupo "Virtual Machines"
4. Escribe la dirección IP del host SNMP, para este caso 192.168.259.235.
5. Selecciona el puerto 161 para el protocolo.

![image](https://github.com/user-attachments/assets/aceab9f6-4307-4127-a71d-cbdff3845e60)

### Segundo Paso

Dirígete a la sección de Macros y en "Value" escribe el nombre de la comunidad creada en el host Windows y haz clic en "Update".

![image](https://github.com/user-attachments/assets/9b59efed-064b-4714-9367-04e4bdd2b195)

### Tercer Paso

Espera la conexión del Servidor al Host y viceversa.

![image](https://github.com/user-attachments/assets/f890b5fb-8dac-44b8-adf2-f088dc175c30)

## c. Monitoreo de dos variables mediante SNMP desde Zabbix a un host Windows

### Primer Paso

Ingresa a los gráficos del Host que se va a monitorear.

![image](https://github.com/user-attachments/assets/010afadd-aa01-45d7-8d87-2d0d6cdb732a)

### Segundo Paso

Allí se encuentran las estadísticas y el monitoreo que está haciendo Zabbix mediante el protocolo SNMP.

![image](https://github.com/user-attachments/assets/99790b20-5d45-4f42-84aa-620aec7c3496)

### Tercer Paso

Escoge dos variables las cuales deseas monitorear y realizar las pruebas pertinentes, en este caso particular, se selecciona el almacenamiento y la tarjeta de red. 

![image](https://github.com/user-attachments/assets/a5149268-6ad3-4fad-b4b0-6de559333ced)

![image](https://github.com/user-attachments/assets/5b56cacf-738a-4bad-910d-5484936787d6)

## d. Envío de Alerta de Falla

### Primer Paso

Realiza una saturación de alguna variable para forzar una falla.

![image](https://github.com/user-attachments/assets/4b28e050-5e6e-403a-8683-3205c62c2678)

### Segundo Paso

En el correo registrado, llegará la información de la falla con lo que está sucediendo.

![image](https://github.com/user-attachments/assets/721b6bb2-9b8d-4e85-9fa8-c2d8cf8563fa)

## e. Protocolo de Prueba de las dos Variables Monitoreadas

### Primer Paso

Dirígete al host y realiza el protocolo de prueba para la tarjeta de red, deshabilitándola y forzando una falla.

![image](https://github.com/user-attachments/assets/60f906eb-173d-4085-b186-ae58ef6268e9)

### Segundo Paso

Revisa en las gráficas lo que sucede con la tarjeta de red y como consecuencia se obtiene que no hay tráfico debido a que se encuentra deshabilitada.

![image](https://github.com/user-attachments/assets/000b0ff5-d954-425a-aa71-6009b1553f6d)

### Tercer Paso

Como consecuencia también el host se cae debido que se encuentra conectado y en comunicación sobre la dirección IP que cuenta el equipo por ello, en el servidor Zabbix se encuentra deshabilitado.

![image](https://github.com/user-attachments/assets/0d4ad5aa-3fa0-4a62-aa84-5dbd4c7b4c7d)

### Cuarto Paso

Procede a habilitar nuevamente la tarjeta de red para que vuelva a haber conexión con el servidor.

![image](https://github.com/user-attachments/assets/5e2b0528-cfa1-4e67-98e6-2526b01bb45d)

### Quinto Paso

Verifica que el host nuevamente se haya incorporado y pueda volver a ser monitoreado por medio del protocolo SNMP.

![image](https://github.com/user-attachments/assets/af5653f8-7ef0-4ed6-9224-35f0eb587f74)

## Conclusiones

Eficiencia de Monitoreo: La implementación del protocolo SNMP desde Zabbix hacia el host Windows ha demostrado ser altamente eficiente, permitiendo una recopilación de datos precisa y en tiempo real de las variables monitorizadas.

Compatibilidad y Configuración: La configuración inicial tanto del servidor Zabbix como del host Windows fue crucial. La instalación y configuración correcta del agente SNMP en el host Windows garantizó una comunicación fluida entre ambos sistemas.

Resiliencia del Sistema: Durante las pruebas, el sistema mostró una alta resiliencia y capacidad de adaptarse a diferentes escenarios, lo que refuerza la estabilidad y confiabilidad de la solución propuesta.

Identificación de Problemas: El proceso de monitoreo permitió la identificación temprana de posibles fallos o ineficiencias en el host Windows.

Optimización de Recursos: La utilización de SNMP para el monitoreo desde Zabbix optimizó los recursos del sistema, reduciendo la carga operativa y mejorando la eficiencia global del monitoreo.

Documentación y Soporte: La documentación detallada de cada paso del proceso, desde la configuración inicial hasta la resolución de problemas, es esencial para futuras implementaciones y soporte continuo.












