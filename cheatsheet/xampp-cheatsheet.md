# XAMPP Cheatsheet - Linux

## Comandos Básicos

### Iniciar Serviços

```bash
# Iniciar todos os serviços (Apache + MySQL + ProFTPD)
sudo /opt/lampp/lampp start

# Iniciar apenas o Apache (PHP)
sudo /opt/lampp/lampp startapache

# Iniciar apenas o MySQL
sudo /opt/lampp/lampp startmysql

# Iniciar apenas o ProFTPD
sudo /opt/lampp/lampp startftp
```

### Parar Serviços

```bash
# Parar todos os serviços
sudo /opt/lampp/lampp stop

# Parar apenas o Apache
sudo /opt/lampp/lampp stopapache

# Parar apenas o MySQL
sudo /opt/lampp/lampp stopmysql

# Parar apenas o ProFTPD
sudo /opt/lampp/lampp stopftp
```

### Reiniciar Serviços

```bash
# Reiniciar todos os serviços
sudo /opt/lampp/lampp restart

# Reiniciar apenas o Apache
sudo /opt/lampp/lampp restartapache

# Reiniciar apenas o MySQL
sudo /opt/lampp/lampp restartmysql
```

### Verificar Status

```bash
# Ver status de todos os serviços
sudo /opt/lampp/lampp status
```

## URLs Úteis

| Recurso | URL |
|---------|-----|
| Localhost | http://localhost |
| Dashboard XAMPP | http://localhost/dashboard |
| phpMyAdmin | http://localhost/phpmyadmin |
| phpInfo | http://localhost/dashboard/phpinfo.php |

## Diretórios Importantes

| Diretório | Descrição |
|-----------|-----------|
| `/opt/lampp/htdocs/` | Seus arquivos PHP/HTML |
| `/opt/lampp/etc/httpd.conf` | Configuração do Apache |
| `/opt/lampp/etc/my.cnf` | Configuração do MySQL |
| `/opt/lampp/logs/` | Logs do Apache e MySQL |
| `/opt/lampp/phpmyadmin/` | phpMyAdmin |

## Comandos PHP Direto

```bash
# Executar PHP via CLI
/opt/lampp/bin/php -v

# Executar script PHP
/opt/lampp/bin/php arquivo.php

# Ver informações do PHP
/opt/lampp/bin/php -i

# Módulos instalados
/opt/lampp/bin/php -m
```

## Comandos MySQL Direto

```bash
# Acessar MySQL via terminal
/opt/lampp/bin/mysql -u root -p

# Dump de banco de dados
/opt/lampp/bin/mysqldump -u root -p nome_banco > backup.sql

# Importar banco de dados
/opt/lampp/bin/mysql -u root -p nome_banco < backup.sql
```

## Solução de Problemas

### Porta 80 já está em uso

```bash
# Verificar o que está usando a porta 80
sudo netstat -tulpn | grep :80

# Parar Apache do sistema (se existir)
sudo systemctl stop apache2
sudo systemctl disable apache2
```

### Porta 3306 já está em uso

```bash
# Verificar o que está usando a porta 3306
sudo netstat -tulpn | grep :3306

# Parar MySQL do sistema (se existir)
sudo systemctl stop mysql
sudo systemctl disable mysql
```

### Permissões de Arquivo

```bash
# Dar permissões corretas ao htdocs
sudo chmod -R 755 /opt/lampp/htdocs/
sudo chown -R $USER:$USER /opt/lampp/htdocs/
```

## Desinstalar XAMPP

```bash
# Parar todos os serviços
sudo /opt/lampp/lampp stop

# Remover XAMPP
sudo rm -rf /opt/lampp
```

## Dicas

- Sempre use `sudo` para comandos do XAMPP no Linux
- Coloque seus projetos PHP em `/opt/lampp/htdocs/`
- Para acessar um projeto: `http://localhost/nome-da-pasta/`
- Logs de erro do Apache: `/opt/lampp/logs/error_log`
- Logs de acesso: `/opt/lampp/logs/access_log`
