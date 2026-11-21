# Etapa 2 — Lua de Masser no servidor

Entrega de 21/11/2026 por Sol High. Candidato para teste local, com revisão cruzada pelo Gemini 4 Neon e validação em jogo pendentes. As decisões de fase, três noites e Glow estão na [investigação de Masser](investigacoes/INVESTIGACAO_21-11_fases-da-lua.md). O coordenador confirmou em 21/11 a preservação do contador pelo botão e da aparência original para Argonianas. A regra do botão também consta em [DECISOES](DECISOES.md#forma-da-besta-no-tab-decisão-do-dev-1911).

## Código e integração

| Local | Commit | Conteúdo |
|---|---|---|
| `C:\Code\Aetherius - Servidor Local\source` | `310cb598fd382cf4d6598cb34eada5db1a3fa9f8` | Base local do servidor |
| Mesmo repositório | `9e313e4406f7915209bf2beecba8a8642f53ac5d` | Registra no Git os cinco módulos preexistentes necessários ao diff. Os hashes dos arquivos coincidem com o overlay publicado antes da mudança |
| Mesmo repositório | `48fb5fe63a14083673b8c5ee66bad3283ee8757a` | Época do relógio e fórmula de Masser |
| Mesmo repositório | `7dab4e2a4d782663b42cad91ec133122fa48c3d9` | Gatilho, recibo lunar durável e HUD |
| Mesmo repositório | `a3ef672364415021830e9e3934f567390d859c0a` | Olhos Bronze e bateria de Masser |
| `C:\Code\Aetherius - Servidor Local` | `8b233e3a4b788fbd029f4407efd0cc533c8f2376` | Overlay `overlays\sobrenatural`, arquivos de gamemode espelhados e patch `035-masser-clock.patch` para os dois arquivos TypeScript |
| `C:\Code\Aetherius - Servidor Local\client-source` | `7e175bb45c0d57a7441420fb89f077805bf6dba1` | Cliente da Terra, patch `039-hud-lua-masser.patch` |

Para integrar sobre uma árvore nova, aplicar o patch 035 do **servidor** e copiar os arquivos de `overlays\sobrenatural\files\server\gamemode` com os demais módulos do Sobrenatural. O cliente usa o patch 039 do **cliente**. Os C++ `AppearanceBinding.cpp` e `AttributeBonusesBinding.cpp` já tinham alterações locais de outra frente. Seus hashes de conteúdo foram conferidos antes e depois da entrega. A atividade no Acervo foi somente leitura, na revisão `3627ceb836b524c521077cd73bd9a0d5914141ce`, com o índice, sistemas dependentes, ADR-0003, ADR-0014 e ADR-0020. Implantação e teste em jogo aguardam a integração do coordenador.

## Relógio, fases e janela da noite

O servidor calcula Masser com uma amostra da autoridade por tick de 250 ms. `C:\Code\Aetherius - Servidor Local\source\server\ts\systems\environmentSystem.ts:83` expõe `clockEpochMinutes()` no símbolo `aetherius.environment.authority.v1`. `environmentAuthority.ts:241` retorna `initialClock(0, this.options.clockConfig).anchorGameMinutes`, a mesma base empregada em `clockSnapshot()` para `gameDaysPassed`. `masser.js:27` lê `gameMinutes` e a época pelo mesmo símbolo, inclusive com `dateChangesEnabled: false`. Com autoridade indisponível, a fase é desconhecida. A mecânica independe da lua no céu. A sincronização do global `GameDaysPassed` continua restrita ao laboratório de calendário.

```js
const gameDaysPassed = (gameMinutes - epochMinutes) / 1440;
const day = Math.floor(gameDaysPassed);
const phase = Math.floor(((day % 24 + 24) % 24) / 3);
```

| Índice | Fase | Dia módulo 24 |
|---|---|---|
| 0 | Lua Cheia | 0–2 |
| 1 | Minguante Gibosa | 3–5 |
| 2 | Quarto Minguante | 6–8 |
| 3 | Lua Minguante | 9–11 |
| 4 | Lua Nova | 12–14 |
| 5 | Lua Crescente | 15–17 |
| 6 | Quarto Crescente | 18–20 |
| 7 | Crescente Gibosa | 21–23 |

`C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\masser.js:18` calcula a chave usando os minutos absolutos de **início da noite**. Na configuração padrão, às 04h59 do dia 3 a chave ainda é 2, enquanto a HUD já mostra fase 1 e a Lua Cheia deixou de manter o Glow. Às 05h00 termina a janela. Calcular pela hora de início mantém a mesma chave antes e depois da meia-noite mesmo com `gameTimeEpochHour` diferente de zero.

## Compulsão e persistência

`C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\forced-werewolf.js:172` avalia Lua Cheia junto com as 72 h reais online. A Lua Cheia usa a mesma checagem de elegibilidade, recusa, retry, persistência, pré-aviso privado de 20 s e animação pública de kdu. Quando inicia pelo critério lunar, a tentativa registra `[werewolf-forced] ... via=fullmoon accepted=true|false`. A sequência lunar em pré-aviso é interrompida ao sair da janela, trocar de noite, perder a autoridade ou ficar inelegível. `accepted=true` indica que o fluxo de kdu começou. O cumprimento depende de confirmação da forma.

`C:\Code\Aetherius - Servidor Local\source\server\gamemode\werewolf-service.js:202` captura a noite no momento da aplicação da fera confirmada pelo servidor. `supernatural\werewolf-transform-journal.js:61` grava `moon: { night, stamp }` no diário durável `data\aetherius-werewolf.json.transforms.json` por personagem e concessão. Uma confirmação posterior de dia mantém o último recibo daquela concessão. Na recuperação de uma intenção ambígua de processo interrompido, somente um recibo anterior já confirmado pode ser reaproveitado. Uma recusa, uma pendência sem evidência da raça ou a observação tardia de um stamp legado sem hora lunar não cumprem a noite.

`forced-werewolf.js:89` copia a chave do recibo para `lastFullMoonNight` no registro `data\aetherius-werewolf-compulsion.json`. O JSON continua na versão 1 e aceita registros antigos sem esse campo. A cópia pode ser reconciliada após reinício, usando a chave gravada no momento da forma. Lua Cheia, 72 h, Z legado, F7 e botão cumprem a noite se a forma for confirmada dentro dela. Só o botão preserva as 72 h e o Glow por tempo online. Como a compulsão de 72 h é independente do gatilho lunar, pode ser devida na mesma noite já cumprida pelo botão, conforme a regra de 19/11.

| Configuração | Padrão |
|---|---|
| `WEREWOLF_FORCE_ONLINE_HOURS` | 72 h reais online |
| `WEREWOLF_FORCE_NIGHT_START` / `WEREWOLF_FORCE_NIGHT_END` | 20h00 inclusivo / 05h00 exclusivo |
| `WEREWOLF_FORCE_TRANSFORM_SECONDS` | 20 s de pré-aviso |
| `WEREWOLF_FORCE_RETRY_SECONDS` | 30 s reais entre tentativas recusadas |
| `WEREWOLF_EYES_GLOW_HOURS` | 24 h reais online, aceita de 1 s a 87.600 h |

## Olhos Bronze e replicação

`C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\veil-visuals.js:25` escolhe a projeção da maldição exclusiva. `forced-werewolf.js:227` retorna Glow se o contador da concessão chegou a 24 h ou a fase de Masser é 0. Os três dias completos mantêm o Glow, inclusive após um reset das 72 h até o final da fase. Com fase desconhecida, permanece a regra de tempo online. A projeção roda na atualização já existente do Véu (1 s) e a escrita de `appearance` só ocorre se o headpart alvo difere do atual. `veil-eyes.js:76` mantém o olho original em `data\aetherius-veil-eyes.json`, compartilhado com a Bruxa. Trocar de paleta preserva esse original, e perder a maldição restaura o headpart de autoria do sistema. A projeção aguarda o retorno da aparência mortal se o personagem estiver em transformação ou em forma de fera.

O canal existente `appearance` faz a replicação. Na base `310cb59`, `server\cpp\addon\property_bindings\AppearanceBinding.cpp:42` envia `UpdateAppearanceMessage` aos listeners do ator e `server\cpp\server_guest_lib\PartOne.cpp` preenche `CreateActorMessage.appearance` com a aparência armazenada ao entrar no alcance. O cliente lê updates do dono e dos observadores em `C:\Code\Aetherius - Servidor Local\client-source\client\src\services\services\remoteServer.ts:915`.

### Evidência das variantes

Miggyluv's Eyes v2.2, Nexus 155332, ESP `C:\modOrganizer\mods\Miggyluv's Eyes\Miggyluv's Eyes.esp`, SHA-256 `1AFDBC39F6F99EE212BD839B1BBB67F91EF68B35380529FDD258B214F2946DAC`. houseCARL `2.0.0+5e3c744862bc17c85ebe850830603a9a3e6f0779` leu os 33 HDPT Bronze na fonte do ESP, fora da ordem ativa do perfil gráfico naquele momento. Os arquivos de perfil `C:\modOrganizer\profiles\AETHERIUS - LOCAL\modlist.txt` e `plugins.txt` listavam `+Miggyluv's Eyes` e `*Miggyluv's Eyes.esp`. Para o teste, conferir a ordem efetiva do host e dos clientes.

Comparação das DDS sob `C:\modOrganizer\mods\Miggyluv's Eyes\textures\actors\character\miggyluv eyes` via Pillow 13.0.0. A luminância média da região central é maior em `Bronze01` nas seis famílias com par. Por exemplo, humano 52,31 contra 51,20 e Altmer 58,98 contra 49,94. Os HDPT Glow compartilham o TextureSet Bronze01 da família e usam a malha `eyes{male,female}[_mer]_bronzeGlow.nif`. Os EDIDs reais usam `BronzeGlow` e `BronzeFGlow`. `Bronze01Glow` e `Bronze01FGlow` são os sufixos dos nomes de exibição.

Os valores abaixo são FormIDs locais de `Miggyluv's Eyes.esp`, em ordem Light / Dark / Glow, com resolução pelo ESP e conferência do EDID em `veil-eyes.js:37`.

| Família | Masculino | Feminino |
|---|---|---|
| Humana | `00001F` / `000020` / `0002CF` | `0000C1` / `0000C2` / `0002CE` |
| Bosmer | `000193` / `000194` / `0002E2` | `0001BB` / `0001BC` / `0002E3` |
| Altmer | `00022E` / `00022F` / `0002EB` | `000272` / `000273` / `0002EC` |
| Dunmer | `00024C` / `00024D` / `0002DB` | `000290` / `000291` / `0002DA` |
| Orc | `0000CB` / `0000CC` / — | `000139` / `00013A` / — |
| Khajiit | `00037A` / `00037B` / — | `0003AC` / `0003AD` / — |
| Argonian | `00032A` / — / — | — |

Sem Glow, a projeção prefere Bronze Light e usa Bronze Dark como alternativa compatível. O HDPT `00032A` é masculino. Argonianas mantêm seus olhos originais, seguindo a prática da Bruxa. Um HDPT Bronze feminino permanece pendente de decisão do dev.

## Contrato privado da HUD

Em `C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\forced-werewolf.js:221`, `aetheriusMoon` tem `isVisibleByOwner: true` e `isVisibleByNeighbors: false`. O payload é exatamente `{ v: 1, phase: 0..7, full: phase === 0 }` ou `null` para outros personagens ou fase desconhecida. O servidor publica na primeira projeção, em mudança de estado ou em nova sessão do lobisomem. O script `updateOwner` é

```js
ctx.sp.storage.aetheriusMoonV1 = ctx.value ?? null;
```

O cliente da Terra lê a mesma chave em `C:\Code\Aetherius - Servidor Local\client-source\client\src\sobrenatural\supernaturalHud.ts:133`. O payload contém só apresentação. O teste Node executa o script com `ctx.sp.storage` simulado e verifica publicação por borda e limpeza.

## Evidência reproduzível

- Node 22.14.0. `server\gamemode\supernatural\tests\masser.test.cjs` passou 21/21 tanto em `source` como no espelho. Cobre a autoridade TypeScript com calendário ligado e desligado, época deslocada, os oito limites de fase, as três noites e o ciclo seguinte, retry, dawn, reinício, JSON antigo, stamps por via, concessão nova, HUD privada, limiares de Glow e troca/limpeza de olhos. Usa relógios simulados e arquivos temporários, sem sleeps.
- Bateria direcionada de regressão 106/106, incluindo compulsão, F7, botão, serviço de lobisomem, mundo do Véu e Masser. O script standalone `teste-servico.cjs` passou. `source\server\node_modules\.bin\tsc.cmd -p tsconfig.json --noEmit` passou a partir de `source\server`.
- Bateria ampliada com `AETHERIUS_VAMPIRE_FEED_ENABLED` em `true` e `false`, 451/453 em cada modo. Dois testes antigos de CEF precisam do mock da nova função `setMoonHud` exigida pelo cliente da Terra. O arquivo isolado passou 7/7 com o mock injetado em memória. [Investigação e reprodução](investigacoes/INVESTIGACAO_21-11_fixture-hud-lua.md).
- Conferidos os hashes do espelho e `git apply --check --whitespace=error` do patch 035 sobre o índice temporário da base `310cb59`, além do reverse-check em `source`.

Da raiz `C:\Code\Aetherius - Servidor Local`, rodar

```powershell
node --test source/server/gamemode/supernatural/tests/masser.test.cjs overlays/sobrenatural/teste-transformacao-forcada.cjs overlays/sobrenatural/teste-painel-f7.cjs overlays/sobrenatural/teste-revisao-f7.cjs overlays/sobrenatural/teste-veu-mundo.cjs source/server/gamemode/werewolf/tests/werewolf.test.cjs
node overlays/sobrenatural/teste-servico.cjs (Resolve-Path 'source/server/gamemode').Path
```

## Roteiro de teste em jogo

1. Integrar o patch do servidor, os módulos espelhados e o cliente da Terra no laboratório local. Conferir Miggyluv v2.2 na ordem efetiva do servidor e de todos os clientes. Preparar três personagens, A lobisomem humano, B vizinho sem maldição e C observador que entra depois. Usar o F7 para conceder e remover a maldição.
2. No laboratório de calendário, usar o controle autorizado de data/hora do F7 para preparar um dia de chave 0 módulo 24. Com a época padrão (ano 199, mês 0, dia 1, hora 0), os dias 1, 2 e 3 do primeiro mês são a fase cheia. Para escolher outro ciclo, somar um múltiplo de 24 dias à época. `time.set` exige `AETHERIUS_WEATHER_LAB_CALENDAR=1` e meses 0–11, conforme `C:\Code\Aetherius - Servidor Local\source\server\ts\systems\environmentAuthority.ts:280`. `time.setHour` vai para a próxima ocorrência da hora se ela já passou. O teste unitário confirma a mecânica também com o laboratório desligado.
3. No início da fase cheia, ainda de dia e com contador baixo, confirmar o ícone de Lua Cheia só em A e Glow visto por A, B e C. Às 19h59 aguardar sem disparo. Às 20h confirmar o pré-aviso só em A, a animação/forma vista pelos vizinhos e o log `via=fullmoon`. Aguardar a confirmação de raça e o encerramento da forma. Relogar na mesma noite e conferir que o aviso lunar não volta.
4. Na noite seguinte repetir após a confirmação anterior. Na terceira noite, confirmar que 00h00–04h59 do dia de fase 1 ainda pertence à terceira noite. Com contador abaixo de 24 h, a virada de fase encerra Glow mesmo enquanto a terceira noite aceita o gatilho. Usar um personagem novo para testar a fronteira das 05h00 durante o pré-aviso e confirmar cancelamento. Na quarta noite, com menos de 72 h, aguardar sem disparo.
5. Para testar o stamp do botão, iniciar o botão antes das 20h num dos três dias cheios e confirmar a forma após as 20h. Depois da volta, ainda na mesma noite e abaixo de 72 h, confirmar que a lua não dispara. Conferir a recarga de duas horas reais e o contador preservado. Em personagens/ciclos distintos, confirmar F7 e Z e verificar que cada um cumpre a noite quando a raça muda, com reset das horas.
6. Fora da fase cheia, usar a variável `WEREWOLF_EYES_GLOW_HOURS` somente no ambiente de teste para atravessar o limiar sem aguardar 24 h. Conferir Light → Glow visto pelo dono, vizinho e observador tardio. Confirmar uma transformação por Z para observar Glow → Light ao retornar humano. Repetir com Orc ou Khajiit para verificar Bronze Light como fallback. Em Argoniana, conferir o olho original.
7. No F7, remover Licantropia e conceder Bruxaria. Repetir a troca na direção oposta. Conferir Bronze → original → Magic e Magic → original → Bronze, incluindo a visão de um observador tardio. Remover a maldição e confirmar olhos originais e HUD lunar oculta após relog. Guardar o log da forma e capturas do dono, vizinho e observador tardio para a revisão.
