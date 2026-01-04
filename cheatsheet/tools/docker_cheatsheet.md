# 🐳 Docker Cheatsheet

Um guia rápido com os principais comandos do Docker, limpeza, Docker Compose tricks e healthchecks.

---

## 🔹 Informações Básicas

```bash
docker --version
```
Mostra a versão do Docker instalada.

```bash
docker info
```
Exibe informações do sistema Docker.

```bash
docker version
```
Exibe versão do cliente e do servidor Docker.

---

## 📦 Imagens

```bash
docker images
```
Lista todas as imagens locais.

```bash
docker pull nginx
```
Baixa uma imagem do Docker Hub.

```bash
docker rmi nginx
```
Remove uma imagem.

```bash
docker build -t minha-imagem .
```
Cria uma imagem a partir de um Dockerfile.

---

## 🚀 Containers

```bash
docker ps
```
Lista containers em execução.

```bash
docker ps -a
```
Lista todos os containers (ativos e parados).

```bash
docker run nginx
```
Cria e executa um container.

```bash
docker run -d -p 8080:80 nginx
```
Executa em background e faz bind de portas.

```bash
docker start <container>
```
Inicia um container parado.

```bash
docker stop <container>
```
Para um container.

```bash
docker restart <container>
```
Reinicia um container.

```bash
docker rm <container>
```
Remove um container.

```bash
docker rm -f <container>
```
Remove um container à força.

---

## 🧠 Inspeção e Logs

```bash
docker logs <container>
```
Mostra os logs do container.

```bash
docker logs -f <container>
```
Acompanha os logs em tempo real.

```bash
docker inspect <container>
```
Exibe detalhes do container.

```bash
docker exec -it <container> bash
```
Acessa o terminal do container.

---

## 🗂 Volumes

```bash
docker volume ls
```
Lista volumes.

```bash
docker volume create meu-volume
```
Cria um volume.

```bash
docker run -v meu-volume:/data nginx
```
Usa um volume no container.

```bash
docker volume rm meu-volume
```
Remove um volume.

---

## 🌐 Redes

```bash
docker network ls
```
Lista redes.

```bash
docker network create minha-rede
```
Cria uma rede.

```bash
docker network connect minha-rede container
```
Conecta container a uma rede.

```bash
docker network rm minha-rede
```
Remove uma rede.

---

## 🧹 Limpeza & Manutenção

### Status do Sistema

```bash
docker system df
```
Mostra uso de espaço em disco.

```bash
docker system df -v
```
Exibe detalhes verbose do uso de espaço.

### Limpeza Geral

```bash
docker system prune
```
Remove containers parados, imagens não usadas e redes não utilizadas.

```bash
docker system prune -a
```
Remove também imagens não usadas (mesmo com tags).

```bash
docker system prune --volumes
```
Remove também volumes não utilizados (CUIDADO!).

### Limpeza Específica

#### Containers

```bash
docker container prune
```
Remove todos os containers parados.

```bash
docker container prune -a
```
Remove containers parados com filtro (sem imagens em uso).

```bash
docker rm -f $(docker ps -aq)
```
Força remoção de todos os containers (parados e rodando).

```bash
docker ps -a -q | xargs docker rm
```
Remove todos os containers parados de forma elegante.

#### Imagens

```bash
docker image prune
```
Remove imagens dangling (sem tags e sem containers).

```bash
docker image prune -a
```
Remove imagens não utilizadas.

```bash
docker image prune -a --filter "until=24h"
```
Remove imagens não usadas há mais de 24h.

```bash
docker rmi $(docker images -q -f "dangling=true")
```
Remove apenas imagens dangling.

```bash
docker rmi $(docker images -q)
```
Remove todas as imagens (CUIDADO!).

#### Volumes

```bash
docker volume prune
```
Remove volumes não utilizados.

```bash
docker volume rm $(docker volume ls -q)
```
Remove todos os volumes (CUIDADO!).

```bash
docker volume ls -f "dangling=true"
```
Lista volumes órfãos.

#### Redes

```bash
docker network prune
```
Remove redes não utilizadas.

```bash
docker network rm $(docker network ls -q)
```
Remove todas as redes (CUIDADO!).

### Script Completo de Limpeza

```bash
# Limpeza segura
docker system prune -f

# Limpeza agressiva (com volumes)
docker system prune -a -f --volumes

# Apenas não imagens
docker container prune -f && docker image prune -a -f && docker volume prune -f
```

---

## 🧩 Docker Compose

### Comandos Básicos

```bash
docker compose up
```
Sobe os serviços.

```bash
docker compose up -d
```
Sobe os serviços em background.

```bash
docker compose down
```
Derruba os serviços.

```bash
docker compose down -v
```
Derruba serviços e remove volumes.

```bash
docker compose down --remove-orphans
```
Remove containers órfãos também.

### Logs e Inspeção

```bash
docker compose logs
```
Mostra logs de todos os serviços.

```bash
docker compose logs -f
```
Acompanha logs em tempo real.

```bash
docker compose logs <service>
```
Mostra logs de um serviço específico.

```bash
docker compose logs -f --tail=100 <service>
```
Últimas 100 linhas de um serviço.

```bash
docker compose ps
```
Lista serviços ativos.

```bash
docker compose ps -a
```
Lista todos os serviços (rodando e parados).

### Recontrução e Restart

```bash
docker compose build
```
Reconstrói as imagens.

```bash
docker compose build --no-cache
```
Reconstrói ignorando o cache.

```bash
docker compose build <service>
```
Reconstrói apenas um serviço.

```bash
docker compose up -d --build
```
Reconstrói e levanta em um comando.

```bash
docker compose restart
```
Reinicia todos os serviços.

```bash
docker compose restart <service>
```
Reinicia um serviço específico.

### Execução de Comandos

```bash
docker compose exec <service> bash
```
Acessa terminal do serviço.

```bash
docker compose exec <service> npm run migrate
```
Executa comando no serviço rodando.

```bash
docker compose exec -u root <service> apt-get update
```
Executa como root.

### Limpeza em Compose

```bash
docker compose down -v
```
Remove containers, networks e volumes.

```bash
docker compose down --rmi all
```
Remove containers, networks, volumes e imagens.

```bash
docker compose down --rmi local
```
Remove apenas imagens criadas (não pull).

### Validação

```bash
docker compose config
```
Valida e mostra o arquivo compose processado.

```bash
docker compose config --resolve-image-digests
```
Resolve digests das imagens.

---

## 🔧 Auto-start no Boot (systemd)

### Desabilitar Auto-start

```bash
# Impedir que container inicie automaticamente
docker update --restart=no <container>
```

### Policies de Restart

```bash
# no - não reinicia
docker run --restart=no nginx

# always - sempre reinicia
docker run --restart=always nginx

# unless-stopped - reinicia a menos que tenha sido parado
docker run --restart=unless-stopped nginx

# on-failure:5 - reinicia 5 vezes em caso de erro
docker run --restart=on-failure:5 nginx

# on-failure:5:10000 - reinicia 5 vezes com 10s de delay
docker run --restart=on-failure:5:10000 nginx
```

### Systemd Service (Quando usando systemd)

```bash
# Desabilitar inicialização automática
sudo systemctl disable docker
sudo systemctl stop docker

# Verificar status
sudo systemctl status docker

# Habilitar novamente
sudo systemctl enable docker
sudo systemctl start docker
```

### Docker Desktop (Mac/Windows)

Settings → General → desmarcae "Start Docker Desktop when you log in"

---

## 💚 Healthchecks

### Healthcheck no Dockerfile

```dockerfile
FROM node:18

WORKDIR /app
COPY . .
RUN npm install

# Healthcheck básico
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1

CMD ["npm", "start"]
```

### Healthcheck em Docker Compose

```yaml
version: '3.8'

services:
  web:
    image: myapp:latest
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 3s
      retries: 3
      start_period: 5s

  db:
    image: postgres:15
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 10s

  redis:
    image: redis:7
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 5s

  api:
    image: api:latest
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "wget", "--quiet", "--tries=1", "--spider", "http://localhost:8000/status"]
      interval: 20s
      timeout: 5s
      retries: 3
      start_period: 10s
```

### Exemplos de Healthchecks

```bash
# HTTP
HEALTHCHECK CMD curl -f http://localhost:3000/health || exit 1

# TCP
HEALTHCHECK CMD nc -z localhost 5432 || exit 1

# Command Shell
HEALTHCHECK CMD ["sh", "-c", "test -f /tmp/healthy"]

# Postgres
HEALTHCHECK CMD ["pg_isready", "-U", "postgres"]

# MySQL
HEALTHCHECK CMD ["mysqladmin", "ping", "-h", "localhost"]

# Redis
HEALTHCHECK CMD ["redis-cli", "ping"]

# Node (com npm)
HEALTHCHECK CMD ["npm", "run", "health"]

# Wget
HEALTHCHECK CMD wget --quiet --tries=1 --spider http://localhost:8000/status || exit 1
```

### Verificar Status do Healthcheck

```bash
docker inspect <container> | grep -A 10 '"Health"'

# Saída esperada:
# "State": {
#   "Status": "running",
#   "Health": {
#     "Status": "healthy",
#     "FailingStreak": 0,
#     "Log": [...]
#   }
# }
```

### Healthchecks em Produção

```yaml
version: '3.8'

services:
  app:
    image: myapp:latest
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
    restart_policy:
      condition: on-failure
      delay: 5s
      max_attempts: 5
      window: 120s

  nginx:
    image: nginx:latest
    depends_on:
      app:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "nginx", "-t"]
      interval: 30s
      timeout: 5s
      retries: 2
```

---

## ✅ Dicas Rápidas

- Use `--name` para nomear containers
- Prefira `docker compose` para projetos
- Use `.dockerignore` para otimizar builds
- Sempre versionar o Dockerfile
- Use `restart_policy` em produção
- Implemente healthchecks para monitoramento
- Limpe recursos regularmente com `docker system prune`
- Configure `restart=unless-stopped` para containers essenciais
- Use `depends_on` com `condition: service_healthy` em compose

---

📌 Ideal para desenvolvimento, testes e produção.

