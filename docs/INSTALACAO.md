# Instalação e ambiente

## 1. Estado atual

Dependências instaladas no snapshot analisado: Laravel `8.83.27`, Laravel UI `3.4.6`, Breeze `1.10.0`, Laravel Collective HTML `6.4.1`, Guzzle `7.8.1` e PHPUnit `9.6.10`. O código inicia comandos Artisan com PHP 8.3.6, mas Laravel 8 está fora de suporte e sua matriz oficial cobria PHP 7.3–8.1. Isso torna PHP 8.3 um ambiente de diagnóstico, não uma combinação de produção homologada.

O front-end ativo usa arquivos já compilados em `public/` (Bootstrap 3 e jQuery). A cadeia de build está inconsistente: há Gulp/Elixir e Vite/Tailwind ao mesmo tempo; faltam `vite` e `laravel-vite-plugin` em `package.json`, falta `resources/js/bootstrap.js`, não há scripts npm e `node_modules` não está instalado.

## 2. Pré-requisitos para recuperação controlada do legado

- Linux, macOS ou Windows/WSL;
- PHP 8.1 para máxima compatibilidade histórica, **apenas em ambiente isolado**, ou PHP 8.3 para diagnóstico com testes completos;
- extensões PHP: Ctype, cURL, DOM/XML, Fileinfo, Mbstring, OpenSSL, PDO e `pdo_mysql`, Session e Tokenizer;
- Composer 2;
- MySQL 8 (o dump foi criado por MySQL 8.0.40);
- servidor web apontando exclusivamente para `public/`;
- Node/npm somente se a cadeia de ativos for antes consolidada.

> PHP 8.1 e Laravel 8 não devem ser usados em nova produção. Esta trilha serve para recuperar e validar o comportamento antes da modernização.

## 3. Passos de desenvolvimento

```bash
git clone <repositorio> laravel-loja
cd laravel-loja
cp .env.example .env
composer install
php artisan key:generate
```

Configure no `.env`, no mínimo:

```dotenv
APP_ENV=local
APP_DEBUG=true
APP_URL=http://127.0.0.1:8000

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=meta
DB_USERNAME=meta_app
DB_PASSWORD=uma_senha_forte

SESSION_DRIVER=file
CACHE_DRIVER=file
QUEUE_CONNECTION=sync

GOOGLE_CAPTCHA_SECRET=chave_do_ambiente
```

Antes de usar `php artisan migrate`, crie as migrations ausentes para `points` e para `matricula`, `whatsapp` e `data_nascimento`. No estado atual, as migrations não reproduzem o banco. Usar o dump bruto é uma alternativa apenas para recuperação autorizada, pois ele contém dados pessoais e hashes de senha.

Depois de corrigir o esquema:

```bash
php artisan migrate --seed
php artisan config:clear
php artisan route:list
php artisan serve
```

Abra `http://127.0.0.1:8000/auth/login`.

## 4. Importação segura do legado

1. Trabalhe sobre cópia criptografada e com acesso restrito.
2. Importe em banco privado, sem exposição à internet.
3. Remova ou substitua dados pessoais em ambientes não produtivos.
4. Invalide todas as sessões e force redefinição de senha após migração.
5. Recalcule saldos a partir do histórico e trate divergências em relatório de exceção.
6. Adicione FK de `points.user_id` e índices depois de corrigir eventuais órfãos.
7. Faça backup verificado antes do corte e ensaie rollback.

## 5. Produção modernizada

- Laravel 13 e PHP 8.5 atualizado, ou outra versão ainda suportada na data do deploy;
- Nginx/Apache/FrankenPHP com raiz em `public/`, HTTPS obrigatório e `APP_DEBUG=false`;
- MySQL 8 com usuário de privilégio mínimo, backups automáticos e teste de restauração;
- segredos em cofre/variáveis do ambiente, nunca em arquivo versionado;
- `php artisan config:cache`, `route:cache` e `view:cache` no deploy;
- health check, logs centralizados, monitoramento de erros e auditoria das transações;
- política LGPD para WhatsApp e data de nascimento.

## 6. Verificações realizadas

| Verificação | Resultado em 21/10/2026 |
|---|---|
| `php artisan --version` | Laravel 8.83.27 |
| `php artisan route:list` | sucesso; 13 rotas ativas |
| `composer validate` | `composer.json` válido |
| PHPUnit | falha fatal: `Tests\\TestCase` não encontrado |
| `npm ls --depth=0` | falha: dependências declaradas não instaladas |
| conexão configurada | inacessível no ambiente de análise; nenhum acesso ao banco remoto foi realizado |
