# ⭐ Angular para Quem Vem do React

Um guia prático que traduz conceitos de React para Angular. **Comece aqui.**

---

## Component = Function Component

### React
```jsx
function UserCard({ user, onDelete }) {
  const [isOpen, setIsOpen] = useState(false);

  useEffect(() => {
    console.log('User loaded:', user.id);
  }, [user.id]);

  return (
    <div>
      <h2>{user.name}</h2>
      <button onClick={() => onDelete(user.id)}>Delete</button>
    </div>
  );
}
```

### Angular (Standalone)
```typescript
import { Component, Input, Output, EventEmitter, OnInit } from '@angular/core';

@Component({
  selector: 'app-user-card',
  standalone: true,
  template: `
    <div>
      <h2>{{ user.name }}</h2>
      <button (click)="onDelete()">Delete</button>
    </div>
  `
})
export class UserCardComponent implements OnInit {
  @Input() user!: User;
  @Output() delete = new EventEmitter<string>();

  ngOnInit() {
    console.log('User loaded:', this.user.id);
  }

  onDelete() {
    this.delete.emit(this.user.id);
  }
}
```

### Mapeamento
| React | Angular |
|-------|---------|
| `props` | `@Input()` |
| `useEffect` | `ngOnInit`, `ngOnChanges` |
| `useState` | propriedades da classe |
| callback em props | `@Output()` + `EventEmitter` |
| JSX | template string ou arquivo .html |

---

## @Input() e @Output() ↔ Props e Callbacks

### Props em React
```jsx
// Parent
<UserCard user={user} onUpdate={handleUpdate} />

// Child
function UserCard({ user, onUpdate }) {
  return (
    <input onChange={(e) => onUpdate(e.target.value)} />
  );
}
```

### @Input / @Output em Angular
```typescript
// Parent
@Component({
  template: `
    <app-user-card
      [user]="user"
      (update)="handleUpdate($event)">
    </app-user-card>
  `
})
export class ParentComponent {
  user = { name: 'João' };

  handleUpdate(value: string) {
    console.log('Updated:', value);
  }
}

// Child
@Component({
  selector: 'app-user-card',
  standalone: true,
  template: `<input (input)="onUpdate($event)" />`
})
export class UserCardComponent {
  @Input() user!: User;
  @Output() update = new EventEmitter<string>();

  onUpdate(event: Event) {
    const value = (event.target as HTMLInputElement).value;
    this.update.emit(value);
  }
}
```

### Detalhe importante
```typescript
// Input com valor default
@Input() title: string = 'Default';

// Input com setter customizado
@Input()
set name(value: string) {
  this._name = value.toUpperCase();
}
get name(): string {
  return this._name;
}
private _name = '';
```

---

## ngIf, ngFor, ngClass ↔ JSX Conditionals

### Renderização Condicional

#### React
```jsx
function Dashboard({ user, isLoading }) {
  if (isLoading) return <p>Loading...</p>;
  if (!user) return <p>No user</p>;

  return <div>{user.name}</div>;
}
```

#### Angular
```typescript
@Component({
  template: `
    <!-- Simples -->
    <div *ngIf="user">{{ user.name }}</div>

    <!-- Com else -->
    <div *ngIf="user; else noUser">
      {{ user.name }}
    </div>
    <ng-template #noUser>
      <p>No user</p>
    </ng-template>

    <!-- Com loading -->
    <div *ngIf="isLoading; then loading; else content"></div>
    <ng-template #loading>Loading...</ng-template>
    <ng-template #content>{{ user.name }}</ng-template>

    <!-- Múltiplas condições -->
    <div *ngIf="user && user.isAdmin">Admin Panel</div>
  `
})
export class DashboardComponent {
  user: User | null = null;
  isLoading = false;
}
```

### Loops (Map)

#### React
```jsx
function UserList({ users }) {
  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

#### Angular
```typescript
@Component({
  template: `
    <!-- Básico -->
    <ul>
      <li *ngFor="let user of users">{{ user.name }}</li>
    </ul>

    <!-- Com index -->
    <div *ngFor="let user of users; let i = index">
      {{ i + 1 }}. {{ user.name }}
    </div>

    <!-- Com trackBy (performance!) -->
    <li *ngFor="let user of users; trackBy: trackByUserId">
      {{ user.name }}
    </li>
  `
})
export class UserListComponent {
  users: User[] = [];

  trackByUserId(index: number, user: User): number {
    return user.id;
  }
}
```

### Classes Dinâmicas

#### React
```jsx
<div className={`card ${isActive ? 'active' : ''}`}>
  Content
</div>
```

#### Angular
```typescript
@Component({
  template: `
    <!-- Objeto -->
    <div [ngClass]="{ 'active': isActive, 'error': hasError }">
      Content
    </div>

    <!-- Array -->
    <div [ngClass]="['card', isActive ? 'active' : '']">
      Content
    </div>

    <!-- String -->
    <div [ngClass]="dynamicClasses">Content</div>
  `
})
export class MyComponent {
  isActive = false;
  hasError = false;
  dynamicClasses = 'card active';
}
```

---

## Services + DI ↔ Hooks + Context

### React (Custom Hook + Context)
```jsx
// Hook customizado
function useUser() {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetchUser().then(setUser);
  }, []);

  return user;
}

// Context
const UserContext = createContext();

function App() {
  const user = useUser();
  return (
    <UserContext.Provider value={user}>
      <Dashboard />
    </UserContext.Provider>
  );
}

function Dashboard() {
  const user = useContext(UserContext);
  return <div>{user?.name}</div>;
}
```

### Angular (Service + DI)
```typescript
// Service (= custom hook + context)
@Injectable({ providedIn: 'root' })
export class UserService {
  private userSubject = new BehaviorSubject<User | null>(null);
  user$ = this.userSubject.asObservable();

  constructor(private http: HttpClient) {
    this.loadUser();
  }

  private loadUser() {
    this.http.get<User>('/api/user').subscribe(user => {
      this.userSubject.next(user);
    });
  }
}

// Component (usar o service)
@Component({
  template: `
    <div>{{ user$ | async | json }}</div>
  `
})
export class DashboardComponent {
  user$ = this.userService.user$;

  constructor(private userService: UserService) {}
}
```

### Mapeamento
| React | Angular |
|-------|---------|
| `useState` | propriedade da classe |
| `useEffect` | `ngOnInit` |
| `useContext` | Injetar `Service` no construtor |
| `custom hook` | `Service` com `@Injectable()` |

---

## ngOnInit ↔ useEffect

### React
```jsx
function UserProfile({ userId }) {
  useEffect(() => {
    console.log('Component mounted');
    loadUser(userId);

    return () => {
      console.log('Cleanup');
    };
  }, [userId]);
}
```

### Angular
```typescript
@Component({...})
export class UserProfileComponent implements OnInit, OnDestroy {
  @Input() userId!: string;
  private subscription?: Subscription;

  ngOnInit() {
    console.log('Component mounted');
    this.subscription = this.loadUser(this.userId).subscribe();
  }

  ngOnDestroy() {
    console.log('Cleanup');
    this.subscription?.unsubscribe();
  }

  private loadUser(id: string) {
    return this.http.get<User>(`/api/users/${id}`);
  }
}
```

### Lifecycle Hooks Comuns
```typescript
import { OnInit, OnDestroy, OnChanges } from '@angular/core';

export class MyComponent implements OnInit, OnDestroy, OnChanges {

  // Equivalente a: useEffect(() => {...}, [])
  ngOnInit() {
    console.log('Mounted');
  }

  // Equivalente a: useEffect(() => {...}, [prop])
  ngOnChanges(changes: SimpleChanges) {
    if (changes['userId']) {
      console.log('userId mudou');
    }
  }

  // Equivalente a: return () => {...} no useEffect
  ngOnDestroy() {
    console.log('Cleanup');
  }
}
```

---

## ChangeDetectionStrategy.OnPush ↔ React.memo / useMemo

### React
```jsx
// Memoizar componente
export const UserCard = React.memo(function UserCard({ user }) {
  return <div>{user.name}</div>;
}, (prev, next) => prev.user.id === next.user.id);
```

### Angular
```typescript
import { ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-user-card',
  template: `<div>{{ user.name }}</div>`,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class UserCardComponent {
  @Input() user!: User;
}
```

### O que faz
- **React.memo**: Só re-renderiza se props mudarem
- **OnPush**: Só re-renderiza se `@Input()` mudarem ou evento disparar

### Quando usar
- Componentes "dumb" (apenas recebem props)
- Componentes em listas (performance!)
- Componentes que não mudam frequentemente

---

## Exemplo Completo: Lado a Lado

### React
```jsx
function UserList() {
  const [users, setUsers] = useState([]);
  const [selectedId, setSelectedId] = useState(null);

  useEffect(() => {
    fetch('/api/users')
      .then(r => r.json())
      .then(setUsers);
  }, []);

  const handleDelete = (id) => {
    setUsers(users.filter(u => u.id !== id));
  };

  return (
    <div>
      {users.map(user => (
        <UserCard
          key={user.id}
          user={user}
          isSelected={user.id === selectedId}
          onSelect={() => setSelectedId(user.id)}
          onDelete={handleDelete}
        />
      ))}
    </div>
  );
}

function UserCard({ user, isSelected, onSelect, onDelete }) {
  return (
    <div className={isSelected ? 'selected' : ''}>
      <h3>{user.name}</h3>
      <button onClick={onSelect}>Select</button>
      <button onClick={() => onDelete(user.id)}>Delete</button>
    </div>
  );
}
```

### Angular
```typescript
// user-list.component.ts
@Component({
  selector: 'app-user-list',
  standalone: true,
  imports: [CommonModule, UserCardComponent],
  template: `
    <app-user-card
      *ngFor="let user of users; trackBy: trackByUserId"
      [user]="user"
      [isSelected]="user.id === selectedId"
      (select)="selectedId = $event"
      (delete)="handleDelete($event)">
    </app-user-card>
  `
})
export class UserListComponent implements OnInit {
  users: User[] = [];
  selectedId: string | null = null;

  constructor(private userService: UserService) {}

  ngOnInit() {
    this.userService.getUsers().subscribe(users => {
      this.users = users;
    });
  }

  handleDelete(id: string) {
    this.users = this.users.filter(u => u.id !== id);
  }

  trackByUserId(index: number, user: User): string {
    return user.id;
  }
}

// user-card.component.ts
@Component({
  selector: 'app-user-card',
  standalone: true,
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <div [ngClass]="{ selected: isSelected }">
      <h3>{{ user.name }}</h3>
      <button (click)="onSelect()">Select</button>
      <button (click)="onDelete()">Delete</button>
    </div>
  `
})
export class UserCardComponent {
  @Input() user!: User;
  @Input() isSelected = false;
  @Output() select = new EventEmitter<string>();
  @Output() delete = new EventEmitter<string>();

  onSelect() {
    this.select.emit(this.user.id);
  }

  onDelete() {
    this.delete.emit(this.user.id);
  }
}
```

---

## Cheat Sheet Rápido

| Necessidade | React | Angular |
|-------------|-------|---------|
| **Componente** | `function Component()` | `@Component({ template })` |
| **Props** | parâmetro | `@Input()` |
| **Callback** | prop callback | `@Output()` |
| **State** | `useState()` | propriedade |
| **Effect** | `useEffect()` | `ngOnInit()` |
| **Context** | `useContext()` | Injetar Service |
| **Lista** | `.map()` | `*ngFor` |
| **Condicional** | `? :` ou `&&` | `*ngIf` |
| **Classe dinâmica** | `className={}` | `[ngClass]` |
| **Estilo dinâmico** | `style={}` | `[ngStyle]` |
| **Cleanup** | `return () => {}` | `ngOnDestroy()` |
| **Memoization** | `React.memo()` | `ChangeDetectionStrategy.OnPush` |

---

## Próximos Passos

1. ✅ **Você está aqui**: Entender componentes e data flow
2. **Standalone Components**: Componentes modernos (sem NgModule)
3. **Services & DI**: Gerenciar dados compartilhados
4. **Reactive Forms**: Formulários com validação
5. **RxJS**: Programação reativa (o "useState" do Angular)
6. **HTTP + Interceptors**: Chamadas de API

Próximo: Leia `angular-standalone-components.md` 👉

