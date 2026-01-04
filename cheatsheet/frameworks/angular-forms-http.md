# ⭐ Angular: Reactive Forms & HTTP

Onde React devs mais sofrem. Um guia prático.

---

## Reactive Forms vs Template-Driven

### Template-Driven (Simples, evite)
```typescript
// ❌ Evite
@Component({
  template: `
    <form #f="ngForm" (ngSubmit)="submit(f)">
      <input name="email" [(ngModel)]="email" required email>
      <button type="submit">Send</button>
    </form>
  `
})
export class LoginComponent {
  email = '';

  submit(form: NgForm) {
    console.log(form.value);
  }
}
```

### Reactive Forms (Profissional, use)
```typescript
// ✅ Use
@Component({
  selector: 'app-login',
  standalone: true,
  imports: [ReactiveFormsModule],
  template: `
    <form [formGroup]="form" (ngSubmit)="submit()">
      <input formControlName="email" type="email" placeholder="Email">
      <div *ngIf="email?.invalid && email?.touched">
        <p *ngIf="email?.errors?.['required']">Obrigatório</p>
        <p *ngIf="email?.errors?.['email']">Email inválido</p>
      </div>

      <input formControlName="password" type="password" placeholder="Password">

      <button type="submit" [disabled]="form.invalid">Login</button>
    </form>
  `
})
export class LoginComponent {
  form = this.fb.group({
    email: ['', [Validators.required, Validators.email]],
    password: ['', Validators.required]
  });

  get email() {
    return this.form.get('email');
  }

  constructor(private fb: FormBuilder) {}

  submit() {
    if (this.form.valid) {
      console.log(this.form.value);
    }
  }
}
```

---

## Criar Formulário com FormBuilder

### Simples
```typescript
this.form = this.fb.group({
  email: [''],
  password: ['']
});
```

### Com Validadores
```typescript
this.form = this.fb.group({
  email: ['', [Validators.required, Validators.email]],
  password: ['', [Validators.required, Validators.minLength(6)]],
  rememberMe: [false]
});
```

### Com Valores Iniciais
```typescript
this.form = this.fb.group({
  email: ['joao@example.com'],
  password: [''],
  rememberMe: [true]
});
```

### FormGroup Aninhado
```typescript
this.form = this.fb.group({
  personalInfo: this.fb.group({
    name: [''],
    email: ['']
  }),
  address: this.fb.group({
    street: [''],
    city: [''],
    zipCode: ['']
  })
});
```

---

## Validadores Built-in

### Básicos
```typescript
import { Validators } from '@angular/forms';

// Text
Validators.required
Validators.minLength(3)
Validators.maxLength(50)
Validators.pattern(/^[a-z]+$/)

// Email
Validators.email

// Numbers
Validators.min(0)
Validators.max(100)

// Multiple
Validators.requiredTrue // checkbox

// Combinar
Validators.compose([
  Validators.required,
  Validators.email
])
```

### Usando no FormControl
```typescript
this.form = this.fb.group({
  email: ['', [Validators.required, Validators.email]],
  age: [null, [Validators.required, Validators.min(18)]],
  terms: [false, Validators.requiredTrue]
});
```

---

## Validadores Customizados

### Síncrono
```typescript
// Criar validador
function noSpacesValidator(control: AbstractControl): ValidationErrors | null {
  const value = control.value;
  if (!value) return null;

  const hasSpaces = value.includes(' ');
  return hasSpaces ? { noSpaces: { value } } : null;
}

// Usar
this.form = this.fb.group({
  username: ['', [Validators.required, noSpacesValidator]]
});

// Exibir erro
<p *ngIf="form.get('username')?.errors?.['noSpaces']">
  Username não pode ter espaços
</p>
```

### Assíncrono (Ex: email já existe?)
```typescript
function emailUniqueValidator(
  service: UserService
): AsyncValidatorFn {
  return (control: AbstractControl): Observable<ValidationErrors | null> => {
    if (!control.value) return of(null);

    return service.checkEmail(control.value).pipe(
      debounceTime(300),
      map(exists => (exists ? { emailTaken: true } : null)),
      catchError(() => of(null))
    );
  };
}

// Usar
this.form = this.fb.group({
  email: [
    '',
    [Validators.required, Validators.email],
    emailUniqueValidator(this.userService)
  ]
});

// Template
<div *ngIf="form.get('email')?.pending">Verificando...</div>
<p *ngIf="form.get('email')?.errors?.['emailTaken']">
  Email já existe
</p>
```

### Cross-field (comparar campos)
```typescript
function passwordMatchValidator(group: FormGroup): ValidationErrors | null {
  const password = group.get('password')?.value;
  const confirm = group.get('confirmPassword')?.value;

  return password === confirm ? null : { passwordsMismatch: true };
}

// Usar
this.form = this.fb.group({
  password: ['', Validators.required],
  confirmPassword: ['', Validators.required]
}, { validators: passwordMatchValidator });

// Template
<p *ngIf="form.errors?.['passwordsMismatch']">
  Senhas não coincidem
</p>
```

---

## Operações no Formulário

### Obter Valores
```typescript
// Valor atual
const data = this.form.value; // { email: 'x@x.com', password: '...' }

// Apenas campos válidos
const validData = this.form.getRawValue();

// Campo específico
const email = this.form.get('email')?.value;

// Array de valores
const passwords = (this.form.get('passwords') as FormArray).value;
```

### Preencher Formulário
```typescript
// Preencher alguns campos
this.form.patchValue({
  email: 'joao@example.com',
  rememberMe: true
});

// Preencher todos (requer todos os campos)
this.form.setValue({
  email: 'joao@example.com',
  password: '123456'
});
```

### Reset
```typescript
// Limpar
this.form.reset();

// Reset com valores
this.form.reset({
  email: '',
  password: ''
});
```

### Status do Formulário
```typescript
console.log(this.form.valid);      // boolean
console.log(this.form.invalid);    // boolean
console.log(this.form.pending);    // validators assíncronos rodando
console.log(this.form.dirty);      // foi modificado?
console.log(this.form.pristine);   // nunca foi modificado?
console.log(this.form.touched);    // campo foi visitado?
console.log(this.form.untouched);  // campo foi visitado?
```

---

## FormArray (Arrays Dinâmicos)

### Criar
```typescript
this.form = this.fb.group({
  name: [''],
  emails: this.fb.array([
    this.fb.control('')
  ])
});

get emails() {
  return this.form.get('emails') as FormArray;
}
```

### Adicionar / Remover
```typescript
addEmail() {
  this.emails.push(this.fb.control(''));
}

removeEmail(index: number) {
  this.emails.removeAt(index);
}
```

### Template
```typescript
<div formArrayName="emails">
  <div *ngFor="let email of emails.controls; let i = index">
    <input [formControlName]="i" type="email" placeholder="Email">
    <button type="button" (click)="removeEmail(i)">Remove</button>
  </div>
</div>
<button type="button" (click)="addEmail()">Add Email</button>
```

---

## Observar Mudanças

### Mudanças de Valor
```typescript
// Qualquer campo
this.form.valueChanges.subscribe(value => {
  console.log('Form changed:', value);
});

// Campo específico
this.form.get('email')?.valueChanges.subscribe(value => {
  console.log('Email changed:', value);
});
```

### Com Debounce (Search)
```typescript
this.form.get('search')?.valueChanges.pipe(
  debounceTime(300),
  distinctUntilChanged(),
  switchMap(term => this.api.search(term))
).subscribe(results => {
  // ...
});
```

### Status (Valid/Invalid)
```typescript
this.form.statusChanges.subscribe(status => {
  console.log('Form status:', status); // VALID, INVALID, PENDING
});
```

---

## HTTP + Interceptors

### Service Básico
```typescript
import { Injectable } from '@angular/core';
import { HttpClient, HttpParams } from '@angular/common/http';
import { Observable } from 'rxjs';

interface User {
  id: string;
  name: string;
  email: string;
}

@Injectable({ providedIn: 'root' })
export class UserService {
  private apiUrl = 'https://api.example.com/api';

  constructor(private http: HttpClient) {}

  // GET
  getUsers(): Observable<User[]> {
    return this.http.get<User[]>(`${this.apiUrl}/users`);
  }

  getUser(id: string): Observable<User> {
    return this.http.get<User>(`${this.apiUrl}/users/${id}`);
  }

  // GET com Query Params
  searchUsers(term: string, limit: number): Observable<User[]> {
    const params = new HttpParams()
      .set('q', term)
      .set('limit', limit.toString());

    return this.http.get<User[]>(`${this.apiUrl}/users`, { params });
  }

  // POST
  createUser(user: Omit<User, 'id'>): Observable<User> {
    return this.http.post<User>(`${this.apiUrl}/users`, user);
  }

  // PUT (replace)
  updateUser(id: string, user: User): Observable<User> {
    return this.http.put<User>(`${this.apiUrl}/users/${id}`, user);
  }

  // PATCH (partial)
  updateUserPartial(id: string, user: Partial<User>): Observable<User> {
    return this.http.patch<User>(`${this.apiUrl}/users/${id}`, user);
  }

  // DELETE
  deleteUser(id: string): Observable<void> {
    return this.http.delete<void>(`${this.apiUrl}/users/${id}`);
  }

  // Com Headers customizados
  getWithHeaders(): Observable<any> {
    const headers = {
      'Authorization': 'Bearer token123',
      'X-Custom-Header': 'valor'
    };

    return this.http.get('/api/data', { headers });
  }
}
```

### Tratamento de Erros
```typescript
import { catchError } from 'rxjs';

class ApiService {
  constructor(private http: HttpClient) {}

  getUser(id: string): Observable<User> {
    return this.http.get<User>(`/api/users/${id}`).pipe(
      catchError(error => {
        console.error('Erro ao buscar usuário:', error);

        if (error.status === 404) {
          return of(null); // Retornar null se não encontrado
        }

        if (error.status === 401) {
          // Fazer logout, redirecionar
        }

        throw error; // Re-throw outros erros
      })
    );
  }
}
```

### Retry
```typescript
import { retry } from 'rxjs';

getUser(id: string): Observable<User> {
  return this.http.get<User>(`/api/users/${id}`).pipe(
    retry({
      count: 3,
      delay: 1000
    })
  );
}
```

---

## Interceptors

### Adicionar Token (Auth)
```typescript
// auth.interceptor.ts
import { HttpInterceptorFn } from '@angular/common/http';
import { inject } from '@angular/core';

export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const authService = inject(AuthService);
  const token = authService.getToken();

  if (token) {
    req = req.clone({
      setHeaders: {
        Authorization: `Bearer ${token}`
      }
    });
  }

  return next(req);
};

// Registrar em main.ts
bootstrapApplication(AppComponent, {
  providers: [
    provideHttpClient(
      withInterceptors([authInterceptor])
    )
  ]
});
```

### Global Error Handler
```typescript
export const errorInterceptor: HttpInterceptorFn = (req, next) => {
  return next(req).pipe(
    catchError((error: HttpErrorResponse) => {
      console.error('HTTP Error:', error);

      if (error.status === 401) {
        // Não autorizado - fazer logout
      }

      if (error.status === 500) {
        // Servidor erro - mostrar notificação
      }

      return throwError(() => error);
    })
  );
};
```

### Loading Indicator
```typescript
export const loadingInterceptor: HttpInterceptorFn = (req, next) => {
  const loadingService = inject(LoadingService);

  loadingService.show();

  return next(req).pipe(
    finalize(() => loadingService.hide())
  );
};
```

---

## Exemplo Completo: Form + HTTP

### Service
```typescript
@Injectable({ providedIn: 'root' })
export class AuthService {
  constructor(private http: HttpClient) {}

  login(credentials: { email: string; password: string }): Observable<{ token: string }> {
    return this.http.post<{ token: string }>('/api/auth/login', credentials).pipe(
      tap(response => {
        localStorage.setItem('token', response.token);
      }),
      catchError(error => {
        console.error('Login failed:', error);
        return throwError(() => new Error('Email ou senha incorretos'));
      })
    );
  }
}
```

### Component
```typescript
@Component({
  selector: 'app-login',
  standalone: true,
  imports: [ReactiveFormsModule],
  template: `
    <form [formGroup]="form" (ngSubmit)="onSubmit()">
      <div>
        <label>Email:</label>
        <input formControlName="email" type="email">
        <div *ngIf="email?.invalid && email?.touched" class="error">
          <p *ngIf="email?.errors?.['required']">Obrigatório</p>
          <p *ngIf="email?.errors?.['email']">Email inválido</p>
        </div>
      </div>

      <div>
        <label>Senha:</label>
        <input formControlName="password" type="password">
        <div *ngIf="password?.invalid && password?.touched" class="error">
          <p *ngIf="password?.errors?.['required']">Obrigatório</p>
        </div>
      </div>

      <button type="submit" [disabled]="form.invalid || (isLoading$ | async)">
        {{ (isLoading$ | async) ? 'Entrando...' : 'Entrar' }}
      </button>

      <div *ngIf="error$ | async as error" class="error">
        {{ error }}
      </div>
    </form>
  `,
  styles: [`
    .error { color: red; }
  `]
})
export class LoginComponent {
  form = this.fb.group({
    email: ['', [Validators.required, Validators.email]],
    password: ['', [Validators.required, Validators.minLength(6)]]
  });

  isLoading$ = new BehaviorSubject(false);
  error$ = new BehaviorSubject<string | null>(null);

  get email() {
    return this.form.get('email');
  }

  get password() {
    return this.form.get('password');
  }

  constructor(
    private fb: FormBuilder,
    private authService: AuthService,
    private router: Router
  ) {}

  onSubmit() {
    if (this.form.invalid) return;

    this.isLoading$.next(true);
    this.error$.next(null);

    this.authService.login(this.form.value as any).subscribe({
      next: () => {
        this.router.navigate(['/dashboard']);
      },
      error: (err) => {
        this.error$.next(err.message);
        this.isLoading$.next(false);
      }
    });
  }
}
```

---

## Dicas Finais

1. **Sempre use Reactive Forms** para formulários profissionais
2. **Validadores síncronos** para lógica simples
3. **Validadores assíncronos** para verificar no servidor
4. **Use `catchError`** para tratar todos os erros HTTP
5. **Interceptors** para lógica comum (auth, errors)
6. **`async` pipe** ao invés de `.subscribe()`
7. **`FormBuilder`** para criar forms legíveis

---

## Checklist

- ✅ FormBuilder para criar form
- ✅ Validadores incorporados + customizados
- ✅ valueChanges para reatividade
- ✅ HTTP service com CRUD
- ✅ Error handling com catchError
- ✅ Retry para requisições
- ✅ Interceptors para auth
- ✅ Async pipe no template

