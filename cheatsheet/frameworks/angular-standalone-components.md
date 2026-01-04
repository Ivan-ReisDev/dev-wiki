# ⭐ Angular Standalone Components (Angular 14+)

Componentes modernos sem NgModule. Próximo ao modelo do React.

---

## Por que Standalone?

### Antes (NgModule - Antigo ❌)
```typescript
// shared.module.ts
@NgModule({
  declarations: [UserCardComponent],
  imports: [CommonModule],
  exports: [UserCardComponent]
})
export class SharedModule {}

// feature.module.ts
@NgModule({
  imports: [SharedModule],
  declarations: [ListComponent]
})
export class FeatureModule {}

// app.module.ts
@NgModule({
  imports: [FeatureModule]
})
export class AppModule {}
```

### Agora (Standalone - Moderno ✅)
```typescript
@Component({
  selector: 'app-user-card',
  standalone: true,
  imports: [CommonModule],
  template: `...`
})
export class UserCardComponent {}

@Component({
  selector: 'app-list',
  standalone: true,
  imports: [CommonModule, UserCardComponent],
  template: `...`
})
export class ListComponent {}

// Inicializar app
bootstrapApplication(AppComponent, {
  providers: [
    provideHttpClient(),
    provideRouter(routes)
  ]
});
```

---

## Estrutura Básica

### Componente Standalone
```typescript
import { Component, Input, Output, EventEmitter, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { ReactiveFormsModule } from '@angular/forms';

interface User {
  id: string;
  name: string;
}

@Component({
  selector: 'app-user-card',
  standalone: true,
  imports: [CommonModule, ReactiveFormsModule], // Imports diretos!
  template: `
    <div class="card">
      <h2>{{ user.name }}</h2>
      <button (click)="delete.emit(user.id)">Delete</button>
    </div>
  `,
  styles: [`
    .card {
      border: 1px solid #ccc;
      padding: 16px;
      border-radius: 4px;
    }
  `]
})
export class UserCardComponent implements OnInit {
  @Input() user!: User;
  @Output() delete = new EventEmitter<string>();

  ngOnInit() {
    console.log('User:', this.user);
  }
}
```

### Usando Componente Standalone
```typescript
@Component({
  selector: 'app-dashboard',
  standalone: true,
  imports: [CommonModule, UserCardComponent], // Componente standalone como import!
  template: `
    <app-user-card
      *ngFor="let user of users"
      [user]="user"
      (delete)="onDelete($event)">
    </app-user-card>
  `
})
export class DashboardComponent {
  users = [{ id: '1', name: 'João' }];

  onDelete(id: string) {
    this.users = this.users.filter(u => u.id !== id);
  }
}
```

---

## Inicialização da Aplicação

### main.ts (Novo)
```typescript
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app/app.component';
import {
  provideRouter,
  Routes
} from '@angular/router';
import { provideHttpClient } from '@angular/common/http';
import { ApplicationConfig } from '@angular/core';

// Definir rotas
const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'users', component: UsersComponent },
  {
    path: 'admin',
    loadComponent: () => import('./admin/admin.component')
      .then(m => m.AdminComponent),
    canActivate: [authGuard]
  }
];

// Configuração da aplicação
const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes),
    provideHttpClient(),
    // Adicionar interceptadores
    withInterceptors([authInterceptor])
  ]
};

bootstrapApplication(AppComponent, appConfig);
```

### App Component
```typescript
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterOutlet, RouterLink } from '@angular/router';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [CommonModule, RouterOutlet, RouterLink],
  template: `
    <nav>
      <a routerLink="/">Home</a>
      <a routerLink="/users">Users</a>
    </nav>
    <router-outlet></router-outlet>
  `
})
export class AppComponent {}
```

---

## Estrutura de Pastas (Standalone)

```
src/
├── app/
│   ├── app.component.ts          # Root component
│   ├── main.ts                   # Bootstrap standalone
│   ├── core/                     # Serviços globais
│   │   ├── auth.service.ts
│   │   ├── http.interceptor.ts
│   │   └── auth.guard.ts
│   ├── shared/                   # Componentes compartilhados
│   │   ├── components/
│   │   │   ├── navbar/navbar.component.ts
│   │   │   └── footer/footer.component.ts
│   │   ├── directives/
│   │   └── pipes/
│   ├── features/                 # Módulos de features
│   │   ├── users/
│   │   │   ├── user-list.component.ts
│   │   │   ├── user-detail.component.ts
│   │   │   └── user.service.ts
│   │   ├── products/
│   │   └── dashboard/
│   └── models/                   # Tipos e interfaces
│       └── user.model.ts
```

---

## Imports & Dependencies

### CommonModule vs Imports Específicos
```typescript
// ❌ Evite: CommonModule é pesado
@Component({
  standalone: true,
  imports: [CommonModule],
  template: `...`
})

// ✅ Use: Apenas o necessário
@Component({
  standalone: true,
  imports: [NgIf, NgFor, NgClass], // Imports específicos
  template: `...`
})
```

### Directives Avulsas
```typescript
import { NgIf, NgFor, NgSwitch, NgSwitchCase } from '@angular/common';
import { FormModule } from '@angular/forms';

@Component({
  standalone: true,
  imports: [
    NgIf,
    NgFor,
    FormModule,
    UserCardComponent,
    UserService  // ❌ NÃO importa services
  ]
})
```

### Services não precisam ser importados
```typescript
// Services são injetados, não importados!
export class UserListComponent {
  constructor(private userService: UserService) {} // Injetado
}
```

---

## Services em Standalone

### Criar Service
```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { BehaviorSubject, Observable } from 'rxjs';

interface User {
  id: string;
  name: string;
}

@Injectable({
  providedIn: 'root' // Fornecedor global
})
export class UserService {
  private users = new BehaviorSubject<User[]>([]);
  users$ = this.users.asObservable();

  constructor(private http: HttpClient) {
    this.loadUsers();
  }

  private loadUsers() {
    this.http.get<User[]>('/api/users').subscribe(users => {
      this.users.next(users);
    });
  }

  getUser(id: string): Observable<User> {
    return this.http.get<User>(`/api/users/${id}`);
  }
}
```

### Usar Service em Componente
```typescript
@Component({
  selector: 'app-users',
  standalone: true,
  imports: [CommonModule, UserCardComponent],
  template: `
    <app-user-card
      *ngFor="let user of users$ | async"
      [user]="user">
    </app-user-card>
  `
})
export class UsersComponent {
  users$ = this.userService.users$;

  constructor(private userService: UserService) {}
}
```

---

## Routes em Standalone

### Definir Rotas
```typescript
import { Routes } from '@angular/router';

export const routes: Routes = [
  {
    path: '',
    component: HomeComponent
  },
  {
    path: 'users',
    component: UsersListComponent
  },
  {
    path: 'users/:id',
    component: UserDetailComponent,
    canActivate: [authGuard]
  },
  {
    path: 'admin',
    loadComponent: () => import('./admin/admin.component')
      .then(m => m.AdminComponent),
    canActivate: [adminGuard]
  },
  {
    path: 'not-found',
    component: NotFoundComponent
  },
  {
    path: '**',
    redirectTo: 'not-found'
  }
];
```

### Guards em Standalone
```typescript
import { inject } from '@angular/core';
import { Router, CanActivateFn } from '@angular/router';

export const authGuard: CanActivateFn = (route, state) => {
  const router = inject(Router);
  const authService = inject(AuthService);

  if (authService.isLoggedIn()) {
    return true;
  }

  router.navigate(['/login'], {
    queryParams: { returnUrl: state.url }
  });

  return false;
};

export const adminGuard: CanActivateFn = (route, state) => {
  const router = inject(Router);
  const authService = inject(AuthService);

  if (authService.isAdmin()) {
    return true;
  }

  router.navigate(['/unauthorized']);
  return false;
};
```

---

## HTTP Interceptors em Standalone

### Functional Interceptor (Novo)
```typescript
import { HttpInterceptorFn, HttpErrorResponse } from '@angular/common/http';
import { inject } from '@angular/core';
import { AuthService } from './auth.service';
import { catchError, throwError } from 'rxjs';

export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const authService = inject(AuthService);

  // Adicionar token
  const token = authService.getToken();
  if (token) {
    req = req.clone({
      setHeaders: {
        Authorization: `Bearer ${token}`
      }
    });
  }

  return next(req).pipe(
    catchError((error: HttpErrorResponse) => {
      if (error.status === 401) {
        // Token expirou
        authService.logout();
      }
      return throwError(() => error);
    })
  );
};

export const errorInterceptor: HttpInterceptorFn = (req, next) => {
  return next(req).pipe(
    catchError((error: HttpErrorResponse) => {
      console.error('HTTP Error:', error.status, error.message);
      return throwError(() => error);
    })
  );
};
```

### Registrar Interceptors
```typescript
import { withInterceptors } from '@angular/common/http';

const appConfig: ApplicationConfig = {
  providers: [
    provideHttpClient(
      withInterceptors([authInterceptor, errorInterceptor])
    )
  ]
};

bootstrapApplication(AppComponent, appConfig);
```

---

## Forms em Standalone

### Reactive Forms
```typescript
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { ReactiveFormsModule, FormBuilder, FormGroup, Validators } from '@angular/forms';

@Component({
  selector: 'app-user-form',
  standalone: true,
  imports: [CommonModule, ReactiveFormsModule],
  template: `
    <form [formGroup]="form" (ngSubmit)="onSubmit()">
      <input formControlName="name" placeholder="Name">
      <input formControlName="email" type="email" placeholder="Email">
      <button type="submit" [disabled]="form.invalid">Save</button>
    </form>
  `
})
export class UserFormComponent implements OnInit {
  form!: FormGroup;

  constructor(private fb: FormBuilder) {}

  ngOnInit() {
    this.form = this.fb.group({
      name: ['', Validators.required],
      email: ['', [Validators.required, Validators.email]]
    });
  }

  onSubmit() {
    if (this.form.valid) {
      console.log(this.form.value);
    }
  }
}
```

---

## Lazy Loading em Standalone

### Componente Lazy Loaded
```typescript
// admin.component.ts
@Component({
  selector: 'app-admin',
  standalone: true,
  imports: [CommonModule],
  template: `<h1>Admin Panel</h1>`
})
export class AdminComponent {}

// Rotas
const routes: Routes = [
  {
    path: 'admin',
    loadComponent: () => import('./admin/admin.component')
      .then(m => m.AdminComponent),
    canActivate: [adminGuard]
  }
];
```

---

## Dependency Injection (DI)

### Fornecer Services Globalmente
```typescript
@Injectable({
  providedIn: 'root' // Disponível globalmente
})
export class UserService {}
```

### Fornecer em Nível de Aplicação
```typescript
const appConfig: ApplicationConfig = {
  providers: [
    UserService,
    { provide: API_URL, useValue: 'https://api.example.com' }
  ]
};

bootstrapApplication(AppComponent, appConfig);
```

### Usar Providers no Componente
```typescript
@Component({
  standalone: true,
  providers: [UserService] // Escopo local
})
export class AdminComponent {
  constructor(private userService: UserService) {}
}
```

---

## Exemplo Completo: CRUD com Standalone

### main.ts
```typescript
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app/app.component';
import { ApplicationConfig } from '@angular/core';
import { provideRouter } from '@angular/router';
import { provideHttpClient, withInterceptors } from '@angular/common/http';
import { routes } from './app/app.routes';
import { authInterceptor } from './app/core/auth.interceptor';

const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes),
    provideHttpClient(withInterceptors([authInterceptor]))
  ]
};

bootstrapApplication(AppComponent, appConfig);
```

### app.routes.ts
```typescript
export const routes: Routes = [
  {
    path: 'users',
    component: UserListComponent
  },
  {
    path: 'users/new',
    component: UserFormComponent
  },
  {
    path: 'users/:id',
    component: UserDetailComponent
  },
  {
    path: 'users/:id/edit',
    component: UserFormComponent
  }
];
```

### user.service.ts
```typescript
@Injectable({ providedIn: 'root' })
export class UserService {
  constructor(private http: HttpClient) {}

  getUsers(): Observable<User[]> {
    return this.http.get<User[]>('/api/users');
  }

  getUser(id: string): Observable<User> {
    return this.http.get<User>(`/api/users/${id}`);
  }

  createUser(user: Omit<User, 'id'>): Observable<User> {
    return this.http.post<User>('/api/users', user);
  }

  updateUser(id: string, user: Partial<User>): Observable<User> {
    return this.http.put<User>(`/api/users/${id}`, user);
  }

  deleteUser(id: string): Observable<void> {
    return this.http.delete<void>(`/api/users/${id}`);
  }
}
```

### user-list.component.ts
```typescript
@Component({
  selector: 'app-user-list',
  standalone: true,
  imports: [CommonModule, UserCardComponent, RouterLink],
  template: `
    <h1>Users</h1>
    <a routerLink="/users/new" class="btn">Add User</a>

    <div *ngIf="users$ | async as users" class="list">
      <app-user-card
        *ngFor="let user of users; trackBy: trackByUserId"
        [user]="user"
        (edit)="onEdit($event)"
        (delete)="onDelete($event)">
      </app-user-card>
    </div>
  `
})
export class UserListComponent {
  users$: Observable<User[]>;

  constructor(
    private userService: UserService,
    private router: Router
  ) {
    this.users$ = this.userService.getUsers();
  }

  onEdit(id: string) {
    this.router.navigate(['/users', id, 'edit']);
  }

  onDelete(id: string) {
    this.userService.deleteUser(id).subscribe(() => {
      this.users$ = this.userService.getUsers();
    });
  }

  trackByUserId(index: number, user: User): string {
    return user.id;
  }
}
```

---

## Checklist Standalone

- ✅ `standalone: true` no `@Component`
- ✅ `imports: [...]` com dependências
- ✅ `bootstrapApplication()` em `main.ts`
- ✅ `ApplicationConfig` com providers
- ✅ Routes com `loadComponent` para lazy loading
- ✅ Services com `providedIn: 'root'`
- ✅ Functional interceptors
- ✅ Functional guards
- ✅ Sem `NgModule` ✨

---

## Comparação: NgModule vs Standalone

| Aspecto | NgModule | Standalone |
|---------|----------|-----------|
| **Declaração** | `@NgModule()` | `standalone: true` |
| **Setup** | `app.module.ts` | `main.ts` |
| **Imports** | no módulo | no componente |
| **Lazy loading** | `loadChildren` | `loadComponent` |
| **Modularidade** | menor | maior |
| **Aprendizado** | complexo | simples |
| **Parecido com** | antigo | React |

---

## Migração para Standalone

### Opção 1: Novo Projeto
```bash
ng new my-app --skip-tests
cd my-app
# Já vem com standalone!
```

### Opção 2: Converter Projeto Existente
```typescript
// app.module.ts → main.ts
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app.component';

bootstrapApplication(AppComponent);
```

---

## Referências

- [Angular Standalone Documentation](https://angular.io/guide/standalone-components)
- [Bootstrap Application](https://angular.io/api/platform-browser/bootstrapApplication)
- [Functional Guards & Interceptors](https://angular.io/guide/http#intercepting-requests-and-responses)

