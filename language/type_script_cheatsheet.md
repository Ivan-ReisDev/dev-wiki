# 🟦 TypeScript Cheatsheet

Guia rápido e **profissional** com os principais conceitos, tipos e boas práticas de **TypeScript** para projetos modernos (frontend e backend).

---

## 🔹 Instalação

```bash
npm install -g typescript
```

```bash
tsc --version
```

---

## 📁 Inicializar Projeto

```bash
tsc --init
```

Gera o `tsconfig.json`.

---

## ▶️ Compilar

```bash
tsc
```

```bash
tsc index.ts
```

```bash
tsc --watch
```

---

## 🔤 Tipos Primitivos

```ts
string
number
boolean
null
undefined
symbol
bigint
```

```ts
let nome: string = 'Ivan';
let idade: number = 30;
let ativo: boolean = true;
```

---

## 🧱 Tipos Avançados

```ts
any      // evita (perde tipagem)
unknown  // seguro
void
never
```

```ts
let valor: unknown;
```

---

## 🧩 Arrays e Tuplas

```ts
const ids: number[] = [1, 2, 3];
```

```ts
const user: [number, string] = [1, 'Ivan'];
```

---

## 🧠 Interfaces

```ts
interface User {
  id: number;
  name: string;
  email?: string;
}
```

```ts
const user: User = { id: 1, name: 'Ivan' };
```

---

## 🧠 Types (Alias)

```ts
type Status = 'active' | 'inactive';
```

```ts
type ID = string | number;
```

---

## ❓ Optional, Readonly e Nullable

```ts
interface Product {
  readonly id: number;
  name?: string;
  price: number | null;
}
```

---

## 🔁 Funções Tipadas

```ts
function soma(a: number, b: number): number {
  return a + b;
}
```

```ts
const dobro = (x: number): number => x * 2;
```

```ts
function log(msg: string): void {}
```

---

## 🧱 Classes

```ts
class UserService {
  constructor(private readonly repo: User[]) {}

  findById(id: number): User | undefined {
    return this.repo.find(u => u.id === id);
  }
}
```

---

## 🧠 Generics

```ts
function identity<T>(value: T): T {
  return value;
}
```

```ts
interface ApiResponse<T> {
  data: T;
}
```

---

## ⚖️ Operadores Importantes

```ts
===  // igualdade estrita
!==  // diferença estrita
??   // nullish coalescing
?.   // optional chaining
!    // non-null assertion
```

```ts
const nome = user?.name ?? 'Anônimo';
```

---

## 🧪 Type Guards

```ts
function isString(value: unknown): value is string {
  return typeof value === 'string';
}
```

---

## 📦 Enums

```ts
enum Role {
  ADMIN = 'admin',
  USER = 'user'
}
```

---

## 🔐 Utility Types

```ts
Partial<T>
Required<T>
Pick<T, K>
Omit<T, K>
Readonly<T>
```

```ts
type UserPreview = Pick<User, 'id' | 'name'>;
```

---

## 🧪 tsconfig (Essencial)

```json
{
  "compilerOptions": {
    "strict": true,
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "Node"
  }
}
```

---

## 🐳 TypeScript com Node

```bash
npm i -D ts-node @types/node
```

```bash
npx ts-node index.ts
```

---

## ✅ Boas Práticas Profissionais

- Use `strict: true`
- Evite `any`
- Prefira `interface` para contratos
- Use `type` para unions
- Tipar sempre retorno de funções
- Combine TS + ESLint

---

📌 Ideal para **Node.js, NestJS, Next.js, React e APIs modernas**.

