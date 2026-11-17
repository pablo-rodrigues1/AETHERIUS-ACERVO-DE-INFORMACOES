---
titulo: Baú coletivo do Colégio e seus registros
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-17
tags: [guildas, skymp, inventario]
relacionados: [../02-sistemas/guildas/README.md, ../02-sistemas/guildas/escopo.md]
---

# ADR-0021 — Baú coletivo do Colégio e seus registros

## Resumo

Usar a estante existente indicada pelo dev como estoque físico único para todos os membros atuais dos Magos, com movimentações confirmadas nos Registros do Arquimago.

## Contexto

O dev esclareceu que o ponto era um Bookcase no Arcanaeum e pediu acesso para aluno, professor, bibliotecário e Arquimago. Depois pediu o log do baú coletivo nos registros da liderança. O compartimento é distinto dos quartos e do baú exclusivo do Arquimago.

## Opções consideradas

| Opção | Avaliação |
|---|---|
| Inventário individual por jogador | Não corresponde ao estoque coletivo pedido |
| Novo móvel | Desnecessário; existe um CONT indicado pelo dev |
| Estante física existente, guardas de filiação e auditoria | Atende ao pedido sem migrar/apagar pertences |

## Decisão

- Referência privada identificada no cliente/servidor: `ff172:JK's College of Winterhold.esp`, no Arcanaeum. Configurar `shared: true`, sem `leaderOnly/roleExclusive`. Nome apresentado: **Baú Coletivo do Colégio**.
- Todos os membros atuais dos Magos abrem/depositam/retiram com mesma instância, proximidade, vida e inventário disponível. Expulsão revoga imediatamente. Outros jogadores não acessam.
- Usar estoque nativo único, sem claim, registro pessoal, cópia por cliente, esvaziamento ou migração do conteúdo existente.
- Registrar depósitos/retiradas somente após a cadeia de hooks permitir e os dois inventários mostrarem deltas opostos da variante e quantidade correspondentes. Não registrar tentativa recusada, sem efeito ou alteração unilateral como transferência.
- Registros exclusivos do Arquimago: nome/ID confiáveis do ator, ação, item/variante, quantidade, horário, referência e antes/depois no baú. Preservar identidade histórica. Usar a retenção existente (100 exibidos por guilda, 5.000 globais); não retroagir aos movimentos anteriores à instalação.
- Preservar políticas distintas dos quartos e do Baú do Arquimago; essa decisão não amplia acesso aos pertences privados.

## Consequências e evidência

Instalação privada sem reinício, preservando estado e inventários; nome nativo observado na referência carregada. Ensaio isolado no addon real com três bots: um membro deposita, outro retira, estoque permanece após fechar/reabrir, terceiro/expulso bloqueados e dois registros confirmados com quantidades corretas. Zero clientes humanos no ensaio. Dois clientes reais, persistência após reinício e homologação oficial permanecem pendentes.
