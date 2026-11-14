---
titulo: Adoção do acervo de informações
tipo: decisao
status: ativo
decisao: aceito          # proposto | aceito | rejeitado | substituido
substitui: null
responsaveis: []
atualizado: 2026-11-14
tags: [processo, documentacao]
relacionados: [../REGRAS.md]
---

# ADR-0000 — Adoção do acervo de informações

## Resumo
A documentação de sistemas e decisões do Aetherius passa a viver neste repositório, somente em Markdown, seguindo `REGRAS.md`.

## Contexto
Informação dispersa em conversas, documentos soltos e PDFs dificultava a consulta pelo time e por IAs.

## Decisão
- Repositório público único, estrutura numerada, apenas `.md`.
- Decisões registradas como ADRs imutáveis.
- Validação automática via CI.

## Consequências
- (+) Consulta rápida e barata por IAs; histórico rastreável via git.
- (−) Materiais em PDF precisam ser resumidos manualmente.
