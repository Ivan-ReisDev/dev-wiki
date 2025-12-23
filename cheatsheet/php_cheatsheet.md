# 🐘 PHP Cheatsheet (Tipado e Completo)

Guia rápido com **PHP moderno (8+)**, incluindo **tipagem**, **operadores**, **comparações** e **boas práticas profissionais**.

---

## 🔹 Versão e Execução

```bash
php -v
```

```bash
php arquivo.php
```

```bash
php -S localhost:8000 -t public
```
Servidor embutido.

---

## 🧠 Tipagem Estrita

```php
declare(strict_types=1);
```

Ativa verificação rígida de tipos (recomendado).

---

## 📄 Tipos Primitivos

```php
int
float
string
bool
array
object
callable
iterable
mixed
void
null
```

---

## 🔤 Variáveis Tipadas

```php
int $idade = 30;
string $nome = "Ivan";
bool $ativo = true;
```

---

## 🧩 Funções Tipadas

```php
function soma(int $a, int $b): int {
    return $a + $b;
}
```

```php
function logMessage(string $msg): void {
    echo $msg;
}
```

```php
$dobro = fn(int $x): int => $x * 2;
```

---

## 🧱 Classes, Propriedades e Métodos

```php
class User
{
    public function __construct(
        public int $id,
        public string $name,
        public ?string $email = null
    ) {}

    public function isAdmin(): bool
    {
        return $this->id === 1;
    }
}
```

---

## ❓ Tipos Nulos e Union Types

```php
function findUser(int $id): ?User {
    return null;
}
```

```php
function parse(int|string $value): string {
    return (string) $value;
}
```

---

## 🔁 Estruturas de Controle

```php
if ($idade >= 18 && $ativo) {
    echo "Permitido";
}
```

```php
for (int $i = 0; $i < 10; $i++) {}
```

```php
foreach ($users as User $user) {
    echo $user->name;
}
```

---

## ⚖️ Operadores de Comparação

```php
==   // igual (valor)
===  // idêntico (valor + tipo)
!=   // diferente
!==  // diferente (valor ou tipo)
<    // menor que
>    // maior que
<=   // menor ou igual
>=   // maior ou igual
<=>  // spaceship
```

```php
$result = $a <=> $b; // -1 | 0 | 1
```

---

## 🔗 Operadores Lógicos

```php
&&  // AND
||  // OR
!   // NOT
```

---

## 🧠 Operadores Modernos

```php
$valor ??= 'default'; // null coalescing assign
```

```php
$result = $a ?? $b ?? 'fallback';
```

```php
$nome = $user?->name; // nullsafe
```

---

## 🧱 Arrays Tipados (Boas Práticas)

```php
/** @var int[] $ids */
$ids = [1, 2, 3];
```

```php
/** @var array<string, int> */
$map = ['a' => 1];
```

---

## 🗄️ Banco de Dados (PDO Tipado)

```php
$pdo = new PDO(
    'mysql:host=localhost;dbname=db',
    'user',
    'pass',
    [PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION]
);
```

```php
$stmt = $pdo->prepare('SELECT * FROM users WHERE id = :id');
$stmt->execute(['id' => 1]);
$user = $stmt->fetchObject(User::class);
```

---

## 🧪 Debug e Tipos

```php
var_dump($var);
```

```php
gettype($var);
```

---

## 🐳 PHP com Docker

```bash
docker run -p 8000:80 php:8.3-apache
```

---

## ✅ Boas Práticas Profissionais

- Sempre use `declare(strict_types=1)`
- Prefira `===` ao invés de `==`
- Tipar tudo: funções, métodos e propriedades
- Use `?Tipo` para valores opcionais
- Evite `mixed` quando possível
- Valide input externo

---

📌 PHP moderno, tipado e seguro (Laravel, Symfony, APIs REST).

