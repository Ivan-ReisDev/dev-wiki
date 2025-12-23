# 🐳 Docker Compose Cheatsheet

Guia rápido com os principais comandos e estruturas do **Docker Compose**, ideal para projetos com múltiplos serviços.

---

## 🔹 Comandos Básicos

```bash
docker compose version
```
Mostra a versão do Docker Compose.

```bash
docker compose up
```
Sobe todos os serviços definidos no `docker-compose.yml`.

```bash
docker compose up -d
```
Sobe os serviços em background (detached).

```bash
docker compose down
```
Para e remove containers, redes e volumes padrão.

```bash
docker compose restart
```
Reinicia todos os serviços.

---

## 📋 Status e Logs

```bash
docker compose ps
```
Lista os serviços e seus status.

```bash
docker compose logs
```
Mostra logs de todos os serviços.

```bash
docker compose logs -f
```
Acompanha logs em tempo real.

```bash
docker compose logs app
```
Mostra logs de um serviço específico.

---

## 🧠 Build e Imagens

```bash
docker compose build
```
Builda todas as imagens.

```bash
docker compose build app
```
Builda apenas um serviço.

```bash
docker compose up --build
```
Força rebuild antes de subir os serviços.

---

## 🚀 Gerenciamento de Serviços

```bash
docker compose start
```
Inicia serviços parados.

```bash
docker compose stop
```
Para serviços sem removê-los.

```bash
docker compose pause
```
Pausa os serviços.

```bash
docker compose unpause
```
Retoma serviços pausados.

---

## 🖥 Acesso aos Containers

```bash
docker compose exec app bash
```
Acessa o terminal de um serviço.

```bash
docker compose exec app sh
```
Usado quando o bash não está disponível.

```bash
docker compose run app npm install
```
Executa um comando pontual em um serviço.

---

## 🧹 Limpeza

```bash
docker compose down -v
```
Remove containers **e volumes**.

```bash
docker compose rm
```
Remove containers parados.

```bash
docker compose down --rmi all
```
Remove containers e imagens.

---

## 📁 Estrutura Básica do docker-compose.yml

```yaml
version: "3.9"

services:
  app:
    build: .
    container_name: app
    ports:
      - "3000:3000"
    volumes:
      - .:/app
    env_file:
      - .env
    depends_on:
      - db

  db:
    image: postgres:16
    container_name: postgres
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: app_db
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

---

## 🔗 Redes

```yaml
networks:
  app-network:
    driver: bridge
```

```yaml
services:
  app:
    networks:
      - app-network
```

---

## ✅ Boas Práticas

- Use `env_file` para variáveis sensíveis
- Prefira nomes explícitos para serviços
- Utilize `depends_on` para ordem de inicialização
- Evite usar `latest` em produção
- Separe compose de **dev** e **prod**

---

📌 Ideal para projetos com **Node.js, NestJS, Next.js, Postgres, Redis, n8n, etc**.

