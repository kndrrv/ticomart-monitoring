# Comandos Semana 2 — Stack Docker y Monitoreo
## TicoMart S.A.

---

## Configurar SWAP de 2GB
```bash
# Crear el archivo de swap
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Hacerlo permanente
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

# Verificar
free -h
```

---

## Levantar los contenedores
```bash
cd ticomart
docker compose up -d

# Verificar que todos están corriendo
docker compose ps
```

---

## Importar la base de datos
```bash
# Subir el archivo SQL desde Windows
scp -i "C:\Users\keive\Downloads\ticomart-key.pem" "C:\Users\keive\Downloads\ticomart_db.sql.txt" ubuntu@32.195.38.102:/home/ubuntu/ticomart/

# Importar la base de datos
docker exec -i ticomart-mysql mysql -u root -pTicoMart2026 ticomart < /home/ubuntu/ticomart/ticomart_db.sql.txt

# Verificar tablas importadas
docker exec -i ticomart-mysql mysql -u root -pTicoMart2026 ticomart -e "SHOW TABLES;"

# Verificar registros
docker exec -i ticomart-mysql mysql -u root -pTicoMart2026 ticomart -e "SELECT COUNT(*) FROM pedidos;"
```

---

## Configurar Prometheus
```bash
# Crear carpeta de configuración
mkdir -p config

# Crear archivo prometheus.yml
cat > config/prometheus.yml << 'EOF'
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node-exporter'
    static_configs:
      - targets: ['ticomart-node-exporter:9100']
EOF

# Agregar volumen al docker-compose.yml
sed -i 's|      - prometheus_data:/prometheus|      - prometheus_data:/prometheus\n      - ./config/prometheus.yml:/etc/prometheus/prometheus.yml|' docker-compose.yml

# Recrear contenedor
docker compose up -d --force-recreate prometheus
```

---

## Configurar Promtail (recolección de logs)
```bash
# Crear archivo de configuración
cat > config/promtail.yml << 'EOF'
server:
  http_listen_port: 9080

positions:
  filename: /tmp/positions.yaml

clients:
  - url: http://ticomart-loki:3100/loki/api/v1/push

scrape_configs:
  - job_name: auth
    static_configs:
      - targets:
          - localhost
        labels:
          job: auth
          __path__: /var/log/auth.log

  - job_name: nginx
    static_configs:
      - targets:
          - localhost
        labels:
          job: nginx
          __path__: /var/log/nginx/*.log
EOF

# Dar permisos correctos
sudo chown root:root config/promtail.yml
sudo chmod go-w config/promtail.yml

# Levantar Promtail
docker compose up -d promtail
```

---

## Verificar estado de todos los contenedores
```bash
docker compose ps
```

---

## URLs de acceso

| Servicio | URL |
|----------|-----|
| Prometheus Targets | http://32.195.38.102:9090/targets |
| Grafana | http://32.195.38.102:3000 |
| N8N | http://32.195.38.102:5678 |

---

## Estado final Semana 2
```bash
# Verificar todos los contenedores
docker compose ps

# Resultado esperado: 8 contenedores Up
# ticomart-nginx
# ticomart-mysql
# ticomart-prometheus
# ticomart-node-exporter
# ticomart-loki
# ticomart-n8n
# ticomart-grafana
# ticomart-promtail
```
