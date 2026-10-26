# Inventário técnico

## Componentes ativos

| Camada | Arquivos principais |
|---|---|
| Bootstrap | `public/index.php`, `bootstrap/app.php` |
| Rotas | `app/Http/routes.php` via `RouteServiceProvider` |
| Controllers | `HomeController`, `Auth/AuthController` |
| Modelos | `App\\User`, `App\\Points` |
| Views | `app.blade.php`, `home.blade.php`, `auth/login.blade.php`, `auth/register.blade.php`, `admin/users/*` |
| Persistência | MySQL, tabelas `users`, `points`, `password_resets`, `migrations` |
| UI | Bootstrap 3, jQuery e CSS próprio já publicados em `public/` |

## Código presente, mas fora do fluxo atual

- `routes/web.php` e `routes/auth.php`;
- controllers e componentes instalados pelo Breeze;
- `resources/views/layouts/*`, `dashboard.blade.php`, views de reset/verificação;
- arquivos `*.new.blade.php`;
- Vite/Tailwind/Alpine;
- `admin/users/list.blade.php` (template de portfólio) e `create.blade.php` sem rota.

O motivo é estrutural: `RouteServiceProvider::map()` inclui diretamente `app/Http/routes.php` e não carrega `routes/web.php`.

## Dependências e build

- Laravel 8.83.27;
- autenticação duplicada: Laravel UI legado + Breeze;
- formulário: Laravel Collective HTML;
- front-end ativo: Bootstrap 3 e jQuery estáticos;
- front-end declarado: Gulp/Elixir e Vite/Tailwind, ambos incompletos;
- suíte de testes mistura `TestCase` global antigo com namespace `Tests` e modelo `App\\Models\\User` inexistente.

## Artefatos e configuração sensíveis

- `.env` local aponta para um host de banco externo; não foi acessado;
- `config/app.php` contém chave de aplicação padrão embutida;
- o dump não está coberto pelo `.gitignore` e contém dados pessoais e hashes;
- `/clear-cache` é uma rota pública;
- `config/database.php` contém defaults inseguros/ambíguos e duas conexões MySQL.

## Evidências reproduzíveis

```bash
php artisan --version
php artisan route:list
composer validate --no-check-publish
./vendor/bin/phpunit --testdox
npm ls --depth=0
```

Resultados da análise: Artisan e lista de rotas funcionam; Composer valida; PHPUnit falha antes de executar os casos; npm indica dependências ausentes.
