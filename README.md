# RPI-Nginx-Proxy-Manager

🇬🇧 **English** | 🇵🇹 [Português](README.pt.md)

Docker setup for **Nginx Proxy Manager** on Raspberry Pi (3, 4, and 5), using **SQLite** as the database. This approach is simple, lightweight, and sufficient for most home environments and homelabs, removing the need for an additional MariaDB container.

---

## 1. Update the system

Before installing Docker, update the operating system:

```bash
sudo apt update
sudo apt full-upgrade -y
sudo reboot
```

After the reboot:

```bash
sudo apt update
```

---

## 2. Install Docker

Install the official Docker version:

```bash
curl -fsSL https://get.docker.com | sh
```

Add your user to the `docker` group to run commands without `sudo`:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

Verify that everything was installed correctly:

```bash
docker version
docker compose version
docker run hello-world
```

---

## 3. Create the directory structure

Create a folder to store the Nginx Proxy Manager configuration:

```bash
mkdir -p ~/docker/nginx-proxy-manager
cd ~/docker/nginx-proxy-manager
```

---

## 4. Create the compose.yml file

Create the file:

```bash
nano compose.yml
```

Copy the content shown below into the file.

### compose.yml

```yaml
services:
  nginx-proxy-manager:
    image: jc21/nginx-proxy-manager:2.12.3
    container_name: nginx-proxy-manager
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
      - "192.168.1.100:81:81"
    environment:
      DISABLE_IPV6: 'true'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
    healthcheck:
      test: ["CMD", "/bin/check-health"]
      interval: 10s
      timeout: 3s
```

> **Note:** replace `192.168.1.100` with your Raspberry Pi's local IP address. This ensures port 81 (the admin interface) is only reachable on the local network, rather than on all interfaces of the machine.

> **Note:** the image version (`2.12.3`) is pinned on purpose, instead of using `latest`, to avoid unexpected automatic updates. Check the [tags on Docker Hub](https://hub.docker.com/r/jc21/nginx-proxy-manager/tags) for the latest version before updating manually.

Save the file (`Ctrl + O`, `Enter`) and exit the editor (`Ctrl + X`).

---

## 5. Start the container

From the folder where `compose.yml` was created, run:

```bash
docker compose up -d
```

Confirm the container started correctly:

```bash
docker ps
```

---

## 6. First access

Open your browser and go to:

```
http://RASPBERRY_PI_IP:81
```

Example:

```
http://192.168.1.100:81
```

Default credentials:

- **Email:** `admin@example.com`
- **Password:** `changeme`

On first login you will be required to change the email and password.

---

## Directory structure

After the first startup, the structure will look like this:

```
nginx-proxy-manager/
├── compose.yml
├── data/
└── letsencrypt/
```

The `data` and `letsencrypt` folders are created automatically.

- `data` contains all configuration and the SQLite database.
- `letsencrypt` contains the SSL certificates.

Do not delete these folders if you want to keep your configuration.

---

## Updating Nginx Proxy Manager

```bash
cd ~/docker/nginx-proxy-manager
docker compose pull
docker compose up -d
```

---

## Useful commands

View logs:

```bash
docker compose logs -f
```

Restart:

```bash
docker compose restart
```

Stop:

```bash
docker compose stop
```

Start again:

```bash
docker compose start
```

Remove only the containers:

```bash
docker compose down
```

Completely remove the container and all data:

```bash
docker compose down -v
```

---

## Updating the Raspberry Pi

```bash
sudo apt update
sudo apt full-upgrade -y
sudo apt autoremove -y
```

---

## Notes

- Compatible with Raspberry Pi 3, 4, and 5.
- Tested on Raspberry Pi OS Bookworm (64-bit).
- Uses SQLite as the database.
- Does not require MariaDB or MySQL.
- Let's Encrypt certificates persist across updates and reboots.
- Only ports 80 and 443 should be exposed to the Internet.
- In this guide's `compose.yml`, port 81 is already bound to the Raspberry Pi's local IP address, instead of being reachable on all interfaces. Make sure the IP address matches your machine before starting the container.
