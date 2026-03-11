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
