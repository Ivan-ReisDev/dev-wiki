# ⭐ Angular CLI Essentials

Comandos que você realmente usa. Nada de excesso.

---

## Instalação

```bash
# Instalar globalmente
npm install -g @angular/cli

# Verificar versão
ng version

# Atualizar
npm update -g @angular/cli
```

---

## Criar Novo Projeto

### Básico (Recomendado)
```bash
ng new meu-app
cd meu-app
ng serve
```

### Com Standalone Components (Moderno)
```bash
ng new meu-app --standalone
```

### Com Routing
```bash
ng new meu-app --routing
```

### Com Style (SCSS)
```bash
ng new meu-app --style=scss
```

### Combinado (Melhor)
```bash
ng new meu-app --routing --style=scss --standalone
```

### Sem Testes (Para prototipagem)
```bash
ng new meu-app --skip-tests
```

---

## Rodar Servidor

### Básico
```bash
ng serve
# Acessa em http://localhost:4200
```

### Em Porta Específica
```bash
ng serve --port 3000
```

### Com Auto-Open
```bash
ng serve --open
```

### Produção (otimizado)
```bash
ng serve --configuration production
```

---

## Build (Para Deploy)

### Desenvolvimento
```bash
ng build
# Gera em: dist/nome-app/
```

### Produção (otimizado)
```bash
ng build --configuration production
ng build --prod  # (versão antiga)
```

### Com Source Maps (debug)
```bash
ng build --source-map
```

### Analisar Bundle Size
```bash
ng build --stats-json
# Depois use: npm install webpack-bundle-analyzer
# npx webpack-bundle-analyzer dist/nome-app/stats.json
```

---

## Gerar Componentes

### Componente Simples
```bash
ng generate component user-card
ng g c user-card  # atalho

# Gera:
# src/app/user-card/
#   └── user-card.component.ts
#   └── user-card.component.html
#   └── user-card.component.scss
#   └── user-card.component.spec.ts
```

### Componente Standalone (Moderno)
```bash
ng g c user-card --standalone

# Gera componente com standalone: true
```

### Componente Sem Testes
```bash
ng g c user-card --skip-tests
```

### Componente Inline (sem arquivos separados)
```bash
ng g c user-card --inline-template --inline-style

# Template e estilos no mesmo arquivo .ts
```

### Componente em Pasta Específica
```bash
ng g c components/user-card
# Cria em: src/app/components/user-card/
```

---

## Gerar Services

### Simples
```bash
ng generate service user
ng g s user  # atalho

# Gera:
# src/app/user.service.ts
# src/app/user.service.spec.ts
```

### Sem Testes
```bash
ng g s services/user --skip-tests
```

### Em Pasta
```bash
ng g s services/auth
# Cria em: src/app/services/auth.service.ts
```

---

## Outros Generators

```bash
# Guard (proteção de rotas)
ng g guard auth

# Directive (comportamento customizado)
ng g directive highlight

# Pipe (transformação de dados)
ng g pipe uppercase

# Interface/Model
ng g interface models/user

# Class
ng g class models/usuario

# Enum
ng g enum enums/status

# Interceptor (HTTP)
ng g interceptor interceptors/auth

# Module (antigo, evite)
ng g module features/users
```

---

## Testes

### Rodar Testes
```bash
ng test
# Abre em modo watch
```

### Testes Uma Vez (CI/CD)
```bash
ng test --watch=false
ng test --watch=false --browsers=ChromeHeadless
```

### Com Coverage
```bash
ng test --code-coverage
# Gera relatório em: coverage/
```

### Testes E2E
```bash
ng e2e
```

---

## Linting & Format

```bash
# Lint (verificar código)
ng lint

# Fix automático
ng lint --fix
```

---

## Adicionar Bibliotecas

```bash
# Angular Material
ng add @angular/material

# Bootstrap
ng add @ng-bootstrap/ng-bootstrap

# Prettier
ng add prettier

# ESLint
ng add @angular-eslint/schematics
```

---

## Atualizar Angular

```bash
# Verificar atualizações disponíveis
ng update

# Atualizar tudo
ng update @angular/core @angular/cli

# Com versão específica
ng update @angular/core@16
```

---

## Estrutura de Arquivos Gerados

```
meu-app/
├── src/
│   ├── app/
│   │   ├── user-card/
│   │   │   ├── user-card.component.ts       # Componente
│   │   │   ├── user-card.component.html     # Template
│   │   │   ├── user-card.component.scss     # Estilos
│   │   │   └── user-card.component.spec.ts  # Testes
│   │   │
│   │   ├── services/
│   │   │   ├── user.service.ts
│   │   │   └── user.service.spec.ts
│   │   │
│   │   ├── app.component.ts
│   │   ├── app.routes.ts          # Rotas (standalone)
│   │   └── app.config.ts           # Config (standalone)
│   │
│   ├── main.ts                     # Entry point
│   ├── index.html
│   ├── styles.scss                 # Estilos globais
│   └── favicon.ico
│
├── angular.json                    # Config CLI
├── tsconfig.json                   # Config TypeScript
├── package.json
└── .gitignore
```

---

## Flags Úteis

### --standalone
```bash
ng g c user-card --standalone
# Cria componente moderno (sem NgModule)
```

### --skip-tests
```bash
ng g c user-card --skip-tests
# Não gera arquivo .spec.ts
```

### --routing
```bash
ng new app --routing
ng g m features/users --routing
# Adiciona routing
```

### --style
```bash
ng new app --style=scss
ng new app --style=sass
ng new app --style=less
```

### --skip-git
```bash
ng new app --skip-git
# Não inicializa git
```

### --directory
```bash
ng generate component user-card --directory=shared/components
# Cria em pasta diferente
```

---

## Ambiente & Variáveis

### Criar Arquivo de Ambiente
```
src/environments/
├── environment.ts          # Desenvolvimento
└── environment.prod.ts     # Produção
```

### Usar Ambiente
```typescript
import { environment } from '../environments/environment';

console.log(environment.apiUrl);
```

### Em angular.json
```json
{
  "configurations": {
    "production": {
      "fileReplacements": [
        {
          "replace": "src/environments/environment.ts",
          "with": "src/environments/environment.prod.ts"
        }
      ]
    }
  }
}
```

---

## Dicas Produtivas

### Atalhos Comuns
```bash
ng new app              # ng n app
ng generate component   # ng g c
ng generate service     # ng g s
ng serve               # ng s
ng build               # ng b
ng test                # ng t
```

### Scaffold Rápido (Feature Completa)
```bash
# Componente + Service em uma pasta
ng g c features/users/user-list --standalone --skip-tests
ng g s features/users/user --skip-tests
```

### Deletar Componente
```bash
# Não tem comando, deletar pasta manual
rm -rf src/app/user-card
```

### Verificar Versões
```bash
ng version
npm list @angular/core
```

---

## Performance

### Analisar Build
```bash
ng build --stats-json
npx webpack-bundle-analyzer dist/*/stats.json
```

### Lazy Loading (no routing)
```typescript
const routes = [
  {
    path: 'users',
    loadComponent: () => import('./users/user-list.component')
      .then(m => m.UserListComponent)
  }
];
```

### Otimizações no Build
```bash
ng build --configuration production --optimization
```

---

## Checklist de Setup

Novo projeto Angular:
- [ ] `ng new app --routing --style=scss --standalone`
- [ ] `npm install`
- [ ] `ng serve`
- [ ] Testar acesso em http://localhost:4200
- [ ] `git init && git add .`
- [ ] Criar componentes: `ng g c components/navbar`
- [ ] Criar services: `ng g s services/api`
- [ ] Estruturar pastas: `src/app/{core,shared,features}`

---

## Problemas Comuns

| Problema | Solução |
|----------|---------|
| **"Cannot find module"** | `npm install`, limpar `node_modules` |
| **Porta 4200 ocupada** | `ng serve --port 3000` |
| **Arquivo gerado sem standalone** | Use flag: `ng g c user --standalone` |
| **TypeScript errors** | `npm run build` para debug completo |
| **Teste não roda** | `ng test --watch=false` para output completo |

---

## Comandos Rápidos (Copy-Paste)

```bash
# Novo projeto moderno
ng new meu-app --routing --style=scss --standalone && cd meu-app && ng serve

# Setup rápido de feature
ng g c components/navbar --standalone --skip-tests
ng g s services/api --skip-tests

# Build para produção
ng build --configuration production

# Testes com coverage
ng test --code-coverage --watch=false

# Verificar código
ng lint --fix

# Atualizar dependências
ng update @angular/core @angular/cli --save
```

---

## Mais Informações

```bash
ng help
ng generate component --help
ng serve --help
```

Ou: [Angular CLI Docs](https://angular.io/cli)

