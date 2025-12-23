# ⚛️ Next.js CLI Cheatsheet

Guia rápido com os principais comandos e fluxos do **Next.js** para desenvolvimento, build e produção.

---

## 🔹 Criação de Projeto

```bash
npx create-next-app@latest nome-do-app
```
Cria um novo projeto Next.js interativo.

```bash
npx create-next-app@latest nome-do-app --typescript
```
Cria projeto com TypeScript.

```bash
npx create-next-app@latest nome-do-app --eslint --tailwind --app
```
Cria projeto com ESLint, Tailwind e App Router.

```bash
npx create-next-app@latest nome-do-app --src-dir
```
Usa pasta `src/`.

---

## ▶️ Executar Aplicação

```bash
npm run dev
```
Inicia o servidor de desenvolvimento.

```bash
npm run build
```
Gera o build de produção.

```bash
npm run start
```
Executa a aplicação em produção.

---

## 📁 Estrutura Comum

```txt
/app        → App Router (Next 13+)
/pages     → Pages Router (legado)
/components→ Componentes reutilizáveis
/lib       → Helpers e libs
/public    → Arquivos públicos
/styles    → Estilos globais
```

---

## 🧱 Geração de Arquivos (Manual)

Next.js não possui CLI própria para gerar arquivos, mas padrões comuns:

```txt
app/page.tsx
app/layout.tsx
app/loading.tsx
app/error.tsx
app/not-found.tsx
```

---

## 🌐 Rotas

### App Router

```txt
app/page.tsx           → /
app/blog/page.tsx      → /blog
app/blog/[slug]/page.tsx → /blog/:slug
```

### API Routes

```txt
app/api/users/route.ts
```

---

## 🔌 Scripts Úteis

```bash
npm run lint
```
Executa o ESLint.

```bash
npm run lint -- --fix
```
Corrige problemas automaticamente.

```bash
npm run dev -- --turbo
```
Usa Turbopack (quando disponível).

---

## 🖼 Assets e Fontes

```tsx
import Image from 'next/image'
```

```tsx
import { Inter } from 'next/font/google'
```

---

## 🌍 Variáveis de Ambiente

```bash
.env.local
.env.development
.env.production
```

```ts
process.env.NEXT_PUBLIC_API_URL
```

---

## 🧪 Testes (Comuns)

```bash
npm i -D jest @testing-library/react
```

```bash
npm i -D playwright
```

---

## 🐳 Next.js com Docker

```bash
docker build -t next-app .
```

```bash
docker run -p 3000:3000 next-app
```

---

## 🚀 Deploy

```bash
npm run build
```

- Vercel (recomendado)
- Docker
- VPS / Cloud

---

## ✅ Boas Práticas

- Prefira **App Router**
- Use Server Components por padrão
- Separe componentes client/server
- Use `next/image