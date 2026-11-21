---
titulo: ESPFE e pasta de jogo únicos do Sobrenatural
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [pablo-rodrigues1, kdu]
atualizado: 2026-11-21
tags: [sobrenatural, modpack, espfe, formid]
relacionados: [../02-sistemas/sobrenatural/README.md, ADR-0027-ceu-sincronizado-pelo-gamedayspassed.md]
---

# ADR-0028 — ESPFE e pasta de jogo únicos do Sobrenatural

## Resumo

O Sobrenatural passa a usar o ESPFE e a pasta de jogo AETHERIUS - SOBRENATURAL. As dependências externas e o UI Core ficam em suas pastas.

## Contexto

A entrega 20/11 e o DVA Visuals usam três plugins próprios. A primeira publicação oficial acrescenta o sistema à ordem existente. No laboratório LOCAL, a troca desloca 34 plugins regulares e muda os FormIDs do derivado visual. Dividir a raiz MeridianUI entre mods já causou 404 no CEF.

## Opções consideradas

| Opção | Prós | Contras |
|---|---|---|
| ESPFE único no slot visual do laboratório e no fim dos leves oficiais | Consolida 149 records novos e 18 overrides. Preserva IDs locais do ESP principal e os 437 slots oficiais | O laboratório precisa de dados compatíveis com a nova ordem |
| ESP regular no slot antigo do laboratório | Preserva IDs numéricos do ESP principal local | Visões perde para Predator Vision nessa posição e o derivado visual muda de IDs |

## Decisão

- O dev escolheu o ESPFE v1.71 com o nome AETHERIUS - SOBRENATURAL. Os IDs locais do derivado DVA ganham +0x100. Preservar os masters da base. O Lobisomem do kdu e os terceiros continuam separados.
- O LOCAL inicia com novos dados e saves de teste depois de backup integral. A ordem e os dados antigos permanecem arquivados como um conjunto para rollback.
- A primeira publicação acrescenta o ESPFE ao fim dos leves, em FE137, e preserva o lock dos 437 plugins existentes e os dados oficiais. Miggyluv, Colored Magic Auras e Werewolf New Anim são dependências separadas.
- O fork MIT do céu entra na pasta do Sobrenatural nos três perfis. O MoonMod upstream continua ativo e fornece ESP e texturas. O dev confirmou a redistribuição do derivado visual e dos WAV.
- O MeridianUI fica completo no UI Core de cada ambiente. O módulo inclui 16 arquivos de runtime, entre eles witch-gauge-reference.png.

## Consequências

- Candidato preparado fora do MO2. A integração oficial exige paridade dos perfis, lock de slots e regeneração do manifesto de segurança com o hash do fork.
- Instalação e arquivamento dos dados de teste aguardam revisão Neon do candidato e autorização do dev. O roteiro exige prova em jogo com dois clientes e verificação de CEF, SKSE e áudio.
