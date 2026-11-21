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

## 2026-11-20, noite (kdu / Claude Opus 5.5)
- **Pedido:** o kdu pediu que todos os menus exclusivos do Aetherius tivessem som em tudo, cliques e o resto.
- **Levantamento das telas:**
  - UI Meridian: módulos, oficinas, gestos, baú, refino;
  - HUD: não tem nada clicável;
  - criação de personagem;
  - painel da Staff, residências e telas injetadas pelo servidor (barracas, mortes, casas, social).
- **Feito:** além do clique, agora tocam:
  - duplo clique no baú;
  - botão direito;
  - escolha em lista;
  - barras deslizantes;
  - arrastar a peça do refino e o traço do molde;
  - roda que gira a peça;
  - Enter em formulário;
  - atalhos de teclado que a tela tratou.
- **Sons novos:**
  - take: ITMGenericUp, para PEGAR/GUARDAR e o duplo clique;
  - place: ITMGenericDown;
  - slide: UIInventorySlide.
- **Criação de personagem:** a DLL da UI ganhou o comando `sound` (`RE::PlaySound`). A DLL compila e o teste nativo passa.
- **Auditoria:** roda os 6 testes de telas reais com o script e acusa ações sem som. Resultado: 74 sons e 0 lacunas. O teste principal passou de 27 para 33/33.
- **Conferido:** o patch aplica na v0.38 pura e sobre o pacotão + lobisomem; `tsc` limpo.
- **Decisões:**
  - Teclas só tocam se a tela tratou a tecla (`preventDefault`). Letras só nas nossas telas, para não fazer barulho no chat do SkyMP.
  - A página só manda som para a DLL da criação se a DLL nova se anunciou. A DLL antiga reenviaria o estado e a barra pularia ao arrastar.
  - O "fechar" logo depois do "cancelar" fica mudo, para não sair som duplo.
  - Intervalo mínimo da página: de 60 ms para 25 ms. A auditoria mostrou que 60 ms engolia cliques rápidos de verdade.
- **Próximo:**
  - ouvir em jogo;
  - DLL de release com o fork real do Meridian;
  - o mercurius17 decide se entra no UI Core.
- **Bloqueios:** nenhum.

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
