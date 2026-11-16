---
titulo: Sobrenatural - origem e tempos dos poderes
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [pablo-rodrigues1]
atualizado: 2026-11-16
tags: [sobrenatural, grants, poderes]
relacionados: [../02-sistemas/sobrenatural/formas-e-poderes.md, ADR-0015-sobrenatural-lotes-js.md]
---

# ADR-0019 — Origem dos grants e tempos dos poderes

## Resumo
O dev determinou preservar magias/perks preexistentes, integrar Bruxaria ao reconciliador por origem e mudar quatro poderes para 40 s com reutilização após 60 s. Este é um candidato de laboratório, sem aprovação de produção.

## Contexto
A união dos catálogos não prova autoria e removia magias pessoais; duas fontes podem compartilhar uma perk. Os records medidos tinham Força/Névoa 10 s, Celeridade 20 s e Enxame 15 s, em vez de um valor único de 20 s.

## Decisão
1. Só remover grants cuja autoria tenha sido registrada/confirmada; histórico ambíguo é preservado. Perks de GameplayCore/Sobrenatural usam claims conjuntos e proteção de origem externa.
2. Força Imensurável, Forma de Névoa, Celeridade Não Natural e Enxame Devorador têm 40 s de efeito e recarga autoritativa de 60 s desde o uso; Morcego mantém dash de 1 s sem recarga. Padrão/local VAMPIRE_POWER_COOLDOWN_SECONDS=60. A delimitação de Sedução/Reanimação fora da mudança é do orquestrador, a confirmar com o dev.
3. Névoa/regeneração e Força/dano continuam parciais; nenhum nativo é alterado. Enxame conserva provisoriamente 10/s e raio 10 pés do COTV, agora pela janela de 40 s autorizada.
4. Reserva de Sangue usa a API do lote Terra reserve/commit/cancel para 5 por uso aceito; a lista de poderes elegíveis é documentada para confirmação. Lorde comita após fase confirmada; módulo ausente mantém caminho sem custo com aviso único.
5. AETHERIUS_VAMPIRE_SERVER_SUN permanece desligada; sua futura ativação suprime apenas o grant solar próprio. O dono do tick solar trata fontes externas antes de ativar.

## Consequências
Revisão Terra e testes em jogo seguem pendentes. Nenhum efeito visual, dano ou replicação é considerado homologado apenas por teste Node; o roteiro fica preparado para depois por decisão do dev.
