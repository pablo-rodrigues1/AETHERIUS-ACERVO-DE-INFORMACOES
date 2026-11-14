---
titulo: Manutenção de equipamentos
tipo: guia-staff
status: ativo
responsaveis: [mercurius17]
atualizado: 2026-11-14
tags: [staff, itens, durabilidade, manutencao, kits]
fonte_tecnica: [../../02-sistemas/gameplay-core/README.md]
relacionados: [../progresso.md]
---

# Manutenção de equipamentos

## Em uma frase
Armas e armaduras precisam de manutenção: o jogador usa kits que dão "cargas", e sem cargas o equipamento rende 30% menos em combate.

## Como funciona
1. Existem **30 kits de manutenção**: 12 para armas e 18 para armaduras, um para cada material (ferro, aço, élfico, couro etc.).
2. Usar um kit dá **50 cargas** daquele material e daquele tipo (arma ou armadura). O jogador pode guardar no máximo **100 cargas** por material e tipo.
3. As cargas só são gastas **lutando**. Uma luta conta como um ciclo de até 30 segundos, que termina depois de 10 segundos sem golpes. Andar, explorar ou só estar com o equipamento vestido **não** gasta carga.
4. Cada ciclo de luta gasta carga do material que foi usado: a arma do atacante, o escudo ao bloquear e a armadura de quem apanha.
5. **Sem carga**, o equipamento daquele material funciona com **70%** da eficiência: a arma causa menos dano e a armadura protege menos.
6. Por enquanto o kit é usado pelo comando **/manutencao** (a aba "Manutenção" na interface ainda vai entrar).
7. Os kits são fabricados com lingotes e couro (tabela abaixo). Alguns materiais ainda não têm receita (ver "Materiais sem receita").

## Exemplo prático
> Um jogador com espada de aço e sem cargas de aço entra numa briga. A espada causa 30% menos dano. Ele usa um kit de aço para armas (`/manutencao usar steel armas`), ganha 50 cargas e, na próxima luta, a espada volta ao dano normal.

## Tabela rápida

| Regra | Valor |
|---|---|
| Cargas por kit | 50 |
| Máximo guardado por material e tipo | 100 |
| Perda sem manutenção | 30% (arma e armadura) |
| Duração máxima de um ciclo de luta | 30 segundos |
| Ciclo termina após | 10 segundos sem golpe |

| Receita do kit | Ingredientes |
|---|---|
| Armas | 2 lingotes do material + 3 tiras de couro |
| Armaduras (metal) | 2 lingotes do material + 2 couros + 2 tiras de couro |
| Armaduras de couro ou pele | 6 couros + 3 tiras de couro |

**Materiais sem receita** (por não ter lingote correspondente definido): armas de daedric, dragonbone, stalhrim e madeira; armaduras de daedric, dragonbone, stalhrim, scaled, chitin, bonemold, falmer, nordic e dragonscale.

| Comando | O que faz |
|---|---|
| `/manutencao` | Mostra as cargas que o personagem tem |
| `/manutencao usar <material> <armas\|armadura>` | Usa um kit do inventário. O material vai em inglês: `iron`, `steel`, `leather`... |

## Perguntas comuns de jogadores
- **Meu kit não ativou.** Ou ele não está no inventário, ou o personagem já tem cargas demais daquele material (precisa ter espaço para as 50).
- **Gasto carga só de andar com a armadura?** Não. Só lutando.
- **Uso kit de ferro na espada de aço?** Não. O kit precisa ser do mesmo material e do mesmo tipo (arma ou armadura).
- **A mensagem diz "Kit ainda não existe no jogo".** Aquele kit ainda não foi criado nos arquivos do jogo; avise a Staff/devs.

## O que a Staff pode e não pode fazer
- **Pode:** orientar o uso do `/manutencao` e entregar kits pelo painel (F7), se tiver a permissão de dar itens.
- **Não pode:** dar cargas diretamente ou mudar a penalidade de um jogador. Os valores são do servidor e valem para todos.

## Quer o detalhe técnico?
Veja [GameplayCore](../../02-sistemas/gameplay-core/README.md) (módulo `durability-system`). Os valores ficam em `modules/durability-system/config/maintenance-config.json` e as receitas em `modules/durability-system/docs/KIT_RECIPES_AND_BALANCE.md`.
