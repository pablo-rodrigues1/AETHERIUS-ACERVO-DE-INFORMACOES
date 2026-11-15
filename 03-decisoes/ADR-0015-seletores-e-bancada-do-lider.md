---
titulo: Seletores estáveis e bancada do líder dos Magos
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-15
tags: [guildas, interface, seletores, biblioteca]
relacionados: [../02-sistemas/guildas/README.md, ADR-0008-visual-dos-paineis-de-guildas.md, ADR-0011-bancada-e-insignias-das-escolas.md, ADR-0013-exclusividade-dos-cargos-dos-magos.md]
---

# ADR-0015 — Seletores e bancada do líder

## Resumo

Todos os seletores dos painéis usam opções desenhadas dentro da interface, preservadas entre atualizações. O líder dos Magos recebe acesso à bancada e à fabricação sem acumular a função de bibliotecário.

## Contexto

O dev relatou opções que fechavam durante a escolha e esclareceu que o problema afetava todos os seletores. Também pediu o blip de fabricação para o líder. A R23 proíbe acumular liderança e bibliotecário; portanto, o acesso precisa vir da autoridade de líder, sem criar outra nomeação.

## Opções consideradas

| Opção | Limitação ou efeito |
|---|---|
| Corrigir somente o seletor de escola | Mantém o problema nos demais controles |
| Nomear o líder bibliotecário | Viola a exclusividade de funções vigente |
| Componente comum e permissão explícita de líder | Corrige todos os seletores e preserva as nomeações |

## Decisão

1. Usar uma lista de opções dentro do painel para todos os seletores, com clique e teclado. Manter o controle de valor, as opções válidas e a lista aberta durante atualizações do mesmo contexto; preservar os nós de opções que não mudaram.
2. Fechar por clique fora, troca de aba/contexto, fechamento do painel ou perda do controle/permissão. Não conservar opções removidas do cadastro ou que perderam elegibilidade.
3. Guardar a escolha em rascunho e usar o token atual no envio. Preservar validações autoritativas, escolas, insígnias e incompatibilidades dos cargos.
4. Permitir ao líder e aos bibliotecários dos Magos receber o marcador, abrir, pesquisar e fabricar na bancada. Não adicionar o líder à lista de bibliotecários. Esta regra substitui a exclusividade de bibliotecário para tomos descrita no ADR-0011; o ADR-0013 continua impedindo acúmulo das funções.
5. Manter a bancada contextual, proximidade, custo de 5 papéis e 1 pedra da alma e duração de três segundos. Revogação de liderança interrompe a autorização e cancela fabricação sem débito. Professor ou membro comum sem autoridade continua sem acesso.
6. Livros autorais permanecem arquivados; não ampliar sua cópia ao líder por efeito indireto da permissão de tomos. Conjuração e demais sistemas não são alterados.

## Consequências

- Uma nova lista pode ser testada sem depender do popup nativo do seletor. A mudança não presume causa única do comportamento no cliente de jogo.
- R24 instalada no privado sem reinício; 119 testes completos, 15 rechecagens da interface e teste nativo isolado do líder aprovados. Fonte R24 e marcador no local atual confirmados no cliente/servidor.
- Inspeção do componente no CEF por chamada direta manteve a lista por 1,3 s. O evento de mouse sintetizado não reproduziu o clique físico; aceite humano continua pendente. Homologação oficial é separada.
