# Comandos Semana 1 — Infraestructura AWS
## TicoMart S.A.

---

## Conectarse al servidor
```bash
ssh -i "ticomart-key.pem" ubuntu@32.195.38.102
```

---

## Actualizar el sistema
```bash
sudo apt update && sudo apt upgrade -y
```

---

## Instalar UFW y fail2ban
```bash
sudo apt install -y ufw fail2ban
```

---

## Configurar firewall UFW
```bash
# Política por defecto
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Abrir puertos necesarios
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 9090/tcp
sudo ufw allow 3000/tcp
sudo ufw allow 5678/tcp

# Activar
sudo ufw --force enable

# Verificar
sudo ufw status verbose
```

---

## Configurar fail2ban
```bash
# Crear archivo de configuración
sudo bash -c 'cat > /etc/fail2ban/jail.local << EOF
[DEFAULT]
bantime  = 3600
findtime = 600
maxretry = 5
backend = systemd

[sshd]
enabled  = true
port     = ssh
logpath  = /var/log/auth.log
maxretry = 5
bantime  = 3600
EOF'

# Activar y arrancar
sudo systemctl enable fail2ban
sudo systemctl restart fail2ban

# Verificar
sudo fail2ban-client status sshd
```

---

## Crear usuario ticomart
```bash
# Crear usuario y agregarlo a sudo
sudo useradd -m -s /bin/bash ticomart && sudo usermod -aG sudo ticomart

# Copiar llaves SSH
sudo mkdir -p /home/ticomart/.ssh && sudo cp /home/ubuntu/.ssh/authorized_keys /home/ticomart/.ssh/ && sudo chown -R ticomart:ticomart /home/ticomart/.ssh && sudo chmod 700 /home/ticomart/.ssh && sudo chmod 600 /home/ticomart/.ssh/authorized_keys

# Verificar
id ticomart
```

---

## Instalar Docker
```bash
# Instalar dependencias
sudo apt-get install -y ca-certificates curl gnupg && sudo install -m 0755 -d /etc/apt/keyrings && curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg && sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Agregar repositorio oficial
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Instalar Docker y Docker Compose
sudo apt-get update -y && sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Agregar usuarios al grupo docker
sudo usermod -aG docker ubuntu && sudo usermod -aG docker ticomart

# Verificar
docker --version && docker compose version
```

---

## Verificación final de todo
```bash
sudo ufw status | grep "Status" && sudo fail2ban-client status | grep "Jail list" && docker --version && id ticomart
```

---

## Resultado esperado
```
Status: active
`- Jail list: sshd
Docker version 29.3.0, build 5927d80
uid=1001(ticomart) gid=1001(ticomart) groups=1001(ticomart),27(sudo),988(docker)
```
