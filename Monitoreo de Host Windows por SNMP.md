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

## a. Habilitación de SNMP sobre Windows

