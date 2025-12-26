# PostgreSQL Cheatsheet

## Instalação

### Linux (Ubuntu/Debian)

```bash
# Instalar PostgreSQL
sudo apt update
sudo apt install postgresql postgresql-contrib

# Verificar status
sudo systemctl status postgresql

# Iniciar/Parar/Reiniciar
sudo systemctl start postgresql
sudo systemctl stop postgresql
sudo systemctl restart postgresql

# Habilitar início automático
sudo systemctl enable postgresql
```

### macOS

```bash
# Com Homebrew
brew install postgresql@16

# Iniciar serviço
brew services start postgresql@16

# Parar serviço
brew services stop postgresql@16
```

### Docker

```bash
# Rodar container
docker run --name postgres -e POSTGRES_PASSWORD=senha -p 5432:5432 -d postgres:16

# Acessar container
docker exec -it postgres psql -U postgres

# Com volume persistente
docker run --name postgres \
  -e POSTGRES_PASSWORD=senha \
  -e POSTGRES_USER=usuario \
  -e POSTGRES_DB=meu_banco \
  -v postgres_data:/var/lib/postgresql/data \
  -p 5432:5432 \
  -d postgres:16
```

## Comandos psql (CLI)

### Conectar ao Banco

```bash
# Conectar como usuário postgres
sudo -u postgres psql

# Conectar a um banco específico
psql -U usuario -d banco -h localhost -p 5432

# Conectar com string de conexão
psql "postgresql://usuario:senha@localhost:5432/banco"

# Conectar com SSL
psql "postgresql://usuario:senha@host:5432/banco?sslmode=require"
```

### Meta-comandos psql

```sql
-- Ajuda
\?                      -- Lista todos os comandos
\h                      -- Ajuda SQL
\h SELECT               -- Ajuda específica

-- Conexão
\c banco                -- Conectar a outro banco
\c banco usuario        -- Conectar como outro usuário
\conninfo               -- Informações da conexão atual

-- Listar
\l                      -- Listar bancos de dados
\l+                     -- Listar bancos com detalhes
\dt                     -- Listar tabelas
\dt+                    -- Listar tabelas com detalhes
\dt schema.*            -- Listar tabelas de um schema
\di                     -- Listar índices
\dv                     -- Listar views
\dm                     -- Listar views materializadas
\ds                     -- Listar sequences
\df                     -- Listar funções
\df+                    -- Listar funções com detalhes
\du                     -- Listar usuários/roles
\dn                     -- Listar schemas
\dx                     -- Listar extensões

-- Descrever objetos
\d tabela               -- Descrever tabela
\d+ tabela              -- Descrever com detalhes
\d *                    -- Descrever todos os objetos

-- Edição e execução
\e                      -- Editar query no editor
\i arquivo.sql          -- Executar arquivo SQL
\o arquivo.txt          -- Redirecionar saída para arquivo
\o                      -- Voltar saída para terminal

-- Formatação
\x                      -- Toggle modo expandido (vertical)
\x on                   -- Ativar modo expandido
\x off                  -- Desativar modo expandido
\a                      -- Toggle alinhamento
\t                      -- Toggle headers
\pset format csv        -- Formato CSV
\pset format html       -- Formato HTML

-- Variáveis
\set NOME valor         -- Definir variável
\echo :NOME             -- Exibir variável
\unset NOME             -- Remover variável

-- Timing
\timing                 -- Toggle tempo de execução
\timing on              -- Ativar timing

-- Sair
\q                      -- Sair do psql
```

### Histórico e Atalhos

```bash
# Histórico de comandos
\s                      # Mostrar histórico
\s arquivo.txt          # Salvar histórico em arquivo

# Atalhos úteis
Ctrl + R                # Buscar no histórico
Ctrl + L                # Limpar tela
Ctrl + C                # Cancelar comando atual
```

## Tipos de Dados PostgreSQL

### Tipos Numéricos

```sql
-- Inteiros
SMALLINT                -- -32768 a 32767 (2 bytes)
INTEGER, INT            -- -2147483648 a 2147483647 (4 bytes)
BIGINT                  -- -9223372036854775808 a ... (8 bytes)

-- Auto incremento
SMALLSERIAL             -- SMALLINT com auto incremento
SERIAL                  -- INTEGER com auto incremento
BIGSERIAL               -- BIGINT com auto incremento

-- Decimais
DECIMAL(p, s)           -- Precisão exata (p = total dígitos, s = decimais)
NUMERIC(p, s)           -- Igual a DECIMAL
REAL                    -- 6 dígitos decimais (4 bytes)
DOUBLE PRECISION        -- 15 dígitos decimais (8 bytes)

-- Monetário
MONEY                   -- Valor monetário com formatação local
```

### Tipos de Texto

```sql
-- Strings
CHAR(n)                 -- String tamanho fixo
VARCHAR(n)              -- String tamanho variável com limite
TEXT                    -- String sem limite (preferido)

-- Exemplos
CREATE TABLE exemplo (
  codigo CHAR(5),           -- Sempre 5 caracteres
  nome VARCHAR(100),        -- Até 100 caracteres
  descricao TEXT            -- Sem limite
);
```

### Tipos de Data e Hora

```sql
DATE                    -- Apenas data (4 bytes)
TIME                    -- Apenas hora sem timezone
TIME WITH TIME ZONE     -- Hora com timezone (TIMETZ)
TIMESTAMP               -- Data e hora sem timezone
TIMESTAMP WITH TIME ZONE -- Data e hora com timezone (TIMESTAMPTZ) - RECOMENDADO
INTERVAL                -- Intervalo de tempo

-- Exemplos
CREATE TABLE eventos (
  data DATE,
  hora TIME,
  criado_em TIMESTAMPTZ DEFAULT NOW(),
  duracao INTERVAL
);

INSERT INTO eventos VALUES (
  '2024-12-25',
  '14:30:00',
  NOW(),
  '2 hours 30 minutes'
);

-- Operações com INTERVAL
SELECT NOW() + INTERVAL '1 day';
SELECT NOW() - INTERVAL '1 week';
SELECT NOW() + INTERVAL '1 month 15 days';
```

### Tipos Booleanos

```sql
BOOLEAN                 -- true, false, null

-- Valores aceitos
-- TRUE: true, 't', 'true', 'y', 'yes', 'on', '1'
-- FALSE: false, 'f', 'false', 'n', 'no', 'off', '0'

CREATE TABLE usuarios (
  ativo BOOLEAN DEFAULT true,
  verificado BOOLEAN DEFAULT false
);
```

### UUID

```sql
-- Habilitar extensão
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Tipo UUID
UUID                    -- 128-bit universally unique identifier

-- Gerar UUID
SELECT uuid_generate_v4();              -- Random
SELECT uuid_generate_v1();              -- Time-based
SELECT gen_random_uuid();               -- Nativo do PostgreSQL 13+

-- Usar em tabela
CREATE TABLE usuarios (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  nome TEXT NOT NULL
);
```

### Arrays

```sql
-- Definir coluna array
CREATE TABLE posts (
  id SERIAL PRIMARY KEY,
  titulo TEXT,
  tags TEXT[],                          -- Array de texto
  notas INTEGER[]                       -- Array de inteiros
);

-- Inserir arrays
INSERT INTO posts (titulo, tags, notas) VALUES
  ('Post 1', ARRAY['tech', 'sql'], ARRAY[1, 2, 3]),
  ('Post 2', '{"web", "js"}', '{4, 5, 6}');        -- Sintaxe alternativa

-- Acessar elementos (índice começa em 1)
SELECT tags[1] FROM posts;              -- Primeiro elemento
SELECT tags[1:2] FROM posts;            -- Slice

-- Operadores de array
SELECT * FROM posts WHERE 'sql' = ANY(tags);       -- Contém elemento
SELECT * FROM posts WHERE tags @> ARRAY['sql'];    -- Contém todos
SELECT * FROM posts WHERE tags && ARRAY['sql','web']; -- Overlap (algum em comum)
SELECT * FROM posts WHERE tags <@ ARRAY['sql','web','tech']; -- Está contido

-- Funções de array
SELECT array_length(tags, 1) FROM posts;           -- Tamanho
SELECT array_cat(ARRAY[1,2], ARRAY[3,4]);          -- Concatenar
SELECT array_append(ARRAY[1,2], 3);                -- Adicionar elemento
SELECT array_prepend(0, ARRAY[1,2]);               -- Adicionar no início
SELECT array_remove(ARRAY[1,2,3], 2);              -- Remover elemento
SELECT unnest(tags) FROM posts;                    -- Expandir para linhas
SELECT array_agg(nome) FROM usuarios;              -- Agregar em array

-- Array para string
SELECT array_to_string(tags, ', ') FROM posts;

-- String para array
SELECT string_to_array('a,b,c', ',');
```

### JSON e JSONB

```sql
-- Tipos JSON
JSON                    -- Armazena texto JSON (mais lento)
JSONB                   -- Armazena binário (mais rápido, recomendado)

-- Criar tabela com JSONB
CREATE TABLE produtos (
  id SERIAL PRIMARY KEY,
  nome TEXT,
  dados JSONB
);

-- Inserir JSON
INSERT INTO produtos (nome, dados) VALUES
  ('Produto 1', '{"preco": 99.90, "estoque": 100, "tags": ["novo", "promoção"]}'),
  ('Produto 2', '{"preco": 149.90, "estoque": 50, "specs": {"cor": "azul", "tamanho": "M"}}');

-- Operadores de acesso
SELECT dados->'preco' FROM produtos;              -- Retorna JSON
SELECT dados->>'preco' FROM produtos;             -- Retorna TEXT
SELECT dados->'specs'->'cor' FROM produtos;       -- Acesso aninhado
SELECT dados->>'specs'->>'cor' FROM produtos;     -- Aninhado como TEXT
SELECT dados#>'{specs,cor}' FROM produtos;        -- Caminho como array
SELECT dados#>>'{specs,cor}' FROM produtos;       -- Caminho como TEXT

-- Operadores de busca
SELECT * FROM produtos WHERE dados ? 'preco';                    -- Tem chave?
SELECT * FROM produtos WHERE dados ?| ARRAY['preco','estoque']; -- Tem alguma chave?
SELECT * FROM produtos WHERE dados ?& ARRAY['preco','estoque']; -- Tem todas as chaves?
SELECT * FROM produtos WHERE dados @> '{"preco": 99.90}';       -- Contém?
SELECT * FROM produtos WHERE dados <@ '{"preco": 99.90}';       -- Está contido?

-- Comparar valores
SELECT * FROM produtos WHERE (dados->>'preco')::NUMERIC > 100;
SELECT * FROM produtos WHERE dados->>'tags' ? 'novo';

-- Funções JSONB
SELECT jsonb_pretty(dados) FROM produtos;                        -- Formatado
SELECT jsonb_typeof(dados->'preco') FROM produtos;               -- Tipo do valor
SELECT jsonb_object_keys(dados) FROM produtos;                   -- Listar chaves
SELECT jsonb_each(dados) FROM produtos;                          -- Key-value pairs
SELECT jsonb_each_text(dados) FROM produtos;                     -- Key-value como texto
SELECT jsonb_array_elements(dados->'tags') FROM produtos;        -- Elementos do array
SELECT jsonb_array_length(dados->'tags') FROM produtos;          -- Tamanho do array

-- Modificar JSONB
SELECT dados || '{"novo_campo": true}' FROM produtos;            -- Merge
SELECT dados - 'estoque' FROM produtos;                          -- Remover chave
SELECT dados #- '{specs,cor}' FROM produtos;                     -- Remover caminho
SELECT jsonb_set(dados, '{preco}', '199.90') FROM produtos;      -- Atualizar valor
SELECT jsonb_insert(dados, '{tags, 0}', '"destaque"') FROM produtos; -- Inserir em array

-- UPDATE com JSONB
UPDATE produtos SET dados = dados || '{"ativo": true}';
UPDATE produtos SET dados = jsonb_set(dados, '{preco}', '199.90');

-- Índice para JSONB
CREATE INDEX idx_produtos_dados ON produtos USING GIN (dados);
CREATE INDEX idx_produtos_preco ON produtos ((dados->>'preco'));
```

### Outros Tipos

```sql
-- Geométricos
POINT                   -- Ponto (x, y)
LINE                    -- Linha infinita
LSEG                    -- Segmento de linha
BOX                     -- Retângulo
PATH                    -- Caminho (aberto ou fechado)
POLYGON                 -- Polígono
CIRCLE                  -- Círculo

-- Rede
INET                    -- Endereço IPv4 ou IPv6
CIDR                    -- Endereço de rede
MACADDR                 -- Endereço MAC

-- Bit String
BIT(n)                  -- String de bits tamanho fixo
BIT VARYING(n)          -- String de bits tamanho variável

-- Range
INT4RANGE               -- Range de integer
INT8RANGE               -- Range de bigint
NUMRANGE                -- Range de numeric
TSRANGE                 -- Range de timestamp
TSTZRANGE               -- Range de timestamptz
DATERANGE               -- Range de date

-- Exemplo de Range
CREATE TABLE reservas (
  id SERIAL PRIMARY KEY,
  sala TEXT,
  periodo TSTZRANGE
);

INSERT INTO reservas (sala, periodo) VALUES
  ('Sala 1', '[2024-01-01 09:00, 2024-01-01 12:00)');

-- Verificar sobreposição
SELECT * FROM reservas
WHERE periodo && '[2024-01-01 10:00, 2024-01-01 11:00)';

-- Enum
CREATE TYPE status_pedido AS ENUM ('pendente', 'processando', 'enviado', 'entregue');

CREATE TABLE pedidos (
  id SERIAL PRIMARY KEY,
  status status_pedido DEFAULT 'pendente'
);

-- Composite Type
CREATE TYPE endereco AS (
  rua TEXT,
  cidade TEXT,
  cep TEXT
);

CREATE TABLE clientes (
  id SERIAL PRIMARY KEY,
  nome TEXT,
  endereco_entrega endereco
);

INSERT INTO clientes (nome, endereco_entrega) VALUES
  ('João', ROW('Rua A, 123', 'São Paulo', '01234-567'));

SELECT (endereco_entrega).cidade FROM clientes;
```

## Recursos Exclusivos do PostgreSQL

### UPSERT (INSERT ON CONFLICT)

```sql
-- Inserir ou atualizar
INSERT INTO usuarios (email, nome, visitas)
VALUES ('joao@email.com', 'João', 1)
ON CONFLICT (email)
DO UPDATE SET
  nome = EXCLUDED.nome,
  visitas = usuarios.visitas + 1;

-- Inserir ou ignorar
INSERT INTO usuarios (email, nome)
VALUES ('joao@email.com', 'João')
ON CONFLICT (email) DO NOTHING;

-- Com constraint específica
INSERT INTO produtos (sku, nome, preco)
VALUES ('ABC123', 'Produto', 99.90)
ON CONFLICT ON CONSTRAINT produtos_sku_key
DO UPDATE SET preco = EXCLUDED.preco;

-- Com WHERE
INSERT INTO usuarios (email, nome, ativo)
VALUES ('joao@email.com', 'João', true)
ON CONFLICT (email)
DO UPDATE SET nome = EXCLUDED.nome
WHERE usuarios.ativo = true;
```

### RETURNING

```sql
-- Retornar dados após INSERT
INSERT INTO usuarios (nome, email)
VALUES ('João', 'joao@email.com')
RETURNING id, criado_em;

-- Retornar tudo
INSERT INTO usuarios (nome, email)
VALUES ('Maria', 'maria@email.com')
RETURNING *;

-- Retornar após UPDATE
UPDATE usuarios SET ativo = false WHERE id = 1
RETURNING nome, email;

-- Retornar após DELETE
DELETE FROM logs WHERE criado_em < NOW() - INTERVAL '30 days'
RETURNING id, mensagem;

-- Usar em CTE
WITH usuario_inserido AS (
  INSERT INTO usuarios (nome, email)
  VALUES ('Pedro', 'pedro@email.com')
  RETURNING id
)
INSERT INTO perfis (usuario_id, bio)
SELECT id, 'Novo usuário' FROM usuario_inserido;
```

### LATERAL Joins

```sql
-- Subquery correlacionada como JOIN
SELECT u.nome, p.titulo, p.criado_em
FROM usuarios u
CROSS JOIN LATERAL (
  SELECT titulo, criado_em
  FROM posts
  WHERE posts.usuario_id = u.id
  ORDER BY criado_em DESC
  LIMIT 3
) p;

-- Top N por grupo
SELECT d.nome AS departamento, f.nome, f.salario
FROM departamentos d
CROSS JOIN LATERAL (
  SELECT nome, salario
  FROM funcionarios
  WHERE departamento_id = d.id
  ORDER BY salario DESC
  LIMIT 3
) f;

-- Com função que retorna set
SELECT *
FROM produtos p,
LATERAL unnest(p.tags) AS tag;
```

### Generate Series

```sql
-- Gerar sequência de números
SELECT generate_series(1, 10);
SELECT generate_series(1, 10, 2);      -- Com step

-- Gerar sequência de datas
SELECT generate_series(
  '2024-01-01'::DATE,
  '2024-12-31'::DATE,
  '1 month'::INTERVAL
);

-- Relatório de vendas por dia (incluindo dias sem vendas)
SELECT
  d.dia,
  COALESCE(SUM(v.valor), 0) AS total
FROM generate_series(
  '2024-01-01'::DATE,
  '2024-01-31'::DATE,
  '1 day'::INTERVAL
) AS d(dia)
LEFT JOIN vendas v ON DATE(v.data) = d.dia
GROUP BY d.dia
ORDER BY d.dia;

-- Gerar dados de teste
INSERT INTO usuarios (nome, email, criado_em)
SELECT
  'Usuario ' || n,
  'usuario' || n || '@email.com',
  NOW() - (n || ' days')::INTERVAL
FROM generate_series(1, 100) AS n;
```

### Full-Text Search

```sql
-- Criar índice de texto
CREATE INDEX idx_posts_busca ON posts
USING GIN (to_tsvector('portuguese', titulo || ' ' || conteudo));

-- Buscar texto
SELECT * FROM posts
WHERE to_tsvector('portuguese', titulo || ' ' || conteudo)
   @@ to_tsquery('portuguese', 'postgresql & banco');

-- Com ranking
SELECT
  titulo,
  ts_rank(to_tsvector('portuguese', titulo || ' ' || conteudo),
          to_tsquery('portuguese', 'postgresql')) AS rank
FROM posts
WHERE to_tsvector('portuguese', titulo || ' ' || conteudo)
   @@ to_tsquery('portuguese', 'postgresql')
ORDER BY rank DESC;

-- Coluna tsvector materializada
ALTER TABLE posts ADD COLUMN busca_vetor TSVECTOR;

UPDATE posts SET busca_vetor =
  to_tsvector('portuguese', titulo || ' ' || COALESCE(conteudo, ''));

CREATE INDEX idx_posts_busca_vetor ON posts USING GIN (busca_vetor);

-- Trigger para manter atualizado
CREATE FUNCTION posts_busca_trigger() RETURNS TRIGGER AS $$
BEGIN
  NEW.busca_vetor := to_tsvector('portuguese', NEW.titulo || ' ' || COALESCE(NEW.conteudo, ''));
  RETURN NEW;
END
$$ LANGUAGE plpgsql;

CREATE TRIGGER tr_posts_busca
BEFORE INSERT OR UPDATE ON posts
FOR EACH ROW EXECUTE FUNCTION posts_busca_trigger();

-- Operadores de busca
'postgresql & database'     -- AND
'postgresql | mysql'        -- OR
'!mysql'                    -- NOT
'postgre:*'                 -- Prefixo
'postgresql <-> database'   -- Seguido por
```

### Particionamento de Tabelas

```sql
-- Criar tabela particionada por RANGE
CREATE TABLE vendas (
  id SERIAL,
  data TIMESTAMPTZ NOT NULL,
  valor DECIMAL(10,2),
  produto_id INT
) PARTITION BY RANGE (data);

-- Criar partições
CREATE TABLE vendas_2024_01 PARTITION OF vendas
  FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');

CREATE TABLE vendas_2024_02 PARTITION OF vendas
  FOR VALUES FROM ('2024-02-01') TO ('2024-03-01');

-- Partição default
CREATE TABLE vendas_default PARTITION OF vendas DEFAULT;

-- Particionamento por LIST
CREATE TABLE logs (
  id SERIAL,
  nivel TEXT,
  mensagem TEXT
) PARTITION BY LIST (nivel);

CREATE TABLE logs_info PARTITION OF logs
  FOR VALUES IN ('INFO', 'DEBUG');

CREATE TABLE logs_error PARTITION OF logs
  FOR VALUES IN ('ERROR', 'CRITICAL');

-- Particionamento por HASH
CREATE TABLE sessoes (
  id SERIAL,
  usuario_id INT,
  dados JSONB
) PARTITION BY HASH (usuario_id);

CREATE TABLE sessoes_0 PARTITION OF sessoes
  FOR VALUES WITH (MODULUS 4, REMAINDER 0);
CREATE TABLE sessoes_1 PARTITION OF sessoes
  FOR VALUES WITH (MODULUS 4, REMAINDER 1);
CREATE TABLE sessoes_2 PARTITION OF sessoes
  FOR VALUES WITH (MODULUS 4, REMAINDER 2);
CREATE TABLE sessoes_3 PARTITION OF sessoes
  FOR VALUES WITH (MODULUS 4, REMAINDER 3);

-- Desanexar partição
ALTER TABLE vendas DETACH PARTITION vendas_2024_01;

-- Anexar partição
ALTER TABLE vendas ATTACH PARTITION vendas_2024_01
  FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');
```

### Herança de Tabelas

```sql
-- Tabela pai
CREATE TABLE veiculos (
  id SERIAL PRIMARY KEY,
  marca TEXT,
  modelo TEXT,
  ano INT
);

-- Tabela filha
CREATE TABLE carros (
  num_portas INT,
  tipo_combustivel TEXT
) INHERITS (veiculos);

CREATE TABLE motos (
  cilindradas INT
) INHERITS (veiculos);

-- Inserir em tabela filha
INSERT INTO carros (marca, modelo, ano, num_portas, tipo_combustivel)
VALUES ('Toyota', 'Corolla', 2024, 4, 'flex');

-- Consultar todos os veículos
SELECT * FROM veiculos;           -- Inclui carros e motos

-- Consultar apenas a tabela pai
SELECT * FROM ONLY veiculos;      -- Apenas veículos diretos
```

## Schemas

```sql
-- Criar schema
CREATE SCHEMA vendas;
CREATE SCHEMA IF NOT EXISTS vendas;

-- Criar schema com owner
CREATE SCHEMA vendas AUTHORIZATION usuario;

-- Listar schemas
\dn
SELECT schema_name FROM information_schema.schemata;

-- Criar tabela em schema
CREATE TABLE vendas.pedidos (
  id SERIAL PRIMARY KEY,
  valor DECIMAL(10,2)
);

-- Acessar tabela
SELECT * FROM vendas.pedidos;

-- Definir search_path
SET search_path TO vendas, public;
SHOW search_path;

-- Alterar search_path permanente para role
ALTER ROLE usuario SET search_path TO vendas, public;

-- Mover tabela para outro schema
ALTER TABLE pedidos SET SCHEMA vendas;

-- Excluir schema
DROP SCHEMA vendas;
DROP SCHEMA vendas CASCADE;      -- Com todos os objetos
```

## Extensões

```sql
-- Listar extensões disponíveis
SELECT * FROM pg_available_extensions;

-- Listar extensões instaladas
\dx
SELECT * FROM pg_extension;

-- Instalar extensões úteis
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";       -- UUIDs
CREATE EXTENSION IF NOT EXISTS "pgcrypto";        -- Criptografia
CREATE EXTENSION IF NOT EXISTS "pg_trgm";         -- Trigram (busca fuzzy)
CREATE EXTENSION IF NOT EXISTS "unaccent";        -- Remover acentos
CREATE EXTENSION IF NOT EXISTS "citext";          -- Case-insensitive text
CREATE EXTENSION IF NOT EXISTS "hstore";          -- Key-value storage
CREATE EXTENSION IF NOT EXISTS "tablefunc";       -- Crosstab
CREATE EXTENSION IF NOT EXISTS "pg_stat_statements"; -- Estatísticas de queries

-- pg_trgm - Busca por similaridade
CREATE INDEX idx_nome_trgm ON usuarios USING GIN (nome gin_trgm_ops);

SELECT * FROM usuarios WHERE nome % 'Joao';       -- Similar
SELECT similarity('João', 'Joao');                -- Score de similaridade
SELECT * FROM usuarios ORDER BY nome <-> 'Joao' LIMIT 10; -- Mais similares

-- unaccent
SELECT unaccent('São Paulo');                     -- 'Sao Paulo'

-- citext - Case insensitive
CREATE TABLE usuarios (
  email CITEXT UNIQUE
);
```

## Roles e Permissões

```sql
-- Criar role (usuário)
CREATE ROLE usuario LOGIN PASSWORD 'senha123';
CREATE USER usuario WITH PASSWORD 'senha123';     -- Alias

-- Criar role com opções
CREATE ROLE admin WITH
  LOGIN
  PASSWORD 'senha123'
  SUPERUSER
  CREATEDB
  CREATEROLE
  VALID UNTIL '2025-01-01';

-- Criar grupo (role sem login)
CREATE ROLE desenvolvedores;

-- Adicionar usuário a grupo
GRANT desenvolvedores TO usuario;

-- Alterar role
ALTER ROLE usuario WITH PASSWORD 'nova_senha';
ALTER ROLE usuario WITH SUPERUSER;
ALTER ROLE usuario RENAME TO novo_nome;

-- Excluir role
DROP ROLE usuario;
DROP ROLE IF EXISTS usuario;

-- Listar roles
\du
SELECT * FROM pg_roles;

-- Permissões em banco
GRANT ALL PRIVILEGES ON DATABASE banco TO usuario;
GRANT CONNECT ON DATABASE banco TO usuario;
REVOKE ALL PRIVILEGES ON DATABASE banco FROM usuario;

-- Permissões em schema
GRANT USAGE ON SCHEMA vendas TO usuario;
GRANT CREATE ON SCHEMA vendas TO usuario;
GRANT ALL ON SCHEMA vendas TO usuario;

-- Permissões em tabelas
GRANT SELECT ON tabela TO usuario;
GRANT SELECT, INSERT, UPDATE ON tabela TO usuario;
GRANT ALL PRIVILEGES ON tabela TO usuario;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO usuario;

-- Permissões default (para objetos futuros)
ALTER DEFAULT PRIVILEGES IN SCHEMA public
  GRANT SELECT ON TABLES TO usuario;

ALTER DEFAULT PRIVILEGES IN SCHEMA public
  GRANT USAGE ON SEQUENCES TO usuario;

-- Revogar permissões
REVOKE SELECT ON tabela FROM usuario;
REVOKE ALL PRIVILEGES ON tabela FROM usuario;

-- Ver permissões
\dp tabela
\z tabela
SELECT * FROM information_schema.table_privileges WHERE table_name = 'tabela';
```

## Backup e Restore

### pg_dump

```bash
# Backup de um banco (formato SQL)
pg_dump banco > backup.sql
pg_dump -U usuario -h host banco > backup.sql

# Backup comprimido
pg_dump -Fc banco > backup.dump           # Custom format (recomendado)
pg_dump -Ft banco > backup.tar            # Tar format
pg_dump -Fd banco -f backup_dir/          # Directory format

# Backup apenas schema
pg_dump -s banco > schema.sql

# Backup apenas dados
pg_dump -a banco > dados.sql

# Backup de tabelas específicas
pg_dump -t tabela1 -t tabela2 banco > tabelas.sql

# Excluir tabelas
pg_dump --exclude-table=logs banco > backup.sql

# Backup com INSERT statements
pg_dump --inserts banco > backup.sql

# Backup de todos os bancos
pg_dumpall > todos_bancos.sql
pg_dumpall --globals-only > roles.sql     # Apenas roles
```

### pg_restore

```bash
# Restaurar de SQL
psql banco < backup.sql
psql -U usuario -d banco -f backup.sql

# Restaurar de custom format
pg_restore -d banco backup.dump
pg_restore -U usuario -h host -d banco backup.dump

# Criar banco e restaurar
pg_restore -C -d postgres backup.dump

# Restaurar apenas schema
pg_restore -s -d banco backup.dump

# Restaurar tabela específica
pg_restore -t tabela -d banco backup.dump

# Restaurar em paralelo (mais rápido)
pg_restore -j 4 -d banco backup.dump

# Ver conteúdo do backup
pg_restore -l backup.dump
```

### COPY (Import/Export)

```sql
-- Exportar para CSV
COPY usuarios TO '/tmp/usuarios.csv' WITH CSV HEADER;

-- Exportar query
COPY (SELECT id, nome FROM usuarios WHERE ativo = true)
TO '/tmp/usuarios_ativos.csv' WITH CSV HEADER;

-- Importar de CSV
COPY usuarios FROM '/tmp/usuarios.csv' WITH CSV HEADER;

-- Importar com opções
COPY usuarios FROM '/tmp/usuarios.csv'
WITH (FORMAT CSV, HEADER true, DELIMITER ';', NULL 'NULL');

-- \copy (client-side, não precisa superuser)
\copy usuarios TO 'usuarios.csv' WITH CSV HEADER
\copy usuarios FROM 'usuarios.csv' WITH CSV HEADER

-- Exportar como JSON
COPY (SELECT row_to_json(u) FROM usuarios u)
TO '/tmp/usuarios.json';
```

## Performance e Manutenção

### EXPLAIN e ANALYZE

```sql
-- Ver plano de execução
EXPLAIN SELECT * FROM usuarios WHERE email = 'joao@email.com';

-- Executar e mostrar tempos reais
EXPLAIN ANALYZE SELECT * FROM usuarios WHERE email = 'joao@email.com';

-- Com mais detalhes
EXPLAIN (ANALYZE, BUFFERS, FORMAT TEXT)
SELECT * FROM usuarios WHERE email = 'joao@email.com';

-- Formato JSON
EXPLAIN (ANALYZE, FORMAT JSON)
SELECT * FROM usuarios WHERE email = 'joao@email.com';

-- Analisar sem executar
EXPLAIN (COSTS, VERBOSE)
SELECT * FROM usuarios WHERE email = 'joao@email.com';
```

### VACUUM e ANALYZE

```sql
-- Vacuum (recuperar espaço e atualizar visibility map)
VACUUM usuarios;
VACUUM VERBOSE usuarios;

-- Vacuum full (recupera todo espaço, mais lento, bloqueia tabela)
VACUUM FULL usuarios;

-- Vacuum em todo o banco
VACUUM;

-- Analyze (atualizar estatísticas)
ANALYZE usuarios;
ANALYZE usuarios (nome, email);    -- Colunas específicas

-- Vacuum e Analyze juntos
VACUUM ANALYZE usuarios;

-- Verificar quando foi última manutenção
SELECT
  schemaname,
  relname,
  last_vacuum,
  last_autovacuum,
  last_analyze,
  last_autoanalyze
FROM pg_stat_user_tables;

-- Configurar autovacuum por tabela
ALTER TABLE logs SET (
  autovacuum_vacuum_scale_factor = 0.1,
  autovacuum_analyze_scale_factor = 0.05
);
```

### Estatísticas e Monitoramento

```sql
-- Estatísticas de tabelas
SELECT * FROM pg_stat_user_tables;

-- Estatísticas de índices
SELECT * FROM pg_stat_user_indexes;

-- Queries em execução
SELECT
  pid,
  now() - pg_stat_activity.query_start AS duration,
  query,
  state
FROM pg_stat_activity
WHERE state != 'idle'
ORDER BY duration DESC;

-- Cancelar query
SELECT pg_cancel_backend(pid);

-- Terminar conexão
SELECT pg_terminate_backend(pid);

-- Conexões por banco
SELECT datname, count(*) FROM pg_stat_activity GROUP BY datname;

-- Bloqueios
SELECT * FROM pg_locks WHERE NOT granted;

-- Tamanho de tabelas
SELECT
  relname AS tabela,
  pg_size_pretty(pg_total_relation_size(relid)) AS tamanho_total,
  pg_size_pretty(pg_relation_size(relid)) AS tamanho_dados,
  pg_size_pretty(pg_indexes_size(relid)) AS tamanho_indices
FROM pg_catalog.pg_statio_user_tables
ORDER BY pg_total_relation_size(relid) DESC;

-- Tamanho do banco
SELECT pg_size_pretty(pg_database_size('meu_banco'));

-- Índices não utilizados
SELECT
  indexrelname,
  idx_scan,
  pg_size_pretty(pg_relation_size(indexrelid)) AS tamanho
FROM pg_stat_user_indexes
WHERE idx_scan = 0
ORDER BY pg_relation_size(indexrelid) DESC;

-- Cache hit ratio
SELECT
  sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) AS ratio
FROM pg_statio_user_tables;
```

## Configuração

### postgresql.conf

```conf
# Memória
shared_buffers = 256MB              # 25% da RAM
work_mem = 64MB                     # Por operação de sort/hash
maintenance_work_mem = 512MB        # Para VACUUM, CREATE INDEX
effective_cache_size = 1GB          # 75% da RAM

# Conexões
max_connections = 100
superuser_reserved_connections = 3

# WAL
wal_level = replica
max_wal_size = 1GB
min_wal_size = 80MB

# Logging
log_destination = 'stderr'
logging_collector = on
log_directory = 'log'
log_filename = 'postgresql-%Y-%m-%d.log'
log_statement = 'all'               # none, ddl, mod, all
log_min_duration_statement = 1000   # Log queries > 1s

# Autovacuum
autovacuum = on
autovacuum_vacuum_cost_delay = 2ms
```

### pg_hba.conf

```conf
# Tipo    Banco     Usuário    Endereço         Método
local     all       postgres                    peer
local     all       all                         md5
host      all       all        127.0.0.1/32     md5
host      all       all        ::1/128          md5
host      all       all        192.168.0.0/24   md5
host      replicacao repl       192.168.0.2/32   md5
```

### Recarregar configuração

```sql
SELECT pg_reload_conf();
```

```bash
sudo systemctl reload postgresql
```

## Funções Úteis

```sql
-- Informações do sistema
SELECT version();                               -- Versão do PostgreSQL
SELECT current_database();                      -- Banco atual
SELECT current_schema();                        -- Schema atual
SELECT current_user;                            -- Usuário atual
SELECT session_user;                            -- Usuário da sessão
SELECT inet_server_addr();                      -- IP do servidor
SELECT inet_server_port();                      -- Porta do servidor

-- Conversão
SELECT 'abc'::TEXT;
SELECT CAST('123' AS INTEGER);
SELECT to_char(123.45, 'FM999.00');
SELECT to_number('$1,234.56', 'L9G999D99');
SELECT to_date('25/12/2024', 'DD/MM/YYYY');
SELECT to_timestamp('2024-12-25 10:30', 'YYYY-MM-DD HH24:MI');

-- Expressões regulares
SELECT 'teste' ~ 'est';                         -- Match
SELECT 'teste' ~* 'EST';                        -- Match case-insensitive
SELECT regexp_match('abc123', '([0-9]+)');      -- Captura
SELECT regexp_matches('a1b2c3', '([0-9])', 'g'); -- Todas as capturas
SELECT regexp_replace('abc123', '[0-9]', 'X', 'g'); -- Substituir

-- Agregação
SELECT string_agg(nome, ', ') FROM usuarios;
SELECT array_agg(nome) FROM usuarios;
SELECT json_agg(row_to_json(u)) FROM usuarios u;
SELECT jsonb_agg(to_jsonb(u)) FROM usuarios u;

-- Condicionais
SELECT GREATEST(1, 5, 3);                       -- 5
SELECT LEAST(1, 5, 3);                          -- 1
SELECT NULLIF(valor, 0);                        -- NULL se valor = 0
SELECT COALESCE(a, b, c);                       -- Primeiro não-null

-- Manipulação de rows
SELECT row_to_json(u) FROM usuarios u;
SELECT to_jsonb(u) FROM usuarios u;
SELECT hstore(u) FROM usuarios u;               -- Requer extensão hstore
```

## Dicas e Boas Práticas

### Nomenclatura
- Use **snake_case** para nomes
- Prefira **singular** para tabelas (usuario, não usuarios)
- Prefixe índices com `idx_`, constraints com `pk_`, `fk_`, `uk_`, `ck_`
- Use nomes descritivos

### Performance
- Use `TIMESTAMPTZ` ao invés de `TIMESTAMP`
- Prefira `TEXT` a `VARCHAR` (mesma performance, sem limite artificial)
- Use `JSONB` ao invés de `JSON`
- Crie índices para colunas em WHERE, JOIN e ORDER BY
- Use `EXPLAIN ANALYZE` para otimizar queries
- Configure `work_mem` adequadamente
- Use particionamento para tabelas grandes

### Segurança
- Nunca use o usuário `postgres` em aplicações
- Use roles com permissões mínimas necessárias
- Use `pg_hba.conf` para controlar acessos
- Habilite SSL para conexões remotas
- Use prepared statements (evitar SQL injection)

### Manutenção
- Configure `autovacuum` adequadamente
- Monitore logs regularmente
- Faça backups diários com `pg_dump`
- Teste restauração de backups periodicamente
- Atualize versões regularmente

## Links Úteis

| Recurso | URL |
|---------|-----|
| Documentação Oficial | https://www.postgresql.org/docs |
| Wiki | https://wiki.postgresql.org |
| PostgreSQL Tutorial | https://www.postgresqltutorial.com |
| PgExercises | https://pgexercises.com |
| Awesome Postgres | https://github.com/dhamaniasad/awesome-postgres |
| Use The Index, Luke | https://use-the-index-luke.com |
| pgAdmin | https://www.pgadmin.org |
| DBeaver | https://dbeaver.io |
