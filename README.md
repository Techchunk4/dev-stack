Here’s your **all-in-one `README.md` file**, ready to drop directly into your project folder — no extra includes or scripts needed. Everything — Docker Compose config, instructions, systemd service, firewall setup, and more — is inside one single markdown file.

---

```markdown
# 🚀 Gitea & WordPress Stack with PostgreSQL & MySQL on CentOS 8 using Docker

This guide walks you through setting up **Gitea (with PostgreSQL)** and **WordPress (with MySQL)** using Docker and Docker Compose on **CentOS 8**.

---

## 📸 Screenshots

| Gitea Setup | WordPress Login |
|-------------|-----------------|
| ![Gitea](screenshots/setup-page.png) | ![WordPress](screenshots/wordpress-login.png) |

---

## ⚙️ Stack Overview

| Service   | Image             | Port | Database   |
|-----------|------------------|------|------------|
| Gitea     | gitea/gitea       | 3000 | PostgreSQL |
| WordPress | wordpress         | 8080 | MySQL      |

---

## 🧰 Prerequisites

- CentOS 8 installed
- Root or sudo access
- Docker & Docker Compose

---

## 📦 Step 1: Install Docker & Docker Compose

```bash
# Add Docker repo
sudo dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo

# Install Docker
sudo dnf install -y docker-ce docker-ce-cli containerd.io
sudo systemctl enable --now docker

# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" \
-o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

---

## 📁 Step 2: Create Project Directory

```bash
mkdir ~/dev-stack && cd ~/dev-stack
```

---

## 📝 Step 3: Create `docker-compose.yml`

Create a file called `docker-compose.yml` and paste the following content:

```yaml
version: '3.8'

services:
  gitea:
    image: gitea/gitea:latest
    environment:
      - DB_TYPE=postgres
      - DB_HOST=gitea_db:5432
      - DB_NAME=gitea
      - DB_USER=gitea
      - DB_PASSWD=gitea
    ports:
      - "3000:3000"
    volumes:
      - gitea_data:/data
    depends_on:
      - gitea_db
    restart: always

  gitea_db:
    image: postgres:alpine
    environment:
      - POSTGRES_USER=gitea
      - POSTGRES_PASSWORD=gitea
      - POSTGRES_DB=gitea
    volumes:
      - gitea_db_data:/var/lib/postgresql/data
    restart: always

  wordpress:
    image: wordpress:latest
    ports:
      - "8080:80"
    environment:
      - WORDPRESS_DB_HOST=wp_db:3306
      - WORDPRESS_DB_USER=wp_user
      - WORDPRESS_DB_PASSWORD=wp_pass
      - WORDPRESS_DB_NAME=wordpress
    volumes:
      - wordpress_data:/var/www/html
    depends_on:
      - wp_db
    restart: always

  wp_db:
    image: mysql:5.7
    command: --default-authentication-plugin=mysql_native_password
    environment:
      - MYSQL_DATABASE=wordpress
      - MYSQL_USER=wp_user
      - MYSQL_PASSWORD=wp_pass
      - MYSQL_ROOT_PASSWORD=rootpass
    volumes:
      - wp_db_data:/var/lib/mysql
    restart: always

volumes:
  gitea_data:
  gitea_db_data:
  wordpress_data:
  wp_db_data:
```

---

## 🔥 Step 4: Configure Firewall

```bash
sudo dnf install -y firewalld
sudo systemctl enable --now firewalld
sudo firewall-cmd --permanent --add-port=3000/tcp   # Gitea
sudo firewall-cmd --permanent --add-port=8080/tcp   # WordPress
sudo firewall-cmd --reload
```

---

## 🚀 Step 5: Launch the Stack

```bash
docker-compose up -d
```

Then visit:
- Gitea: [http://your-server-ip:3000](http://your-server-ip:3000)
- WordPress: [http://your-server-ip:8080](http://your-server-ip:8080)

---

## 🛠 Step 6: Optional — Add Systemd Service

```bash
sudo tee /etc/systemd/system/dev-stack.service > /dev/null <<EOF
[Unit]
Description=Dev Stack (Gitea + WordPress)
Requires=docker.service
After=docker.service

[Service]
Type=oneshot
RemainAfterExit=yes
WorkingDirectory=/home/$(whoami)/dev-stack
ExecStart=/usr/local/bin/docker-compose up -d
ExecStop=/usr/local/bin/docker-compose down
TimeoutStartSec=0

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reexec
sudo systemctl enable --now dev-stack.service
```

---

## 🧪 Resource Snapshot

| Service   | RAM   | CPU |
|-----------|-------|-----|
| Gitea     | ~60MB | ~1% |
| PostgreSQL| ~40MB | ~1% |
| WordPress | ~80MB | ~2% |
| MySQL     | ~90MB | ~2% |

---

## 🧰 Useful Commands

```bash
# Start stack
docker-compose up -d

# Stop stack
docker-compose down

# View logs
docker-compose logs -f

# Update services
docker-compose pull && docker-compose up -d
```

---

## 👨‍💻 Author

**Your Name**  
Linux & Cloud Engineer  
📧 your@email.com | 🌐 your-portfolio.com

---

## 📜 License

MIT License
```

---

Let me know if you'd like this converted into a downloadable `.zip` or if you want a shell script version too.
