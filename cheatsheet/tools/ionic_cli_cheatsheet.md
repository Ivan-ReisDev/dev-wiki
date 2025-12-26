# ⚡ Ionic CLI Cheatsheet

Guia rápido com os principais comandos do **Ionic CLI** para criar, desenvolver e buildar aplicações mobile e web com Ionic.

---

## 🔹 Instalação

```bash
npm install -g @ionic/cli
```
Instala o Ionic CLI globalmente.

```bash
ionic --version
```
Mostra a versão do Ionic CLI.

---

## 🚀 Criar Projeto

```bash
ionic start nome-do-app
```
Cria um novo projeto interativo.

```bash
ionic start nome-do-app blank
```
Cria projeto com template blank.

```bash
ionic start nome-do-app tabs --type=angular
```
Cria projeto Ionic Angular.

```bash
ionic start nome-do-app tabs --type=react
```
Cria projeto Ionic React.

```bash
ionic start nome-do-app tabs --type=vue
```
Cria projeto Ionic Vue.

---

## ▶️ Executar Aplicação

```bash
ionic serve
```
Executa a aplicação no navegador.

```bash
ionic serve --lab
```
Mostra preview Android e iOS.

```bash
ionic serve --external
```
Permite acesso via IP local.

---

## 🧱 Geração de Código

### Ionic Angular

```bash
ionic generate page home
```
Gera uma página.

```bash
ionic generate component header
```
Gera um componente.

```bash
ionic generate service auth
```
Gera um serviço.

---

## 📱 Capacitor (Mobile)

```bash
ionic build
```
Builda a aplicação web.

```bash
ionic capacitor add android
```
Adiciona plataforma Android.

```bash
ionic capacitor add ios
```
Adiciona plataforma iOS.

```bash
ionic capacitor sync
```
Sincroniza plugins e código.

```bash
ionic capacitor open android
```
Abre o projeto Android.

```bash
ionic capacitor open ios
```
Abre o projeto iOS.

---

## 🔌 Plugins e Recursos

```bash
ionic integrations enable capacitor
```
Habilita Capacitor.

```bash
ionic doctor check
```
Verifica problemas no ambiente.

---

## 🧪 Testes

```bash
npm run test
```
Executa testes.

```bash
npm run e2e
```
Executa testes end-to-end.

---

## 🧹 Limpeza

```bash
ionic cache clear
```
Limpa cache do Ionic.

---

## 📦 Build e Deploy

```bash
ionic build --prod
```
Build de produção.

```bash
ionic build --configuration=production
```
Build otimizado (Angular).

---

## ✅ Boas Práticas

- Use Capacitor ao invés de Cordova
- Prefira componentes reutilizáveis
- Teste em múltiplas plataformas
- Versione `capacitor.config.ts`
- Combine Ionic + APIs REST

---

📌 Ideal para **apps Android, iOS e PWAs** usando **Angular, React ou Vue**.

