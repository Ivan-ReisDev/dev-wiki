# 📚 Angular Cheatsheets - Índice Completo

Guias práticos para aprender Angular com foco em devs que vêm do React.

---

## 🚀 Começar Aqui (Ordem Recomendada)

### 1️⃣ **[Angular para Quem Vem do React](angular-for-react-devs.md)** ⭐ IMPORTANTE
**Tempo: 15 min** | Dificuldade: Fácil

Traduz conceitos de React para Angular:
- Component = function component
- @Input/@Output ↔ props/callbacks
- ngIf/ngFor ↔ JSX conditionals
- Services + DI ↔ hooks + context
- ngOnInit ↔ useEffect
- ChangeDetectionStrategy.OnPush ↔ memoization

**Próximos passos:** Leia este primeiro. Depois prossiga para Standalone Components.

---

### 2️⃣ **[Angular Standalone Components](angular-standalone-components.md)** ⭐ MUITO IMPORTANTE
**Tempo: 20 min** | Dificuldade: Médio

Angular moderno (14+) sem NgModule:
- `standalone: true` e imports diretos
- Bootstrap com `bootstrapApplication()`
- Estrutura de pastas
- Routes em standalone
- Guards funcionais
- HTTP Interceptors funcionais

**Por quê:** Se afasta do Angular antigo (NgModule), fica parecido com React.

---

### 3️⃣ **[RxJS Survival Guide](rxjs-survival-guide.md)** ⭐ IMPORTANTE
**Tempo: 20 min** | Dificuldade: Médio

RxJS o suficiente para sobreviver:
- Observable vs Promise
- Subscribe vs async pipe
- Operadores essenciais (map, switchMap, debounceTime, catchError)
- Padrões comuns (search, requisição dependente, polling)
- Subject vs BehaviorSubject
- State management simples

**Próximos passos:** Leia antes de fazer HTTP/Forms.

---

### 4️⃣ **[Angular: Reactive Forms & HTTP](angular-forms-http.md)** ⭐ IMPORTANTE
**Tempo: 25 min** | Dificuldade: Médio

Onde React devs mais sofrem:
- Reactive Forms vs Template-Driven
- FormBuilder
- Validadores (built-in + customizados)
- FormArray (arrays dinâmicos)
- HTTP Service (CRUD)
- Tratamento de erros
- Interceptors

**Por quê:** Formulários e HTTP são diferentes no Angular.

---

### 5️⃣ **[Angular CLI Essentials](angular-cli-essentials.md)** (NOVO)
**Tempo: 10 min** | Dificuldade: Fácil

Comandos que você realmente usa:
- `ng new`, `ng serve`, `ng build`
- `ng generate component`, `ng generate service`
- Flags úteis (`--standalone`, `--skip-tests`)
- Onde fica cada arquivo

---

## 📖 Referência Completa

### 6️⃣ **[angular-cheatsheet.md](angular-cheatsheet.md)**
Cheatsheet tradicional com tudo:
- Instalação e setup
- Componentes (básico + standalone)
- Data binding (interpolação, property, event, two-way)
- Diretivas estruturais (*ngIf, *ngFor, *ngSwitch)
- Diretivas de atributo (ngClass, ngStyle)
- Input/Output
- Services e DI
- Routing
- Forms (template-driven + reactive)
- Pipes
- Lifecycle hooks
- Decorators (@ViewChild, @HostListener, etc)
- RxJS básico
- Boas práticas
- Estrutura de pastas

---

## 🎯 Guias por Tópico

### Component & Data Binding
- 📄 [angular-for-react-devs.md](angular-for-react-devs.md) - Conceitos básicos
- 📄 [angular-standalone-components.md](angular-standalone-components.md) - Componentes modernos
- 📄 [angular-cheatsheet.md](angular-cheatsheet.md) - Data binding detalhado

### State & HTTP
- 📄 [rxjs-survival-guide.md](rxjs-survival-guide.md) - Observable e operadores
- 📄 [angular-forms-http.md](angular-forms-http.md) - HTTP + Interceptors
- 📄 [angular-cheatsheet.md](angular-cheatsheet.md) - Services avançados

### Formulários
- 📄 [angular-forms-http.md](angular-forms-http.md) - Reactive Forms + validação
- 📄 [angular-cheatsheet.md](angular-cheatsheet.md) - Template-driven forms

### Roteamento
- 📄 [angular-standalone-components.md](angular-standalone-components.md) - Routes em standalone
- 📄 [angular-cheatsheet.md](angular-cheatsheet.md) - Routing completo

### CLI e Setup
- 📄 [angular-cli-essentials.md](angular-cli-essentials.md) - Comandos essenciais
- 📄 [angular-standalone-components.md](angular-standalone-components.md) - Setup moderno

---

## ❌ O Que NÃO Estudar (Por Enquanto)

Esqueça por enquanto:

| Tópico | Por quê |
|--------|---------|
| **NgModule** | Antigo, use Standalone |
| **Angular Animations** | Profundo, aprenda depois |
| **NgRx** | Complexo, state simples funciona |
| **Angular Universal (SSR)** | Específico, só se precisar |
| **Material Design** | Aprende conforme usa |
| **Dependency Injection avançado** | Base é suficiente |

---

## 💡 Quick Reference

### Comparação React ↔ Angular

| Conceito | React | Angular |
|----------|-------|---------|
| **Componente** | `function Component()` | `@Component()` |
| **Props** | parâmetro | `@Input()` |
| **Callback** | prop callback | `@Output()` |
| **State** | `useState()` | propriedade |
| **Effect** | `useEffect()` | `ngOnInit()` |
| **Context** | `useContext()` | Service injetado |
| **Loop** | `.map()` | `*ngFor` |
| **Condicional** | `? :` / `&&` | `*ngIf` |
| **Classe dinâmica** | `className={}` | `[ngClass]` |
| **HTTP** | fetch/axios | `HttpClient` |
| **Validação** | libs | Validators incorporado |
| **Router** | react-router | @angular/router |

---

## 🎓 Roadmap de Aprendizado

### Semana 1: Fundamentos
- [ ] Ler: Angular para React devs
- [ ] Ler: Standalone Components
- [ ] Praticar: Criar componente simples com @Input/@Output

### Semana 2: State & HTTP
- [ ] Ler: RxJS Survival Guide
- [ ] Ler: Reactive Forms & HTTP
- [ ] Praticar: CRUD simples com API

### Semana 3: Aplicação Real
- [ ] Ler: angular-cheatsheet.md (seções que faltam)
- [ ] Praticar: Construir mini-app
- [ ] Debugar: Usar Angular DevTools

### Semana 4: Produção
- [ ] Otimizar: ChangeDetectionStrategy.OnPush
- [ ] Estrutura: Organizar pastas
- [ ] Deploy: ng build --prod

---

## 📚 Referências Externas

- [Angular Official Docs](https://angular.io)
- [Angular Standalone Guide](https://angular.io/guide/standalone-components)
- [RxJS Docs](https://rxjs.dev)
- [HTTP Client Guide](https://angular.io/guide/http)
- [Forms Guide](https://angular.io/guide/reactive-forms)
- [Angular Update Guide](https://update.angular.io)

---

## 🔥 Hot Tips

1. **Use `async` pipe** ao invés de `.subscribe()` - evita memory leaks
2. **`ChangeDetectionStrategy.OnPush`** em componentes "dumb" - melhora performance
3. **`trackBy` em `*ngFor`** com listas grandes - muito mais rápido
4. **`switchMap` é seu amigo** - para cancelar requisições anteriores
5. **Validadores customizados** são super úteis - use!
6. **Interceptors** para lógica comum (auth, errors)
7. **Lazy loading** de componentes com `loadComponent`
8. **Compartilhe Observables** com `shareReplay(1)`

---

## 🚨 Erros Comuns

### ❌ Memory Leak
```typescript
// Errado
ngOnInit() {
  this.service.getData().subscribe(data => {
    this.data = data;
  }); // Sem unsubscribe!
}
```

```typescript
// Certo
<div>{{ (data$ | async)?.value }}</div>
```

### ❌ Validador Assíncrono Sem Debounce
```typescript
// Errado - faz requisição a cada keystroke
```

```typescript
// Certo - espera antes de validar
input.valueChanges.pipe(
  debounceTime(300),
  distinctUntilChanged(),
  switchMap(value => validate(value))
)
```

### ❌ NgModule em novo projeto
```typescript
// ❌ Evitar
@NgModule({
  declarations: [...]
})
```

```typescript
// ✅ Usar
@Component({
  standalone: true
})
```

---

## 🎯 Goals Alcançáveis

Depois de estudar estes guias, você consegue:

- ✅ Ler e entender código Angular existente
- ✅ Criar componentes modernos (Standalone)
- ✅ Fazer requisições HTTP com tratamento de erro
- ✅ Criar formulários complexos com validação
- ✅ Compartilhar estado com Services
- ✅ Entender RxJS o suficiente para trabalhar
- ✅ Usar Angular CLI com confiança
- ✅ Debugar problemas comuns

**Você não precisa dominar tudo para ser produtivo!**

---

## 💬 Dúvidas Frequentes

### P: Por onde começo?
**R:** Leia na ordem: React devs → Standalone → RxJS → Forms/HTTP

### P: Preciso aprender NgModule?
**R:** Não. Standalone é o futuro. Aprenda se precisar ler código antigo.

### P: RxJS é tão complexo?
**R:** Não. Use apenas: map, switchMap, debounceTime, catchError.

### P: Reactive ou Template Forms?
**R:** Sempre Reactive. Template é simples demais para produção.

### P: Quando usar Subject vs BehaviorSubject?
**R:** BehaviorSubject para estado, Subject para eventos.

---

## 📝 Próximos Passos

1. Escolha um dos 5 cheatsheets principais acima
2. Leia e pratique
3. Crie um projeto pequenininho
4. Consulte [angular-cheatsheet.md](angular-cheatsheet.md) para detalher
5. Ganhe experiência com projetos reais

**Sucesso!** 🚀

