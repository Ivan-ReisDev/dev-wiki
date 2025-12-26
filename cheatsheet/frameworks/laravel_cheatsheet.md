# 🚀 Laravel Cheatsheet

Guia rápido e **profissional** com os principais comandos, conceitos e boas práticas do **Laravel (9/10/11)**.

---

## 🔹 Requisitos

```bash
php -v
composer --version
```

Recomendado: **PHP 8.1+**

---

## 📦 Instalação do Laravel

```bash
composer create-project laravel/laravel nome-do-projeto
```

```bash
cd nome-do-projeto
php artisan serve
```

Servidor padrão: `http://127.0.0.1:8000`

---

## ⚙️ Arquivo .env

```env
APP_NAME=Laravel
APP_ENV=local
APP_DEBUG=true
APP_URL=http://localhost

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=app
DB_USERNAME=root
DB_PASSWORD=
```

---

## ▶️ Executar Aplicação

```bash
php artisan serve
```

```bash
php artisan serve --port=8080
```

---

## 🧱 Artisan (Geração de Código)

```bash
php artisan make:controller UserController
```

```bash
php artisan make:model User
```

```bash
php artisan make:model User -mfc
```
Model + Migration + Factory + Controller

```bash
php artisan make:request StoreUserRequest
```

```bash
php artisan make:middleware AuthMiddleware
```

```bash
php artisan make:policy UserPolicy
```

```bash
php artisan make:resource UserResource
```

---

## 🌐 Rotas

```php
use App\Http\Controllers\UserController;

Route::get('/users', [UserController::class, 'index']);
Route::post('/users', [UserController::class, 'store']);
```

```php
Route::apiResource('users', UserController::class);
```

---

## 📥 Controllers (Tipado)

```php
class UserController extends Controller
{
    public function index(): JsonResponse
    {
        return response()->json(User::all());
    }
}
```

---

## 🗄️ Migrations

```bash
php artisan make:migration create_users_table
```

```php
Schema::create('users', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->string('email')->unique();
    $table->timestamps();
});
```

```bash
php artisan migrate
php artisan migrate:rollback
```

---

## 🧠 Eloquent ORM

```php
class User extends Model
{
    protected $fillable = ['name', 'email'];
}
```

```php
User::create(['name' => 'Ivan', 'email' => 'ivan@email.com']);
```

```php
User::where('active', true)->get();
```

---

## 🔐 Validação (Form Request)

```php
class StoreUserRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'email' => ['required', 'email'],
            'name' => ['required', 'string', 'min:3'],
        ];
    }
}
```

---

## 🔑 Autenticação

```bash
php artisan make:auth
```

Laravel moderno:

```bash
composer require laravel/breeze --dev
php artisan breeze:install
```

---

## 🧪 Testes

```bash
php artisan test
```

```bash
php artisan make:test UserTest
```

---

## 🧹 Cache e Config

```bash
php artisan config:clear
php artisan cache:clear
php artisan route:clear
```

```bash
php artisan optimize
```

---

## 🐳 Laravel com Docker (Resumo)

```bash
docker run -p 8000:80 laravelphp/php-fpm
```

---

## ✅ Boas Práticas Profissionais

- Use **Form Requests** para