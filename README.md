RPI-Nginx-Reverse-Proxy — Docker Setup (Raspberry Pi)

Configuração completa e funcional do Nginx Proxy Manager em Docker num Raspberry Pi.
Inclui MariaDB como base de dados e correções específicas para bugs conhecidos em ARM-based systems (RPI).

 1. Instalar o Docker
curl -sSL https://get.docker.com | sh


Verifica se o Docker foi instalado corretamente:

sudo docker run hello-world

 2. Instalar o Docker Compose
sudo apt-get install libffi-dev libssl-dev -y
sudo apt-get install python3-dev -y
sudo apt-get install -y python3 python3-pip
sudo pip3 install docker-compose
sudo systemctl enable docker


Confirma a instalação:

docker-compose version

 3. Criar o ficheiro docker-compose.yml
sudo nano docker-compose.yml


Copia o seguinte conteúdo:

version: "3"

services:
  app:
    image: jc21/nginx-proxy-manager:latest
    restart: always
    ports:
      - "80:80"    # HTTP público
      - "443:443"  # HTTPS público
      - "81:81"    # Interface de administração
    environment:
      DB_MYSQL_HOST: "db"
      DB_MYSQL_PORT: 3306
      DB_MYSQL_USER: "ADDUSER"
      DB_MYSQL_PASSWORD: "CHANGEPASSWORD"
      DB_MYSQL_NAME: "npm"
      # Caso pretendas usar SQLite em vez de MySQL, remove as variáveis acima
      # e descomenta a linha seguinte:
      # DB_SQLITE_FILE: "/data/database.sqlite"
      # Descomenta esta linha se o IPv6 não estiver ativo no sistema:
      # DISABLE_IPV6: "true"
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


Nota: substitui ADDUSER, CHANGEPASSWORD, changeuser, changepass e changeme

 4. Iniciar o ambiente Docker
sudo docker-compose up -d


Verifica se os serviços estão a correr corretamente:

sudo docker ps

 5. Credenciais por defeito

Acede ao painel através de:

http://<IP_DO_RASPBERRY>:81


Credenciais iniciais:

Email:    admin@example.com
Password: changeme


Serás solicitado a definir novas credenciais no primeiro login.

 Estrutura de Diretórios
RPI-Nginx-Reverse-Proxy/
├── docker-compose.yml
├── data/
│   ├── nginx-proxy-manager/
│   └── mariadb/
└── letsencrypt/

 Notas:

Testado em Raspberry Pi 3/4/5 com Raspberry Pi OS (64-bit).

Corrigidos bugs conhecidos relacionados com a arquitetura ARM e permissões nos volumes.

O diretório ./letsencrypt deve manter persistência entre reinicializações para não perder certificados.

O parâmetro DISABLE_IPV6 pode ser ativado em redes que não suportem IPv6, evitando falhas de binding.

 Diagnóstico rápido

Ver logs da aplicação:
sudo docker-compose logs -f app

Reiniciar apenas o Nginx Proxy Manager:
sudo docker-compose restart app

Remover tudo:
sudo docker-compose down -v

Para manter o sistema atualizado:
sudo docker pull jc21/nginx-proxy-manager:latest
sudo docker pull ghcr.io/linuxserver/mariadb
sudo docker-compose up -
