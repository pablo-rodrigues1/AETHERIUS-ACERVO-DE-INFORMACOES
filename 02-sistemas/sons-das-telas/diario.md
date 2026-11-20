---
titulo: Diário dos Sons nas telas
tipo: historico
status: ativo
responsaveis: [kdu]
atualizado: 2026-11-20
tags: [diario, ui, som]
relacionados: [README.md]
---

# Diário dos Sons nas telas

## Resumo
Registro cronológico, do mais recente para o mais antigo.

## 2026-11-20 (kdu / Claude Opus 5.5)
- **Feito:**
  - Pedido do Melekyy: som nos cliques das telas, com sons do Skyrim. O kdu pediu sons variados.
  - São 13 tipos de som, ligados por contexto (ver README), na UI Meridian e no navegador do SkyMP.
  - Só cliente: nada vai ao servidor, sem plugin e sem DLL.
  - O patch aplica na v0.38 pura e também depois do pacotão + lobisomem; `tsc` limpo.
  - Teste no navegador: 27/27.
- **Decisão:** a classificação é automática (classe, texto e tipo do controle), para não ter que editar cada módulo. O `data-sound` fica para ajustes finos.
- **Próximo:** ouvir em jogo; o mercurius17 decide se entra no UI Core.
- **Bloqueios:** nenhum.
