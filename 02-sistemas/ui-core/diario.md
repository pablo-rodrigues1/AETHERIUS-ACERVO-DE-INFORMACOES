---
titulo: Diário do Aetherius UI Core
tipo: historico
status: ativo
responsaveis: [mercurius17]
atualizado: 2026-11-14
tags: [diario, interface]
relacionados: [README.md]
---

# Diário do Aetherius UI Core

## Resumo
Registro cronológico do desenvolvimento, do mais recente para o mais antigo. Entradas curtas.

## 2026-11-14, relatos do Akatosh (kdu / Claude Opus 5.5)
- **Feito** (entregue em `missoes/2026-11-14-bugs-akatosh` do kdu, patches sobre a v0.38, não aplicado no repositório):
  - Inventário: arma na mão mostrava "R›" com as letras sobrepostas (`letter-spacing:-3px`). Agora tudo na mão usa a marca da magia, `L`, `R` ou `L R`.
  - FEITIÇOS: magia não desequipava. A tela já pedia, mas o servidor (`aetheriusInventoryService.ts`) não oferecia `unequipAbility` e a VM Papyrus não tinha `Actor.UnequipSpell`. Os dois foram feitos, com teste 4/4.
  - Tela de loading: a view `loading.html` nunca aparece, porque a ponte a mostra numa task do SKSE que só roda depois do carregamento. A mesma imagem foi embutida no `loadingmenu.swf` do mod "MENU E LOGO", girando no canto.
  - Constelação no `;`: no ABNT2 o `;` é o 0x35, e o controlmap do mod de compatibilidade liga o Quick Stats nele. A linha passou para 0xff.
- **Testes:** suíte do ui-inventory igual antes e depois (65/13). Nada testado em jogo.
- **Próximo:** o mercurius17 decidir se aplica; testar em jogo.
- **Bloqueios:** nenhum

## 2026-11-14 (mercurius17 / Claude Opus 5.5)
- **Feito:** sincronização SYNC-1114, commit `e9075d0` na `main`, a partir da cópia `client/ui-core` do cliente oficial de 13/11 (base anterior: `6658310`). Entrou:
  - criação de personagem que edita pelo RaceSexMenu e finaliza de verdade (`chargen-finish.js`, `native/src/chargen*`);
  - bridge lendo descrições de feitiço/item e efeitos ativos (`game_text.h`, `localized_presentation.h`, `native-presentation.js`);
  - tela de carregamento (`loading.html`, `brand/carregando.png`);
  - shell com trava do menu (ferido/espírito), abertura direta de módulo (tecla I) e aviso de inventário aberto;
  - fonte Sovngarde (OFL) e ajustes do inventário (marca de tier de refino).
- **Adaptações:** `native/CMakeLists.txt` recebeu só as mudanças reais (guarda de `CMAKE_MSVC_RUNTIME_LIBRARY`, alvo `LocalizedPresentationTests`), com os caminhos deste repositório. O teste `character-creation-import.test.cjs` deixou de exigir "nenhuma escrita" e passou a conferir que as escritas passam por `IsChargenCommand` e que o nome é validado antes de `ChangeName`.
- **Ficou de fora:** profissões, loja, casas, roda de gestos (GPL-3.0), refino, baú e propriedades. Sem essas telas o `index.html` do cliente é igual ao daqui.
- **Testes:** JS alterados sem erro de sintaxe; `character-creation-import` 3/3; `native-chargen-policy` passa (MinGW g++ 8.1); `native-localized-presentation` e a DLL não compilados nesta máquina.
- **Próximo:** compilar a bridge e o teste de apresentação localizada em ambiente com MSVC; atualizar o README do repositório (trechos de 04/10).
- **Bloqueios:** nenhum
- **Decisões:** [ADR-0002](../../03-decisoes/ADR-0002-sincronizacao-dos-cores-com-os-repositorios-oficiais.md)
- **Nota completa no repositório:** `docs/SINCRONIZACAO_CLIENTE_20261114.md`
