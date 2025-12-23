# 🐱‍🏍 NestJS CLI Cheatsheet

Guia rápido com os principais comandos do **Nest CLI** para criar, gerar e manter aplicações NestJS.

---

## 🔹 Instalação do Nest CLI

```bash
npm i -g @nestjs/cli
```
Instala o Nest CLI globalmente.

```bash
nest --version
```
Mostra a versão do Nest CLI.

---

## 🚀 Criar Projeto

```bash
nest new nome-do-projeto
```
Cria um novo projeto NestJS.

```bash
nest new nome-do-projeto --package-manager npm
```
Define o gerenciador de pacotes.

```bash
nest new nome-do-projeto --skip-git
```
Cria o projeto sem inicializar Git.

---

## ▶️ Executar Aplicação

```bash
npm run start
```
Inicia a aplicação.

```bash
npm run start:dev
```
Inicia em modo desenvolvimento (watch).

```bash
npm run start:prod
```
Inicia em modo produção.

---

## 🧱 Geradores (Generate)

Formato geral:

```bash
nest generate <schematic> <nome>
```

Ou forma curta:

```bash
nest g <schematic> <nome>
```

### Principais Schematics

```bash
nest g module users
```
Cria um módulo.

```bash
nest g controller users
```
Cria um controller.

```bash
nest g service users
```
Cria um service.

```bash
nest g resource users
```
Cria módulo + controller + service (CRUD).

```bash
nest g guard auth
```
Cria um guard.

```bash
nest g interceptor logging
```
Cria um interceptor.

```bash
nest g pipe validation
```
Cria um pipe.

```bash
nest g filter http-exception
```
Cria um exception filter.

```bash
nest g middleware logger
```
Cria um middleware.

---

## ⚙️ Opções Úteis

```bash
nest g controller users --no-spec
```
Gera sem arquivo de teste.

```bash
nest g resource users --no-spec
```
Resource sem testes.

```bash
nest g resource users --type rest
```
Define tipo REST (default).

```bash
nest g resource users --type graphql-code-first
```
Resource com GraphQL (code first).

---

## 🧪 Testes

```bash
npm run test
```
Executa testes unitários.

```bash
npm run test:watch
```
Testes em modo watch.

```bash
npm run test:e2e
```
Executa testes end-to-end.

---

## 🧹 Build e Produção

```bash
npm run build
```
Gera build da aplicação.

```bash
node dist/main.js
```
Executa build gerado.

---

## 📦 Integrações Comuns

```bash
npm i @nestjs/config
```
Variáveis de ambiente.

```bash
npm i @nestjs/swagger
```
Swagger / OpenAPI.

```bash
npm i @nestjs/jwt passport passport-jwt
```
Autenticação JWT.

```bash
npm i @nestjs/typeorm typeorm pg
```
TypeORM + Postgres.

---

## ✅ Boas Práticas

- Use `nest g resource` para CRUD rápido
- Separe módulos por domínio
- Prefira DTOs com validação
- Evite lógica no controller
- Combine NestJS + Docker + Compose

---

📌 Ideal para **APIs REST, GraphQL, microsserviços e aplicações escaláveis**.

