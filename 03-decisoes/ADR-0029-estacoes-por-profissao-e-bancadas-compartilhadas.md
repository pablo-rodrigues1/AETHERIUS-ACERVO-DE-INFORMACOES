---
titulo: Estações por profissão e bancadas compartilhadas
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [pablo-rodrigues1]
atualizado: 2026-11-24
tags: [profissoes, artesanato, refino, alquimia, encantamento, estacoes]
relacionados: [../02-sistemas/profissoes/README.md, ../02-sistemas/profissoes/artesanato.md, ../02-sistemas/profissoes/refino-e-encantamento.md, ADR-0003-codigo-prevalece-sobre-planilhas-de-design.md]
---

# ADR-0029 — Estações por profissão e bancadas compartilhadas

## Resumo
Cada estação só abre para a profissão certa, decidido no servidor. Forja e Curtume são compartilhados por Ferreiro, Artífice, Alfaiate e Curtidor e abrem sempre a oficina inteira da profissão do jogador, com a animação vanilla da bancada.

## Contexto
A staff (20/11/2026) relatou que refino, alquimia e encantamento estavam abertos a qualquer jogador. A especificação do Core (`WORKSTATION_AUTHORIZATION_AND_RECIPE_VISIBILITY.md`) previa que, na bancada compartilhada, aparecessem só as receitas da profissão ligadas àquela bancada.

## Opções consideradas
| Opção | Prós | Contras |
|---|---|---|
| Receitas filtradas pela bancada (especificação) | Coerência visual bancada/receita | Curtidor na forja veria lista vazia |
| Oficina inteira da profissão em qualquer bancada compartilhada | Simples para o jogador; uma tela por profissão | Diverge da especificação |

## Decisão
Decisão do dev (23/11/2026), implementada no servidor v0.47.0:
- Forja e Curtume: Ferreiro, Artífice, Alfaiate e Curtidor; tela e receitas da profissão do jogador.
- Amolador e bancada de armadura: Ferreiro ou Curtidor (conferido ao ativar e ao iniciar o refino).
- Mesa de alquimia: Alquimista. Mesa de encantamento: Encantador, que segue fechado à escolha do jogador mas pode ser atribuído pela staff.
- Demais estações: profissão dona das receitas.

## Consequências
- (+) Acesso controlado no servidor; aviso "Apenas X pode usar esta estação."
- (−) Diverge da especificação do Core (registrado em `docs/mechanics/PROFISSOES_SERVIDOR_V0.47.0.md`).
- (−) Refino, alquimia e encantamento dependem do mundo das profissões ligado (`AETHERIUS_PROFESSIONS_WORLD`).
