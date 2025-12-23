# 🌱 Git Cheatsheet

Guia rápido com os principais comandos **Git** para uso diário em projetos individuais ou em equipe.

---

## 🔹 Configuração Inicial

```bash
git --version
```
Verifica a versão do Git.

```bash
git config --global user.name "Seu Nome"
```
Define o nome do usuário.

```bash
git config --global user.email "seu@email.com"
```
Define o e-mail do usuário.

```bash
git config --list
```
Lista todas as configurações.

---

## 📁 Repositório

```bash
git init
```
Inicializa um repositório.

```bash
git clone https://github.com/user/repo.git
```
Clona um repositório remoto.

---

## 📄 Status e Histórico

```bash
git status
```
Mostra o status dos arquivos.

```bash
git log
```
Exibe o histórico de commits.

```bash
git log --oneline --graph --all
```
Histórico resumido e visual.

---

## ➕ Adicionar Arquivos

```bash
git add arquivo.txt
```
Adiciona um arquivo.

```bash
git add .
```
Adiciona todos os arquivos.

```bash
git restore arquivo.txt
```
Descarta alterações não adicionadas.

---

## 💾 Commit

```bash
git commit -m "mensagem"
```
Cria um commit.

```bash
git commit --amend
```
Edita o último commit.

---

## 🌿 Branches

```bash
git branch
```
Lista branches.

```bash
git branch nova-branch
```
Cria uma branch.

```bash
git checkout nova-branch
```
Troca de branch.

```bash
git checkout -b nova-branch
```
Cria e troca para a branch.

```bash
git switch main
```
Troca de branch (forma moderna).

```bash
git branch -d branch
```
Remove uma branch.

---

## 🔀 Merge e Rebase

```bash
git merge feature
```
Mescla uma branch na atual.

```bash
git rebase main
```
Reaplica commits sobre outra branch.

```bash
git rebase -i HEAD~3
```
Rebase interativo.

---

## 🌍 Repositório Remoto

```bash
git remote -v
```
Lista repositórios remotos.

```bash
git remote add origin https://github.com/user/repo.git
```
Adiciona um remoto.

```bash
git push origin main
```
Envia commits para o remoto.

```bash
git push -u origin main
```
Define upstream.

```bash
git pull
```
Busca e mescla alterações.

```bash
git fetch
```
Busca alterações sem mesclar.

---

## 🧹 Desfazer Alterações

```bash
git reset --soft HEAD~1
```
Desfaz commit mantendo arquivos staged.

```bash
git reset --hard HEAD~1
```
Remove commit e alterações.

```bash
git revert <hash>
```
Cria commit revertendo outro.

---

## 🏷 Tags

```bash
git tag
```
Lista tags.

```bash
git tag v1.0.0
```
Cria uma tag.

```bash
git push origin v1.0.0
```
Envia tag para o remoto.

---

## 🔐 SSH (GitHub)

```bash
ssh-keygen -t ed25519 -C "email@exemplo.com"
```
Gera chave SSH.

```bash
ssh -T git@github.com
```
Testa conexão SSH.

---

## ✅ Boas Práticas

- Commits pequenos e descritivos
- Use branches para features
- Evite `--force` em branches compartilhadas
- Prefira `pull --rebase`
- Versione `.gitignore`

---

📌 Ideal para projetos com **Docker, Node.js, NestJS, Next.js e CI/CD**.

