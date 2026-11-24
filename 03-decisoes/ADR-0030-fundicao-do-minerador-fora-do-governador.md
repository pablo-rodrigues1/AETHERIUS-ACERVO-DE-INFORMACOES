---
titulo: Fundição do Minerador como serviço próprio
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [pablo-rodrigues1]
atualizado: 2026-11-24
tags: [profissoes, minerador, fundicao, economia]
relacionados: [../02-sistemas/profissoes/coleta.md, ../02-sistemas/profissoes/artesanato.md]
---

# ADR-0030 — Fundição do Minerador como serviço próprio

## Resumo
A fundição é um serviço do servidor separado do governador de profissões (`smelter-service.js`): só o Minerador usa, 3 de cada minério da receita por lingote, sem XP.

## Contexto
A fundição vanilla não funcionava nem tinha interface. A regra de domínio do governador só admite receitas de produção para artesãos; o Minerador é profissão de coleta.

## Opções consideradas
| Opção | Prós | Contras |
|---|---|---|
| Receita de produção do Minerador no governador | XP, auditoria e journal do governador | Muda `config.ts`, `rules.ts`, XP e o domínio de coleta |
| Serviço próprio, no modelo do refino | Isolado, sem mexer no domínio | Sem XP de fundição |

## Decisão
Serviço próprio, ligado com `AETHERIUS_PROFESSIONS_WORLD`. 12 receitas de minério da estação `CraftingSmelter` (houseCARL). Aço = 3 Ferro + 3 Coríndon (confirmado pelo dev). Usa a tela de refino no modo "Fundição".

## Consequências
- (+) Entregue sem alterar o domínio do governador.
- (−) Fundir não dá XP; se for desejado, revisar esta decisão.
