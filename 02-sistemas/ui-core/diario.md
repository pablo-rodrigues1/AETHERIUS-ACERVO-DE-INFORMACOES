---
titulo: Diário do Aetherius UI Core
tipo: historico
status: ativo
responsaveis: [mercurius17]
atualizado: 2026-11-20
tags: [diario, interface]
relacionados: [README.md]
---

# Diário do Aetherius UI Core

## Resumo
Registro cronológico do desenvolvimento, do mais recente para o mais antigo. Entradas curtas.

## 2026-11-20, performance dos menus (Meridian / Aetherius UI) (mercurius17 / Claude Opus 5.5)
- **Feito:** investigação de performance com três modelos em paralelo (Sol High, Gemini 4 Argon High, Opus 5.5 Medium), revisão de código e segurança, benchmark cruzado num harness único e versão final integrada. Entregue em `ENTREGA_PERFORMANCE_UI_2026-11-20/` (patch de 13 arquivos, guia de integração, relatório PDF, testes e benchmarks), **não aplicado** no repositório.
  - Causa do "Aguardando estado do servidor": todo menu abria do zero, as páginas do snapshot eram pedidas em série e o servidor reconstruía o modelo do inventário a cada página (paginação O(n²)).
  - Versão final: cache entre aberturas com revalidação condicional (`knownRevision` → `notModified`), pré-carga no radial e após o login (itens e feitiços), páginas em paralelo, servidor com o modelo montado uma vez por abertura e paginação O(n), fila da ponte que não trava os outros menus enquanto o Papyrus lê escolas de magia, loader de módulos em paralelo, snapshot do baú no pacote de abertura, animações do radial e do workspace encurtadas (mesmo estilo). Desfoque e transparência mantidos.
  - Segurança: cota geral mantida em 30/10 s; a cota de páginas vale só para continuação de inventário/feitiços com revisão e nunca reconstrói o modelo. As ações ficam travadas até o servidor confirmar o estado.
  - Resultado (simulação Chrome/Node, 300 itens, RTT 40 ms): reabrir o inventário 1.060 → 34 ms até a lista; primeira abertura após o login 1.163 → 76 ms; CPU do servidor por abertura com 600 itens 64,9 → 13,0 ms.
- **Testes:** suítes do projeto 50/51 (a falha restante, `armor-native-isolation`, exige o addon nativo e falha também no original); navegador 6/6; contrato do servidor 6/6. Nada medido em jogo; a DLL SKSE (`main.cpp`) não foi compilada.
- **Próximo:** o mercurius17 decidir se aplica; compilar a DLL; seguir o roteiro de validação em jogo do guia (11 verificações).
- **Bloqueios:** nenhum

## 2026-11-14, impacto da conciliação documental de feitiços (mercurius17 / Codex)
- **Feito:** registrada a [lista de 164 feitiços por classe](../gameplay-core/feiticos-por-classe.md), sincronizada entre as duas abas da planilha e as fichas da Staff.
- **Impacto:** os dados/espelhos do ClassSystem e a interface não foram alterados. O resumo authorizedSpells continua divergente da lista detalhada; o novo catálogo documental não comprova atualização do grimório em execução.
- **Próximo:** quando houver implementação autorizada, alinhar os campos de feitiços e os espelhos de UI com a política aprovada.
- **Bloqueios:** nenhum para documentação; nenhuma mudança nos contratos ou na implementação do UI Core.

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

## 2026-11-26 (pablo-rodrigues1 / Claude Opus 5.5)
- **Feito:** UI inteira no CEF do Skyrim Platform; Meridian UI, Prisma UI e o mod de compatibilidade removidos dos perfis Qualidade e Performance ([ADR-0037](../../03-decisoes/ADR-0037-ui-no-cef-do-skyrim-platform-sem-meridian-e-prisma.md), detalhes em [migracao-cef-skyrim-platform.md](migracao-cef-skyrim-platform.md)).
  - Bridge sem API do Meridian: camada `Host` sobre o IPC do SP; views viram iframes da página do SP; TweenMenu redirecionado como I/Q/P.
  - Client: `AetheriusHostService`, `hostFocus.ts` no lugar do `MeridianUI_FocusMenu`; SP original (v0.8.1) com `BackendName=tilted`.
  - MO2: mod novo `AETHERIUS - SKYRIM PLATFORM CEF`; `AETHERIUS - UI CORE` de ~600 MB para 15 MB.
  - Servidor local montado e testado (boot sem falhas) em `servidor-local/`.
  - Investigação do inventário do SkyUI com autoridade do servidor: [investigacao-inventario-skyui.md](investigacao-inventario-skyui.md).
- **Testes:** tsc limpo; testes Node 181/188 (7 falhas pré-existentes); DLL compilada. Nada testado em jogo.
- **Próximo:** testar em jogo nos dois perfis (login, chargen, TAB, inventário, mapa, voz); publicar releases de client/mods/servidor; assinar DLLs.
- **Bloqueios:** nenhum. Responsável do sistema (mercurius17) deve revisar; o dev pediu execução sem confirmações.
- **Perdas conhecidas:** prévia 3D de itens e atalho/navegação de gamepad do Meridian.

## 2026-11-27 (pablo-rodrigues1 / Claude Opus 5.5)
- **Corrigido:** mouse girando a câmera com a UI aberta.
  - O hook de DirectInput do Skyrim Platform só zera os botões do mouse; a bridge agora desliga câmera, movimento e combate no ControlMap enquanto uma view tem o foco.
  - TAB/I/P/Q saíram do controlmap do jogo e a bridge as escuta direto. O redirecionamento do TweenMenu falhava com o Skyrim Souls: o menu continuava no `menuStack` e a UI não abria por cima.
  - O `shell.js` não usa mais `focus`/`blur` da janela quando roda em iframe.
- **Feito:** design novo.
  - Shell sem radial: barra lateral com todos os menus e abertura direta no último usado; TAB/ESC fecham.
  - `theme.css` carregado por último em todas as views, e `page-theme.css` na página do SP (login, avisos, HUD, painel da Staff, residências). Visual sem molduras: seleção por realce de fundo, abas sublinhadas, botões fantasma.
- **Testes:** testes Node 181/188 (7 já falhavam antes); shell renderizado no Chrome headless. Os módulos não foram vistos com dados reais; o teste em jogo continua pendente.
- **Bloqueios:** nenhum.

## 2026-11-27 (pablo-rodrigues1 / Claude Opus 5.5) — menus nativos
- **Decisão do dev:** inventário, feitiços, favoritos, mapa, baú e o TAB voltam a ser os menus nativos, com o skin Norden UI e o Tween Menu Overhaul. A UI Core (CEF) fica só para as telas sem equivalente nativo.
- **Feito:**
  - Bridge sem redirecionar InventoryMenu/FavoritesMenu/MagicMenu/TweenMenu; controlmap original restaurado.
  - Baú pelo ContainerMenu vanilla (`remoteServer`); a pose da mochila do SkyInventory segue o InventoryMenu.
  - Opções do TMO em `client/ui-core/tweenoptions/aetherius-*.json` (instaladas em `interface/tweenoptions/`), com `papyrus_event` = `AetheriusTween_<Módulo>`. A bridge abre o módulo: Personagem, Classe, Sobrenatural (categoria Personagem); Grupo, Profissões (Diário); Propriedades, Casas, Loja (Mundo). A barra lateral da UI Core saiu.
  - Mods de UI (Norden UI, TMO, Infinity UI, Character Menu SE, Show Player In Menus, Compass Navigation Overhaul, Map Menu Extension, Scaleform Translation++) nos dois perfis. Plugins novos `Character Menu SE.esp` e `ShowPlayerInMenus.esp` entraram no fim da trava de slots, sem mudar slot nenhum. Servidor local sincronizado e security-manifest refeito.
- **Autoridade do servidor:** os menus nativos usam os fluxos originais do SkyMP (DropItem/PutItem/TakeItem/EatItem conferidos pelo servidor), com os ganchos do gamemode por cima.
- **Pendente:** teste em jogo; favoritos marcados no menu nativo não são salvos no servidor (os antigos da UI ainda são reaplicados no login); release do servidor com os 2 plugins novos.

## 2026-11-27 (pablo-rodrigues1 / Claude Opus 5.5) — opções do Tween sem efeito
- **Bug:** as opções próprias do Tween não abriam nada. O TMO mandava o evento, mas a bridge tentava abrir com o TweenMenu ainda no `menuStack`; as novas tentativas por `AddTask` recursivo se esgotavam no mesmo quadro.
- **Correção:** espera em tempo real (50 ms × 60) até o jogo liberar, mais log de cada pedido recebido ("Tween pediu ..."). Não testado em jogo.
- **Avaliado:** trocar as telas CEF por SWF (Scaleform). É possível, mas é reescrita completa de cada módulo em ActionScript 2 e um menu nativo (IMenu) na bridge por tela. Proposto começar com um piloto (Sobrenatural).
