# Auditoria e plano de modernização

## Prioridade P0 — corrigir antes de nova publicação

1. **Autorização:** proteger `/admin`, `/alunos` e `/points/*` com policies/gates ou middleware por papel. Hoje qualquer autenticado alcança as rotas.
2. **Rota operacional pública:** remover `/clear-cache`; operações de deploy devem ocorrer por CLI autenticada.
3. **Segredos:** remover fallback de `APP_KEY`, revogar chaves/senhas já expostas, manter segredos em cofre e revisar o histórico Git.
4. **Dados pessoais:** retirar o dump da árvore, adicioná-lo ao `.gitignore`, criptografar backups e definir acesso/retenção.
5. **Consistência financeira:** validar entradas; usar transação SQL, atualização atômica/bloqueio, idempotência e registrar o operador.
6. **Reprodução:** criar migrations completas e seeders fictícios; não depender do dump para instalar.
7. **Plataforma:** sair de Laravel 8 e de versões PHP fora de suporte. Em 21/10/2026, Laravel 13 requer PHP 8.3+; use uma versão PHP com suporte ativo e patch de segurança corrente.

## Prioridade P1 — estabilização

- consolidar as rotas no padrão moderno e remover código Breeze/legado não utilizado;
- corrigir testes e criar cobertura para RBAC, saldo, concorrência, validação e cadastro;
- adotar Form Requests e validações de `whatsapp`, nascimento, motivo e quantidade;
- trocar logout para POST + CSRF e adicionar rate limit ao login/cadastro;
- usar HTTP client com timeout/retry para reCAPTCHA e manter chaves só no ambiente;
- limitar `order` a `matricula`, `name`, `email`, `whatsapp`, `data_nascimento` e `points`;
- corrigir caminhos de assets (`/resources/img/oio.png` não pertence ao webroot; JS relativo quebra em rotas aninhadas);
- configurar timezone, `APP_URL`, logs e tratamento de erros via ambiente;
- adicionar FK e política de exclusão/restrição.

## Prioridade P2 — produto e experiência

- extrato de honras para aluno e operador;
- pesquisa, filtros, ordenação, paginação acessível e exportação;
- catálogo de recompensas, estoque, resgates e estornos;
- dashboard por papel, relatórios e conciliação;
- notificações e comprovante de lançamento;
- trilha de auditoria e aprovação para ajustes sensíveis;
- responsividade, acessibilidade WCAG, estados de carregamento/erro/vazio;
- máscaras e mensagens de validação em português;
- atualização visual seguindo o conceito em `assets/mockups/dashboard-modernizado-conceito.png`.

## Opções de migração

| Opção | Quando usar | Benefícios | Custos/riscos |
|---|---|---|---|
| Laravel 8→13 incremental | equipe quer preservar a base | mudanças menores por etapa, comportamento preservado | convive mais tempo com dívida; exige saltos 9/10/11/12/13 |
| Novo Laravel 13 + Blade/Livewire | **recomendado para este escopo** | base limpa, entrega rápida, baixo custo operacional | requer migração e testes de paridade |
| Laravel API + Vue/React | SPA/mobile e UX altamente dinâmica | API reutilizável e front-end rico | mais infraestrutura, autenticação e observabilidade |
| Django, NestJS ou .NET | padrão estratégico da organização/equipe | alinhamento de talentos e ecossistema | reescrita total, maior risco de regressão e custo |

Recomendação: construir um novo monólito modular em Laravel 13, com Blade/Livewire ou Inertia somente se necessário. O domínio possui poucas telas e não justifica microserviços. Separar o código por casos de uso (`Students`, `Points`, `Rewards`, `Auth`) e manter uma API interna clara facilita evolução futura.

## Roteiro sugerido

### Fase 0 — contenção (1–3 dias)

Fechar autorizações, remover rota pública, revogar segredos, proteger o dump e corrigir validação/transação.

### Fase 1 — baseline (3–5 dias)

Migrations completas, seed fictício, testes críticos, inventário e conciliação do banco.

### Fase 2 — nova base (1–2 semanas)

Laravel 13/PHP suportado, autenticação, RBAC, alunos, razão de pontos, layout responsivo e importador.

### Fase 3 — ensaio e corte (3–5 dias)

Importação anonimizada, teste de aceitação, ensaio de backup/rollback, corte com janela definida e redefinição de senhas.

### Fase 4 — evolução

Catálogo, resgates, relatórios, notificações e observabilidade.

As durações são estimativas para equipe pequena e devem ser refinadas após regras de negócio e volume real serem confirmados.

## Referências oficiais consultadas

- Laravel 8: suporte de segurança terminou em 24/01/2023 — https://laravel.com/docs/8.x/releases
- Laravel 13: PHP mínimo 8.3 — https://laravel.com/docs/releases
- Requisitos de deploy Laravel 13 — https://laravel.com/docs/deployment
- Versões PHP suportadas — https://www.php.net/supported-versions.php
