# Documentação do sistema META

Análise produzida em **21/10/2026**, sobre o commit `369302d` (`master`). O sistema é uma aplicação Laravel monolítica para cadastro de participantes e gestão de “honras/moedas” de alunos.

## Entregáveis

- [Documentação técnica em PDF](./documentacao-tecnica-meta.pdf) — documento principal, diagramado e pronto para distribuição.
- [Versão HTML do documento](./documentacao-tecnica-meta.html) — fonte navegável do PDF.
- [Instalação e ambiente](./INSTALACAO.md) — execução do legado, limitações e implantação.
- [Modelo de dados](./MODELO-DE-DADOS.md) — esquema observado e modelo-alvo recomendado.
- [Fluxos funcionais](./FLUXOS-FUNCIONAIS.md) — perfis, rotas e regras atuais.
- [Auditoria e plano de modernização](./AUDITORIA-E-MODERNIZACAO.md) — erros, riscos, prioridades e opções tecnológicas.
- [Inventário técnico](./INVENTARIO-TECNICO.md) — componentes ativos, legados e evidências de validação.
- [`assets/diagrams`](./assets/diagrams/) — diagramas vetoriais de arquitetura, dados e fluxos.
- [`assets/screenshots`](./assets/screenshots/) — telas atuais reconstruídas com dados fictícios.
- [`assets/mockups`](./assets/mockups/) — conceito visual de uma interface modernizada.

## Escopo e privacidade

O dump `dump/Meta20241130.sql` foi usado apenas para reconstruir o esquema e obter métricas agregadas. Nomes, e-mails, telefones, datas de nascimento e hashes reais não foram copiados para estes documentos. As capturas usam registros fictícios. O dump contém dados pessoais e deve ser retirado da árvore de trabalho, armazenado de forma criptografada e submetido a política de retenção e acesso.

## Resumo executivo

O núcleo funcional é pequeno e compreensível, mas a base combina convenções Laravel 5 e dependências Laravel 8/Breeze. A aplicação carrega `app/Http/routes.php`; `routes/web.php`, os controllers do Breeze e as views Tailwind não estão no fluxo real. O maior risco é de autorização: as rotas administrativas exigem apenas autenticação, então ocultar o menu por perfil não impede um aluno de chamar diretamente URLs de gestão.

Prioridades imediatas: bloquear rotas por papel, remover a rota pública `/clear-cache`, revogar segredos expostos, proteger o dump, tornar lançamentos transacionais, completar migrations e testes e migrar para uma versão suportada do framework/PHP.
