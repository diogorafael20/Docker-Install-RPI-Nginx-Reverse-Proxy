# RPI-Nginx-Proxy-Manager

Configuração do **Nginx Proxy Manager** em Docker para Raspberry Pi (3, 4 e 5), utilizando **SQLite** como base de dados. Esta abordagem é simples, leve e suficiente para a maioria dos ambientes domésticos e homelabs, eliminando a necessidade de um container adicional de MariaDB.

---

## 1. Atualizar o sistema

Antes de instalar o Docker, atualizar o sistema operativo:

```bash
sudo apt update
sudo apt full-upgrade -y
sudo reboot
```

Após o reinício:

```bash
sudo apt update
```

---

## 2. Instalar o Docker

Instalar a versão oficial do Docker:

```bash
curl -fsSL https://get.docker.com | sh
```

Adicionar o utilizador ao grupo `docker` para executar comandos sem `sudo`:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

Verificar que tudo ficou corretamente instalado:

```bash
docker version
docker compose version
docker run hello-world
```

---

## 3. Criar a estrutura de diretórios

Criar uma pasta para armazenar a configuração do Nginx Proxy Manager:

```bash
mkdir -p ~/docker/nginx-proxy-manager
cd ~/docker/nginx-proxy-manager
```

---

## 4. Criar o ficheiro compose.yml

Criar o ficheiro:

```bash
nano compose.yml
```

Copiar para o ficheiro o conteúdo apresentado abaixo.

### compose.yml

(ver ficheiro `compose.yml` deste repositório)

Guardar o ficheiro (`Ctrl + O`, `Enter`) e sair do editor (`Ctrl + X`).

---

## 5. Iniciar o container

Na pasta onde foi criado o `compose.yml`, executar:

```bash
docker compose up -d
```

Confirmar que o container foi iniciado corretamente:

```bash
docker ps
```

---

## 6. Primeiro acesso

Abrir o navegador e aceder a:

```
http://IP_DO_RASPBERRY:81
```

Exemplo:

```
http://192.168.1.100:81
```

Credenciais iniciais:

- **Email:** `admin@example.com`
- **Password:** `changeme`

No primeiro acesso será obrigatório alterar o email e a palavra-passe.

---

## Estrutura de diretórios

Após o primeiro arranque, a estrutura ficará semelhante a:

```
nginx-proxy-manager/
├── compose.yml
├── data/
└── letsencrypt/
```

As pastas `data` e `letsencrypt` são criadas automaticamente.

- `data` contém toda a configuração e a base de dados SQLite.
- `letsencrypt` contém os certificados SSL.

Não eliminar estas pastas caso se pretenda manter a configuração.

---

## Atualizar o Nginx Proxy Manager

```bash
cd ~/docker/nginx-proxy-manager
docker compose pull
docker compose up -d
```

---

## Comandos úteis

Ver logs:

```bash
docker compose logs -f
```

Reiniciar:

```bash
docker compose restart
```

Parar:

```bash
docker compose stop
```

Voltar a iniciar:

```bash
docker compose start
```

Remover apenas os containers:

```bash
docker compose down
```

Remover completamente o container e todos os dados:

```bash
docker compose down -v
```

---

## Atualizar o Raspberry Pi

```bash
sudo apt update
sudo apt full-upgrade -y
sudo apt autoremove -y
```

---

## Notas

- Compatível com Raspberry Pi 3, 4 e 5.
- Testado em Raspberry Pi OS Bookworm (64-bit).
- Utiliza SQLite como base de dados.
- Não necessita de MariaDB nem MySQL.
- Os certificados Let's Encrypt permanecem após atualizações e reinicializações.
- Apenas as portas 80 e 443 devem ser expostas à Internet.
- Recomenda-se manter a porta 81 acessível apenas na rede local.
