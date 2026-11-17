---
titulo: Retirada dos quartos e preservação das portas do Arquimago
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-17
tags: [guildas, magos, quartos, portas, inventario]
relacionados: [ADR-0016-quartos-escolas-e-cronograma-dos-magos.md, ADR-0018-bau-exclusivo-do-arquimago.md, ADR-0021-bau-coletivo-do-colegio.md, ADR-0024-quartos-sem-deposito-de-septins.md, ../02-sistemas/guildas/escopo.md]
---

# ADR-0025 — Retirada dos quartos e preservação das portas do Arquimago

## Resumo

Retirar a gestão de quartos dos Magos e preservar o controle geral de portas exclusivo do Arquimago, o baú do cargo e o coletivo.

## Contexto

O dev pediu retirar toda a lógica de quartos, baús particulares de alunos e respectivas abas, mantendo o restante. Esclareceu expressamente que o Arquimago deve continuar trancando/destrancando as portas. Pediu reposicionar seu baú na mesma sala, preferindo um baú físico.

## Opções consideradas

| Opção | Avaliação |
|---|---|
| Apenas esconder a aba | Mantém acesso, cadastro e restrições antigas no servidor |
| Retirar também controle geral das portas | Contraria o esclarecimento do dev |
| Retirar quartos e manter portas/baús indicados | Escopo adotado |

## Decisão

- Não carregar StudentRooms nem seu renderer, configuração ou timers. Recusar ações/abas antigas e retirar a permissão/modelo de quartos. Arquivar o fonte e dados antes de instalar; não apagar inventários.
- Retirar filiação de baú particular dos Magos e suas tags, nomes personalizados e bloqueio de septins. Manter somente os pontos roleExclusive e shared dos Magos. Outros grupos e as demais funções não mudam.
- **Manter CollegeDoors**, sua aba exclusiva do Arquimago e trancas anteriores nas 68 passagens. Nenhum dono de quarto ou professor ganha exceção à tranca geral. Esta preservação é requisito explícito do dev.
- Manter a mesma referência CONT do Baú do Arquimago nos aposentos, em outro ponto da lateral da sala, preservando todos os itens. Posição aplicada pelo SetPosition nativo do SkyMP e reaplicada no cliente ao carregar. Não criar baú, escolher gaveteiro ou migrar estoque.
- Manter estante coletiva, estoque compartilhado e registros de movimentação exclusivos do Arquimago.
- Membros, cargos, funções, escolas/insígnias, Biblioteca, receitas, cronograma, registros, Staff/Classes e conjuração permanecem nos seus escopos. Somente documentação é enviada ao acervo.
- Testes no Skyrim ficam com o próprio dev; não abrir/controlar o jogo para testá-los. Validação local não é aceite visual nem homologação com dois jogadores.

## Consequências

Esta decisão substitui a gestão dos dez quartos do ADR-0016 e a regra de depósito do ADR-0024. Preserva o cronograma e retirada de escola do ADR-0016, o baú do ADR-0018 e o coletivo do ADR-0021. Backups e conteúdos continuam locais. Os pacotes R28/R29 são históricos e não representam esta revisão. Homologação oficial permanece pendente.
