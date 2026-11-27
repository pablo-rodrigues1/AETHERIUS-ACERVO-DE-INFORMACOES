---
titulo: UI no CEF do Skyrim Platform, sem Meridian UI e Prisma UI
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [mercurius17, pablo-rodrigues1]
atualizado: 2026-11-26
tags: [interface, cef, skyrim-platform, meridian, prisma, desempenho]
relacionados: [../02-sistemas/ui-core/README.md, ../02-sistemas/ui-core/diario.md, ../02-sistemas/ui-core/migracao-cef-skyrim-platform.md, ../02-sistemas/ui-core/investigacao-inventario-skyui.md]
---

# ADR-0037 — UI no CEF do Skyrim Platform, sem Meridian UI e Prisma UI

## Resumo
Todas as interfaces (UI Core, HUD, criação de personagem, página do SkyMP) rodam num único navegador: o CEF do próprio Skyrim Platform (backend `tilted`, Chromium 108). Os mods Meridian UI, Prisma UI e "AETHERIUS - MERIDIAN - COMPATIBILIDADE" saíram dos dois perfis.

## Contexto
- O Meridian UI (CEF 152) trazia um segundo runtime Chromium (~600 MB no mod UI CORE), uma view CEF por tela (main, HUD, loading, chargen), um menu de foco próprio e o renderer de NIF/RenderLayer. O cliente já carregava o CEF do Skyrim Platform no pacote, sem usar.
- O Prisma UI estava instalado e ligado nos perfis, mas nenhum plugin o usava (só a própria `PrismaUI.dll` cita a API).
- O `SkyrimPlatformImpl.dll` distribuído desde o v0.8.2 só tinha o backend Meridian. O par original (v0.8.1, mesmo código do SP no aetherius-server) aceita `off/tilted/nirnlab/auto`; a API TypeScript é idêntica (as únicas diferenças do fork eram do backend de navegador).

## Opções consideradas
| Opção | Prós | Contras |
|---|---|---|
| Manter Meridian | Nada muda; prévia 3D de itens | Dois runtimes Chromium, várias views CEF, ~600 MB, foco por menu próprio |
| Prisma UI | Framework mantido | Mais um CEF; reescrever a bridge do mesmo jeito |
| **CEF do Skyrim Platform (escolhida)** | Um só navegador, já carregado pelo SP; menos memória/GPU; mod UI CORE com 15 MB | Chromium 108 (mais antigo); perde a prévia 3D (NifView) e o atalho de controle do Meridian |

## Decisão
- Mod novo **AETHERIUS - SKYRIM PLATFORM CEF** (no lugar do de compatibilidade): `SkyrimPlatform.dll` + `SkyrimPlatformImpl.dll` originais (v0.8.1), `BackendName=tilted`, runtime CEF 108 completo na mesma pasta física (o Impl importa `libcef.dll`; o MO2 exige a dependência ao lado), só locales en-US/pt-BR, e os arquivos de Interface que vinham no mod de compatibilidade.
- As views viram **iframes** da página `Data/Platform/UI/index.html` (`aetherius-host.js`). Cada iframe tem origem `file://` própria; a conversa é só por `postMessage` (`aetheriusui/host-shim.js`).
- A **AetheriusUIBridge** deixou a API do Meridian: uma camada `Host` (`native/src/host.*`) com os mesmos métodos (CreateView, ExecuteJavaScript, TryFocus, RegisterListener...) fala com o client pelo **IPC do Skyrim Platform** (`SkyrimPlatform_IpcSend`/`IpcSubscribe`; `ipcMessage` chega no *tick*, que roda com o jogo pausado — necessário no RaceSexMenu). Mod events só chegam no *update* e parariam.
- No client, `AetheriusHostService` liga IPC ↔ página e dá foco ao navegador quando uma view tem foco; `src/aetherius/hostFocus.ts` substitui todas as checagens de `MeridianUI_FocusMenu`.
- TAB/I/Q/P: o menu vanilla aberto pela tecla é fechado na hora e a UI abre no lugar (TweenMenu agora entra junto com Inventory/Favorites/Magic). Sem hook de input novo.

## Consequências
- (+) Um único Chromium no processo; mod UI CORE de ~600 MB para 15 MB; sem view CEF de HUD/loading separadas (iframes escondidas não desenham).
- (+) Bridge compilada (MSVC 14.51, CommonLibSSE-NG `ng`) sem dependência do Meridian.
- (−) Prévia 3D de itens desligada (o frontend esconde a área; mostra o ícone).
- (−) Atalho de controle (Select+LB) e navegação por gamepad do `MeridianInput` não existem mais.
- (−) Sem movimento do personagem com a UI aberta (o SP bloqueia a entrada do jogo com o navegador focado) — igual ao comportamento "UI aberta = parado" que já era o pretendido.
- (−) A DLL nova exige o VC++ Redistributable 14.5x (VS 2026).
- Pendente: teste em jogo (login, chargen, TAB, inventário, mapa, voz) nos dois perfis, release do client/mods e assinatura das DLLs.
