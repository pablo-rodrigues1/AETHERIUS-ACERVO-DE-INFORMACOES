---
titulo: Migração da UI para o CEF do Skyrim Platform (sem Meridian/Prisma) e servidor local
tipo: referencia
status: ativo
responsaveis: [mercurius17, pablo-rodrigues1]
atualizado: 2026-11-26
tags: [interface, cef, skyrim-platform, mo2, servidor-local]
relacionados: [README.md, diario.md, ../../03-decisoes/ADR-0037-ui-no-cef-do-skyrim-platform-sem-meridian-e-prisma.md]
---

# Migração da UI para o CEF do Skyrim Platform

## Resumo
Como o cliente passou a usar só o CEF do Skyrim Platform (ADR-0037), o que mudou em cada repositório/mod e como subir o servidor local para testar.

## Arquitetura
```
AetheriusUIBridge.dll ──IPC do SP ("AetheriusHost")──▶ skymp5-client (AetheriusHostService)
        ▲                                                   │ executeJavaScript
        │ sp.sendIpcMessage("AetheriusHostIn")              ▼
        └──────────── skyrimPlatform.sendMessage ◀── Data/Platform/UI/index.html (aetherius-host.js)
                                                            │ postMessage
                                                            ▼
                                     iframes aetheriusui/{index,hud,loading,chargen}.html (host-shim.js)
```
- Ordens da bridge: `create`, `destroy`, `visible`, `focus`, `exec`, `listen`. Pedidos das views: `ready`, `call` (só funções registradas pela bridge).
- O caminho antigo `mod://aetheriusui/x.html` vira `aetheriusui/x.html` relativo à página.
- Os pacotes do UI Core (`AetheriusUI.ToView`/`FromView`/`FocusState`) continuam por mod event; só o transporte das views mudou.

## Arquivos (aetherius-client)
- `client/ui-core/native/src/host.{h,cpp}` (novo), `main.cpp`, `chargen.{h,cpp}`, `CMakeLists.txt` (sem `MERIDIAN_SOURCE_DIR`; teste `KeyboardMovementPolicyTests` removido).
- `client/ui-core/frontend/host-shim.js` (novo, incluído em index/hud/loading/chargen.html); `native-presentation.js` sem prévia 3D.
- `client/aetherius-host.js` (novo), `client/index.html` (contêiner `#aetherius-views`; módulos do sobrenatural em `aetheriusui/`).
- `client/meridian/bootstrap.js` (só `front-loaded` e atalhos), `SkyrimPlatform.ini` (`BackendName=tilted`), testes `bootstrap/config/emote-control`.
- `client/src/services/services/aetheriusHostService.ts` (novo), `src/aetherius/hostFocus.ts` (novo), `browserService.ts`, `index.ts`; `meridianDiagnosticsService.ts` removido.
- `client/ui-core/scripts/package.cjs`: monta `Platform/UI` + `Platform/UI/aetheriusui`, sem `--meridian`.
- Perfis Qualidade/Performance: `+AETHERIUS - SKYRIM PLATFORM CEF` no lugar do mod de compatibilidade; Meridian UI e Prisma UI fora.
- O `build-client.bat` (fora deste snapshot) precisa copiar `aetherius-host.js`, `meridian/bootstrap.js` e `meridian/widgets.js` para `Data/Platform/UI` e trocar o par SP do `Data` pelo original (ou confiar no mod do MO2, que vence o `Data`).

## Testes feitos
- `tsc --noEmit` limpo; webpack ok; `node --test meridian/*.test.cjs`: 181/188 — as 7 falhas já existiam na base original (Teleport2, BrowserService/PTT).
- AetheriusUIBridge.dll compilada (Release, x64) sem import do Meridian.
- **Não testado em jogo** (a pasta do jogo desta máquina não tem SKSE/client instalados).

## Servidor local (`C:\Code\Aetherius - OFICIAL\servidor-local`)
- Base: binários do release server v0.50.0 (`scam_native.node`, `dist_back/skymp5-server.js`); `gamemode`/`config` do repositório (27 arquivos mais novos); `gameplay` compilado do fonte (`tools/stage-gameplay.js`); `data/` sincronizado do MO2 (`sync-client-plugins.js`, 444 plugins, sem mudança de slot).
- PostgreSQL próprio em `servidor-local/pgdata` (porta 5433, só 127.0.0.1, sem senha). `db/site-local.sql` cria a tabela `users` mínima do site e um usuário de teste (profileId 105890).
- `.env`: todos os módulos do oficial, exceto a Alma (precisa do bot do Discord). SkyDeath no lugar do death-service antigo.
- `iniciar-servidor-local.bat` / `parar-servidor-local.bat`. Boot: 17 módulos ativos, 0 falhas, 7777/UDP e voz em 7778.
- No cliente: ligar o mod **AETHERIUS - SERVIDOR LOCAL** (só aponta para 127.0.0.1) e desligar para jogar no oficial.
- Não regenerar: o `security-manifest.json` foi refeito com `--previous` incluindo as DLLs novas.
