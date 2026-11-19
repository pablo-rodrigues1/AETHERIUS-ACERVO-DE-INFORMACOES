# Investigação de 19/11 — auras Feral e Sobrecarga

**Estado.** Candidato `89d4b201f851aaeb1fc681b22316a238202d6d4f` no laboratório. Revisão independente por Gemini 4 Neon e reteste em jogo pendentes.

## O que o teste de 19/11 mostrou

`C:\Code\Aetherius - New Systems\Sobrenatural\docs\TESTE-JOGO_19-11.md:27–28` registra a ausência das duas auras no teste solo. O laboratório estava em `5effbd8` e o `client-source` em `295f02e`. O `C:\Code\Aetherius - Servidor Local\logs\server-20261119-003042.stdout.log:44,47` confirma o gamemode em `runtime\server\gamemode\phase0-basic.js`. O stdout e o stderr não contêm `aetheriusBloodFeral`, `aetheriusVeilAura` ou uma falha de reprodução identificada para essas auras. O teste e os logs existentes, portanto, não demonstram a causa da ausência em cena nem o recebimento de pacote por observadores.

O ponto comum comprovado é o recurso escolhido no checkpoint. Em `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\supernatural\blood-effects-client.js` (`5effbd8`, linhas 64–80), Feral resolve `000800:SkyDeath-Ghosts.esp` e o mesmo helper passa `000807:SkyDeath-Ghosts.esp` à Bruxa (`veil-visuals.js:15`). O houseCARL confirmou `SkyDeathGhost_red` e `SkyDeathGhost_violet` como `EFSH` com `NoParticleShader, TextureEffectProjectUVs` e as texturas `SkyDeath\Spirit-red.dds` e `SkyDeath\Spirit-violet.dds`, fornecidas pelo mod ativo `AETHERIUS - SKYDEATH`. O ESP também estava no manifesto do teste. Esses records implementam uma cobertura de superfície, distinta da aura de partículas solicitada. Os dados descartam a ausência do ESP ou das duas texturas antigas no perfil consultado como explicação demonstrada para T7/T8.

## Records escolhidos e perfil LOCAL

Consulta houseCARL 2.0.0 (`5e3c744862bc17c85ebe850830603a9a3e6f0779`), epoch `e2-22a0c680484df11f`, a partir de `C:\modOrganizer\mods\Colored Magic Auras\Colored Magic Auras.esp` como fonte fora da ordem ativa. Os dois `RFCT` têm `Flags = 0` e `Shader` nulo. O próprio mod liga os mesmos `RFCT` pela propriedade `AuraParticlesFX` de `spgAuraActorScript`.

| Estado | `RFCT` reproduzido | `ARTO` em `EffectArt` | `SPEL` / `MGEF` Self de origem |
|---|---|---|---|
| Vampiro Feral, sangue ≤0 | `000D62` `spgFXAuraParticlesEffectRed` | `00080B` `spgFXAuraParticlesObjectRed` | `000832` / `00082A` |
| Bruxa em Sobrecarga, Vínculo >100 | `000801` `spgFXAuraParticlesEffectPurple` | `00080D` `spgFXAuraParticlesObjectPurple` | `000831` / `000829` |

A leitura de `C:\modOrganizer\mods\Colored Magic Auras\Colored Magic Auras.bsa` confirmou `meshes\effects\spgFXAuraParticlesRed.nif` e `spgFXAuraParticlesPurple.nif`. O perfil `AETHERIUS - LOCAL` tem o mod desativado e o ESP fora de `loadorder.txt`. A verificação local de `C:\Code\Aetherius - Servidor Local\runtime\server\data\aetherius-loadorder.json` (gerado `2026-11-19T03:30:40.553Z`, perfil LOCAL, cliente `295f02e`) encontrou `SkyDeath-Ghosts.esp` no índice zero-based 427 e não encontrou `Colored Magic Auras.esp` em `loadOrder`, em `plugins` nem no diretório `data`. Essa ausência bloqueia a reprodução dos novos records até a ativação. [Colored Magic Auras v1.0, Spongeman131](https://www.nexusmods.com/skyrimspecialedition/mods/25773)

## Implementação e sincronização

- `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\supernatural\blood-effects.js:25–39` e `veil-visuals.js:15–34` publicam `aetheriusBloodFeral` e `aetheriusVeilAura` com ambas as opções de visibilidade e o mesmo script para `updateOwner` e `updateNeighbor`. As propriedades carregam um indicador de apresentação e `sample`. O estado de maldição continua calculado no servidor.
- O SkyMP inclui as propriedades públicas no `CreateActor` de quem entra em relevância (`C:\Code\Aetherius - Servidor Local\source\server\cpp\server_guest_lib\PartOne.cpp:823–848`). O cliente lê essas propriedades em `client-source\client\src\services\services\remoteServer.ts:577–584` e roda o handler de observador para o ator local em `client-source\client\src\view\formView.ts:346`. A atualização do dono usa o modelo local em `gamemodeUpdateService.ts:192–217`.
- `blood-effects-client.js:64–109` resolve o `RFCT` com `Game.getFormFromFile(effectId, 'Colored Magic Auras.esp')` e chama `VisualEffect.play(actor, 2.5, null)`. A forma é resolvida pelo plugin em cada tentativa e o wrapper não é guardado entre frames. O intervalo para renovar ou refazer uma busca sem record é de 1,5 s, com reaplicação antecipada na troca de célula e no retorno do 3D. Estado nulo, morte, perda do 3D ou amostra com mais de 2,5 s encerram a reprodução. O tempo finito cobre a perda do ator sem callback de encerramento.
- A escolha usa os `RFCT` originais, acessíveis pela API `VisualEffect` já usada no ambiente (`werewolf\client.js:139`). As magias Self do mod carregam um efeito de 180 s com script próprio. A integração por `RFCT` não lança essas magias e dispensa um novo master no ESP do Aetherius. As duas cópias dos módulos foram mantidas iguais em `overlays\sobrenatural\files\server\gamemode\supernatural` e `source\server\gamemode\supernatural`, ambas sob `C:\Code\Aetherius - Servidor Local`.

## Verificação

- Testes direcionados `node --test overlays/sobrenatural/teste-veu-mundo.cjs overlays/sobrenatural/teste-sangue-efeitos.cjs` passaram **25/25**. Os callbacks serializados foram executados para dono e observador tardio, com as duas cores e a limpeza. O teste compartilhado verifica recuperação do 3D, troca de célula, morte, amostra vencida e ausência controlada do plugin. O mock da Bruxa também valida o uso do wrapper no frame em que foi resolvido.
- Bateria Node `teste-*.cjs` somada a `werewolf.test.cjs`, `magic.test.cjs`, `survival-service.test.js`, `survival-effects.test.js` e `skysocial-service.test.js` passou **503/503 em cada modo** de `AETHERIUS_VAMPIRE_FEED_ENABLED` (`false` e `true`, Node 22.14.0). Os três scripts executados à parte, serviço com caminho absoluto do gamemode, painel F7 e revisão F7, retornaram OK.
- No `client-source` `295f02e`, `tsc --noEmit` e `npm run build -- --output-path <diretório temporário>` retornaram exit 0. O webpack foi executado com `DEPLOY_PLUGIN=false` e `ZIP_PLUGIN=false`. Esses testes cobrem o roteamento e o build. A aparência e a continuidade do efeito no motor e uma sessão real com observadores ainda precisam de validação.

## Ação do dev e roteiro de reteste

1. Habilitar `Colored Magic Auras` e marcar `Colored Magic Auras.esp` nos dois painéis do perfil `AETHERIUS - LOCAL` do MO2. Sincronizar `modlist.txt`, `loadorder.txt` e `plugins.txt` com `C:\Code\Aetherius - Servidor Local\client-source\client\profiles\AETHERIUS - LOCAL`.
2. Na próxima preparação autorizada, executar `C:\Code\Aetherius - Servidor Local\scripts\Prepare.ps1` com o servidor local parado. O script chama `source\server\tools\sync-client-plugins.js`, que monta a lista a partir do perfil em `client-source`, compara com MO2 e copia o ESP para `runtime\server\data`. Conferir `Colored Magic Auras.esp` tanto em `loadOrder` quanto em `plugins` no novo manifesto e o BSA disponível nos clientes.
3. Entrar com cliente iniciado do zero e testar Feral em 0 e recuperação acima de 0, Sobrecarga acima de 100 e volta a 100 ou menos. Confirmar Red e Purple no dono e no observador presente. Fazer um segundo observador entrar depois da transição, sair e voltar à relevância. Repetir ao trocar célula, descarregar 3D, morrer e perder a maldição. Observar a continuidade das partículas entre renovações.
