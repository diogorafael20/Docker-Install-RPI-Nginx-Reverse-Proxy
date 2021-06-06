## Docker Install // RPI-Nginx-Reverse-Proxy ##


##Installing docker

$ curl -sSL https://get.docker.com | sh

##Installing Docker Compose

$ sudo apt-get install libffi-dev libssl-dev
  sudo apt install python3-dev
  sudo apt-get install -y python3 python3-pip
  sudo pip3 install docker-compose
  sudo systemctl enable docker

##Command to check if docker is alive

docker run hello-world

##Create docker compose file

sudo nano docker-compose.yml

##paste the following code:

version: "3"
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: always
    ports:
      # Public HTTP Port:
      - '80:80'
      # Public HTTPS Port:
      - '443:443'
      # Admin Web Port:
      - '81:81'
    environment:
      # These are the settings to access your db
      DB_MYSQL_HOST: "db"
      DB_MYSQL_PORT: 3306
      DB_MYSQL_USER: "ADDUSER"
      DB_MYSQL_PASSWORD: "CHANGEPASSWORD"
      DB_MYSQL_NAME: "npm"
      # If you would rather use Sqlite uncomment this
      # and remove all DB_MYSQL_* lines above
      # DB_SQLITE_FILE: "/data/database.sqlite"
      # Uncomment this if IPv6 is not enabled on your host
      # DISABLE_IPV6: 'true'
    volumes:
      - ./data/nginx-proxy-manager:/data
      - ./letsencrypt:/etc/letsencrypt
    depends_on:
      - db
  db:
    image: ghcr.io/linuxserver/mariadb
    restart: unless-stopped
    environment:
      PUID: 1001
      PGID: 1001
      TZ: "Europe/London"
      MYSQL_ROOT_PASSWORD: "changeme"
      MYSQL_DATABASE: "npm"
      MYSQL_USER: "changeuser"
      MYSQL_PASSWORD: "changepass"
    volumes:
      - ./data/mariadb:/config


##change "ADDUSER" and "CHANGEPASSWORD" credentials on lines 42 and 43.

##then run it

$ run docker-compose up -d

Default credentials:

Email:    admin@example.com
Password: changeme

