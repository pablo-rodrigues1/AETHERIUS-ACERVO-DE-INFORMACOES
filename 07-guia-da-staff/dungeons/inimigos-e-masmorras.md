---
titulo: Inimigos e masmorras
tipo: guia-staff
status: ativo
responsaveis: [mercurius17]
atualizado: 2026-11-14
tags: [staff, dungeons, inimigos, respawn]
fonte_tecnica: [../../02-sistemas/gameplay-core/README.md]
relacionados: [../progressao/nivel-xp-e-atributos.md, ../progresso.md]
---

# Inimigos e masmorras

## Em uma frase
O mundo aberto tem animais e bandidos que voltam depois de um tempo; cada masmorra tem uma "leva" de inimigos compartilhada por todos os jogadores, que só é trocada quando a masmorra fica vazia por um tempo.

## Como funciona
1. **Mundo aberto:** só animais e bandidos aparecem, nos pontos de sempre do jogo. Quando um morre, ele volta no mesmo lugar depois de 60 minutos (valor padrão).
2. **Cidades e vilas** não têm esses animais, mesmo que o jogo original colocasse algum lá dentro.
3. **Masmorras:** cada masmorra tem uma **geração** de inimigos, a mesma para todo mundo.
   - Ela começa **adormecida**. O primeiro jogador que entra **ativa** a masmorra e os inimigos aparecem.
   - Os inimigos são sorteados de acordo com o tipo e a faixa de nível da masmorra. O sorteio é fixo para aquela geração: sair, reconectar ou reiniciar o servidor **não** sorteia de novo.
   - Inimigo morto numa masmorra **não volta sozinho**. Quando todos morrem, a masmorra fica **limpa**.
   - A masmorra só ganha uma **nova geração** quando fica vazia (nenhum jogador dentro) pelo tempo de reset da tabela abaixo.
4. **Nível do inimigo:** cada inimigo tem um nível de combate de 1 a 100. Quanto maior, mais XP ele vale (ver [Nível, XP e pontos de atributo](../progressao/nivel-xp-e-atributos.md)).

## Exemplo prático
> Um grupo limpa a Bleak Falls Barrow e sai. Doze horas depois, se ninguém entrou lá nesse tempo, a masmorra recomeça com uma nova leva de inimigos. Se outro jogador entrou e saiu no meio, a contagem recomeça a partir da saída dele.

> Um jogador entra numa masmorra, mata metade dos inimigos e vai embora. Se ninguém voltar em 3 horas, a masmorra recomeça do zero, com inimigos novos.

## Tabela rápida

| Situação | Tempo padrão até voltar |
|---|---|
| Animal ou bandido do mundo aberto morto | 60 minutos, no mesmo lugar |
| Masmorra **limpa** e vazia | 12 horas (720 minutos) |
| Masmorra **não limpa** e vazia | 3 horas (180 minutos) |
| Inimigo morto dentro de masmorra | Não volta; só com a nova geração |

| Nível do inimigo | Quanto o XP é multiplicado |
|---|---|
| 1 | 1× |
| 20 | 4,8× |
| 40 | 5,8× |
| 100 | 7× |

Os tempos são opções do servidor (`spawn.exteriorRespawnMinutes`, `spawn.dungeonClearedResetMinutes`, `spawn.dungeonIdleResetMinutes`) e podem estar diferentes no oficial.

## Perguntas comuns de jogadores
- **Entrei na masmorra e não tinha ninguém. Por quê?** Outro jogador limpou essa geração e ela ainda não resetou.
- **Saí e voltei; os inimigos são os mesmos?** Sim. A geração só muda depois do tempo de reset com a masmorra vazia.
- **Por que não tem animais na cidade?** De propósito: cidades e vilas ficam sem esses spawns.

## O que a Staff pode e não pode fazer
- **Pode:** pedir aos devs para mudar os tempos de reset e de respawn (são opções do servidor) e criar NPCs pelo painel (F7) quando houver NPCs liberados.
- **Não pode:** forçar o reset de uma masmorra específica ou trocar os inimigos sorteados pelo painel; isso não existe hoje.

## Quer o detalhe técnico?
Veja [GameplayCore](../../02-sistemas/gameplay-core/README.md) (módulo `enemy-system`). A ligação com o servidor fica em `server/gamemode/enemy-spawns-service.js` do `aetherius-server`.
