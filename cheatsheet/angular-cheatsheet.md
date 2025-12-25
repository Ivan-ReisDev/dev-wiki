# Angular Cheatsheet

## Instalação e Setup

### Instalar Angular CLI

```bash
# Instalar globalmente
npm install -g @angular/cli

# Verificar versão
ng version

# Atualizar Angular CLI
npm update -g @angular/cli
```

### Criar Projeto

```bash
# Criar novo projeto
ng new meu-projeto

# Com opções
ng new meu-projeto --routing --style=scss --strict

# Sem testes
ng new meu-projeto --skip-tests

# Entrar no diretório
cd meu-projeto

# Iniciar servidor de desenvolvimento
ng serve

# Servidor em porta específica
ng serve --port 4300

# Abrir automaticamente no navegador
ng serve --open
```

## Angular CLI - Comandos

### Generate (Criar)

```bash
# Componente
ng generate component nome-componente
ng g c nome-componente

# Componente sem arquivo de teste
ng g c nome-componente --skip-tests

# Componente inline (sem arquivo separado)
ng g c nome-componente --inline-template --inline-style

# Service
ng generate service services/nome-service
ng g s services/nome-service

# Module
ng generate module nome-module
ng g m nome-module

# Module com routing
ng g m nome-module --routing

# Directive
ng generate directive directives/nome-directive
ng g d directives/nome-directive

# Pipe
ng generate pipe pipes/nome-pipe
ng g pipe pipes/nome-pipe

# Guard
ng generate guard guards/auth
ng g g guards/auth

# Interface
ng generate interface models/usuario
ng g i models/usuario

# Class
ng generate class models/produto
ng g class models/produto

# Enum
ng generate enum enums/status
ng g enum enums/status
```

### Build e Deploy

```bash
# Build para desenvolvimento
ng build

# Build para produção
ng build --prod
ng build --configuration production

# Build com configuração específica
ng build --configuration staging

# Analisar bundle size
ng build --stats-json
```

### Testes

```bash
# Rodar testes unitários
ng test

# Rodar testes uma vez (CI)
ng test --watch=false --browsers=ChromeHeadless

# Testes e2e
ng e2e

# Code coverage
ng test --code-coverage
```

### Outros Comandos

```bash
# Adicionar biblioteca
ng add @angular/material

# Atualizar dependências
ng update

# Verificar o que pode ser atualizado
ng update --all

# Lint
ng lint

# Criar library
ng generate library minha-lib
```

## Componentes

### Criar Componente

```typescript
// usuario.component.ts
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-usuario',
  templateUrl: './usuario.component.html',
  styleUrls: ['./usuario.component.scss']
})
export class UsuarioComponent implements OnInit {
  // Propriedades
  nome: string = 'João';
  idade: number = 25;
  ativo: boolean = true;

  // Array
  usuarios: string[] = ['Maria', 'Pedro', 'Ana'];

  // Objeto
  usuario = {
    nome: 'João',
    email: 'joao@email.com',
    idade: 25
  };

  constructor() {
    // Inicialização no construtor
  }

  ngOnInit(): void {
    // Inicialização após o componente ser criado
    console.log('Componente inicializado');
  }

  // Métodos
  saudar(): void {
    console.log(`Olá, ${this.nome}!`);
  }

  incrementarIdade(): void {
    this.idade++;
  }
}
```

```html
<!-- usuario.component.html -->
<div class="usuario">
  <h1>{{ nome }}</h1>
  <p>Idade: {{ idade }}</p>
  <button (click)="incrementarIdade()">Aumentar Idade</button>
</div>
```

### Componente Standalone (Angular 14+)

```typescript
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';

@Component({
  selector: 'app-meu-componente',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h1>Componente Standalone</h1>
  `,
  styles: [`
    h1 { color: blue; }
  `]
})
export class MeuComponenteComponent {
  // Lógica do componente
}
```

## Data Binding

### Interpolação {{ }}

```html
<!-- String interpolation -->
<h1>{{ titulo }}</h1>
<p>{{ nome + ' ' + sobrenome }}</p>
<p>{{ 2 + 2 }}</p>
<p>{{ getNome() }}</p>
<p>{{ usuario.nome }}</p>
```

### Property Binding []

```typescript
// component.ts
imagemUrl: string = 'https://exemplo.com/imagem.jpg';
desabilitado: boolean = true;
corFundo: string = 'lightblue';
```

```html
<!-- Property binding -->
<img [src]="imagemUrl" alt="Imagem">
<button [disabled]="desabilitado">Clique</button>
<div [style.background-color]="corFundo">Conteúdo</div>
<div [class.ativo]="isAtivo">Elemento</div>
<input [value]="nome" type="text">

<!-- Binding de atributos -->
<div [attr.data-id]="usuario.id">Conteúdo</div>
```

### Event Binding ()

```typescript
// component.ts
handleClick(): void {
  console.log('Clicou!');
}

handleInput(event: Event): void {
  const valor = (event.target as HTMLInputElement).value;
  console.log(valor);
}

handleKeyPress(event: KeyboardEvent): void {
  console.log(event.key);
}
```

```html
<!-- Event binding -->
<button (click)="handleClick()">Clique</button>
<button (click)="contador = contador + 1">Incrementar</button>

<input (input)="handleInput($event)" type="text">
<input (keypress)="handleKeyPress($event)" type="text">

<!-- Outros eventos -->
<div (mouseenter)="onMouseEnter()" (mouseleave)="onMouseLeave()">
  Passe o mouse aqui
</div>

<form (submit)="onSubmit($event)">
  <button type="submit">Enviar</button>
</form>
```

### Two-Way Binding [(ngModel)]

```typescript
// Importar FormsModule no module
import { FormsModule } from '@angular/forms';

@NgModule({
  imports: [FormsModule]
})
```

```typescript
// component.ts
nome: string = '';
email: string = '';
```

```html
<!-- Two-way binding -->
<input [(ngModel)]="nome" type="text">
<p>Nome: {{ nome }}</p>

<input [(ngModel)]="email" type="email">
<textarea [(ngModel)]="mensagem"></textarea>
<select [(ngModel)]="paisSelecionado">
  <option value="br">Brasil</option>
  <option value="pt">Portugal</option>
</select>
```

## Diretivas Estruturais

### *ngIf - Condicional

```html
<!-- ngIf básico -->
<div *ngIf="isLogado">
  <p>Bem-vindo!</p>
</div>

<!-- ngIf com else -->
<div *ngIf="isLogado; else naoLogado">
  <p>Usuário logado</p>
</div>
<ng-template #naoLogado>
  <p>Faça login</p>
</ng-template>

<!-- ngIf com then e else -->
<div *ngIf="isLogado; then logado; else naoLogado"></div>
<ng-template #logado>
  <p>Logado</p>
</ng-template>
<ng-template #naoLogado>
  <p>Não logado</p>
</ng-template>

<!-- ngIf com variável -->
<div *ngIf="usuario$ | async as usuario">
  <p>{{ usuario.nome }}</p>
</div>

<!-- Múltiplas condições -->
<div *ngIf="isLogado && isAdmin">
  <p>Admin Panel</p>
</div>
```

### *ngFor - Loop

```typescript
// component.ts
usuarios = [
  { id: 1, nome: 'João', idade: 25 },
  { id: 2, nome: 'Maria', idade: 30 },
  { id: 3, nome: 'Pedro', idade: 35 }
];

numeros = [1, 2, 3, 4, 5];
```

```html
<!-- ngFor básico -->
<ul>
  <li *ngFor="let usuario of usuarios">
    {{ usuario.nome }} - {{ usuario.idade }} anos
  </li>
</ul>

<!-- ngFor com index -->
<div *ngFor="let item of items; let i = index">
  {{ i + 1 }}. {{ item }}
</div>

<!-- ngFor com trackBy (performance) -->
<div *ngFor="let usuario of usuarios; trackBy: trackByUsuarioId">
  {{ usuario.nome }}
</div>
```

```typescript
// trackBy method
trackByUsuarioId(index: number, usuario: any): number {
  return usuario.id;
}
```

```html
<!-- ngFor com variáveis especiais -->
<div *ngFor="let item of items;
             let i = index;
             let primeiro = first;
             let ultimo = last;
             let par = even;
             let impar = odd">
  <p>Índice: {{ i }}</p>
  <p *ngIf="primeiro">Primeiro item</p>
  <p *ngIf="ultimo">Último item</p>
  <p [class.par]="par">{{ item }}</p>
</div>
```

### *ngSwitch

```typescript
// component.ts
role: string = 'admin';
```

```html
<!-- ngSwitch -->
<div [ngSwitch]="role">
  <p *ngSwitchCase="'admin'">Admin Panel</p>
  <p *ngSwitchCase="'user'">User Panel</p>
  <p *ngSwitchCase="'guest'">Guest Panel</p>
  <p *ngSwitchDefault>Sem permissões</p>
</div>
```

## Diretivas de Atributo

### ngClass

```html
<!-- ngClass com objeto -->
<div [ngClass]="{
  'ativo': isAtivo,
  'destaque': isDestaque,
  'erro': temErro
}">
  Conteúdo
</div>

<!-- ngClass com array -->
<div [ngClass]="['classe1', 'classe2', condicao ? 'classe3' : '']">
  Conteúdo
</div>

<!-- ngClass com string -->
<div [ngClass]="classesDinamicas">Conteúdo</div>
```

### ngStyle

```html
<!-- ngStyle com objeto -->
<div [ngStyle]="{
  'background-color': corFundo,
  'font-size': tamanhoFonte + 'px',
  'color': isAtivo ? 'green' : 'red'
}">
  Conteúdo
</div>

<!-- Propriedades CSS individuais -->
<div [style.background-color]="corFundo">Conteúdo</div>
<div [style.font-size.px]="tamanhoFonte">Conteúdo</div>
<div [style.width.%]="largura">Conteúdo</div>
```

## Input e Output (Comunicação entre Componentes)

### @Input - Passar dados para filho

```typescript
// filho.component.ts
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-filho',
  template: `
    <div>
      <h2>{{ titulo }}</h2>
      <p>{{ usuario.nome }}</p>
    </div>
  `
})
export class FilhoComponent {
  @Input() titulo: string = '';
  @Input() usuario: any;
  @Input() contador: number = 0;

  // Input com alias
  @Input('nomeExterno') nomeInterno: string = '';

  // Input com setter
  @Input()
  set nome(valor: string) {
    this._nome = valor.toUpperCase();
  }
  get nome(): string {
    return this._nome;
  }
  private _nome: string = '';
}
```

```html
<!-- pai.component.html -->
<app-filho
  [titulo]="'Meu Título'"
  [usuario]="usuarioObj"
  [contador]="10"
  nomeExterno="João">
</app-filho>
```

### @Output - Emitir eventos para pai

```typescript
// filho.component.ts
import { Component, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-filho',
  template: `
    <button (click)="notificar()">Notificar Pai</button>
    <button (click)="enviarDados()">Enviar Dados</button>
  `
})
export class FilhoComponent {
  @Output() mensagem = new EventEmitter<string>();
  @Output() dadosEnviados = new EventEmitter<any>();

  notificar(): void {
    this.mensagem.emit('Olá do filho!');
  }

  enviarDados(): void {
    this.dadosEnviados.emit({
      nome: 'João',
      idade: 25
    });
  }
}
```

```typescript
// pai.component.ts
@Component({
  template: `
    <app-filho
      (mensagem)="receberMensagem($event)"
      (dadosEnviados)="receberDados($event)">
    </app-filho>
  `
})
export class PaiComponent {
  receberMensagem(msg: string): void {
    console.log('Mensagem recebida:', msg);
  }

  receberDados(dados: any): void {
    console.log('Dados recebidos:', dados);
  }
}
```

## Services e Dependency Injection

### Criar Service

```typescript
// usuario.service.ts
import { Injectable } from '@angular/core';
import { BehaviorSubject, Observable } from 'rxjs';

@Injectable({
  providedIn: 'root' // Disponível globalmente
})
export class UsuarioService {
  private usuarios: any[] = [
    { id: 1, nome: 'João' },
    { id: 2, nome: 'Maria' }
  ];

  // BehaviorSubject para estado compartilhado
  private usuariosSubject = new BehaviorSubject<any[]>(this.usuarios);
  public usuarios$ = this.usuariosSubject.asObservable();

  constructor() { }

  // Métodos
  getUsuarios(): any[] {
    return this.usuarios;
  }

  getUsuario(id: number): any {
    return this.usuarios.find(u => u.id === id);
  }

  adicionarUsuario(usuario: any): void {
    this.usuarios.push(usuario);
    this.usuariosSubject.next(this.usuarios);
  }

  removerUsuario(id: number): void {
    this.usuarios = this.usuarios.filter(u => u.id !== id);
    this.usuariosSubject.next(this.usuarios);
  }

  atualizarUsuario(id: number, dados: any): void {
    const index = this.usuarios.findIndex(u => u.id === id);
    if (index !== -1) {
      this.usuarios[index] = { ...this.usuarios[index], ...dados };
      this.usuariosSubject.next(this.usuarios);
    }
  }
}
```

### Injetar Service no Componente

```typescript
// component.ts
import { Component, OnInit } from '@angular/core';
import { UsuarioService } from './services/usuario.service';

@Component({
  selector: 'app-lista-usuarios',
  templateUrl: './lista-usuarios.component.html'
})
export class ListaUsuariosComponent implements OnInit {
  usuarios: any[] = [];

  // Injetar service no construtor
  constructor(private usuarioService: UsuarioService) { }

  ngOnInit(): void {
    // Usar o service
    this.usuarios = this.usuarioService.getUsuarios();

    // Subscrever ao Observable
    this.usuarioService.usuarios$.subscribe(usuarios => {
      this.usuarios = usuarios;
    });
  }

  adicionar(): void {
    this.usuarioService.adicionarUsuario({ id: 3, nome: 'Pedro' });
  }
}
```

### Service com HttpClient

```typescript
// api.service.ts
import { Injectable } from '@angular/core';
import { HttpClient, HttpHeaders, HttpParams } from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError, map, retry } from 'rxjs/operators';

@Injectable({
  providedIn: 'root'
})
export class ApiService {
  private apiUrl = 'https://api.exemplo.com';

  constructor(private http: HttpClient) { }

  // GET
  getUsuarios(): Observable<any[]> {
    return this.http.get<any[]>(`${this.apiUrl}/usuarios`).pipe(
      retry(3),
      catchError(this.handleError)
    );
  }

  // GET com parâmetros
  getUsuario(id: number): Observable<any> {
    return this.http.get<any>(`${this.apiUrl}/usuarios/${id}`);
  }

  // GET com query params
  buscarUsuarios(filtro: string): Observable<any[]> {
    const params = new HttpParams()
      .set('q', filtro)
      .set('limit', '10');

    return this.http.get<any[]>(`${this.apiUrl}/usuarios`, { params });
  }

  // POST
  criarUsuario(usuario: any): Observable<any> {
    return this.http.post<any>(`${this.apiUrl}/usuarios`, usuario).pipe(
      catchError(this.handleError)
    );
  }

  // PUT
  atualizarUsuario(id: number, usuario: any): Observable<any> {
    return this.http.put<any>(`${this.apiUrl}/usuarios/${id}`, usuario);
  }

  // PATCH
  atualizarParcial(id: number, dados: any): Observable<any> {
    return this.http.patch<any>(`${this.apiUrl}/usuarios/${id}`, dados);
  }

  // DELETE
  deletarUsuario(id: number): Observable<any> {
    return this.http.delete<any>(`${this.apiUrl}/usuarios/${id}`);
  }

  // Com Headers customizados
  getComHeaders(): Observable<any> {
    const headers = new HttpHeaders({
      'Content-Type': 'application/json',
      'Authorization': 'Bearer token123'
    });

    return this.http.get<any>(`${this.apiUrl}/data`, { headers });
  }

  // Tratamento de erro
  private handleError(error: any): Observable<never> {
    console.error('Erro:', error);
    return throwError(() => new Error('Erro ao processar requisição'));
  }
}
```

### HTTP Interceptor

```typescript
// auth.interceptor.ts
import { Injectable } from '@angular/core';
import {
  HttpInterceptor,
  HttpRequest,
  HttpHandler,
  HttpEvent
} from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    // Adicionar token de autenticação
    const token = localStorage.getItem('token');

    if (token) {
      const clonedReq = req.clone({
        headers: req.headers.set('Authorization', `Bearer ${token}`)
      });
      return next.handle(clonedReq);
    }

    return next.handle(req);
  }
}
```

```typescript
// Registrar no app.module.ts
import { HTTP_INTERCEPTORS } from '@angular/common/http';

@NgModule({
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: AuthInterceptor,
      multi: true
    }
  ]
})
export class AppModule { }
```

## Routing (Rotas)

### Configurar Rotas

```typescript
// app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { HomeComponent } from './home/home.component';
import { SobreComponent } from './sobre/sobre.component';
import { UsuariosComponent } from './usuarios/usuarios.component';
import { UsuarioDetalheComponent } from './usuario-detalhe/usuario-detalhe.component';
import { NaoEncontradoComponent } from './nao-encontrado/nao-encontrado.component';
import { AuthGuard } from './guards/auth.guard';

const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'sobre', component: SobreComponent },
  { path: 'usuarios', component: UsuariosComponent },

  // Rota com parâmetro
  { path: 'usuarios/:id', component: UsuarioDetalheComponent },

  // Rota com guard
  {
    path: 'admin',
    component: AdminComponent,
    canActivate: [AuthGuard]
  },

  // Lazy loading
  {
    path: 'produtos',
    loadChildren: () => import('./produtos/produtos.module')
      .then(m => m.ProdutosModule)
  },

  // Rotas filhas
  {
    path: 'dashboard',
    component: DashboardComponent,
    children: [
      { path: 'perfil', component: PerfilComponent },
      { path: 'configuracoes', component: ConfiguracoesComponent }
    ]
  },

  // Redirect
  { path: 'home', redirectTo: '', pathMatch: 'full' },

  // 404
  { path: '**', component: NaoEncontradoComponent }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

### Navegação

```html
<!-- app.component.html -->
<nav>
  <!-- RouterLink -->
  <a routerLink="/">Home</a>
  <a routerLink="/sobre">Sobre</a>
  <a routerLink="/usuarios">Usuários</a>

  <!-- RouterLink com parâmetros -->
  <a [routerLink]="['/usuarios', usuario.id]">Ver Usuário</a>

  <!-- RouterLink com query params -->
  <a [routerLink]="['/produtos']" [queryParams]="{page: 1, sort: 'nome'}">
    Produtos
  </a>

  <!-- RouterLinkActive (classe ativa) -->
  <a routerLink="/home" routerLinkActive="active">Home</a>

  <!-- RouterLinkActive exato -->
  <a routerLink="/" routerLinkActive="active" [routerLinkActiveOptions]="{exact: true}">
    Home
  </a>
</nav>

<!-- Outlet para renderizar componente da rota -->
<router-outlet></router-outlet>
```

### Navegação Programática

```typescript
// component.ts
import { Router, ActivatedRoute } from '@angular/router';

@Component({...})
export class MeuComponent {
  constructor(
    private router: Router,
    private route: ActivatedRoute
  ) { }

  // Navegar para rota
  irParaSobre(): void {
    this.router.navigate(['/sobre']);
  }

  // Navegar com parâmetros
  verUsuario(id: number): void {
    this.router.navigate(['/usuarios', id]);
  }

  // Navegar com query params
  buscar(): void {
    this.router.navigate(['/produtos'], {
      queryParams: { q: 'termo', page: 1 }
    });
  }

  // Navegar relativamente
  irParaPerfil(): void {
    this.router.navigate(['perfil'], { relativeTo: this.route });
  }

  // Voltar
  voltar(): void {
    this.router.navigate(['../'], { relativeTo: this.route });
  }

  // Obter parâmetros da rota
  ngOnInit(): void {
    // Snapshot (valor atual)
    const id = this.route.snapshot.params['id'];
    const query = this.route.snapshot.queryParams['q'];

    // Observable (atualiza quando params mudam)
    this.route.params.subscribe(params => {
      const id = params['id'];
      console.log('ID:', id);
    });

    this.route.queryParams.subscribe(params => {
      const page = params['page'];
      console.log('Page:', page);
    });
  }
}
```

### Route Guard

```typescript
// auth.guard.ts
import { Injectable } from '@angular/core';
import {
  CanActivate,
  ActivatedRouteSnapshot,
  RouterStateSnapshot,
  Router,
  UrlTree
} from '@angular/router';
import { Observable } from 'rxjs';
import { AuthService } from '../services/auth.service';

@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate {
  constructor(
    private authService: AuthService,
    private router: Router
  ) { }

  canActivate(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot
  ): Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree {

    if (this.authService.isLogado()) {
      return true;
    }

    // Redirecionar para login
    return this.router.createUrlTree(['/login'], {
      queryParams: { returnUrl: state.url }
    });
  }
}

// CanDeactivate Guard (confirmar saída)
export interface CanComponentDeactivate {
  canDeactivate: () => Observable<boolean> | Promise<boolean> | boolean;
}

@Injectable({
  providedIn: 'root'
})
export class CanDeactivateGuard implements CanDeactivate<CanComponentDeactivate> {
  canDeactivate(
    component: CanComponentDeactivate
  ): Observable<boolean> | Promise<boolean> | boolean {
    return component.canDeactivate ? component.canDeactivate() : true;
  }
}
```

## Forms

### Template-Driven Forms

```typescript
// Importar FormsModule no module
import { FormsModule } from '@angular/forms';

@NgModule({
  imports: [FormsModule]
})
```

```typescript
// component.ts
export class FormularioComponent {
  usuario = {
    nome: '',
    email: '',
    senha: '',
    genero: '',
    pais: 'br',
    aceito: false
  };

  onSubmit(form: any): void {
    if (form.valid) {
      console.log('Dados:', this.usuario);
    }
  }
}
```

```html
<!-- template-driven form -->
<form #f="ngForm" (ngSubmit)="onSubmit(f)">
  <!-- Input text -->
  <input
    type="text"
    name="nome"
    [(ngModel)]="usuario.nome"
    #nome="ngModel"
    required
    minlength="3">

  <div *ngIf="nome.invalid && nome.touched">
    <p *ngIf="nome.errors?.['required']">Nome é obrigatório</p>
    <p *ngIf="nome.errors?.['minlength']">Mínimo 3 caracteres</p>
  </div>

  <!-- Email -->
  <input
    type="email"
    name="email"
    [(ngModel)]="usuario.email"
    #email="ngModel"
    required
    email>

  <div *ngIf="email.invalid && email.touched">
    <p *ngIf="email.errors?.['required']">Email é obrigatório</p>
    <p *ngIf="email.errors?.['email']">Email inválido</p>
  </div>

  <!-- Select -->
  <select name="pais" [(ngModel)]="usuario.pais">
    <option value="br">Brasil</option>
    <option value="pt">Portugal</option>
    <option value="us">EUA</option>
  </select>

  <!-- Radio -->
  <label>
    <input type="radio" name="genero" value="m" [(ngModel)]="usuario.genero">
    Masculino
  </label>
  <label>
    <input type="radio" name="genero" value="f" [(ngModel)]="usuario.genero">
    Feminino
  </label>

  <!-- Checkbox -->
  <label>
    <input type="checkbox" name="aceito" [(ngModel)]="usuario.aceito">
    Aceito os termos
  </label>

  <button type="submit" [disabled]="f.invalid">Enviar</button>
</form>

<p>Form válido: {{ f.valid }}</p>
<p>Form inválido: {{ f.invalid }}</p>
<p>Form touched: {{ f.touched }}</p>
```

### Reactive Forms

```typescript
// Importar ReactiveFormsModule no module
import { ReactiveFormsModule } from '@angular/forms';

@NgModule({
  imports: [ReactiveFormsModule]
})
```

```typescript
// component.ts
import { Component, OnInit } from '@angular/core';
import { FormBuilder, FormGroup, FormControl, Validators } from '@angular/forms';

@Component({...})
export class FormularioComponent implements OnInit {
  formulario!: FormGroup;

  constructor(private fb: FormBuilder) { }

  ngOnInit(): void {
    // Criar formulário com FormBuilder
    this.formulario = this.fb.group({
      nome: ['', [Validators.required, Validators.minLength(3)]],
      email: ['', [Validators.required, Validators.email]],
      senha: ['', [Validators.required, Validators.minLength(6)]],
      confirmarSenha: ['', Validators.required],
      telefone: [''],
      endereco: this.fb.group({
        rua: [''],
        cidade: [''],
        estado: ['']
      })
    }, {
      validators: this.senhasIguais // Validador customizado
    });

    // Observar mudanças
    this.formulario.get('nome')?.valueChanges.subscribe(valor => {
      console.log('Nome mudou:', valor);
    });

    // Observar status
    this.formulario.statusChanges.subscribe(status => {
      console.log('Status:', status); // VALID, INVALID, PENDING
    });
  }

  // Validador customizado
  senhasIguais(group: FormGroup): { [key: string]: boolean } | null {
    const senha = group.get('senha')?.value;
    const confirmar = group.get('confirmarSenha')?.value;

    return senha === confirmar ? null : { senhasDiferentes: true };
  }

  // Getters para facilitar acesso no template
  get nome() { return this.formulario.get('nome'); }
  get email() { return this.formulario.get('email'); }
  get senha() { return this.formulario.get('senha'); }

  onSubmit(): void {
    if (this.formulario.valid) {
      console.log('Dados:', this.formulario.value);

      // Reset do formulário
      this.formulario.reset();
    } else {
      // Marcar todos como touched para mostrar erros
      this.formulario.markAllAsTouched();
    }
  }

  // Preencher formulário
  preencherDados(): void {
    this.formulario.patchValue({
      nome: 'João',
      email: 'joao@email.com'
    });

    // ou setValue (requer todos os campos)
    this.formulario.setValue({
      nome: 'João',
      email: 'joao@email.com',
      senha: '123456',
      confirmarSenha: '123456',
      telefone: '',
      endereco: { rua: '', cidade: '', estado: '' }
    });
  }
}
```

```html
<!-- reactive form -->
<form [formGroup]="formulario" (ngSubmit)="onSubmit()">
  <!-- Input text -->
  <input type="text" formControlName="nome">

  <div *ngIf="nome?.invalid && nome?.touched">
    <p *ngIf="nome?.errors?.['required']">Nome é obrigatório</p>
    <p *ngIf="nome?.errors?.['minlength']">
      Mínimo {{ nome?.errors?.['minlength'].requiredLength }} caracteres
    </p>
  </div>

  <!-- Email -->
  <input type="email" formControlName="email">

  <div *ngIf="email?.invalid && email?.touched">
    <p *ngIf="email?.errors?.['required']">Email é obrigatório</p>
    <p *ngIf="email?.errors?.['email']">Email inválido</p>
  </div>

  <!-- Senha -->
  <input type="password" formControlName="senha">
  <input type="password" formControlName="confirmarSenha">

  <div *ngIf="formulario.errors?.['senhasDiferentes'] && formulario.touched">
    <p>As senhas não coincidem</p>
  </div>

  <!-- FormGroup aninhado -->
  <div formGroupName="endereco">
    <input type="text" formControlName="rua" placeholder="Rua">
    <input type="text" formControlName="cidade" placeholder="Cidade">
    <input type="text" formControlName="estado" placeholder="Estado">
  </div>

  <button type="submit" [disabled]="formulario.invalid">Enviar</button>
</form>

<p>Formulário válido: {{ formulario.valid }}</p>
<p>Valor: {{ formulario.value | json }}</p>
```

### FormArray (Arrays Dinâmicos)

```typescript
import { FormArray, FormBuilder, FormGroup } from '@angular/forms';

export class FormularioComponent {
  formulario: FormGroup;

  constructor(private fb: FormBuilder) {
    this.formulario = this.fb.group({
      nome: [''],
      telefones: this.fb.array([])
    });
  }

  get telefones(): FormArray {
    return this.formulario.get('telefones') as FormArray;
  }

  adicionarTelefone(): void {
    this.telefones.push(this.fb.control(''));
  }

  removerTelefone(index: number): void {
    this.telefones.removeAt(index);
  }
}
```

```html
<form [formGroup]="formulario">
  <input type="text" formControlName="nome">

  <div formArrayName="telefones">
    <div *ngFor="let telefone of telefones.controls; let i = index">
      <input type="tel" [formControlName]="i">
      <button type="button" (click)="removerTelefone(i)">Remover</button>
    </div>
  </div>

  <button type="button" (click)="adicionarTelefone()">Adicionar Telefone</button>
</form>
```

## Pipes

### Pipes Built-in

```html
<!-- DatePipe -->
<p>{{ hoje | date }}</p>
<p>{{ hoje | date:'dd/MM/yyyy' }}</p>
<p>{{ hoje | date:'short' }}</p>
<p>{{ hoje | date:'fullDate' }}</p>

<!-- UpperCase / LowerCase -->
<p>{{ nome | uppercase }}</p>
<p>{{ nome | lowercase }}</p>

<!-- TitleCase -->
<p>{{ 'olá mundo' | titlecase }}</p> <!-- Olá Mundo -->

<!-- CurrencyPipe -->
<p>{{ preco | currency }}</p> <!-- $1,234.56 -->
<p>{{ preco | currency:'BRL' }}</p> <!-- R$ 1.234,56 -->
<p>{{ preco | currency:'EUR':'symbol':'1.2-2' }}</p>

<!-- DecimalPipe -->
<p>{{ numero | number }}</p>
<p>{{ numero | number:'1.2-3' }}</p> <!-- min.min-max decimals -->

<!-- PercentPipe -->
<p>{{ 0.5 | percent }}</p> <!-- 50% -->

<!-- JsonPipe (debug) -->
<pre>{{ objeto | json }}</pre>

<!-- SlicePipe -->
<p>{{ texto | slice:0:10 }}</p> <!-- Primeiros 10 caracteres -->
<ul>
  <li *ngFor="let item of items | slice:0:5">{{ item }}</li>
</ul>

<!-- AsyncPipe (Observables) -->
<p>{{ usuario$ | async | json }}</p>

<!-- Encadear pipes -->
<p>{{ hoje | date:'fullDate' | uppercase }}</p>
<p>{{ preco | currency:'BRL' | uppercase }}</p>
```

### Pipe Customizado

```typescript
// filtro.pipe.ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'filtro'
})
export class FiltroPipe implements PipeTransform {
  transform(items: any[], termo: string, campo: string): any[] {
    if (!items || !termo) {
      return items;
    }

    termo = termo.toLowerCase();

    return items.filter(item => {
      return item[campo].toLowerCase().includes(termo);
    });
  }
}
```

```html
<!-- Usar pipe customizado -->
<input [(ngModel)]="termoBusca" placeholder="Buscar">

<ul>
  <li *ngFor="let usuario of usuarios | filtro:termoBusca:'nome'">
    {{ usuario.nome }}
  </li>
</ul>
```

## Lifecycle Hooks

```typescript
import {
  Component,
  OnInit,
  OnChanges,
  DoCheck,
  AfterContentInit,
  AfterContentChecked,
  AfterViewInit,
  AfterViewChecked,
  OnDestroy,
  SimpleChanges
} from '@angular/core';

export class MeuComponent implements OnInit, OnChanges, OnDestroy {

  // 1. Executado quando @Input() muda
  ngOnChanges(changes: SimpleChanges): void {
    console.log('ngOnChanges', changes);
  }

  // 2. Executado após a criação do componente
  ngOnInit(): void {
    console.log('ngOnInit - Inicializar aqui');
  }

  // 3. Executado a cada detecção de mudanças
  ngDoCheck(): void {
    console.log('ngDoCheck');
  }

  // 4. Executado após conteúdo projetado ser inicializado
  ngAfterContentInit(): void {
    console.log('ngAfterContentInit');
  }

  // 5. Executado após cada verificação do conteúdo
  ngAfterContentChecked(): void {
    console.log('ngAfterContentChecked');
  }

  // 6. Executado após view ser inicializada
  ngAfterViewInit(): void {
    console.log('ngAfterViewInit - Acessar @ViewChild aqui');
  }

  // 7. Executado após cada verificação da view
  ngAfterViewChecked(): void {
    console.log('ngAfterViewChecked');
  }

  // 8. Executado antes do componente ser destruído
  ngOnDestroy(): void {
    console.log('ngOnDestroy - Cleanup aqui');
    // Cancelar subscriptions
    // Limpar timers
  }
}
```

## Decorators

### @ViewChild e @ViewChildren

```typescript
import { Component, ViewChild, ViewChildren, QueryList, ElementRef, AfterViewInit } from '@angular/core';

@Component({
  template: `
    <input #inputRef type="text">
    <app-filho #filho></app-filho>
    <app-item *ngFor="let item of items"></app-item>
  `
})
export class MeuComponent implements AfterViewInit {
  // Referenciar elemento do template
  @ViewChild('inputRef') inputElement!: ElementRef;

  // Referenciar componente filho
  @ViewChild('filho') filhoComponent!: FilhoComponent;

  // Referenciar múltiplos componentes
  @ViewChildren(ItemComponent) itemComponents!: QueryList<ItemComponent>;

  ngAfterViewInit(): void {
    // Acessar elemento nativo
    this.inputElement.nativeElement.focus();

    // Acessar propriedade/método do filho
    console.log(this.filhoComponent.algumaPropriedade);

    // Iterar sobre múltiplos componentes
    this.itemComponents.forEach(item => {
      console.log(item);
    });

    // Observar mudanças
    this.itemComponents.changes.subscribe(items => {
      console.log('Items mudaram', items);
    });
  }
}
```

### @ContentChild e @ContentChildren

```typescript
import { Component, ContentChild, ContentChildren, QueryList, AfterContentInit } from '@angular/core';

@Component({
  selector: 'app-container',
  template: `
    <div class="container">
      <ng-content></ng-content>
    </div>
  `
})
export class ContainerComponent implements AfterContentInit {
  // Referenciar conteúdo projetado
  @ContentChild('header') header!: ElementRef;
  @ContentChildren(ItemComponent) items!: QueryList<ItemComponent>;

  ngAfterContentInit(): void {
    console.log('Header:', this.header);
    console.log('Items:', this.items);
  }
}
```

```html
<!-- Usar -->
<app-container>
  <div #header>Header Content</div>
  <app-item></app-item>
  <app-item></app-item>
</app-container>
```

### @HostListener e @HostBinding

```typescript
import { Component, HostListener, HostBinding } from '@angular/core';

@Component({
  selector: 'app-meu-componente',
  template: `<div>Componente</div>`
})
export class MeuComponent {
  // Bind propriedade do host
  @HostBinding('class.active') isActive = false;
  @HostBinding('style.backgroundColor') bgColor = 'white';

  // Escutar eventos do host
  @HostListener('click')
  onClick(): void {
    this.isActive = !this.isActive;
  }

  @HostListener('mouseenter')
  onMouseEnter(): void {
    this.bgColor = 'lightblue';
  }

  @HostListener('mouseleave')
  onMouseLeave(): void {
    this.bgColor = 'white';
  }

  // Escutar eventos do window/document
  @HostListener('window:resize', ['$event'])
  onResize(event: Event): void {
    console.log('Window resized', event);
  }

  @HostListener('document:keydown', ['$event'])
  onKeyDown(event: KeyboardEvent): void {
    if (event.key === 'Escape') {
      console.log('ESC pressionado');
    }
  }
}
```

## RxJS Básico

```typescript
import { Observable, of, from, interval, Subject, BehaviorSubject } from 'rxjs';
import { map, filter, tap, debounceTime, distinctUntilChanged, switchMap, catchError } from 'rxjs/operators';

export class ExemploRxJS {
  // Criar Observable
  numeros$ = of(1, 2, 3, 4, 5);

  array$ = from([1, 2, 3, 4, 5]);

  timer$ = interval(1000); // Emite a cada 1 segundo

  // Subject (emit e subscribe)
  subject = new Subject<string>();

  // BehaviorSubject (tem valor inicial)
  behaviorSubject = new BehaviorSubject<number>(0);

  exemploOperadores(): void {
    // Map
    this.numeros$.pipe(
      map(n => n * 2)
    ).subscribe(resultado => console.log(resultado));

    // Filter
    this.numeros$.pipe(
      filter(n => n % 2 === 0)
    ).subscribe(resultado => console.log(resultado));

    // Tap (side effects)
    this.numeros$.pipe(
      tap(n => console.log('Antes:', n)),
      map(n => n * 2),
      tap(n => console.log('Depois:', n))
    ).subscribe();

    // Debounce (esperar 300ms após última emissão)
    this.subject.pipe(
      debounceTime(300),
      distinctUntilChanged()
    ).subscribe(valor => console.log(valor));

    // SwitchMap (cancelar requisição anterior)
    this.subject.pipe(
      debounceTime(300),
      switchMap(termo => this.buscarAPI(termo))
    ).subscribe(resultado => console.log(resultado));

    // CatchError
    this.buscarDados().pipe(
      catchError(error => {
        console.error('Erro:', error);
        return of([]); // Retornar valor padrão
      })
    ).subscribe();
  }

  buscarAPI(termo: string): Observable<any> {
    return of({ resultados: [] });
  }

  buscarDados(): Observable<any> {
    return of([]);
  }

  // Unsubscribe
  subscription = this.numeros$.subscribe();

  ngOnDestroy(): void {
    this.subscription.unsubscribe();
  }
}
```

## Dicas e Boas Práticas

- **Use Reactive Forms** para formulários complexos (validações, lógica condicional)
- **Use Template-Driven Forms** para formulários simples
- **OnPush Change Detection** para melhorar performance em componentes que não mudam frequentemente
- **TrackBy** em *ngFor para melhor performance em listas grandes
- **Lazy Loading** de módulos para reduzir bundle inicial
- **Unsubscribe de Observables** no ngOnDestroy para evitar memory leaks
- **Use AsyncPipe** quando possível (gerencia subscription automaticamente)
- **providedIn: 'root'** para services singleton globais
- **Evite lógica complexa** nos templates (mova para o component)
- **Use TypeScript strict mode** para type safety
- **Organize por features** ao invés de tipo de arquivo
- **Prefira Standalone Components** (Angular 14+) para maior modularidade
- **Use Signals** (Angular 16+) para state management reativo
- **Evite any** - sempre defina tipos apropriados
- **Use interfaces** para definir contratos de dados
- **Nomeação consistente** - sufixos .component, .service, .pipe, .directive
- **Smart vs Dumb components** - separe lógica de apresentação

## Estrutura de Pastas Recomendada

```
src/
├── app/
│   ├── core/                # Singleton services, guards, interceptors
│   │   ├── services/
│   │   ├── guards/
│   │   ├── interceptors/
│   │   └── core.module.ts
│   ├── shared/              # Componentes, pipes, diretivas compartilhados
│   │   ├── components/
│   │   ├── pipes/
│   │   ├── directives/
│   │   └── shared.module.ts
│   ├── features/            # Módulos de funcionalidades
│   │   ├── usuarios/
│   │   │   ├── components/
│   │   │   ├── services/
│   │   │   ├── models/
│   │   │   └── usuarios.module.ts
│   │   └── produtos/
│   ├── models/              # Interfaces e types globais
│   ├── app-routing.module.ts
│   ├── app.component.ts
│   └── app.module.ts
├── assets/
├── environments/
└── styles/
```

## Angular CLI Config

```json
// angular.json - Configurações importantes
{
  "projects": {
    "meu-app": {
      "architect": {
        "build": {
          "options": {
            "budgets": [
              {
                "type": "initial",
                "maximumWarning": "2mb",
                "maximumError": "5mb"
              }
            ]
          },
          "configurations": {
            "production": {
              "optimization": true,
              "sourceMap": false,
              "buildOptimizer": true
            }
          }
        }
      }
    }
  }
}
```

## Ferramentas e Bibliotecas Úteis

| Ferramenta | Descrição |
|------------|-----------|
| Angular DevTools | Extensão do navegador para debug |
| Angular Material | Biblioteca de componentes UI |
| PrimeNG | Biblioteca de componentes UI |
| NgRx | State management (Redux pattern) |
| RxJS | Programação reativa |
| Akita | State management alternativo |
| ng-bootstrap | Bootstrap para Angular |
| Compodoc | Documentação automática |

## Links Úteis

| Recurso | URL |
|---------|-----|
| Documentação Oficial | https://angular.io |
| Angular CLI | https://angular.io/cli |
| Angular Material | https://material.angular.io |
| RxJS | https://rxjs.dev |
| NgRx | https://ngrx.io |
| Angular Update Guide | https://update.angular.io |
