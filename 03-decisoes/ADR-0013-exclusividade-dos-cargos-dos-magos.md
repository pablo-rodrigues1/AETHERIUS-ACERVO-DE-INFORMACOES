---
titulo: Exclusividade dos cargos de professor e bibliotecário dos Magos
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-15
tags: [guildas, magos, cargos, professores, bibliotecarios]
relacionados: [../02-sistemas/guildas/README.md, ADR-0011-bancada-e-insignias-das-escolas.md, ADR-0012-portas-do-colegio-e-funcoes-incompativeis.md]
---

# ADR-0013 — Exclusividade dos cargos dos Magos

## Resumo

Professor ou bibliotecário dos Magos não pode acumular outros cargos ou funções, preservando apenas o vínculo pessoal com sua escola. A regra vale no servidor, no estado persistido e na interface.

## Contexto

Após a incompatibilidade professor/bibliotecário da R22, o dev ampliou a restrição para todos os demais cargos. A filiação a uma escola é um vínculo independente, com insígnia própria, e precisa permanecer intacta.

Esta decisão complementa o ADR-0012 e encerra a possibilidade de líder acumular bibliotecário descrita no ADR-0011. As demais regras de bancada e insígnias daquele ADR permanecem vigentes.

## Opções consideradas

| Opção | Consequência |
|---|---|
| Esconder apenas o seletor de cargos | Pedidos forjados e dados persistidos ainda permitiriam acúmulo |
| Permitir exceção pelo nome do cargo | Cargo renomeado como escola poderia contornar a restrição |
| Validar função e vínculos canônicos no servidor | Mantém a escola real e recusa todos os acúmulos proibidos |

## Decisão

1. Nos Magos, professor ou bibliotecário mantém a função principal e pode manter o vínculo pessoal de escola. Não pode receber cargos personalizados nem acumular liderança ou a outra função. Professor ocupa somente um posto de professor dentre as cinco escolas.
2. Recusar pedidos de cargo enquanto a função estiver ativa. No sentido inverso, exigir a retirada dos cargos antes da nomeação; o líder não pode ser nomeado professor/bibliotecário enquanto liderar. A promoção administrativa de um especialista exige revogação prévia da função.
3. Preservar a autoridade existente: somente o líder nomeia, revoga e atribui cargos. A Staff continua definindo liderança por integração autorizada. Não existe autoelevação no painel.
4. O vínculo permitido é o campo canônico de escola do membro, não um cargo com nome semelhante. Não trocar escola, ensino ou insígnia automaticamente para resolver cargos extras.
5. Validar as regras também na carga do estado e em cada gravação. Filtrar candidatos incompatíveis e retirar a opção de atribuir cargo a especialistas; não depender da interface para autorizar pedidos.
6. Na migração privada, fazer backup e validar uma projeção antes de mudar dados. Preservar função e escola; retirar somente cargos personalizados incompatíveis pela ação normal do líder, com um registro por retirada. Outros conflitos não planejados abortam a atualização antes da mutação.
7. Limitar esta regra aos Magos. Não alterar funções de outras guildas, Classes, receita de tomos, insígnias, portas, pertences ou conjuração.

## Consequências

- Para mudar de função, retirar/revogar o vínculo incompatível antes da nova nomeação. Uma tentativa recusada não gera registro de alteração bem-sucedida.
- Estados antigos incompatíveis exigem conferência e migração antes da implantação; o carregamento não apaga cargos silenciosamente.
- R23 instalada no privado sem reinício: três cargos extras retirados de dois integrantes fictícios, com histórico, funções e escolas preservados. Liderança, inventários e demais dados conferidos.
- 117 testes completos e teste nativo isolado com quatro bots aprovados. Disco e estado vivo conferidos; cliente conectado confirmou R23. Prévia com dados fictícios, sem aceite visual humano ou homologação oficial nesta etapa.
