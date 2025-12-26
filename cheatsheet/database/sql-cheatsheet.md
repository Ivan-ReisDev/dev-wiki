# SQL Cheatsheet

## Comandos Básicos

### SELECT - Consultar Dados

```sql
-- Selecionar todas as colunas
SELECT * FROM usuarios;

-- Selecionar colunas específicas
SELECT nome, email FROM usuarios;

-- Selecionar com alias
SELECT nome AS nome_usuario, email AS email_usuario FROM usuarios;

-- Selecionar valores únicos
SELECT DISTINCT cidade FROM usuarios;

-- Selecionar com limite
SELECT * FROM usuarios LIMIT 10;

-- Selecionar com offset (paginação)
SELECT * FROM usuarios LIMIT 10 OFFSET 20;

-- MySQL/MariaDB
SELECT * FROM usuarios LIMIT 20, 10;
```

### INSERT - Inserir Dados

```sql
-- Inserir uma linha
INSERT INTO usuarios (nome, email, idade)
VALUES ('João', 'joao@email.com', 25);

-- Inserir múltiplas linhas
INSERT INTO usuarios (nome, email, idade) VALUES
  ('Maria', 'maria@email.com', 30),
  ('Pedro', 'pedro@email.com', 28),
  ('Ana', 'ana@email.com', 22);

-- Inserir com SELECT
INSERT INTO usuarios_backup (nome, email)
SELECT nome, email FROM usuarios WHERE ativo = true;

-- Inserir e retornar ID (PostgreSQL)
INSERT INTO usuarios (nome, email)
VALUES ('João', 'joao@email.com')
RETURNING id;

-- Inserir e retornar ID (MySQL)
INSERT INTO usuarios (nome, email) VALUES ('João', 'joao@email.com');
SELECT LAST_INSERT_ID();
```

### UPDATE - Atualizar Dados

```sql
-- Atualizar uma coluna
UPDATE usuarios SET nome = 'João Silva' WHERE id = 1;

-- Atualizar múltiplas colunas
UPDATE usuarios
SET nome = 'João Silva', email = 'joao.silva@email.com'
WHERE id = 1;

-- Atualizar com cálculo
UPDATE produtos SET preco = preco * 1.10;

-- Atualizar com subquery
UPDATE pedidos
SET status = 'cancelado'
WHERE usuario_id IN (SELECT id FROM usuarios WHERE ativo = false);

-- Atualizar com JOIN (MySQL)
UPDATE pedidos p
INNER JOIN usuarios u ON p.usuario_id = u.id
SET p.status = 'vip'
WHERE u.tipo = 'premium';
```

### DELETE - Excluir Dados

```sql
-- Excluir linhas específicas
DELETE FROM usuarios WHERE id = 1;

-- Excluir múltiplas linhas
DELETE FROM usuarios WHERE idade < 18;

-- Excluir com subquery
DELETE FROM pedidos
WHERE usuario_id IN (SELECT id FROM usuarios WHERE ativo = false);

-- Excluir todas as linhas (cuidado!)
DELETE FROM logs;

-- Excluir todas as linhas mais rápido (reseta auto_increment)
TRUNCATE TABLE logs;
```

## Cláusula WHERE

### Operadores de Comparação

```sql
-- Igual
SELECT * FROM usuarios WHERE idade = 25;

-- Diferente
SELECT * FROM usuarios WHERE idade != 25;
SELECT * FROM usuarios WHERE idade <> 25;

-- Maior / Menor
SELECT * FROM usuarios WHERE idade > 25;
SELECT * FROM usuarios WHERE idade < 25;
SELECT * FROM usuarios WHERE idade >= 25;
SELECT * FROM usuarios WHERE idade <= 25;

-- Entre valores
SELECT * FROM usuarios WHERE idade BETWEEN 18 AND 30;

-- Lista de valores
SELECT * FROM usuarios WHERE cidade IN ('São Paulo', 'Rio de Janeiro', 'Curitiba');

-- Não está na lista
SELECT * FROM usuarios WHERE cidade NOT IN ('São Paulo', 'Rio de Janeiro');
```

### Operadores Lógicos

```sql
-- AND
SELECT * FROM usuarios WHERE idade > 18 AND ativo = true;

-- OR
SELECT * FROM usuarios WHERE cidade = 'São Paulo' OR cidade = 'Rio de Janeiro';

-- NOT
SELECT * FROM usuarios WHERE NOT cidade = 'São Paulo';

-- Combinando operadores
SELECT * FROM usuarios
WHERE (cidade = 'São Paulo' OR cidade = 'Rio de Janeiro')
  AND idade > 18
  AND ativo = true;
```

### Operadores de Texto

```sql
-- LIKE - Padrão de texto
SELECT * FROM usuarios WHERE nome LIKE 'João%';      -- Começa com
SELECT * FROM usuarios WHERE nome LIKE '%Silva';     -- Termina com
SELECT * FROM usuarios WHERE nome LIKE '%Santos%';   -- Contém
SELECT * FROM usuarios WHERE nome LIKE 'J_ão';       -- Um caractere qualquer

-- ILIKE (PostgreSQL) - Case insensitive
SELECT * FROM usuarios WHERE nome ILIKE '%joão%';

-- Case insensitive (MySQL)
SELECT * FROM usuarios WHERE LOWER(nome) LIKE '%joão%';

-- Expressões regulares (PostgreSQL)
SELECT * FROM usuarios WHERE email ~ '^[a-z]+@gmail\.com$';

-- Expressões regulares (MySQL)
SELECT * FROM usuarios WHERE email REGEXP '^[a-z]+@gmail\.com$';
```

### Valores NULL

```sql
-- Verificar NULL
SELECT * FROM usuarios WHERE telefone IS NULL;

-- Verificar NOT NULL
SELECT * FROM usuarios WHERE telefone IS NOT NULL;

-- COALESCE - Valor padrão para NULL
SELECT nome, COALESCE(telefone, 'Não informado') AS telefone FROM usuarios;

-- NULLIF - Retorna NULL se valores forem iguais
SELECT NULLIF(quantidade, 0) FROM produtos;

-- IFNULL (MySQL) / NVL (Oracle)
SELECT nome, IFNULL(telefone, 'Não informado') FROM usuarios;
```

## ORDER BY - Ordenação

```sql
-- Ordenar crescente (padrão)
SELECT * FROM usuarios ORDER BY nome;
SELECT * FROM usuarios ORDER BY nome ASC;

-- Ordenar decrescente
SELECT * FROM usuarios ORDER BY nome DESC;

-- Ordenar por múltiplas colunas
SELECT * FROM usuarios ORDER BY cidade ASC, nome DESC;

-- Ordenar por posição da coluna
SELECT nome, email, idade FROM usuarios ORDER BY 3 DESC;

-- Ordenar com NULLS
SELECT * FROM usuarios ORDER BY telefone NULLS FIRST;
SELECT * FROM usuarios ORDER BY telefone NULLS LAST;

-- Ordenação customizada
SELECT * FROM pedidos
ORDER BY
  CASE status
    WHEN 'urgente' THEN 1
    WHEN 'normal' THEN 2
    WHEN 'baixo' THEN 3
  END;
```

## GROUP BY - Agrupamento

```sql
-- Agrupar e contar
SELECT cidade, COUNT(*) AS total FROM usuarios GROUP BY cidade;

-- Agrupar com soma
SELECT categoria, SUM(preco) AS total FROM produtos GROUP BY categoria;

-- Agrupar com média
SELECT departamento, AVG(salario) AS media_salario
FROM funcionarios
GROUP BY departamento;

-- Agrupar por múltiplas colunas
SELECT cidade, genero, COUNT(*) AS total
FROM usuarios
GROUP BY cidade, genero;

-- Agrupar com HAVING (filtro após agrupamento)
SELECT cidade, COUNT(*) AS total
FROM usuarios
GROUP BY cidade
HAVING COUNT(*) > 10;

-- GROUP BY com ORDER BY
SELECT categoria, SUM(preco) AS total
FROM produtos
GROUP BY categoria
ORDER BY total DESC;
```

## Funções de Agregação

```sql
-- COUNT - Contar linhas
SELECT COUNT(*) FROM usuarios;
SELECT COUNT(telefone) FROM usuarios;  -- Ignora NULL
SELECT COUNT(DISTINCT cidade) FROM usuarios;

-- SUM - Soma
SELECT SUM(preco) FROM produtos;
SELECT SUM(quantidade * preco) AS total FROM itens_pedido;

-- AVG - Média
SELECT AVG(idade) FROM usuarios;
SELECT AVG(preco)::NUMERIC(10,2) FROM produtos;  -- PostgreSQL

-- MIN / MAX
SELECT MIN(preco), MAX(preco) FROM produtos;
SELECT MIN(data_nascimento), MAX(data_nascimento) FROM usuarios;

-- STRING_AGG (PostgreSQL) / GROUP_CONCAT (MySQL)
-- PostgreSQL
SELECT cidade, STRING_AGG(nome, ', ') AS nomes
FROM usuarios
GROUP BY cidade;

-- MySQL
SELECT cidade, GROUP_CONCAT(nome SEPARATOR ', ') AS nomes
FROM usuarios
GROUP BY cidade;
```

## JOINs

### Diagrama de JOINs

```
    INNER JOIN         LEFT JOIN          RIGHT JOIN         FULL OUTER JOIN
    ┌───┬───┐          ┌───┬───┐          ┌───┬───┐          ┌───┬───┐
    │   │███│          │███│███│          │   │███│          │███│███│
    │   │███│          │███│███│          │   │███│          │███│███│
    │   │███│          │███│███│          │   │███│          │███│███│
    └───┴───┘          └───┴───┘          └───┴───┘          └───┴───┘
     A   B              A   B              A   B              A   B
```

### INNER JOIN

```sql
-- Apenas registros com correspondência em ambas tabelas
SELECT u.nome, p.titulo
FROM usuarios u
INNER JOIN pedidos p ON u.id = p.usuario_id;

-- Múltiplos JOINs
SELECT u.nome, p.titulo, i.quantidade, pr.nome AS produto
FROM usuarios u
INNER JOIN pedidos p ON u.id = p.usuario_id
INNER JOIN itens_pedido i ON p.id = i.pedido_id
INNER JOIN produtos pr ON i.produto_id = pr.id;
```

### LEFT JOIN (LEFT OUTER JOIN)

```sql
-- Todos os registros da tabela esquerda + correspondências
SELECT u.nome, p.titulo
FROM usuarios u
LEFT JOIN pedidos p ON u.id = p.usuario_id;

-- Usuários SEM pedidos
SELECT u.nome
FROM usuarios u
LEFT JOIN pedidos p ON u.id = p.usuario_id
WHERE p.id IS NULL;
```

### RIGHT JOIN (RIGHT OUTER JOIN)

```sql
-- Todos os registros da tabela direita + correspondências
SELECT u.nome, p.titulo
FROM usuarios u
RIGHT JOIN pedidos p ON u.id = p.usuario_id;
```

### FULL OUTER JOIN

```sql
-- Todos os registros de ambas tabelas
SELECT u.nome, p.titulo
FROM usuarios u
FULL OUTER JOIN pedidos p ON u.id = p.usuario_id;

-- Simular FULL JOIN no MySQL (não suporta nativamente)
SELECT u.nome, p.titulo FROM usuarios u LEFT JOIN pedidos p ON u.id = p.usuario_id
UNION
SELECT u.nome, p.titulo FROM usuarios u RIGHT JOIN pedidos p ON u.id = p.usuario_id;
```

### CROSS JOIN

```sql
-- Produto cartesiano (todas as combinações)
SELECT cores.nome, tamanhos.nome
FROM cores
CROSS JOIN tamanhos;

-- Equivalente
SELECT cores.nome, tamanhos.nome
FROM cores, tamanhos;
```

### SELF JOIN

```sql
-- Tabela com ela mesma
SELECT e.nome AS funcionario, g.nome AS gerente
FROM funcionarios e
LEFT JOIN funcionarios g ON e.gerente_id = g.id;
```

### JOIN com condições múltiplas

```sql
SELECT *
FROM pedidos p
INNER JOIN produtos pr ON p.produto_id = pr.id
  AND p.data >= '2024-01-01'
  AND pr.ativo = true;
```

## Subqueries

### Subquery no WHERE

```sql
-- Subquery retornando um valor
SELECT * FROM produtos
WHERE preco > (SELECT AVG(preco) FROM produtos);

-- Subquery retornando lista
SELECT * FROM usuarios
WHERE id IN (SELECT usuario_id FROM pedidos WHERE status = 'completo');

-- EXISTS
SELECT * FROM usuarios u
WHERE EXISTS (
  SELECT 1 FROM pedidos p WHERE p.usuario_id = u.id
);

-- NOT EXISTS
SELECT * FROM usuarios u
WHERE NOT EXISTS (
  SELECT 1 FROM pedidos p WHERE p.usuario_id = u.id
);
```

### Subquery no FROM

```sql
-- Subquery como tabela derivada
SELECT categoria, media_preco
FROM (
  SELECT categoria, AVG(preco) AS media_preco
  FROM produtos
  GROUP BY categoria
) AS subconsulta
WHERE media_preco > 100;
```

### Subquery no SELECT

```sql
-- Subquery correlacionada
SELECT
  u.nome,
  (SELECT COUNT(*) FROM pedidos p WHERE p.usuario_id = u.id) AS total_pedidos
FROM usuarios u;
```

### CTE (Common Table Expression)

```sql
-- WITH clause
WITH vendas_por_categoria AS (
  SELECT categoria_id, SUM(valor) AS total
  FROM vendas
  GROUP BY categoria_id
)
SELECT c.nome, v.total
FROM categorias c
JOIN vendas_por_categoria v ON c.id = v.categoria_id
WHERE v.total > 1000;

-- CTE recursiva
WITH RECURSIVE subordinados AS (
  SELECT id, nome, gerente_id, 1 AS nivel
  FROM funcionarios
  WHERE gerente_id IS NULL

  UNION ALL

  SELECT f.id, f.nome, f.gerente_id, s.nivel + 1
  FROM funcionarios f
  INNER JOIN subordinados s ON f.gerente_id = s.id
)
SELECT * FROM subordinados;
```

## UNION e Operações de Conjunto

```sql
-- UNION (remove duplicatas)
SELECT nome, email FROM clientes
UNION
SELECT nome, email FROM fornecedores;

-- UNION ALL (mantém duplicatas - mais rápido)
SELECT nome FROM clientes
UNION ALL
SELECT nome FROM fornecedores;

-- INTERSECT (registros em ambas)
SELECT nome FROM clientes
INTERSECT
SELECT nome FROM fornecedores;

-- EXCEPT / MINUS (registros apenas na primeira)
SELECT nome FROM clientes
EXCEPT
SELECT nome FROM fornecedores;
```

## Criação de Tabelas

### CREATE TABLE

```sql
-- Criar tabela básica
CREATE TABLE usuarios (
  id SERIAL PRIMARY KEY,                    -- PostgreSQL
  -- id INT AUTO_INCREMENT PRIMARY KEY,     -- MySQL
  nome VARCHAR(100) NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  senha VARCHAR(255) NOT NULL,
  idade INT CHECK (idade >= 0),
  salario DECIMAL(10, 2) DEFAULT 0.00,
  ativo BOOLEAN DEFAULT true,
  criado_em TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  atualizado_em TIMESTAMP
);

-- Criar tabela com chave estrangeira
CREATE TABLE pedidos (
  id SERIAL PRIMARY KEY,
  usuario_id INT NOT NULL,
  valor DECIMAL(10, 2) NOT NULL,
  status VARCHAR(20) DEFAULT 'pendente',
  criado_em TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

  CONSTRAINT fk_usuario
    FOREIGN KEY (usuario_id)
    REFERENCES usuarios(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);

-- Criar tabela com chave composta
CREATE TABLE itens_pedido (
  pedido_id INT,
  produto_id INT,
  quantidade INT NOT NULL DEFAULT 1,
  preco_unitario DECIMAL(10, 2) NOT NULL,

  PRIMARY KEY (pedido_id, produto_id),
  FOREIGN KEY (pedido_id) REFERENCES pedidos(id),
  FOREIGN KEY (produto_id) REFERENCES produtos(id)
);

-- Criar tabela a partir de outra
CREATE TABLE usuarios_backup AS
SELECT * FROM usuarios WHERE ativo = true;

-- Criar tabela temporária
CREATE TEMPORARY TABLE temp_dados (
  id INT,
  valor DECIMAL(10, 2)
);
```

### ALTER TABLE

```sql
-- Adicionar coluna
ALTER TABLE usuarios ADD COLUMN telefone VARCHAR(20);

-- Adicionar coluna com default
ALTER TABLE usuarios ADD COLUMN status VARCHAR(20) DEFAULT 'ativo';

-- Remover coluna
ALTER TABLE usuarios DROP COLUMN telefone;

-- Modificar tipo de coluna (PostgreSQL)
ALTER TABLE usuarios ALTER COLUMN idade TYPE BIGINT;

-- Modificar tipo de coluna (MySQL)
ALTER TABLE usuarios MODIFY COLUMN idade BIGINT;

-- Renomear coluna (PostgreSQL)
ALTER TABLE usuarios RENAME COLUMN nome TO nome_completo;

-- Renomear coluna (MySQL)
ALTER TABLE usuarios CHANGE COLUMN nome nome_completo VARCHAR(100);

-- Adicionar constraint
ALTER TABLE usuarios ADD CONSTRAINT email_unique UNIQUE (email);

-- Remover constraint
ALTER TABLE usuarios DROP CONSTRAINT email_unique;

-- Adicionar chave estrangeira
ALTER TABLE pedidos
ADD CONSTRAINT fk_usuario
FOREIGN KEY (usuario_id) REFERENCES usuarios(id);

-- Remover chave estrangeira
ALTER TABLE pedidos DROP CONSTRAINT fk_usuario;

-- Renomear tabela
ALTER TABLE usuarios RENAME TO clientes;
```

### DROP TABLE

```sql
-- Excluir tabela
DROP TABLE usuarios;

-- Excluir se existir
DROP TABLE IF EXISTS usuarios;

-- Excluir com cascata (PostgreSQL)
DROP TABLE usuarios CASCADE;
```

## Constraints (Restrições)

```sql
-- PRIMARY KEY
CREATE TABLE usuarios (
  id INT PRIMARY KEY
);

-- PRIMARY KEY composta
CREATE TABLE itens (
  pedido_id INT,
  produto_id INT,
  PRIMARY KEY (pedido_id, produto_id)
);

-- FOREIGN KEY
CREATE TABLE pedidos (
  usuario_id INT REFERENCES usuarios(id)
);

-- Com ações
CREATE TABLE pedidos (
  usuario_id INT,
  FOREIGN KEY (usuario_id) REFERENCES usuarios(id)
    ON DELETE CASCADE      -- Exclui pedidos ao excluir usuário
    ON UPDATE SET NULL     -- Define NULL ao atualizar id
);

-- UNIQUE
CREATE TABLE usuarios (
  email VARCHAR(255) UNIQUE
);

-- UNIQUE composta
CREATE TABLE inscricoes (
  usuario_id INT,
  evento_id INT,
  UNIQUE (usuario_id, evento_id)
);

-- NOT NULL
CREATE TABLE usuarios (
  nome VARCHAR(100) NOT NULL
);

-- CHECK
CREATE TABLE produtos (
  preco DECIMAL(10, 2) CHECK (preco >= 0),
  quantidade INT CHECK (quantidade >= 0 AND quantidade <= 1000)
);

-- DEFAULT
CREATE TABLE usuarios (
  ativo BOOLEAN DEFAULT true,
  criado_em TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## Índices

```sql
-- Criar índice simples
CREATE INDEX idx_usuarios_nome ON usuarios(nome);

-- Criar índice único
CREATE UNIQUE INDEX idx_usuarios_email ON usuarios(email);

-- Criar índice composto
CREATE INDEX idx_pedidos_usuario_data ON pedidos(usuario_id, criado_em);

-- Criar índice parcial (PostgreSQL)
CREATE INDEX idx_usuarios_ativos ON usuarios(nome) WHERE ativo = true;

-- Criar índice de texto (PostgreSQL)
CREATE INDEX idx_produtos_busca ON produtos USING GIN (to_tsvector('portuguese', nome));

-- Criar índice FULLTEXT (MySQL)
CREATE FULLTEXT INDEX idx_produtos_nome ON produtos(nome, descricao);

-- Excluir índice
DROP INDEX idx_usuarios_nome;

-- Listar índices (PostgreSQL)
SELECT * FROM pg_indexes WHERE tablename = 'usuarios';

-- Listar índices (MySQL)
SHOW INDEX FROM usuarios;
```

## Views

```sql
-- Criar view
CREATE VIEW usuarios_ativos AS
SELECT id, nome, email, cidade
FROM usuarios
WHERE ativo = true;

-- Usar view
SELECT * FROM usuarios_ativos WHERE cidade = 'São Paulo';

-- Criar ou substituir view
CREATE OR REPLACE VIEW usuarios_ativos AS
SELECT id, nome, email, cidade, criado_em
FROM usuarios
WHERE ativo = true;

-- View materializada (PostgreSQL)
CREATE MATERIALIZED VIEW vendas_por_mes AS
SELECT
  DATE_TRUNC('month', data) AS mes,
  SUM(valor) AS total
FROM vendas
GROUP BY DATE_TRUNC('month', data);

-- Atualizar view materializada
REFRESH MATERIALIZED VIEW vendas_por_mes;

-- Excluir view
DROP VIEW usuarios_ativos;
DROP VIEW IF EXISTS usuarios_ativos;
```

## Transações

```sql
-- Iniciar transação
BEGIN;
-- ou
START TRANSACTION;

-- Executar comandos
UPDATE contas SET saldo = saldo - 100 WHERE id = 1;
UPDATE contas SET saldo = saldo + 100 WHERE id = 2;

-- Confirmar transação
COMMIT;

-- Desfazer transação
ROLLBACK;

-- Savepoint (ponto de salvamento)
BEGIN;
UPDATE contas SET saldo = saldo - 100 WHERE id = 1;
SAVEPOINT sp1;
UPDATE contas SET saldo = saldo + 100 WHERE id = 2;
-- Se algo der errado, voltar ao savepoint
ROLLBACK TO sp1;
-- Continuar de sp1
UPDATE contas SET saldo = saldo + 100 WHERE id = 3;
COMMIT;

-- Níveis de isolamento
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

## Funções de String

```sql
-- Concatenar
SELECT CONCAT(nome, ' ', sobrenome) FROM usuarios;
SELECT nome || ' ' || sobrenome FROM usuarios;  -- PostgreSQL

-- Tamanho
SELECT LENGTH(nome) FROM usuarios;
SELECT CHAR_LENGTH(nome) FROM usuarios;

-- Maiúsculas / Minúsculas
SELECT UPPER(nome), LOWER(email) FROM usuarios;
SELECT INITCAP(nome) FROM usuarios;  -- PostgreSQL

-- Substring
SELECT SUBSTRING(nome, 1, 5) FROM usuarios;
SELECT LEFT(nome, 3), RIGHT(nome, 3) FROM usuarios;

-- Substituir
SELECT REPLACE(email, '@gmail.com', '@empresa.com') FROM usuarios;

-- Remover espaços
SELECT TRIM(nome) FROM usuarios;
SELECT LTRIM(nome), RTRIM(nome) FROM usuarios;

-- Posição
SELECT POSITION('@' IN email) FROM usuarios;
SELECT CHARINDEX('@', email) FROM usuarios;  -- SQL Server

-- Dividir / Separar (PostgreSQL)
SELECT SPLIT_PART(email, '@', 1) AS usuario FROM usuarios;
SELECT STRING_TO_ARRAY(tags, ',') FROM produtos;

-- Padding
SELECT LPAD(codigo, 5, '0') FROM produtos;  -- '00123'
SELECT RPAD(nome, 20, '.') FROM usuarios;   -- 'João.............'

-- Reverter
SELECT REVERSE(nome) FROM usuarios;

-- Repetir
SELECT REPEAT('*', 5);  -- '*****'
```

## Funções de Data e Hora

```sql
-- Data/hora atual
SELECT CURRENT_DATE;
SELECT CURRENT_TIME;
SELECT CURRENT_TIMESTAMP;
SELECT NOW();

-- Extrair partes da data
SELECT EXTRACT(YEAR FROM data_nascimento) FROM usuarios;
SELECT EXTRACT(MONTH FROM criado_em) FROM pedidos;
SELECT EXTRACT(DAY FROM criado_em) FROM pedidos;
SELECT EXTRACT(HOUR FROM criado_em) FROM pedidos;

-- DATE_PART (PostgreSQL)
SELECT DATE_PART('year', data_nascimento) FROM usuarios;

-- YEAR, MONTH, DAY (MySQL)
SELECT YEAR(data_nascimento), MONTH(data_nascimento), DAY(data_nascimento)
FROM usuarios;

-- Formatar data
-- PostgreSQL
SELECT TO_CHAR(criado_em, 'DD/MM/YYYY HH24:MI:SS') FROM pedidos;
SELECT TO_CHAR(criado_em, 'YYYY-MM-DD') FROM pedidos;

-- MySQL
SELECT DATE_FORMAT(criado_em, '%d/%m/%Y %H:%i:%s') FROM pedidos;

-- Truncar data (PostgreSQL)
SELECT DATE_TRUNC('month', criado_em) FROM pedidos;
SELECT DATE_TRUNC('year', criado_em) FROM pedidos;

-- Adicionar/Subtrair
-- PostgreSQL
SELECT criado_em + INTERVAL '7 days' FROM pedidos;
SELECT criado_em - INTERVAL '1 month' FROM pedidos;

-- MySQL
SELECT DATE_ADD(criado_em, INTERVAL 7 DAY) FROM pedidos;
SELECT DATE_SUB(criado_em, INTERVAL 1 MONTH) FROM pedidos;

-- Diferença entre datas
-- PostgreSQL
SELECT AGE(data_fim, data_inicio) FROM projetos;
SELECT data_fim - data_inicio AS dias FROM projetos;

-- MySQL
SELECT DATEDIFF(data_fim, data_inicio) FROM projetos;
SELECT TIMESTAMPDIFF(DAY, data_inicio, data_fim) FROM projetos;

-- Converter string para data
SELECT TO_DATE('25/12/2024', 'DD/MM/YYYY');  -- PostgreSQL
SELECT STR_TO_DATE('25/12/2024', '%d/%m/%Y');  -- MySQL
```

## Funções Condicionais

```sql
-- CASE WHEN
SELECT
  nome,
  CASE
    WHEN idade < 18 THEN 'Menor'
    WHEN idade BETWEEN 18 AND 65 THEN 'Adulto'
    ELSE 'Idoso'
  END AS faixa_etaria
FROM usuarios;

-- CASE simples
SELECT
  nome,
  CASE status
    WHEN 'A' THEN 'Ativo'
    WHEN 'I' THEN 'Inativo'
    WHEN 'P' THEN 'Pendente'
    ELSE 'Desconhecido'
  END AS status_descricao
FROM usuarios;

-- COALESCE
SELECT COALESCE(telefone, celular, 'Não informado') AS contato FROM usuarios;

-- NULLIF
SELECT valor / NULLIF(divisor, 0) FROM calculos;  -- Evita divisão por zero

-- IIF (SQL Server)
SELECT IIF(ativo = 1, 'Ativo', 'Inativo') FROM usuarios;

-- IF (MySQL)
SELECT IF(ativo = 1, 'Ativo', 'Inativo') FROM usuarios;

-- GREATEST / LEAST
SELECT GREATEST(preco1, preco2, preco3) AS maior_preco FROM produtos;
SELECT LEAST(preco1, preco2, preco3) AS menor_preco FROM produtos;
```

## Funções Numéricas

```sql
-- Arredondamento
SELECT ROUND(preco, 2) FROM produtos;          -- 10.56
SELECT CEIL(preco) FROM produtos;              -- 11
SELECT FLOOR(preco) FROM produtos;             -- 10
SELECT TRUNC(preco, 2) FROM produtos;          -- 10.56 (sem arredondamento)

-- Valor absoluto
SELECT ABS(-100);  -- 100

-- Módulo (resto da divisão)
SELECT MOD(17, 5);  -- 2
SELECT 17 % 5;      -- 2

-- Potência e raiz
SELECT POWER(2, 10);  -- 1024
SELECT SQRT(144);     -- 12

-- Logaritmos
SELECT LOG(100);      -- Log natural
SELECT LOG10(100);    -- Log base 10

-- Aleatório
SELECT RANDOM();                    -- PostgreSQL (0 a 1)
SELECT RAND();                      -- MySQL (0 a 1)
SELECT FLOOR(RANDOM() * 100);       -- PostgreSQL (0 a 99)
SELECT FLOOR(RAND() * 100);         -- MySQL (0 a 99)

-- Sinal
SELECT SIGN(-10);  -- -1
SELECT SIGN(0);    -- 0
SELECT SIGN(10);   -- 1
```

## Window Functions

```sql
-- ROW_NUMBER - Numerar linhas
SELECT
  nome,
  salario,
  ROW_NUMBER() OVER (ORDER BY salario DESC) AS ranking
FROM funcionarios;

-- ROW_NUMBER com partição
SELECT
  departamento,
  nome,
  salario,
  ROW_NUMBER() OVER (PARTITION BY departamento ORDER BY salario DESC) AS ranking_dept
FROM funcionarios;

-- RANK e DENSE_RANK
SELECT
  nome,
  salario,
  RANK() OVER (ORDER BY salario DESC) AS rank,         -- Pula posições em empate
  DENSE_RANK() OVER (ORDER BY salario DESC) AS dense   -- Não pula posições
FROM funcionarios;

-- NTILE - Dividir em grupos
SELECT
  nome,
  salario,
  NTILE(4) OVER (ORDER BY salario DESC) AS quartil
FROM funcionarios;

-- LAG / LEAD - Valor anterior/próximo
SELECT
  data,
  valor,
  LAG(valor, 1) OVER (ORDER BY data) AS valor_anterior,
  LEAD(valor, 1) OVER (ORDER BY data) AS valor_proximo
FROM vendas;

-- Variação percentual
SELECT
  data,
  valor,
  (valor - LAG(valor) OVER (ORDER BY data)) / LAG(valor) OVER (ORDER BY data) * 100 AS variacao_pct
FROM vendas;

-- FIRST_VALUE / LAST_VALUE
SELECT
  departamento,
  nome,
  salario,
  FIRST_VALUE(nome) OVER (PARTITION BY departamento ORDER BY salario DESC) AS maior_salario
FROM funcionarios;

-- SUM/AVG como window function
SELECT
  data,
  valor,
  SUM(valor) OVER (ORDER BY data) AS acumulado,
  AVG(valor) OVER (ORDER BY data ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS media_movel
FROM vendas;
```

## Stored Procedures e Functions

### PostgreSQL

```sql
-- Criar função
CREATE OR REPLACE FUNCTION calcular_desconto(preco DECIMAL, porcentagem DECIMAL)
RETURNS DECIMAL AS $$
BEGIN
  RETURN preco * (1 - porcentagem / 100);
END;
$$ LANGUAGE plpgsql;

-- Usar função
SELECT calcular_desconto(100, 10);  -- 90

-- Função que retorna tabela
CREATE OR REPLACE FUNCTION buscar_usuarios_por_cidade(p_cidade VARCHAR)
RETURNS TABLE(id INT, nome VARCHAR, email VARCHAR) AS $$
BEGIN
  RETURN QUERY
  SELECT u.id, u.nome, u.email
  FROM usuarios u
  WHERE u.cidade = p_cidade;
END;
$$ LANGUAGE plpgsql;

-- Usar
SELECT * FROM buscar_usuarios_por_cidade('São Paulo');
```

### MySQL

```sql
-- Criar procedure
DELIMITER //
CREATE PROCEDURE buscar_usuario(IN usuario_id INT)
BEGIN
  SELECT * FROM usuarios WHERE id = usuario_id;
END //
DELIMITER ;

-- Chamar procedure
CALL buscar_usuario(1);

-- Procedure com parâmetros OUT
DELIMITER //
CREATE PROCEDURE contar_usuarios(OUT total INT)
BEGIN
  SELECT COUNT(*) INTO total FROM usuarios;
END //
DELIMITER ;

-- Usar
CALL contar_usuarios(@total);
SELECT @total;

-- Criar função
DELIMITER //
CREATE FUNCTION calcular_idade(data_nasc DATE)
RETURNS INT
DETERMINISTIC
BEGIN
  RETURN TIMESTAMPDIFF(YEAR, data_nasc, CURDATE());
END //
DELIMITER ;

-- Usar
SELECT nome, calcular_idade(data_nascimento) AS idade FROM usuarios;
```

## Triggers

### PostgreSQL

```sql
-- Criar função para trigger
CREATE OR REPLACE FUNCTION atualizar_timestamp()
RETURNS TRIGGER AS $$
BEGIN
  NEW.atualizado_em = CURRENT_TIMESTAMP;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Criar trigger
CREATE TRIGGER tr_usuarios_update
BEFORE UPDATE ON usuarios
FOR EACH ROW
EXECUTE FUNCTION atualizar_timestamp();

-- Trigger para log
CREATE OR REPLACE FUNCTION registrar_alteracao()
RETURNS TRIGGER AS $$
BEGIN
  INSERT INTO log_alteracoes (tabela, operacao, dados_antigos, dados_novos, data)
  VALUES (TG_TABLE_NAME, TG_OP, ROW_TO_JSON(OLD), ROW_TO_JSON(NEW), NOW());
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER tr_log_usuarios
AFTER UPDATE OR DELETE ON usuarios
FOR EACH ROW
EXECUTE FUNCTION registrar_alteracao();
```

### MySQL

```sql
-- Trigger antes de inserir
DELIMITER //
CREATE TRIGGER tr_antes_inserir_usuario
BEFORE INSERT ON usuarios
FOR EACH ROW
BEGIN
  SET NEW.criado_em = NOW();
  SET NEW.email = LOWER(NEW.email);
END //
DELIMITER ;

-- Trigger após atualizar
DELIMITER //
CREATE TRIGGER tr_apos_atualizar_usuario
AFTER UPDATE ON usuarios
FOR EACH ROW
BEGIN
  INSERT INTO log_usuarios (usuario_id, operacao, data)
  VALUES (NEW.id, 'UPDATE', NOW());
END //
DELIMITER ;
```

## Performance e Otimização

### EXPLAIN

```sql
-- Analisar plano de execução
EXPLAIN SELECT * FROM usuarios WHERE email = 'joao@email.com';

-- Com mais detalhes (PostgreSQL)
EXPLAIN ANALYZE SELECT * FROM usuarios WHERE email = 'joao@email.com';

-- MySQL
EXPLAIN FORMAT=JSON SELECT * FROM usuarios WHERE email = 'joao@email.com';
```

### Dicas de Otimização

```sql
-- Usar índices
CREATE INDEX idx_email ON usuarios(email);

-- Evitar SELECT *
SELECT nome, email FROM usuarios;  -- Melhor
SELECT * FROM usuarios;            -- Evitar

-- Usar EXISTS ao invés de IN para subqueries grandes
-- Bom
SELECT * FROM usuarios u WHERE EXISTS (
  SELECT 1 FROM pedidos p WHERE p.usuario_id = u.id
);
-- Menos eficiente
SELECT * FROM usuarios WHERE id IN (SELECT usuario_id FROM pedidos);

-- Usar JOIN ao invés de subquery
-- Bom
SELECT u.nome FROM usuarios u
INNER JOIN pedidos p ON u.id = p.usuario_id;

-- Menos eficiente
SELECT nome FROM usuarios WHERE id IN (SELECT usuario_id FROM pedidos);

-- Limitar resultados
SELECT * FROM logs ORDER BY data DESC LIMIT 100;

-- Usar UNION ALL quando não precisa remover duplicatas
SELECT nome FROM tabela1 UNION ALL SELECT nome FROM tabela2;
```

## Comandos Administrativos

```sql
-- Criar banco de dados
CREATE DATABASE meu_banco;
CREATE DATABASE meu_banco WITH ENCODING='UTF8';  -- PostgreSQL

-- Excluir banco de dados
DROP DATABASE meu_banco;
DROP DATABASE IF EXISTS meu_banco;

-- Listar bancos (PostgreSQL)
SELECT datname FROM pg_database;
\l

-- Listar bancos (MySQL)
SHOW DATABASES;

-- Usar banco (MySQL)
USE meu_banco;

-- Listar tabelas (PostgreSQL)
SELECT tablename FROM pg_tables WHERE schemaname = 'public';
\dt

-- Listar tabelas (MySQL)
SHOW TABLES;

-- Descrever tabela (PostgreSQL)
\d usuarios

-- Descrever tabela (MySQL)
DESCRIBE usuarios;
SHOW COLUMNS FROM usuarios;

-- Backup (PostgreSQL)
-- pg_dump meu_banco > backup.sql

-- Backup (MySQL)
-- mysqldump -u root -p meu_banco > backup.sql

-- Restaurar
-- psql meu_banco < backup.sql
-- mysql -u root -p meu_banco < backup.sql
```

## Dicas e Boas Práticas

### Nomenclatura

- Use **snake_case** para nomes de tabelas e colunas
- Use nomes no **singular** para tabelas (usuario, não usuarios)
- Prefixe índices com `idx_`, constraints com `pk_`, `fk_`, `uk_`, `ck_`
- Use nomes descritivos e significativos

### Performance

- Sempre crie índices para colunas em WHERE, JOIN e ORDER BY
- Evite `SELECT *` - selecione apenas colunas necessárias
- Use `LIMIT` para grandes conjuntos de dados
- Use `EXISTS` ao invés de `IN` para subqueries grandes
- Analise queries com `EXPLAIN`
- Evite funções em colunas no WHERE (impede uso de índice)

### Segurança

- **Nunca** concatene strings SQL diretamente (SQL Injection)
- Use prepared statements / parametrized queries
- Limite permissões de usuários do banco
- Faça backup regularmente

### Design

- Normalize seu banco de dados (pelo menos até 3FN)
- Use tipos de dados apropriados
- Defina constraints para integridade dos dados
- Use transações para operações críticas
- Documente seu schema

## Tipos de Dados Comuns

| Tipo | PostgreSQL | MySQL | Descrição |
|------|------------|-------|-----------|
| Inteiro | INTEGER, INT | INT | Número inteiro |
| Inteiro grande | BIGINT | BIGINT | Número inteiro grande |
| Inteiro auto | SERIAL | AUTO_INCREMENT | Inteiro com autoincremento |
| Decimal | DECIMAL(p,s) | DECIMAL(p,s) | Número decimal |
| Ponto flutuante | REAL, DOUBLE PRECISION | FLOAT, DOUBLE | Número com ponto flutuante |
| String fixa | CHAR(n) | CHAR(n) | String tamanho fixo |
| String variável | VARCHAR(n) | VARCHAR(n) | String tamanho variável |
| Texto longo | TEXT | TEXT | Texto sem limite |
| Data | DATE | DATE | Apenas data |
| Hora | TIME | TIME | Apenas hora |
| Data e hora | TIMESTAMP | DATETIME | Data e hora |
| Booleano | BOOLEAN | TINYINT(1) | Verdadeiro/Falso |
| JSON | JSON, JSONB | JSON | Dados JSON |
| UUID | UUID | CHAR(36) | Identificador único |
| Binário | BYTEA | BLOB | Dados binários |

## Links Úteis

| Recurso | URL |
|---------|-----|
| PostgreSQL Docs | https://www.postgresql.org/docs |
| MySQL Docs | https://dev.mysql.com/doc |
| SQL Tutorial | https://www.w3schools.com/sql |
| SQLBolt | https://sqlbolt.com |
| Mode SQL Tutorial | https://mode.com/sql-tutorial |
| Use The Index, Luke | https://use-the-index-luke.com |
| SQLZoo | https://sqlzoo.net |
