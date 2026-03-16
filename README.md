# AWS Cowrie Honeypot & Server Hardening

**Descripción:** Creación de un honeypot en AWS usando Cowrie y hardening de instancia para la recolección de Inteligencia de Amenazas (Threat Intelligence).

## Objetivo del Proyecto
Desplegar un entorno seguro en la nube para capturar, monitorear y analizar ataques automatizados de fuerza bruta vía SSH, recolectando Indicadores de Compromiso (IoCs) reales de botnets globales sin comprometer la infraestructura subyacente.

## Arquitectura y Topología
* **Cloud Provider:** AWS (Amazon Web Services) - Instancia EC2 Ubuntu.
* **Aislamiento:** Contenedor Docker para segmentar el entorno del honeypot del sistema operativo host.
* **Honeypot:** [Cowrie](https://github.com/cowrie/cowrie) (Honeypot de alta interacción para emular un servidor SSH vulnerable).

## Hardening de la Instancia (Seguridad)
Para garantizar que los atacantes interactuaran con la trampa y no con el servidor real, se aplicaron las siguientes políticas de seguridad:
1. **Reubicación de Servicios:** Migración del servicio SSH legítimo del puerto estándar `22` al puerto `2222`.
2. **Desactivación de Sockets:** Deshabilitación de `ssh.socket` en Ubuntu para forzar la lectura del archivo `sshd_config`.
3. **Port Forwarding (Docker):** Redirección de todo el tráfico malicioso que llega al puerto `22` (AWS Security Groups) hacia el puerto `2222` interno del contenedor de Cowrie.

## Comandos de Despliegue
Implementación del Honeypot mediante Docker:
```bash
# Actualización e instalación de dependencias
sudo apt update && sudo apt install -y docker.io
sudo systemctl enable --now docker

# Ejecución del contenedor aislando el tráfico
sudo docker run -d -p 22:2222 --name honeypot-cowrie --restart=always cowrie/cowrie
