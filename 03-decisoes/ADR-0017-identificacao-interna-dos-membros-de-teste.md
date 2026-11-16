---
titulo: Identificação interna dos membros de teste
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-15
tags: [guildas, interface, testes]
relacionados: [../02-sistemas/guildas/README.md, ADR-0009-bibliotecarios-fabricacao-e-traducao.md]
---

# ADR-0017 — Identificação interna dos membros de teste

## Contexto

O dev pediu retirar a tag de fictício da lista e acrescentar quatro membros de teste no privado. O ADR-0009 previa identificação no painel.

## Decisão

- Retirar apenas a tag visual da lista; manter `testOnly` no estado persistido para distinguir os perfis reservados dos reais. Isto substitui somente a identificação visual mencionada no item 8 do ADR-0009.
- Acrescentar quatro cadastros comuns exclusivos do privado, preservando os anteriores. Não criar contas/atores, dar funções ou definir escolas automaticamente. O total privado desta operação é oito membros, dos quais sete são de teste.
- Registrar as inclusões com autor e alvo no histórico privado. Não publicar nomes/IDs ou snapshots de jogadores no acervo.
- Preservar cargos, liderança, escolas, quartos, inventários e regras de acesso. A identificação interna não concede nem retira permissões.

## Consequências

O operador usa o cadastro interno para reconhecer perfis de teste; a interface não tem mais a tag solicitada. Os registros continuam exclusivos do privado, sem comprovar integração/homologação no servidor oficial.
