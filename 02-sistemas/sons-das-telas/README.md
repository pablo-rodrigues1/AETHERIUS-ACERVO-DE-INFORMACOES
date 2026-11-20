---
titulo: Sons nas telas
tipo: sistema
status: rascunho
fase: em-teste
responsaveis: [kdu]
repositorio: "pacote separado: patch sobre aetherius-client v0.38 (ainda não integrado ao oficial)"
depende_de: [ui-core]
atualizado: 2026-11-20
tags: [ui, som, cliente, meridian]
relacionados: [diario.md, ../ui-core/README.md]
---

# Sons nas telas

## Resumo
Os cliques nas telas próprias do Aetherius passam a fazer barulho, só com sons do próprio Skyrim. Cada tipo de ação tem um som: clique comum, fechar, aba, página, compra, confirmação, ligar/desligar e mouse em cima. Abrir e fechar o menu também tocam, e o inventário tem o som de abrir. Pedido do Melekyy em 19/11.

## Onde está o código
- Página: `client/ui-core/frontend/ui-sounds.js` (carregado no `index.html` antes do `shell.js`).
- Cliente: `client/src/services/services/uiSoundService.ts` e `uiSoundScript.ts`, o mesmo script em texto, injetado no navegador do SkyMP.
- Teste: `client/ui-core/tests/ui-sounds.browser.cjs`.

## Escopo
- Inclui:
  - a UI Meridian toda (menu do TAB e módulos, roda de gestos, baú, refino);
  - o navegador do SkyMP (login, chat, painel da Staff, residências).
- Não inclui:
  - os menus do jogo, que já têm som;
  - a criação de personagem, que só fala com o DLL do bridge.

## Funcionamento
- A página percebe o clique e decide o tipo de som em quatro passos, nesta ordem:
  1. `data-sound` no elemento;
  2. tipo do controle;
  3. classe ou id;
  4. texto curto do botão.
- A página avisa o cliente.
  - Na UI Meridian, o aviso vai como envelope sem sessão. O cliente nunca repassa esse envelope ao servidor.
  - No navegador do SkyMP, vai como mensagem `aetherius-ui:som`.
- O cliente toca o som do Skyrim no personagem. Nada vai ao servidor.

## Parâmetros e fórmulas
| Tipo | Quando | Som do Skyrim (SOUN) |
|---|---|---|
| ok | clique comum | UIMenuOK 0x137E7 |
| cancel | fechar, cancelar, voltar | UIMenuCancel 0x137E8 |
| tab | abas | UIMenuPrevNext 0x137E9 |
| next / prev | página seguinte / anterior | ITMBookPageTurnForward 0x7EA3F / Backward 0x8691E |
| gold | comprar, vender, pagar | ITMGoldUp 0x334AA |
| confirm | confirmar, aceitar, criar, forjar, aprender | UISkillsPerkSelectA 0x643F5 |
| on / off | caixa de marcar, chave | UIItemGenericUp 0x14110 / Down 0x14111 |
| focus | mouse em cima de botão ou aba | UISkillsFocus 0x6CC5E |
| open / close | a UI pega / solta o foco | UIMenuActive 0x57F94 / UIMenuInactive 0x57F95 |
| inventory | abrir o inventário | UIInventoryOpen 0x3AD50 |

| Parâmetro | Valor | Descrição |
|---|---|---|
| `aetherius-ui-sounds` (skymp5-client) | ligado / `false` | desliga tudo |
| `aetherius-ui-sounds-hover` (skymp5-client) | ligado / `false` | desliga só o som de mouse em cima |
| `data-sound` (HTML) | ok, cancel, tab, next, prev, gold, confirm, on, off, focus, none | força o som de um elemento |
| Intervalo mínimo entre sons | 50 ms (cliente), 60 ms (clique), 90 ms (mouse em cima) | evita som repetido |

## Dependências
| Depende de | Por quê |
|---|---|
| UI Core (mercurius17) | as páginas e o canal `aetheriusUiSend` |
| Skyrim.esm | os sons |

## Pendências conhecidas
- Ouvir em jogo: volume do som de mouse em cima; conferir se o som toca com a UI aberta.
- Criação de personagem sem som: precisaria mudar o bridge nativo.
