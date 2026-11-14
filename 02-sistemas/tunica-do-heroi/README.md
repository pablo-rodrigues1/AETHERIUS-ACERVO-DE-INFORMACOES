---
titulo: Túnica do Herói
tipo: sistema
status: rascunho
fase: em-teste
responsaveis: [kdu]
repositorio: "pacote separado: mod AETHERIUS - TUNICA DO HEROI (plugin ESL próprio)"
depende_de: [../gameplay-core/README.md]
atualizado: 2026-11-14
tags: [item, roupa, alfaiate, profissoes]
relacionados: [diario.md]
---

# Túnica do Herói

## Resumo
As 16 peças do mod Hero's Tunic (conjuntos do Herói, Goron, Zora e Sombrio) como itens próprios, feitos só pelo alfaiate, como o Mercurius pediu. Os gorros passam a mostrar o cabelo.

## Onde está o código
- Mod: `AETHERIUS - TUNICA DO HEROI`, com plugin ESL próprio, modelos e texturas 4K.
- Receitas: proposta em `receitas-alfaiate-tunica-do-heroi.json`, no formato do `tailor-recipe-catalog.json` do GameplayCore.

## Escopo
- Inclui: as 16 peças, gorros com o cabelo à mostra e as receitas do alfaiate (proposta).
- Não inclui: os baús que o mod original espalha pelo mundo; receita de forja.

## Funcionamento
- Gorros: saem do encaixe de cabelo (31) e vão para o de tiara (42). Os modelos foram copiados com a partição 142, para não ficarem invisíveis.
- O plugin não altera nenhum registro do jogo base.

## Parâmetros e fórmulas
| Peças | Patente proposta | Materiais propostos |
|---|---|---|
| Túnicas | adepto | 6 tiras de couro + 4 couros |
| Gorros | aprendiz | 3 tiras + 2 couros |
| Luvas e botas | novato | 3 a 4 tiras + 2 couros |
| Conjunto sombrio | perito | iguais aos da peça equivalente |

## Dependências
| Depende de | Por quê |
|---|---|
| [GameplayCore](../gameplay-core/README.md) (profissões) | receitas do alfaiate |

## Pendências conhecidas
- Testar o cabelo com o gorro em jogo: cabelo volumoso pode atravessar o gorro.
- Mercurius decidir patente e materiais e colar as receitas no catálogo.
- Conferir a permissão dos arquivos do mod original para o modpack.
