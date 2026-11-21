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
Todos os menus exclusivos do Aetherius fazem barulho em tudo, só com sons do próprio Skyrim. Cada tipo de ação tem um som:
- clique, fechar, aba, página;
- compra, confirmação, ligar/desligar;
- pegar e soltar item, deslizar, mouse em cima.

Os sons cobrem também duplo clique, botão direito, listas, barras deslizantes, arrastar, roda do mouse, envio de formulário e atalhos de teclado. Abrir e fechar o menu tocam, e o inventário tem o som de abrir. Pedido do Melekyy em 19/11.

## Onde está o código
- **Página:** `client/ui-core/frontend/ui-sounds.js`. Carregado no `index.html` antes do `shell.js` e no `chargen.html` antes do `chargen-visuals.js`.
- **Cliente:** `client/src/services/services/uiSoundService.ts` e `uiSoundScript.ts` (o mesmo script em texto, injetado no navegador do SkyMP).
- **Criação de personagem (DLL da UI):**
  - o comando `sound` em `client/ui-core/native/src/chargen.cpp`;
  - a tabela de editor IDs em `chargen-policy.h`.
- **Testes:**
  - `client/ui-core/tests/ui-sounds.browser.cjs`;
  - a auditoria `ui-sounds-audit.cjs`, que roda os testes das telas reais e acusa qualquer ação sem som.

## Escopo
- Inclui:
  - a UI Meridian toda: menu do TAB e todos os módulos, as oficinas (incluindo o molde do curtume), a roda de gestos, o baú e o refino;
  - a criação de personagem, quando a DLL da UI for recompilada;
  - o navegador do SkyMP: login, chat, painel da Staff, residências e as telas que o servidor injeta (barracas, mortes, casas, social).
- Não inclui:
  - os menus do jogo, que já têm som;
  - o HUD, que não tem nada clicável;
  - digitação em campos de texto.

## Funcionamento
1. **A página percebe a ação.** Pode ser um clique, duplo clique, botão direito, escolha em lista, barra, arrasto, roda, envio de formulário ou tecla.
   - Teclas só contam quando a tela tratou a tecla. Letras só tocam nas nossas telas, para o chat do SkyMP não fazer barulho a cada letra.
2. **A página decide o tipo de som,** nesta ordem:
   1. `data-sound` no elemento;
   2. tipo do controle;
   3. classe ou id;
   4. texto curto do botão.
3. **A página avisa quem toca o som:**
   - **UI Meridian:** envelope sem sessão, que o cliente nunca repassa ao servidor.
   - **Navegador do SkyMP:** mensagem `aetherius-ui:som`.
   - **Criação de personagem:** comando `sound` para a DLL da UI, que toca com `RE::PlaySound`. A página só manda se a DLL nova se anunciou (`__aetheriusChargenSound`). A DLL antiga reenviaria o estado da tela a cada som.
4. **O som toca no personagem.** Nada vai ao servidor. Fechar pelo "×" ou ESC já tocou o "cancelar", então o "fechar" que vem logo depois fica mudo.

## Parâmetros e fórmulas
| Tipo | Quando | Som do Skyrim (SOUN) |
|---|---|---|
| ok | clique comum, botão direito em item, Enter, letra de atalho | UIMenuOK 0x137E7 |
| cancel | fechar, cancelar, voltar, ESC, botão direito na roda de gestos | UIMenuCancel 0x137E8 |
| tab | abas, tecla TAB | UIMenuPrevNext 0x137E9 |
| next / prev | página seguinte / anterior | ITMBookPageTurnForward 0x7EA3F / Backward 0x8691E |
| gold | comprar, vender, pagar | ITMGoldUp 0x334AA |
| confirm | confirmar, aceitar, criar, forjar, aprender, encaixar, enviar formulário | UISkillsPerkSelectA 0x643F5 |
| on / off | caixa de marcar, chave, filtro | UIItemGenericUp 0x14110 / Down 0x14111 |
| focus | mouse em cima de botão ou aba, setas | UISkillsFocus 0x6CC5E |
| take | PEGAR / GUARDAR / TODOS no baú, duplo clique que passa item, pegar a peça | ITMGenericUp 0x14115 |
| place | soltar a peça arrastada | ITMGenericDown 0x14114 |
| slide | barra deslizante, girar a peça com a roda, traço do molde | UIInventorySlide 0x3AD51 |
| open / close | a UI pega / solta o foco | UIMenuActive 0x57F94 / UIMenuInactive 0x57F95 |
| inventory | abrir o inventário | UIInventoryOpen 0x3AD50 |

| Parâmetro | Valor | Descrição |
|---|---|---|
| `aetherius-ui-sounds` (skymp5-client) | ligado / `false` | desliga tudo |
| `aetherius-ui-sounds-hover` (skymp5-client) | ligado / `false` | desliga só o som de mouse em cima |
| `data-sound` (HTML) | ok, cancel, tab, next, prev, gold, confirm, on, off, focus, take, place, slide, none | força o som de um elemento |
| `data-sound-drag`, `data-sound-wheel`, `data-sound-dblclick`, `data-sound-context="cancel"` (HTML) | presença | ligam pegar/soltar ao arrastar, tique na roda, duplo clique que move item e botão direito que fecha em telas novas |
| Intervalo mínimo entre sons | 20 ms (cliente), 25 ms (ação na página), 90 ms (mouse em cima e tiques de barra/roda) | junta clique, tecla e envio do mesmo gesto num som só |
| "Fechar" calado depois do "cancelar" | 400 ms | evita som duplo ao fechar pelo "×" ou ESC |

## Dependências
| Depende de | Por quê |
|---|---|
| UI Core (mercurius17) | as páginas, o canal `aetheriusUiSend` e a DLL da criação de personagem |
| Skyrim.esm | os sons |

## Pendências conhecidas
- Ouvir em jogo:
  - o volume do som de mouse em cima;
  - se o som toca com a UI aberta;
  - se o `RE::PlaySound` toca com o menu de raça aberto.
- DLL de release, com o fork real do Meridian, para a criação de personagem ter som. Sem ela, só a criação fica muda.
