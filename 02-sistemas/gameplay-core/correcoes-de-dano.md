---
titulo: Correções de dano no servidor (frente de dano)
tipo: sistema
status: rascunho
responsaveis: [kdu, mercurius17]
repositorio: "patches sobre AetheriusRP/aetherius-server v0.38 (addon nativo de combate e gamemode)"
depende_de: [README.md]
atualizado: 2026-11-14
tags: [combate, dano, resistencia, armadura, magia, servidor]
relacionados: [README.md, diario.md]
---

# Correções de dano no servidor

## Resumo
Correções da cadeia de dano do servidor (addon nativo de combate do `aetherius-server`), feitas a partir da missão de 11/11 (dano igual em PvP e PvE). Cada correção tem uma chave em `server-settings.json` e fica **desligada** sem ela. O pacote foi entregue ao Mercurius em 13/11 sobre a v0.38.

## Onde está o código
- Patches: `servidor-sobre-v0.38.patch` e `cliente-sobre-v0.38.patch` (pacote "AETHERIUS - PACOTÃO CORREÇÕES v0.38", 13/11).
- Servidor: addon nativo de combate (C++, `scam_native.node`) e `gamemode/magic-service.js`.
- Cliente: envio do disparo do arco, envio de acertos de encantamento e filtro do equipamento.
- Testes: `unit/AetheriusCombatFixesTest.cpp` e `unit/AetheriusTurnUndeadConditionsTest.cpp` (tag `[AetheriusFixes]`, 17/17).

## Escopo
- Inclui: validação dos acertos, multiplicador de dano, defesa (armadura e resistências), arco, pergaminho, magia de NPC, cajados e armas encantadas, condições de magia (Turn Undead/Banish), invocação amiga do dono e do grupo, veneno com resistência.
- Não inclui: XP, classes, perks e o motor DS-5a do GameplayCore (ver [README](README.md)).

## Funcionamento
Liga-se em fases; cada fase pode ser desfeita só desligando a chave (reiniciar o servidor).

| Fase | Chaves (`aetheriusCombat.fixes`) | Efeito | Muda balanceamento? |
|---|---|---|---|
| 1 | `meleeReach`, `powerAttackEvidence`, `requireWornWeapon`, `rejectDeadTargets`, `equipmentIdxGuard`, `summonOwner`, `spellHitCadence` + `damageMultFormulaSettings.multiplier = 1` | o servidor confere melhor cada acerto e o dano ×2 de NPC contra jogador acaba | não |
| 1b | `armorCurve`, `magicResist` | tabela de armadura da chefia e resistência à magia/elemento | sim |
| 2 | `rangedAmmo`, `bowShotLedger`, `arrowMinPower`, `scrolls` | arco e pergaminho contados pelo servidor (exige o cliente novo) | sim |
| 3 | `npcSpells` | magia de NPC passa a causar dano | sim |
| 3b | `magicItems` | cajado e arma encantada causam o dano do encantamento (exige o cliente novo) | sim |
| — | `spellConditions` | magia com condição (ex.: só morto-vivo) só faz efeito quando a condição vale | sim |

Fora até decisão: `ignoreClientSneak` e `weaponPoison` (veneno de arma suspenso pelo dono).

## Parâmetros e fórmulas
| Parâmetro | Valor | Descrição |
|---|---|---|
| Armadura (`armorCurve`) | 80% de redução com 500; 90% com 1000; teto 90% | tabela da chefia |
| Resistência (`magicResist`) | resistência à magia e depois a do elemento; teto 90% cada | fraqueza aumenta o dano até o dobro |
| `arrowMinPower` | 0,55 | força mínima do arco para o disparo valer |
| Cajado de concentração | 1 acerto por segundo | cadência aceita |

## Dependências
| Depende de | Por quê |
|---|---|
| [GameplayCore](README.md) | dono do domínio de combate e dano |
| `aetherius-server` v0.38 | base dos patches |

## Pendências conhecidas
- Confirmar quais chaves estão ligadas no servidor oficial.
- Decidir se a tabela de armadura vale para a armadura base ou com bônus de skill/perk.
- Roupa inicial do jogador sorteada de forma diferente no jogo e no servidor (decisão do Mercurius): armadura que não conta.
- Regerar o catálogo de combate com o `AETHERIUS - ITEM BALANCE.esp` atual.
- Teste em jogo de cajado, Banish Undead, invocação e veneno.
