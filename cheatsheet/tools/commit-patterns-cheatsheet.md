# Padrões de Commit - Cheatsheet

## Conventional Commits

O **Conventional Commits** é uma convenção para mensagens de commit que facilita a leitura do histórico, geração de changelogs automáticos e versionamento semântico.

### Estrutura Básica

```
<tipo>[escopo opcional]: <descrição>

[corpo opcional]

[rodapé(s) opcional(is)]
```

### Estrutura com Emoji

```
<emoji> <tipo>[escopo opcional]: <descrição>

[corpo opcional]

[rodapé(s) opcional(is)]
```

## Tipos de Commit

| Tipo | Emoji | Descrição | Semver |
|------|-------|-----------|--------|
| **feat** | ✨ `:sparkles:` | Adiciona uma nova funcionalidade | MINOR |
| **fix** | 🐛 `:bug:` | Corrige um bug | PATCH |
| **docs** | 📚 `:books:` | Alterações na documentação | - |
| **style** | 💄 `:lipstick:` | Formatação de código (espaços, vírgulas, etc) | - |
| **refactor** | ♻️ `:recycle:` | Refatoração sem alterar funcionalidade | - |
| **perf** | ⚡ `:zap:` | Melhoria de performance | PATCH |
| **test** | 🧪 `:test_tube:` | Adiciona ou modifica testes | - |
| **build** | 📦 `:package:` | Alterações no build ou dependências | - |
| **ci** | 🧱 `:bricks:` | Alterações na configuração de CI/CD | - |
| **chore** | 🔧 `:wrench:` | Tarefas de manutenção e configurações | - |
| **revert** | ⏪ `:rewind:` | Reverte um commit anterior | - |
| **raw** | 🗃️ `:card_file_box:` | Alterações em arquivos de config/dados | - |
| **cleanup** | 🧹 `:broom:` | Remove código comentado ou desnecessário | - |
| **remove** | 🗑️ `:wastebasket:` | Remove arquivos, diretórios ou funcionalidades | - |

## Emojis Especiais

| Emoji | Código | Uso |
|-------|--------|-----|
| 🎉 | `:tada:` | Commit inicial do projeto |
| 🔖 | `:bookmark:` | Release/versão tag |
| 🚀 | `:rocket:` | Deploy de aplicação |
| 🔒️ | `:lock:` | Correção de segurança |
| 🔐 | `:closed_lock_with_key:` | Adiciona/atualiza secrets |
| 🚨 | `:rotating_light:` | Remove warnings do linter |
| 🚧 | `:construction:` | Trabalho em progresso (WIP) |
| 💥 | `:boom:` | Breaking changes |
| 🔥 | `:fire:` | Remove código ou arquivos |
| ✅ | `:white_check_mark:` | Adiciona/atualiza/passa nos testes |
| 🎨 | `:art:` | Melhora estrutura/formato do código |
| 📝 | `:memo:` | Adiciona/atualiza documentação |
| 🚑️ | `:ambulance:` | Hotfix crítico |
| 💬 | `:speech_balloon:` | Adiciona/atualiza textos e literais |
| 🌐 | `:globe_with_meridians:` | Internacionalização e localização |
| ♿ | `:wheelchair:` | Melhora acessibilidade |
| 🔍️ | `:mag:` | Melhora SEO |
| 🏗️ | `:building_construction:` | Mudanças na arquitetura |
| 📱 | `:iphone:` | Trabalho em design responsivo |
| 🤡 | `:clown_face:` | Mock de dados |
| 🥚 | `:egg:` | Adiciona easter egg |
| 🙈 | `:see_no_evil:` | Adiciona/atualiza .gitignore |
| 📸 | `:camera_flash:` | Adiciona/atualiza snapshots |
| ⚗️ | `:alembic:` | Experimenta coisas novas |
| 🔊 | `:loud_sound:` | Adiciona/atualiza logs |
| 🔇 | `:mute:` | Remove logs |
| 👥 | `:busts_in_silhouette:` | Adiciona/atualiza contribuidores |
| 🍱 | `:bento:` | Adiciona/atualiza assets |
| ⚰️ | `:coffin:` | Remove código morto |
| 🧪 | `:test_tube:` | Adiciona teste que falha |
| 💡 | `:bulb:` | Adiciona/atualiza comentários no código |
| 🗃️ | `:card_file_box:` | Mudanças relacionadas ao banco de dados |
| 📈 | `:chart_with_upwards_trend:` | Adiciona analytics ou tracking |
| ➕ | `:heavy_plus_sign:` | Adiciona dependência |
| ➖ | `:heavy_minus_sign:` | Remove dependência |
| 🔧 | `:wrench:` | Adiciona/atualiza arquivos de config |
| 🔨 | `:hammer:` | Adiciona/atualiza scripts de dev |
| 🌱 | `:seedling:` | Adiciona/atualiza seed files |
| 🚩 | `:triangular_flag_on_post:` | Adiciona/atualiza feature flags |
| 🥅 | `:goal_net:` | Captura erros |
| 💫 | `:dizzy:` | Adiciona/atualiza animações |
| 🗑️ | `:wastebasket:` | Deprecia código |
| 🛂 | `:passport_control:` | Trabalho relacionado a autorização/roles |
| 🩹 | `:adhesive_bandage:` | Fix simples para problema não crítico |
| 🧐 | `:monocle_face:` | Exploração/inspeção de dados |
| ⚰️ | `:coffin:` | Remove código morto |
| 🧑‍💻 | `:technologist:` | Melhora experiência do desenvolvedor |
| 💸 | `:money_with_wings:` | Adiciona código relacionado a pagamentos |
| 🧵 | `:thread:` | Adiciona/atualiza código relacionado a multithreading |
| 🦺 | `:safety_vest:` | Adiciona/atualiza código relacionado a validação |

## Exemplos de Commits

### Sem Emoji

```bash
# Feature
git commit -m "feat: adiciona sistema de autenticação"
git commit -m "feat(auth): implementa login com Google"

# Fix
git commit -m "fix: corrige erro ao salvar usuário"
git commit -m "fix(api): resolve timeout em requisições longas"

# Docs
git commit -m "docs: atualiza README com instruções de instalação"

# Style
git commit -m "style: formata código com Prettier"
git commit -m "style(header): ajusta espaçamento dos elementos"

# Refactor
git commit -m "refactor: simplifica lógica de validação"
git commit -m "refactor(utils): extrai função de formatação de data"

# Performance
git commit -m "perf: otimiza carregamento de imagens"
git commit -m "perf(database): adiciona índice na tabela users"

# Test
git commit -m "test: adiciona testes para UserService"
git commit -m "test(auth): aumenta cobertura de testes de login"

# Build
git commit -m "build: atualiza dependências do projeto"
git commit -m "build(deps): atualiza React para versão 18"

# CI
git commit -m "ci: adiciona workflow de deploy automático"
git commit -m "ci(github): configura actions para rodar testes"

# Chore
git commit -m "chore: atualiza configuração do ESLint"
git commit -m "chore(scripts): adiciona comando de limpeza"

# Revert
git commit -m "revert: reverte commit abc123"
```

### Com Emoji

```bash
# Feature
git commit -m "✨ feat: adiciona sistema de autenticação"
git commit -m "✨ feat(auth): implementa login com Google"

# Fix
git commit -m "🐛 fix: corrige erro ao salvar usuário"
git commit -m "🐛 fix(api): resolve timeout em requisições longas"

# Docs
git commit -m "📚 docs: atualiza README com instruções de instalação"

# Style
git commit -m "💄 style: formata código com Prettier"

# Refactor
git commit -m "♻️ refactor: simplifica lógica de validação"

# Performance
git commit -m "⚡ perf: otimiza carregamento de imagens"

# Test
git commit -m "🧪 test: adiciona testes para UserService"

# Build
git commit -m "📦 build: atualiza dependências do projeto"

# CI
git commit -m "🧱 ci: adiciona workflow de deploy automático"

# Chore
git commit -m "🔧 chore: atualiza configuração do ESLint"

# Especiais
git commit -m "🎉 initial commit"
git commit -m "🚀 deploy: versão 1.0.0 em produção"
git commit -m "🔒️ fix: corrige vulnerabilidade XSS"
git commit -m "💥 refactor!: remove suporte ao IE11"
git commit -m "🚑️ fix: corrige erro crítico em produção"
```

### Com Escopo

```bash
git commit -m "feat(user): adiciona página de perfil"
git commit -m "fix(payment): corrige cálculo de juros"
git commit -m "docs(api): documenta endpoints de produtos"
git commit -m "style(navbar): ajusta cores do menu"
git commit -m "refactor(database): migra para PostgreSQL"
git commit -m "perf(images): implementa lazy loading"
git commit -m "test(login): adiciona teste de integração"
```

### Com Corpo e Rodapé

```bash
git commit -m "feat(auth): adiciona autenticação de dois fatores

Implementa sistema 2FA usando TOTP.
Adiciona QR code para configuração no app autenticador.
Valida código de 6 dígitos no login.

Refs: #123
Co-authored-by: João Silva <joao@email.com>"
```

### Breaking Changes

```bash
# Método 1: ! após o tipo
git commit -m "refactor!: remove suporte ao Node 12"

# Método 2: BREAKING CHANGE no rodapé
git commit -m "feat: migra para ESM modules

BREAKING CHANGE: Agora usa import/export ao invés de require/module.exports"

# Com emoji
git commit -m "💥 refactor!: remove API v1"
```

## Scopes Comuns

| Escopo | Uso |
|--------|-----|
| `api` | Alterações na API |
| `auth` | Autenticação e autorização |
| `database` ou `db` | Banco de dados |
| `ui` | Interface do usuário |
| `ux` | Experiência do usuário |
| `config` | Arquivos de configuração |
| `deps` | Dependências |
| `docker` | Configurações Docker |
| `tests` | Testes |
| `docs` | Documentação |
| `security` | Segurança |
| `i18n` | Internacionalização |
| `a11y` | Acessibilidade |
| `seo` | SEO |
| `analytics` | Analytics |
| `performance` ou `perf` | Performance |
| `release` | Release/deploy |

## Boas Práticas

### ✅ Fazer

- **Use o imperativo**: "adiciona" ao invés de "adicionado" ou "adicionando"
- **Seja conciso**: Máximo de 50-72 caracteres no título
- **Primeira letra minúscula**: Após o tipo e dois pontos
- **Sem ponto final**: Não adicione ponto no final da descrição
- **Use o corpo** para explicar o "porquê" e "como"
- **Referencie issues**: Use "Refs #123" ou "Closes #123"
- **Um commit por mudança lógica**: Não misture alterações não relacionadas
- **Commits frequentes**: Commits pequenos e focados

### ❌ Evitar

- Mensagens genéricas: "fix", "update", "changes"
- Commits muito grandes com muitas alterações
- Misturar refatoração com features
- Commitar código não testado
- Mensagens em primeira pessoa: "eu adicionei..."
- Usar abreviações excessivas

## Exemplos Práticos Completos

### Feature

```bash
# Simples
git commit -m "✨ feat: adiciona filtro de busca por categoria"

# Com escopo
git commit -m "✨ feat(products): adiciona ordenação por preço"

# Completo
git commit -m "✨ feat(cart): implementa carrinho de compras

- Adiciona/remove produtos
- Atualiza quantidade
- Calcula total automaticamente
- Persiste no localStorage

Closes #45"
```

### Bug Fix

```bash
# Simples
git commit -m "🐛 fix: corrige validação de email"

# Com escopo
git commit -m "🐛 fix(auth): resolve erro ao fazer logout"

# Completo
git commit -m "🐛 fix(checkout): corrige cálculo de frete

O cálculo estava considerando peso em gramas ao invés de kg.
Agora converte corretamente antes de consultar a API.

Fixes #78"
```

### Hotfix Crítico

```bash
git commit -m "🚑️ fix: corrige vazamento de memória em produção

Loop infinito no componente UserList causava crash.
Adiciona cleanup no useEffect.

Priority: HIGH
Refs #234"
```

### Refatoração

```bash
git commit -m "♻️ refactor(api): extrai lógica de autenticação para service

Move validação de token e refresh para AuthService.
Facilita reutilização e testes unitários.

Refs #89"
```

### Performance

```bash
git commit -m "⚡ perf(images): implementa lazy loading e WebP

- Lazy loading para imagens abaixo da dobra
- Conversão automática para WebP com fallback
- Reduz tempo de carregamento em 40%

Refs #156"
```

### Breaking Change

```bash
git commit -m "💥 refactor!: remove suporte ao Internet Explorer

BREAKING CHANGE: IE não é mais suportado.
Navegadores mínimos: Chrome 90+, Firefox 88+, Safari 14+

Closes #267"
```

## Ferramentas

### Commitizen

```bash
# Instalar globalmente
npm install -g commitizen cz-conventional-changelog

# Configurar no projeto
commitizen init cz-conventional-changelog --save-dev --save-exact

# Usar
git cz
# ou
npm run commit
```

### Commitlint

```bash
# Instalar
npm install --save-dev @commitlint/cli @commitlint/config-conventional

# Configurar (commitlint.config.js)
module.exports = {
  extends: ['@commitlint/config-conventional']
};

# Usar com husky
npm install --save-dev husky
npx husky add .husky/commit-msg 'npx --no -- commitlint --edit $1'
```

### Conventional Changelog

```bash
# Instalar
npm install --save-dev standard-version

# Adicionar no package.json
{
  "scripts": {
    "release": "standard-version"
  }
}

# Usar
npm run release
npm run release -- --release-as minor
npm run release -- --release-as major
```

### Gitmoji CLI

```bash
# Instalar
npm install -g gitmoji-cli

# Usar
gitmoji -c
```

## Conventional Commits + Semantic Versioning

| Tipo de Commit | Versão | Exemplo |
|----------------|--------|---------|
| `fix:` | PATCH | 1.0.0 → 1.0.1 |
| `feat:` | MINOR | 1.0.0 → 1.1.0 |
| `BREAKING CHANGE:` ou `!` | MAJOR | 1.0.0 → 2.0.0 |

### Exemplos

```bash
# PATCH (1.0.0 → 1.0.1)
git commit -m "fix: corrige bug na validação"

# MINOR (1.0.0 → 1.1.0)
git commit -m "feat: adiciona modo escuro"

# MAJOR (1.0.0 → 2.0.0)
git commit -m "refactor!: remove API v1"
git commit -m "feat: novo sistema de auth

BREAKING CHANGE: Tokens antigos não funcionam mais"
```

## Templates de Commit

### Template Simples (.gitmessage)

```
# <tipo>[escopo opcional]: <descrição>
#
# <corpo opcional>
#
# <rodapé opcional>
#
# Tipos:
#   feat:     Nova funcionalidade
#   fix:      Correção de bug
#   docs:     Documentação
#   style:    Formatação
#   refactor: Refatoração
#   perf:     Performance
#   test:     Testes
#   build:    Build
#   ci:       CI/CD
#   chore:    Manutenção
#
# Lembre-se:
#   - Use imperativo ("adiciona" não "adicionado")
#   - Máximo 50 caracteres no título
#   - Primeira letra minúscula
#   - Sem ponto final
```

### Configurar Template

```bash
# Criar arquivo .gitmessage no projeto
touch .gitmessage

# Configurar globalmente
git config --global commit.template ~/.gitmessage

# Ou apenas no projeto
git config commit.template .gitmessage
```

## Aliases do Git

```bash
# Adicionar aliases úteis
git config --global alias.feat '!git commit -m "✨ feat: $1"'
git config --global alias.fix '!git commit -m "🐛 fix: $1"'
git config --global alias.docs '!git commit -m "📚 docs: $1"'
git config --global alias.style '!git commit -m "💄 style: $1"'
git config --global alias.refactor '!git commit -m "♻️ refactor: $1"'
git config --global alias.perf '!git commit -m "⚡ perf: $1"'
git config --global alias.test '!git commit -m "🧪 test: $1"'
git config --global alias.chore '!git commit -m "🔧 chore: $1"'

# Usar
git feat "adiciona login social"
git fix "corrige erro de validação"
```

## Checklist de Commit

Antes de fazer commit, pergunte-se:

- [ ] A mensagem descreve claramente **o que** mudou?
- [ ] O tipo de commit está correto?
- [ ] O escopo está adequado (se aplicável)?
- [ ] A descrição está no imperativo?
- [ ] A descrição tem menos de 72 caracteres?
- [ ] O corpo explica **por que** a mudança foi feita (se necessário)?
- [ ] Issues/PRs relacionados estão referenciados?
- [ ] Breaking changes estão documentados?
- [ ] O código foi testado?
- [ ] O commit contém apenas mudanças relacionadas?

## Recursos Úteis

| Recurso | Descrição |
|---------|-----------|
| [Conventional Commits](https://www.conventionalcommits.org) | Especificação oficial |
| [Gitmoji](https://gitmoji.dev) | Guia de emojis para commits |
| [Commitizen](https://github.com/commitizen/cz-cli) | CLI para commits padronizados |
| [Commitlint](https://commitlint.js.org) | Validação de mensagens |
| [Semantic Release](https://semantic-release.gitbook.io) | Automação de releases |
| [Standard Version](https://github.com/conventional-changelog/standard-version) | Versionamento automático |
| [Husky](https://typicode.github.io/husky) | Git hooks |

## Links Úteis

| Recurso | URL |
|---------|-----|
| Conventional Commits | https://www.conventionalcommits.org |
| Gitmoji | https://gitmoji.dev |
| Padrões de Commits (PT-BR) | https://github.com/iuricode/padroes-de-commits |
| Semantic Versioning | https://semver.org |
| Commitizen | https://github.com/commitizen/cz-cli |
| Commitlint | https://commitlint.js.org |
