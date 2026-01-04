# ⭐ RxJS Survival Guide

Você **não precisa dominar RxJS**, só sobreviver. Um guia prático para React devs.

---

## Observable vs Promise

### Promise (você já conhece)
```javascript
// Resolve uma vez
fetch('/api/user')
  .then(r => r.json())
  .then(user => console.log(user))
  .catch(err => console.error(err));
```

### Observable (RxJS)
```typescript
// Pode emitir múltiplas vezes
this.http.get('/api/user').subscribe(
  user => console.log(user),
  err => console.error(err),
  () => console.log('Completo')
);
```

### Mapeamento
| Promise | Observable |
|---------|-----------|
| `.then()` | `.pipe()` + `map()` |
| `.catch()` | `.pipe()` + `catchError()` |
| Emite 1x | Pode emitir N vezes |
| Fire-and-forget | Precisa de `subscribe()` |

---

## Subscribe vs Async Pipe

### ❌ Subscribe (cuidado com memory leaks!)
```typescript
@Component({
  template: `<div>{{ user?.name }}</div>`
})
export class UserComponent implements OnInit, OnDestroy {
  user: User | null = null;
  private subscription?: Subscription;

  ngOnInit() {
    this.subscription = this.userService.getUser().subscribe(user => {
      this.user = user;
    });
  }

  ngOnDestroy() {
    this.subscription?.unsubscribe(); // ⚠️ Não esqueça!
  }
}
```

### ✅ Async Pipe (automático!)
```typescript
@Component({
  template: `<div>{{ (user$ | async)?.name }}</div>`
})
export class UserComponent {
  user$ = this.userService.getUser(); // Observable
  // Sem subscribe, sem unsubscribe!
}
```

**Regra de Ouro:** Use `async` pipe sempre que possível.

---

## Operadores Essenciais

### map - Transformar dados
```typescript
// React: data.map()
this.userService.getUser().pipe(
  map(user => user.name)
).subscribe(name => console.log(name));

// Ou com typing
this.userService.getUser().pipe(
  map((user: User) => ({
    id: user.id,
    displayName: user.name.toUpperCase()
  }))
).subscribe(data => console.log(data));
```

### switchMap - Requisição dependente
```typescript
// Quando userId muda, faz nova requisição
this.route.params.pipe(
  switchMap(params => this.userService.getUser(params['id']))
).subscribe(user => console.log(user));

// Exemplo com form input
this.searchInput.valueChanges.pipe(
  debounceTime(300),
  switchMap(searchTerm => this.userService.search(searchTerm))
).subscribe(results => console.log(results));
```

### debounceTime - Esperar antes de emitir
```typescript
// React: useEffect + timeout
this.searchInput.valueChanges.pipe(
  debounceTime(300), // Espera 300ms
  distinctUntilChanged() // Só emite se mudou
).subscribe(value => this.search(value));
```

### catchError - Tratar erro
```typescript
this.userService.getUser().pipe(
  catchError(error => {
    console.error('Erro:', error);
    return of({ id: 0, name: 'Default User' }); // Valor fallback
  })
).subscribe(user => console.log(user));
```

### tap - Side effects (debug)
```typescript
this.userService.getUser().pipe(
  tap(user => console.log('User fetched:', user)),
  map(user => user.name),
  tap(name => console.log('Transformed:', name))
).subscribe(name => console.log(name));
```

### filter - Filtrar valores
```typescript
this.userService.getUsers().pipe(
  filter(users => users.length > 0)
).subscribe(users => console.log(users));
```

### take - Pegar N valores
```typescript
// Pega apenas o primeiro valor e encerra
this.userService.getUser().pipe(
  take(1)
).subscribe(user => console.log(user));
```

---

## Padrões Comuns

### 1. Busca com Debounce (Search)
```typescript
@Component({
  template: `
    <input [formControl]="searchControl">
    <div *ngFor="let result of results$ | async">
      {{ result.name }}
    </div>
  `
})
export class SearchComponent {
  searchControl = new FormControl('');

  results$ = this.searchControl.valueChanges.pipe(
    debounceTime(300),
    distinctUntilChanged(),
    switchMap(term => this.api.search(term)),
    catchError(() => of([]))
  );

  constructor(private api: ApiService) {}
}
```

### 2. Requisição Dependente (User → Posts)
```typescript
@Component({
  template: `
    <div *ngIf="posts$ | async as posts">
      <div *ngFor="let post of posts">{{ post.title }}</div>
    </div>
  `
})
export class UserPostsComponent implements OnInit {
  posts$!: Observable<Post[]>;

  ngOnInit() {
    this.posts$ = this.route.params.pipe(
      switchMap(params =>
        this.userService.getUser(params['id']).pipe(
          switchMap(user => this.postService.getUserPosts(user.id))
        )
      )
    );
  }
}
```

### 3. Combinar Múltiplos Observables
```typescript
import { combineLatest, forkJoin } from 'rxjs';

// combineLatest: emite quando QUALQUER um muda
combineLatest([
  this.userService.getUser(),
  this.userService.getPosts()
]).pipe(
  map(([user, posts]) => ({ user, posts }))
).subscribe(data => console.log(data));

// forkJoin: espera todos terminarem (como Promise.all)
forkJoin([
  this.userService.getUser(),
  this.userService.getPosts(),
  this.userService.getComments()
]).subscribe(([user, posts, comments]) => {
  console.log(user, posts, comments);
});
```

### 4. Polling (requisição periódica)
```typescript
import { interval } from 'rxjs';

// A cada 5 segundos
interval(5000).pipe(
  switchMap(() => this.userService.getUser())
).subscribe(user => console.log(user));

// Versão melhorada com timer inicial
timer(0, 5000).pipe(
  switchMap(() => this.userService.getUser())
).subscribe(user => console.log(user));
```

### 5. Compartilhar Subscription (shareReplay)
```typescript
import { shareReplay } from 'rxjs/operators';

// Sem shareReplay: faz requisição a cada subscribe
const users$ = this.http.get('/api/users');
users$.subscribe(); // requisição 1
users$.subscribe(); // requisição 2

// Com shareReplay: faz requisição 1x, compartilha resultado
const users$ = this.http.get('/api/users').pipe(
  shareReplay(1)
);
users$.subscribe(); // requisição 1
users$.subscribe(); // reutiliza resultado
```

---

## Subject e BehaviorSubject

### Subject (simples)
```typescript
class UserStore {
  private userSubject = new Subject<User>();
  user$ = this.userSubject.asObservable();

  setUser(user: User) {
    this.userSubject.next(user); // emitir
  }
}

// Usar
const store = new UserStore();
store.user$.subscribe(user => console.log(user));
store.setUser({ id: 1, name: 'João' });
```

### BehaviorSubject (com valor inicial)
```typescript
class UserStore {
  // Sempre tem um valor (mesmo antes de subscrive)
  private userSubject = new BehaviorSubject<User | null>(null);
  user$ = this.userSubject.asObservable();

  setUser(user: User) {
    this.userSubject.next(user);
  }

  getUser(): User | null {
    return this.userSubject.value;
  }
}

// Usar
const store = new UserStore();
store.user$.subscribe(user => console.log(user)); // emite null imediatamente
store.setUser({ id: 1, name: 'João' });
```

### Quando Usar
- **Subject**: Eventos, múltiplos produtores
- **BehaviorSubject**: Estado compartilhado (como Context do React)

---

## Padrão de State Management (Simples)

### State Store (sem NgRx)
```typescript
import { Injectable } from '@angular/core';
import { BehaviorSubject } from 'rxjs';

interface AppState {
  user: User | null;
  isLoading: boolean;
  error: string | null;
}

@Injectable({ providedIn: 'root' })
export class AppStore {
  private state = new BehaviorSubject<AppState>({
    user: null,
    isLoading: false,
    error: null
  });

  state$ = this.state.asObservable();

  user$ = this.state$.pipe(map(state => state.user));
  isLoading$ = this.state$.pipe(map(state => state.isLoading));
  error$ = this.state$.pipe(map(state => state.error));

  loadUser(id: string) {
    this.setState({ isLoading: true });
    this.userService.getUser(id).subscribe(
      user => this.setState({ user, isLoading: false }),
      err => this.setState({ error: err.message, isLoading: false })
    );
  }

  private setState(partial: Partial<AppState>) {
    const current = this.state.value;
    this.state.next({ ...current, ...partial });
  }
}

// Usar
@Component({
  template: `
    <div *ngIf="isLoading$ | async">Loading...</div>
    <div *ngIf="error$ | async as err" class="error">{{ err }}</div>
    <div *ngIf="user$ | async as user">{{ user.name }}</div>
  `
})
export class UserComponent implements OnInit {
  user$ = this.store.user$;
  isLoading$ = this.store.isLoading$;
  error$ = this.store.error$;

  constructor(private store: AppStore) {}

  ngOnInit() {
    this.store.loadUser('123');
  }
}
```

---

## Quando NÃO usar RxJS

❌ **Evite RxJS para:**
- Estado simples (use classe normal)
- Valores que mudam raramente
- Lógica síncrona (use função normal)

✅ **Use RxJS para:**
- Chamadas de API
- Eventos (cliques, inputs)
- Estado complexo
- Fluxos de dados

---

## Operadores Úteis (Referência Rápida)

```typescript
import { of, from, interval, throwError } from 'rxjs';
import {
  map,           // transformar
  filter,        // filtrar
  tap,           // debug
  debounceTime,  // esperar
  distinctUntilChanged, // sem repetir
  switchMap,     // cancelar anterior
  catchError,    // tratar erro
  take,          // pegar N
  takeUntil,     // parar quando
  combineLatest, // juntar múltiplos
  forkJoin,      // esperar todos
  shareReplay,   // compartilhar
  startWith,     // valor inicial
  defaultIfEmpty // fallback
} from 'rxjs';

// Operadores que NÃO vão usar agora:
// mergeMap, concatMap, exhaustMap, retry,
// timeout, timeout, scan, reduce, partition...
```

---

## Cheat Sheet Rápido

| Necessidade | Como fazer |
|-------------|-----------|
| **Transformar** | `.pipe(map(...))` |
| **Filtrar** | `.pipe(filter(...))` |
| **Requisição dependente** | `.pipe(switchMap(...))` |
| **Esperar antes de emitir** | `.pipe(debounceTime(300))` |
| **Tratar erro** | `.pipe(catchError(...))` |
| **Debug** | `.pipe(tap(x => console.log(x)))` |
| **Pegar 1 valor** | `.pipe(take(1))` |
| **Juntar múltiplos** | `combineLatest([...])` |
| **Compartilhar** | `.pipe(shareReplay(1))` |
| **Cancelar sub** | Usar `async` pipe! |

---

## Exemplo: Formulário Reativo com RxJS

```typescript
@Component({
  template: `
    <form [formGroup]="form">
      <input formControlName="email" placeholder="Email">
      <input formControlName="password" placeholder="Password">

      <div *ngIf="emailErrors$ | async as errors">
        <p *ngIf="errors?.required">Email é obrigatório</p>
        <p *ngIf="errors?.pattern">Email inválido</p>
      </div>

      <div *ngIf="isChecking$ | async">Validando...</div>

      <button [disabled]="(isSubmitting$ | async) || !form.valid">
        {{ (isSubmitting$ | async) ? 'Enviando...' : 'Enviar' }}
      </button>
    </form>
  `
})
export class LoginComponent implements OnInit {
  form = this.fb.group({
    email: ['', [Validators.required, Validators.email]],
    password: ['', Validators.required]
  });

  isSubmitting$ = new BehaviorSubject(false);
  isChecking$ = new BehaviorSubject(false);

  emailErrors$ = this.form.get('email')!.statusChanges.pipe(
    startWith(this.form.get('email')!.status),
    map(() => this.form.get('email')!.errors)
  );

  constructor(
    private fb: FormBuilder,
    private authService: AuthService
  ) {}

  ngOnInit() {
    // Validação assíncrona
    this.form.get('email')!.valueChanges.pipe(
      debounceTime(300),
      distinctUntilChanged(),
      tap(() => this.isChecking$.next(true)),
      switchMap(email => this.authService.checkEmail(email)),
      tap(() => this.isChecking$.next(false))
    ).subscribe();
  }

  onSubmit() {
    this.isSubmitting$.next(true);
    this.authService.login(this.form.value).pipe(
      tap(() => this.isSubmitting$.next(false)),
      catchError(err => {
        this.isSubmitting$.next(false);
        alert(err.message);
        return throwError(() => err);
      })
    ).subscribe(() => {
      // Login bem-sucedido
    });
  }
}
```

---

## Dicas Finais

1. **Prefira `async` pipe**: Evita memory leaks automaticamente
2. **Use `tap` para debug**: `pipe(tap(x => console.log(x)))`
3. **`switchMap` é seu amigo**: Para cancelar requisição anterior
4. **Não se estresse**: RxJS é grande, você só precisa de 5% para trabalhar
5. **Comece com o básico**: map, filter, switchMap, catchError
6. **Share observables**: Use `.pipe(shareReplay(1))` se precisa

---

## Próximo Passo

Você agora sabe RxJS o suficiente para:
- ✅ Ler código existente
- ✅ Fazer requisições HTTP
- ✅ Manipular formulários
- ✅ Compartilhar estado

Para dominar, leia: [RxJS Docs](https://rxjs.dev)

