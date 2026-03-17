# ticomart-monitoring
Bot de monitoreo de red con IA

**Docente:** Prof. Joshua Vargas Salas  
**Entrega final:** 14 de abril de 2026  
**Integrantes:** Susana Herrera, Fabian Vilchez, Kendra Gutiérrez, Jorge Chacon y Pablo Garro

---

## ¿Qué es este proyecto?

Sistema de monitoreo inteligente para la empresa ficticia TicoMart S.A.  
El sistema recopila métricas y logs del servidor Linux, los analiza con 
inteligencia artificial y envía alertas automáticas por Telegram.

Todo el proyecto usa herramientas 100% gratuitas.

## Datos del servidor

| Campo | Valor |
|-------|-------|
| IP del servidor | 32.195.38.102 |
| Sistema operativo | Ubuntu 22.04 LTS |
| Tipo de instancia | AWS EC2 t3.micro (Free Tier) |
| Región | us-east-1 (N. Virginia) |


## Cómo conectarse al servidor

Necesitás el archivo ticomart-key.pem (pedírselo a Keive por privado).
```bash
ssh -i "ticomart-key.pem" ubuntu@32.195.38.102
```

## Lo que hicimos cada semana

### Semana 1 — Infraestructura AWS (7–11 marzo)
- Creamos la cuenta AWS con Free Tier
- Configuramos alerta de facturación de $1 USD
- Lanzamos el servidor EC2 t3.micro con Ubuntu 22.04
- Asignamos IP elástica fija: 32.195.38.102
- Configuramos Security Groups (puertos 22, 80, 443, 9090, 3000, 5678)
- Actualizamos el sistema operativo
- Instalamos y configuramos UFW (firewall)
- Instalamos y configuramos fail2ban (protección SSH)
- Creamos el usuario ticomart
- Instalamos Docker 29.3.0 y Docker Compose 5.1.0

## Puertos del sistema

| Puerto | Servicio | Para qué |
|--------|----------|----------|
| 22 | SSH | Conectarse al servidor |
| 80 | Nginx | Tráfico web |
| 443 | Nginx HTTPS | Tráfico web cifrado |
| 9090 | Prometheus | Métricas del sistema |
| 3000 | Grafana | Dashboard visual |
| 5678 | N8N | Automatización e IA |


## Estado del servidor

| Componente | Estado |
|-----------|--------|
| UFW (firewall) | Activo |
| fail2ban | Activo |
| Usuario ticomart | Creado |
| Docker 29.3.0 | Instalado |
| Docker Compose 5.1.0 | Instalado |

### Semana 2 — Stack Docker y Monitoreo (12-15 marzo)
- Configurado SWAP de 2GB para optimizar memoria del servidor
- Levantados 7 contenedores con Docker Compose
- Base de datos TicoMart importada (12 tablas, 60,000 pedidos)
- Grafana conectado a MySQL con dashboard de pedidos por día
- Prometheus configurado con Node Exporter (métricas en tiempo real)
- Dashboard Node Exporter Full importado en Grafana (CPU, RAM, disco, red)
- Promtail configurado para enviar logs del servidor a Loki
- Logs visibles en Grafana Explore


### Semana 3 — Bot Telegram con IA (16 marzo)
- Bot de Telegram creado (@ticomartbot)
- Cuenta Groq configurada con API Key gratuita
- Flujo N8N creado con 4 nodos:
  - Schedule Trigger (cada 5 minutos)
  - HTTP Request → consulta CPU a Prometheus
  - Code JavaScript → formatea el mensaje
  - HTTP Request → envía métricas a Groq IA
  - Telegram → envía análisis al bot
- Bot enviando reportes automáticos cada 5 minutos con análisis de IA
```

---

## 2. Nuevo archivo: comandos-semana3.md

Creá un archivo nuevo en GitHub llamado `comandos-semana3.md` y pegá esto:

~~~markdown
# Semana 3 — Bot Telegram con IA
## BCD 7212 — TicoMart S.A.

---

## Herramientas utilizadas

| Herramienta | Uso | Costo |
|-------------|-----|-------|
| Telegram BotFather | Crear el bot | Gratis |
| Groq API | IA para analizar métricas | Gratis |
| N8N | Automatización del flujo | Gratis (self-hosted) |

---

## Paso 1 — Crear el bot de Telegram

1. Abrí Telegram y buscá @BotFather
2. Escribile `/newbot`
3. Seguí las instrucciones — te da un token
4. Escribile `/start` al bot para activarlo
5. Obtener el Chat ID abriendo esta URL en el navegador:
```
https://api.telegram.org/bot<TOKEN>/getUpdates
```

---

## Paso 2 — Obtener API Key de Groq

1. Entrá a https://console.groq.com
2. Creá una cuenta gratuita
3. Andá a API Keys → Create API Key
4. Guardá el key generado

---

## Paso 3 — Flujo N8N

El flujo tiene 5 nodos en este orden:

### Nodo 1 — Schedule Trigger
- Trigger interval: Minutes
- Minutes between triggers: 5

### Nodo 2 — HTTP Request (Prometheus)
- Method: GET
- URL:
```
http://ticomart-prometheus:9090/api/v1/query?query=100-(avg(rate(node_cpu_seconds_total{mode="idle"}[5m]))*100)
