# Etapa 2 — HUD da Lua de Masser (21/11/2026)

**Estado:** candidata implementada por Terra High; revisão Gemini Neon, integração com a propriedade do servidor, validação CEF e teste em jogo pendentes. Sem instalação no modpack ou deploy. Base do cliente `ae7c1720bcb45912bd31b1885785e246cb25aebe`; entrega `7e175bb45c0d57a7441420fb89f077805bf6dba1` no repositório `C:\Code\Aetherius - Servidor Local\client-source`. Patch `C:\Code\Aetherius - Servidor Local\overlays\cliente-local\039-hud-lua-masser.patch`, conferido com `git apply --check --reverse` sobre a entrega.

## Arte e comportamento

Referência aprovada: `C:\Code\Aetherius - New Systems\Sobrenatural\preview\hud-lua-propostas.html`, `hudFinal`, `litPath`, `.hud-final`. Disco, aro translúcido com oito marcas, diamante âmbar; Lua Cheia tem olho em fenda, brilho e aro respirando. Sem número, fundo ou tooltip. Opacidade .82, cheia 1 como a prévia. Fases mudam por interpolação de opacidade entre discos em 1,5 s; aro e estado cheio também transitam, com animações/transições desligadas em movimento reduzido. Primeiro estado e restauração são imediatos; repetição só renova watchdog de 3,5 s, sem reconstruir o SVG ou reiniciar o pulso.

Fases 0–7: Cheia, Minguante Gibosa, Quarto Minguante, Minguante, Nova, Crescente, Quarto Crescente, Crescente Gibosa. Aceita somente `{v:1, phase: inteiro 0..7, full: boolean}` e `full` deve equivaler a `phase === 0`; nulo ou inválido oculta. O proprietário e a fase são decisão do servidor; nenhum cálculo astronômico ou mutação de gameplay ocorre no cliente.

## Integração necessária do servidor

O serviço de Sol deve configurar o `updateOwner` da propriedade privada `aetheriusMoon` para publicar o payload, ou `null`, em `ctx.sp.storage.aetheriusMoonV1`, como as propriedades de sangue e Véu fazem. Esse nome de storage é um contrato adicional da ponte, ainda **não confirmado contra o serviço paralelo**. `client/src/sobrenatural/supernaturalHud.ts` lê a chave apenas para sessão ativa e jogador vivo, encaminha pelo evento privado do UI Core, renova uma vez por segundo e apaga-a no reset; `client/ui-core/modules/supernatural/supernatural-hud.js` despacha à apresentação. Conferir também recebimento inicial, troca de personagem/maldição, reconexão e invalidação pelo serviço antes de considerar a integração concluída.

Maldições são exclusivas. Se um pacote inconsistente contiver lua válida simultânea com sangue ou Véu, a lua tem prioridade na projeção, que chama os outros dois com `null`; nulo ou lua inválida não suprime os demais. A sobrevivência coexiste com a lua e está em outra página. O watchdog oculta a lua se cessarem os snapshots.

## Posição e superfícies

A HUD real de fome/sede/frio é `#survival-hud` em `C:\Code\Aetherius - Servidor Local\client-source\client\index.html`, alimentada por `C:\Code\Aetherius - Servidor Local\source\server\gamemode\survival-hud.js` via `window.setSurvivalHud`. Não é a linha `hud-needs` à esquerda em `client/ui-core/frontend/hud.html`. A sobrevivência usa `right:2vw`, `bottom:3vh`, `--sv-d:clamp(42px,3.2vw,78px)`, caixa `2.82 × 2.52` vezes `--sv-d`. A lua no HUD nativo espelha esse cálculo e é centrada na caixa, 0,416667vw acima dela. Em 1920×1080: `--sv-d=61,44px`; sobrevivência x≈1708,34–1881,60, y≈892,77–1047,60; lua 56×56, x≈1766,97–1822,97, y≈828,77–884,77, folga 8px. Em 1280×720: disco ≈37,33px, folga ≈5,33px. O limite mínimo de 42px da sobrevivência altera a proporcionalidade em telas menores. Coordenadas são CSS previstas, sujeitas a escalas independentes das duas superfícies no CEF; confirmar no jogo em ambas resoluções e ajustes de escala antes de liberar.

## Verificação

- `node --test client/ui-core/tests/moon-hud.test.cjs`: 2/2, fases, cheia, repetição, inválidos, nulo, timeout e restauração imediata.
- Em `client/`: `npx tsc --noEmit` e `npm run build`: sucesso (webpack 5.94.0).
- `git apply --check --reverse` do patch: sucesso no cliente acima.
- Pendente: revisão independente por Gemini 4 Neon; confirmar contrato real com o Sol, testes do transporte, CEF e jogo (posicionamento, visibilidade em menus, troca de maldição, morte, desconexão, todas as fases e movimento reduzido). Não foi executada revisão cruzada nem houve validação visual real nesta entrega.
