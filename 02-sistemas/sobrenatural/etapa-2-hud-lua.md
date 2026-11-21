# Etapa 2 — HUD da Lua de Masser (21/11/2026)

**Estado:** HUD da Lua e Cálice da Noite instalados no perfil LOCAL em 21/11. A validação no CEF do jogo está pendente. A entrega inicial de Terra High foi `7e175bb45c0d57a7441420fb89f077805bf6dba1` sobre `ae7c1720bcb45912bd31b1885785e246cb25aebe` no repositório `C:\Code\Aetherius - Servidor Local\client-source`. O patch `C:\Code\Aetherius - Servidor Local\overlays\cliente-local\039-hud-lua-masser.patch` passou no reverse-check sobre essa entrega. [Instalação e evidências](#instalação-2111)

## Arte e comportamento

Referência aprovada: `C:\Code\Aetherius - New Systems\Sobrenatural\preview\hud-lua-propostas.html`, `hudFinal`, `litPath`, `.hud-final`. Disco, aro translúcido com oito marcas, diamante âmbar; Lua Cheia tem olho em fenda, brilho e aro respirando. Sem número, fundo ou tooltip. Opacidade .82, cheia 1 como a prévia. Fases mudam por interpolação de opacidade entre discos em 1,5 s; aro e estado cheio também transitam, com animações/transições desligadas em movimento reduzido. Primeiro estado e restauração são imediatos; repetição só renova watchdog de 3,5 s, sem reconstruir o SVG ou reiniciar o pulso.

Fases 0–7: Cheia, Minguante Gibosa, Quarto Minguante, Minguante, Nova, Crescente, Quarto Crescente, Crescente Gibosa. Aceita somente `{v:1, phase: inteiro 0..7, full: boolean}` e `full` deve equivaler a `phase === 0`; nulo ou inválido oculta. O proprietário e a fase são decisão do servidor; nenhum cálculo astronômico ou mutação de gameplay ocorre no cliente.

## Integração necessária do servidor

O `updateOwner` da propriedade privada `aetheriusMoon` publica o payload ou `null` em `ctx.sp.storage.aetheriusMoonV1`. A pré-checagem da instalação confirmou a chave no servidor preparado e no bundle LOCAL. `client/src/sobrenatural/supernaturalHud.ts` lê a chave apenas para sessão ativa e jogador vivo, encaminha pelo evento privado do UI Core, renova uma vez por segundo e apaga-a no reset. `client/ui-core/modules/supernatural/supernatural-hud.js` despacha à apresentação. Ainda falta observar recebimento inicial, troca de personagem/maldição, reconexão e invalidação no CEF.

Maldições são exclusivas. Se um pacote inconsistente contiver lua válida simultânea com sangue ou Véu, a lua tem prioridade na projeção, que chama os outros dois com `null`; nulo ou lua inválida não suprime os demais. A sobrevivência coexiste com a lua e está em outra página. O watchdog oculta a lua se cessarem os snapshots.

## Posição e superfícies

A HUD real de fome/sede/frio é `#survival-hud` em `C:\Code\Aetherius - Servidor Local\client-source\client\index.html`, alimentada por `C:\Code\Aetherius - Servidor Local\source\server\gamemode\survival-hud.js` via `window.setSurvivalHud`. Não é a linha `hud-needs` à esquerda em `client/ui-core/frontend/hud.html`. A sobrevivência usa `right:2vw`, `bottom:3vh`, `--sv-d:clamp(42px,3.2vw,78px)`, caixa `2.82 × 2.52` vezes `--sv-d`. A lua no HUD nativo espelha esse cálculo e é centrada na caixa, 0,416667vw acima dela. Em 1920×1080: `--sv-d=61,44px`; sobrevivência x≈1708,34–1881,60, y≈892,77–1047,60; lua 56×56, x≈1766,97–1822,97, y≈828,77–884,77, folga 8px. Em 1280×720: disco ≈37,33px, folga ≈5,33px. O limite mínimo de 42px da sobrevivência altera a proporcionalidade em telas menores. Coordenadas são CSS previstas, sujeitas a escalas independentes das duas superfícies no CEF; confirmar no jogo em ambas resoluções e ajustes de escala antes de liberar.

## Verificação

- `node --test client/ui-core/tests/moon-hud.test.cjs`: 2/2, fases, cheia, repetição, inválidos, nulo, timeout e restauração imediata.
- Em `client/`: `npx tsc --noEmit` e `npm run build`: sucesso (webpack 5.94.0).
- `git apply --check --reverse` do patch: sucesso no cliente acima.
- Pendente: revisão independente por Gemini 4 Neon; confirmar contrato real com o Sol, testes do transporte, CEF e jogo (posicionamento, visibilidade em menus, troca de maldição, morte, desconexão, todas as fases e movimento reduzido). Não foi executada revisão cruzada nem houve validação visual real nesta entrega.

## Instalação 21/11

**Pré-checagem antes da cópia.** O cliente LOCAL é `C:\Code\Aetherius - Servidor Local\client-source` em `8cfa5534865257fd71345f14aacc2f9d1ab4035e`. Os patches 039/040 alteram quatro ativos implantáveis. `client\ui-core\frontend\hud.html` registra `moon-hud.js` antes de `supernatural-hud.js`. Os três arquivos restantes ficam em `client\ui-core\modules\supernatural`: `moon-hud.js`, `supernatural-hud.js` e `supernatural-hud.css`. O TypeScript já está no bundle com `aetheriusMoonV1` e o teste de Node fica no repositório. A consulta VFS do houseCARL ao perfil LOCAL confirmou que `C:\modOrganizer\mods\AETHERIUS - UI CORE (LOCAL)\MeridianUI\aetheriusui` fornece a página e os assets compartilhados. O `moon-hud.js` está ausente na pré-checagem.

**Cálice da Noite incluído com autorização.** A versão anterior do LOCAL gerava `#blood-reserve-hud` e `.blood-vessel`. O CSS de `8cfa553` usa `#sn-blood-reserve-hud` e `.sn-blood-liquid`, que correspondem ao `blood-reserve-hud.js` aprovado em `afd96a0` e `9a96871`. O coordenador autorizou instalar também esse JS, com backup, para manter o Cálice e a Lua consistentes com a folha compartilhada.

**Arquivos instalados.** Os cinco ativos foram extraídos diretamente dos blobs do commit `8cfa5534865257fd71345f14aacc2f9d1ab4035e`. Os caminhos abaixo são relativos a `C:\modOrganizer\mods\AETHERIUS - UI CORE (LOCAL)\MeridianUI\aetheriusui`. A comparação byte a byte e `git diff --no-index --exit-code` deram igualdade nos cinco. O inventário por SHA-256 de todo o mod passou de 403 a 408 arquivos. As únicas alterações foram os cinco ativos autorizados e as quatro cópias de backup. O houseCARL confirmou o próprio mod como vencedor para todos os caminhos no VFS LOCAL. `hud.html:22,24–25` inclui Cálice, Lua e roteador. `supernatural-hud.js:38` chama `window.setMoonHud` com o payload validado ou `null`.

| Caminho instalado | SHA-256 novo | SHA-256 do backup `.bak-lua-2111` ao lado |
|---|---|---|
| `hud.html` | `31B8B085CBCF42053FD945D77CE7A29BA1B861E32A7AE97ECAD102F5C8404CC8` | `5A7B9E21F194974757E4B0E7620ED08D8B6B7734A57DCDCF1BFB03A6E2B6740F` |
| `modules\supernatural\moon-hud.js` | `F86DE8C0E57A3547792ED6CCFA41640459AF9B2D41D5747C219C749345B3AA52` | Ativo novo |
| `modules\supernatural\supernatural-hud.js` | `EAA830480B7353611644F08955AAF31C2B554A5514DCC1E999CC897514FC2220` | `CADF79B3D09935A01C03DFA80E284AF07F41DE8EB9CDE238722236755BC13567` |
| `modules\supernatural\supernatural-hud.css` | `2ADEECC15F3CAAFA5DB9CBEBB9241E25A271E3C297606F0D1EEC864F2A38E02E` | `59528D2450327C4E222002592ABBC69799EF5BF3D6880FC7C57197F5B08944E7` |
| `modules\supernatural\blood-reserve-hud.js` | `E4DEABFE25A2F4B4E7CF0E3116DABBC23449C1E770DD2A52C051636077F736C6` | `E9C552C8C59BA137E714DF48C4DB5E24C05EFA1D238B95FE2E48D34C2485BC3D` |

Os dois testes da Lua passaram com uma cópia dos bytes implantados. A regressão Masser passou 21/21. Recibos em `C:\Code\Aetherius - Servidor Local\evidence\hud-lua-2111-install.json`, `hud-lua-2111-vfs.json` e `hud-lua-2111-installed-test.log`. Skyrim ficou fechado. Para reverter o lote, restaurar os quatro backups sobre seus nomes originais e retirar apenas o novo `moon-hud.js`.

**Servidor preparado.** `C:\Code\Aetherius - Servidor Local\runtime\server\gamemode\supernatural` coincide byte a byte com `source\server\gamemode\supernatural` para os quatro módulos abaixo. Todos correspondem ao HEAD `79ce921793329bf98ef54776f23efe80e4d716b1` após normalizar finais de linha do checkout. `runtime\server\gamemode\phase0-basic.js:449,453` chama os instaladores de aparência e compulsão no boot. `veil-visuals.js:31–32` passa ao projetor a variante retornada por `eyesOf`, que seleciona Glow na fase cheia ou pelo contador. `forced-werewolf.js:222` publica `aetheriusMoonV1` ao dono. O houseCARL confirmou `Miggyluv's Eyes.esp` ativo no perfil LOCAL. Estas são verificações do runtime preparado e da ordem de plugins. Falta observar a execução em jogo.

| Arquivo em `runtime\server\gamemode\supernatural` | SHA-256 |
|---|---|
| `masser.js` | `2B5B9D52346771B236411F70BBEA914DF41B0D04DAA70D0CA8A3EA16378C7AE0` |
| `veil-eyes.js` | `0DCAFE109CAE1ECA20917C737D61E10DD89E1FB52E14EDF2A2013DEBABA62212` |
| `forced-werewolf.js` | `E4F1C875EE71029F7630C48A70EF69B5299921EFB54A051780BE08B63EB730F0` |
| `veil-visuals.js` | `6096EC2B20497BA622C043AE98CA1F1D7BC4195AB764A4191D193947339C986E` |
