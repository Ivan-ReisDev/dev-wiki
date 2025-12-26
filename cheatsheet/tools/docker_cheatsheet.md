# 🐳 Docker Cheatsheet

Um guia rápido com os principais comandos do Docker para uso diário.

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

## 🧹 Limpeza

```bash
docker system df
```
Mostra uso de espaço.

```bash
docker system prune
```
Remove recursos não utilizados.

```bash
docker container prune
```
Remove containers parados.

```bash
docker image prune
```
Remove imagens não usadas.

---

## 🧩 Docker Compose

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
docker compose logs -f
```
Mostra logs do compose.

```bash
docker compose ps
```
Lista serviços ativos.

---

## ✅ Dicas Rápidas

- Use `--name` para nomear containers
- Prefira `docker compose` para projetos
- Use `.dockerignore` para otimizar builds
- Sempre versionar o Dockerfile

---

📌 Ideal para desenvolvimento, testes e produção.

