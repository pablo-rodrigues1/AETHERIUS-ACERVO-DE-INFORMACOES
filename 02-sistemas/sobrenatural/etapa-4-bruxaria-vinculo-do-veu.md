# ETAPA 4 — Bruxaria: Vínculo do Véu (especificação confirmada, NÃO iniciada)

**Data:** 18/11/2026 · **Autor:** Opus 5.5 (orquestrador) · **Status:** escopo confirmado pelo dev; implementação **não despachada** (aguarda ordem do dev).

**Fonte:** `C:\Code\Aetherius - New Systems\referencia\Aetherius_Sistema_Bruxaria_Vinculo_do_Veu.md` (especificação funcional), com as respostas do dev de 18/11 abaixo, que **prevalecem** sobre os itens marcados como "sugestão" na fonte.

Referências de arte e recursos verificados:
- `C:\Code\Aetherius - New Systems\referencia\witch gauge.png` — base do selo.
- `C:\Code\Aetherius - New Systems\Sobrenatural\Bruxas\Audio\` — 4 MP3: `breathing_witch.mp3`, `scream_witch.mp3`, `whisper2_witch.mp3`, `whispering_witch.mp3`.
- `UIHealthHeartbeatALP` = `04DAAF:Skyrim.esm` (SNDR), descritor `04DAAD`, `Sound\FX\UI\Health\Heartbeat\UI_Health_Heartbeat_A_LP.wav`, loop nativo (houseCARL, 17/11).
- Runa: `MasterSpellsRunes.esp` (runa de Conjuração), informado pelo dev.

## 1. Regras (servidor autoritativo)

| Item | Valor confirmado |
|---|---|
| Faixa | 0–100, fracionário internamente |
| Inicial | **100** ao setar a maldição Bruxa |
| Consumo | 100 → 0 em **12 h online** (1 ponto / 432 s); **nada offline**; persiste entre sessões e reinícios |
| Estágio III (≤50 e >30) | −50% regeneração de Magicka |
| Estágio IV (≤30 e >0) | −50% regeneração e −50% Magicka máxima + terror (tentativa a cada 12–20 min; 35% nada) |
| Estágio V (**0**) | **−100% Magicka máxima** (confirmado) + terror **a cada 5 min, sempre com evento** (sem "nada") |
| Saída dos estágios | acima de 30 remove Magicka máx. e terror; acima de 50 remove regeneração |
| Sem duplicação | relog/troca de célula/reentrada no mesmo estágio não reaplica |

Reaproveitar a infraestrutura da Reserva de Sangue (relógio de sessão `min(now, offlineAt)`, gravação em lote, regra D7 "na dúvida, não credita").

## 2. Terror (local à bruxa)

| Resultado | Chance (estágio IV) |
|---|---|
| Nada | 35% |
| Áudio (`scream_witch`, `whisper2_witch`, `whispering_witch`) | 30% |
| Pensamento intrusivo (notificação vanilla; 10 frases da fonte §6.2-B) | 18% |
| `UIHealthHeartbeatALP` por exatamente 10 s | 12% |
| Tela preta 1–1,5 s + `breathing_witch` (retorno gradual) | 5% |

- No estágio V, as mesmas categorias redistribuídas sem "nada", a cada 5 min.
- Tela preta: cooldown mínimo 90 min; nunca em combate, salto, queda, diálogo, menu ou cena; restauração garantida em interrupção/célula/desconexão.
- Uma manifestação por vez; antirrepetição de áudio e texto; sem acúmulo após reconexão; tudo para ao passar de 30.
- Áudio: MP3 → WAV PCM → XWM, originais preservados, volume normalizado, tabela de correspondência; sons registrados no `AETHERIUS - SOBRENATURAL.esp` (escrita **autorizada** pelo dev).

## 3. Selo (HUD)
- **Centro inferior** da tela (exclusividade entre maldições: nunca coexiste com a barra de sangue).
- ~90 px em 1920×1080, proporcional à largura em 1280×720, baseado em `witch gauge.png`; **barra central em degradê** como na referência, enchendo de baixo para cima; violeta; segmentos apagados em cinza/branco/dourado; rachaduras e perda de brilho progressivas; pulsação sutil no crítico; **sem número nem tooltip**; só para Bruxa.
- Interface apenas nas cópias `(LOCAL)` dos mods; nunca sobrepor pastas `MeridianUI`.

## 4. Nova tela: Ritual (TAB → Sobrenatural → Bruxa)
- Botão na tela da Bruxa abre a interface do Ritual, no estilo do painel da Bruxa (fundo preto, paleta violeta, tipografia; `docs/DIRECAO-DE-ARTE.md`).
- Ilustração central de uma **entidade** do outro lado do Véu.
- Texto **extremamente conciso**, ex.: "Ofereça 3 ingredientes diferentes. 5 minutos. +50 Vínculo."
- 7 ingredientes como ícones: acesos se presentes no inventário, apagados se não.
- Botão **Iniciar Ritual** ativo só com 3 categorias disponíveis; durante o ritual, progresso + **Cancelar**.
- **Suposição registrada (não respondida pelo dev):** o servidor escolhe automaticamente as 3 categorias no inventário; sem seleção manual. Confirmar antes de implementar a UI.

## 5. Ritual (servidor)
- Ingredientes — 3 **categorias** distintas: Coração Daedra; Pedra da Alma Negra (**cheia e vazia**); Carne animal (**todas as carnes cruas**); Pele animal (**todas as peles**); Livro de feitiço (**qualquer tomo**); Nightshade; Nirnroot. Mapear FormIDs via houseCARL (somente leitura).
- 300 s; animação sustentada de Conjuração; **runa de Conjuração do `MasterSpellsRunes.esp`** visível e sincronizada para jogadores próximos.
- Reserva lógica dos itens; interrupção (cancelar, mover, combate, desconexão, morte, perda da maldição) = sem recompensa e sem consumo.
- Conclusão: validação final, consumo dos 3 itens, `min(100, vínculo + 50)`; regra D7.

## 6. Distribuição planejada
| Quem | O quê |
|---|---|
| Sol High (GPT-6.2) | Vínculo, penalidades, ritual, reserva de itens, sincronização da runa, persistência |
| Terra High | Selo, tela do Ritual + entidade, terror no cliente, conversão de áudio |
| houseCARL | Records no `AETHERIUS - SOBRENATURAL.esp` (autorizado) |
| Revisão | cruzada Sol ↔ Terra |

Sem instalação no MO2 e sem jogo até a rodada de testes.

## 7. Critérios de aceite
Os da fonte §12, com: estágio V = −100% Magicka máx. e evento a cada 5 min; selo com degradê central; tela do Ritual com entidade e texto mínimo.

## 18/11 — protótipo visual do selo (sem mecânica)

Autor Terra High. Pedido do dev neste despacho limitado ao visual; a seção inicial “não iniciado” continua válida para mecânicas e integração. Em `C:\Code\Aetherius - Servidor Local\client-source\client\ui-core\modules\supernatural\`, os arquivos `veil-seal.js`, `veil-seal.css` e `witch-gauge-reference.png` foram preparados no commit cliente `28383f9`. A imagem é cópia byte a byte da referência `C:\Code\Aetherius - New Systems\referencia\witch gauge.png` (SHA-256 `02E7ABFA2771AD99C8200D6E6E260E2BB3497DEDE0CF9EDD8CCC110D0A769CF7`); SVG a desenha em cinza, recorta o cristal colorido da mesma arte de baixo para cima e acrescenta rúnicos dourados, símbolo e rachaduras.

Na primeira versão, tamanho 56×56 px e centro inferior a 30 px; a decisão posterior de tamanho e posição está registrada na seção seguinte. Cinco classes de apresentação usam faixas >70, <=70, <=50, <=30 e 0; brilho diminui, rachaduras se destacam e os estágios 4/5 pulsam suavemente, respeitando `prefers-reduced-motion`. Não há texto, número ou tooltip no selo. `createVeilSeal(assetUrl).setValue(valor)` é **API de mock visual**, não é contrato de dados nem integração com maldição, servidor ou UI ativa. Não foram implementados persistência, seleção de bruxa, exclusividade com sangue, terror ou ritual; esses itens dependem de integração futura.

Patch incremental CSS/JS de arquivos novos `C:\Code\Aetherius - Servidor Local\overlays\cliente-local\011-selo-vinculo-visual.patch` após 010; o asset binário separado `overlays/cliente-local/assets/witch-gauge-reference.png` deve ser copiado junto ao CSS/JS na futura integração; nenhum arquivo em `MeridianUI` foi copiado ou modificado e nada foi instalado no MO2. Prévia em `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\preview-vinculo\index.html?value=60` (depende dos três arquivos do client-source; alterar `value` para cada valor). As doze capturas Orca usam `vinculo-<largura>x<altura>-<valor>.png` na mesma pasta, valores 100/80/60/40/20/0 em 1920×1080 e 1280×720; fundo é simulado, não jogo.

Verificação: `node --test overlays/sobrenatural/teste-visual-vinculo.cjs` **2/2**, fronteiras/clamp, direção do preenchimento e ausência de texto/tooltip; renderização Orca conferiu largura 56, estágios e dimensão dos 12 PNGs; reverse-check do patch passou. Revisão independente e avaliação visual do dev pendentes; testar CEF real, contraste no cenário e sobreposição com HUDs antes de integrar. Nenhum servidor, cliente de jogo ou modpack foi iniciado/alterado nesta tarefa.


## 18/11 — ajuste de escala do selo

Por pedido do dev, `client-source/client/ui-core/modules/supernatural/veil-seal.css` usa `--veil-size:min(80px,4.1666667vw)` e `bottom:9vh`, permanecendo centrado e sem mudança de arte, valores ou estágios. O resultado no Orca foi **80×80 px** em 1920×1080 (borda inferior a 97,19 px) e **53,33×53,33 px** em 1280×720 (borda inferior a 64,80 px). O recuo inferior tenta preservar espaço para barras; a prévia não contém HUD vanilla, de modo que não comprova ausência de sobreposição com barras, bússola, outros HUDs ou escala de jogo. Validar no CEF e em jogo antes de instalar.

Cliente `0432493` em `C:\Code\Aetherius - Servidor Local\client-source`; patch incremental `C:\Code\Aetherius - Servidor Local\overlays\cliente-local\012-selo-tamanho.patch` após 011. As doze capturas `vinculo-<largura>x<altura>-<valor>.png` em `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\preview-vinculo` foram regeneradas; verificados dimensões dos PNGs, posição central, tamanho, estágios e preenchimento. `node --test overlays/sobrenatural/teste-visual-vinculo.cjs` passou 2/2; reverse-check do patch passou. São prévias de fundo simulado, sem instalação no MO2 ou execução do jogo; revisão cruzada pendente.


## 18/11 — selo aumentado para 90 px e reposicionado

Pedido do dev: aumentar de 80 para 90 px e baixar um pouco, preservando arte e estados. `client-source/client/ui-core/modules/supernatural/veil-seal.css` usa agora `--veil-size:min(90px,4.6875vw)` e `bottom:7vh`: no Orca, 90 px e recuo inferior 75,59 px em 1920×1080, 60 px e recuo 50,39 px em 1280×720. O centro horizontal permanece igual; o recuo é 2vh menor que antes. Cliente `e82ac03`; patch incremental `overlays/cliente-local/013-selo-90px.patch` após 012.

As doze capturas de `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\preview-vinculo` foram regeneradas nos seis valores e ambas as resoluções; teste `node --test overlays/sobrenatural/teste-visual-vinculo.cjs` passou 2/2 e o reverse-check do patch passou. O fundo da prévia não contém a HUD do Skyrim: posição e eventual sobreposição das barras precisam de validação em jogo; não houve instalação no MO2 nem execução do jogo. Revisão independente pendente.

## 8. Sobrecarga do Vínculo (adicionada ao plano em 18/11, NÃO iniciada)

**Fonte:** `C:\Code\Aetherius - New Systems\referencia\Aetherius_Implementacao_Sobrecarga_Bruxas.md`. Complementa as seções 1–7 sem alterar esgotamento, penalidades nem ritual normal.

### 8.1 Regras confirmadas pela fonte
- Teto normal 100; **teto absoluto 300**. Com ≥100, a bruxa pode fazer **sacrifício de sobrecarga**: +50 por ritual, até 300.
- Receita de sobrecarga = **dobro** das quantidades do ritual normal, mantendo 3 categorias distintas; mesmo tempo (300 s) e animação; regra D7. No teto, recusar sem consumir; sem ultrapassar 300 por chamadas concorrentes.
- Sobrecarga = `vínculo > 100`. Ao chegar a ≤100, **todos** os buffs cessam imediatamente.

| Vínculo | Blocos | Regeneração de Magicka | Criaturas extras (invocações + reanimações, compartilhado) | Magicka máxima |
|---|---|---|---|---|
| 100 | 0 | 0% | 0 | 0% |
| 150 | 1 | +50% | +1 | +50% |
| 200 | 2 | +100% | +2 | +50% |
| 250 | 3 | +150% | +3 | +50% |
| 300 | 4 | +200% | +4 | +50% (fixo) |

- Modificadores identificáveis e removíveis; sem preencher Magicka atual ao ganhar o bônus; limite base de criaturas vem do sistema existente (classe/perks).

### 8.2 Apresentação
- **Olhos** (mod Nexus 155332, variantes **violetas**, não Multicolor): Magic **Dark** se `vínculo < 50`; Magic **Light** se `50 ≤ vínculo ≤ 100`; Magic **Glow** se `> 100`. Vale para **toda bruxa**, não só na sobrecarga. Visível a todos (inclusive entrada tardia); restaurar a aparência ao perder a maldição.
- **Nome:** "BRUXA" em violeta acima do personagem (SkySocial identity, mesmo padrão do VAMPIRO FERAL), sem alterar o nome salvo.
- **Aura roxa**, reutilizando a integração da aura Feral do vampiro.
- **Sussurros** durante a sobrecarga, sem instâncias duplicadas; encerram ao sair da sobrecarga/maldição/sessão.
- **Selo da HUD** (só para o próprio jogador): até 100 normal; `>100` até 200 barra central brilhante com raios violetas vazando para o suporte; `>200` até 300 símbolo inteiro vira **capacete aberto demoníaco**, violeta mais brilhante e progressivo; reversão ao cruzar 200 e 100; reconstrução correta ao reconectar.

### 8.3 Pendências a decidir antes de implementar
| # | Pendência |
|---|---|
| S1 | **Consumo acima de 100:** a fonte não define; proposta: mesma taxa linear (1 ponto/432 s), ou seja, 300→100 em 24 h online. |
| S2 | **Blocos intermediários:** proposta da fonte `blocos = piso((vínculo−100)/50)`; com 101–149 há Glow/aura/BRUXA/sussurros/HUD e +50% Magicka máx., mas sem bônus de regeneração nem criatura extra. |
| S3 | **Criaturas acima do novo limite** ao perder blocos: qual sai (ou manter até expirarem); sem política existente conhecida. |
| S4 | **Sussurros:** a fonte cita `whisper_witch`, mas a pasta tem `whisper2_witch.mp3` e `whispering_witch.mp3`; definir qual(is), cadência e se outros jogadores ouvem (e alcance). |
| S5 | **Olhos:** confirmar que o mod 155332 está no modpack (houseCARL) e se as variantes são textura/olho de raça ou headpart; troca precisa sincronizar via appearance. |
| S6 | **Botão do ritual:** a tela do Ritual (seção 4) precisa de um modo "Sobrecarga" (receita dobrada) quando vínculo ≥100. |
| S7 | Herdado: quem escolhe os ingredientes (suposição: servidor automático). |

### 8.4 Distribuição planejada (acréscimo)
Sol High: blocos/buffs/limite de criaturas, sacrifício de sobrecarga, olhos/nome/aura sincronizados. Terra High: estados de sobrecarga do selo (raios, capacete demoníaco), modo Sobrecarga na tela do Ritual, sussurros no cliente. Revisão cruzada.


### 8.6 Elegibilidade da Sobrecarga com drenagem fracionária

Opus autorizou em 18/11 avaliar `99 < vínculo < 300` no início do ritual. O limiar inferior é configurável e resolve a janela instantânea do valor 100 em tempo online. A drenagem segue durante os 300 s e o resultado credita até 50 sobre o saldo projetado ao concluir, com teto 300. Blocos, bônus e apresentação continuam com as fronteiras fracionárias da §8.5. A UI usa `modes.overload.eligible` do [contrato v1](contrato-vinculo-do-veu.md).

## 18/11 — Servidor candidato, relógio e agenda de terror

Sol High implementou a base persistida `7dc6c23` e o agendamento `944b0c3` no repositório `C:\Code\Aetherius - Servidor Local`. Os arquivos abaixo ficam em `source\server\gamemode`, com os módulos Sobrenatural espelhados em `overlays\sobrenatural\files\server\gamemode` e os pontos de entrada transferíveis pelos patches 019 e 020.

- `supernatural\blood-reserve.js:26,52,75,149` permite um recurso adicional com as mesmas fronteiras de sessão, lote e confirmação de gravação. O esquema existente do sangue continua com `blood`. `supernatural\veil-bond.js:11,23,46` usa `bond` e grava `data\aetherius-veil-bond.json` por personagem e concessão. O projetor expõe um estado privado v1; `commands.js:25,46,67,75` e `supernatural-service.js:186,190` fazem o fechamento da sessão e a limpeza ao perder a concessão.
- `supernatural\veil-terror.js:24,61` agenda IV a cada 12–20 min online, com 35% sem evento, e V a cada 5 min, com evento. Guarda o último áudio e pensamento por sessão e grava o cooldown da tela preta antes de publicá-la. Uma gravação ambígua, cooldown vigente ou veto local da tela preta exige retorno seguro. A caixa de eventos é privada e transitória. A reprodução, a restauração da tela e os sussurros dependem da integração nativa do cliente.

Resultados Node desta rodada: 24/24 de Reserva de Sangue, 4/4 de Vínculo e 3/3 do agendador. Os pontos de integração permanecem candidatos até revisão independente e rodadas de teste no jogo.

## 18/11 — entrega de apresentação para integração (Terra; escopo reduzido por Opus)

**Decisões:** Opus dispensou XWM e autorizou WAV PCM 16-bit, 44.100 Hz, mono para os efeitos locais; records SNDR/SOUN e integração com contrato, cliente nativo e servidor passam a Sol. O dev pediu remover o anel rúnico em todos os estágios: removido da arte SVG sem alterar os MP3 ou a imagem original; `C:\Code\Aetherius - Servidor Local\client-source\client\ui-core\modules\supernatural\veil-seal.js:16` usa duas camadas da própria referência, `:19` mantém os raios, `:24` aceita 0–300. CSS interpola deslocamento/expansão e brilho em 1,2 s (`veil-seal.css`, após as regras do protótipo); a montagem não substitui a imagem por outro desenho. Cliente `ce281d6` sucede `850c082` e `d6ae059`; patch completo incremental após 013 em `C:\Code\Aetherius - Servidor Local\overlays\cliente-local\014-selo-sobrecarga.patch`, verificado com `git apply --check --reverse`.

**WAVs:** `C:\Code\Aetherius - New Systems\Sobrenatural\Bruxas\Audio\wav\` e cópia empacotada em `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\Sound\FX\Aetherius\Witch\`; os quatro MP3 permanecem intactos. FFmpeg: `-af loudnorm=I=-20:TP=-3:LRA=11 -ar 44100 -ac 1 -c:a pcm_s16le`. O pico abaixo é o pico amostral verificado por `volumedetect`, não um teste de reprodução em jogo.

| Original MP3 | WAV | Duração | Pico amostral | Uso planejado |
|---|---|---:|---:|---|
| `breathing_witch.mp3` | `breathing_witch.wav` | 18,90 s | −5,8 dBFS | somente escuridão |
| `scream_witch.mp3` | `scream_witch.wav` | 10,98 s | −10,1 dBFS | evento de áudio |
| `whisper2_witch.mp3` | `whisper2_witch.wav` | 6,06 s | −9,3 dBFS | terror ou sobrecarga privada |
| `whispering_witch.mp3` | `whispering_witch.wav` | 11,19 s | −6,1 dBFS | terror ou sobrecarga privada |

O arquivo de respiração dura mais que a tela preta; sua interrupção ou duração de reprodução é responsabilidade da integração, ainda não testada. `04DAAF:Skyrim.esm` é **SOUN** `UIHealthHeartbeatALP`, que referencia SNDR `04DAAD:Skyrim.esm`; nenhum record foi criado nesta entrega. Cópia WAV empacotada: `973ea57` (whisper2 inicial) e `8738b14` (demais três).

**API do Ritual:** `C:\Code\Aetherius - New Systems\Sobrenatural\ui-core-module\modules\supernatural\ritual-screen.js:6`, CSS adjacente; cópias versionadas em `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\ui-core\modules\supernatural\`. Carregar CSS/JS e chamar `createRitualScreen(container, {onStart, onCancel, onBack})`. `onStart({mode,categories})` envia somente a intenção com três IDs; `onCancel({ritualId})` envia o ID da execução; `onBack()` permite fechar a tela. `screen.setView(ritual)` recebe `RitualView` v1, valida estrutura e elegibilidade, desabilita opções insuficientes e devolve `false` quando inválido; `screen.setError(texto)` mostra erro e `screen.destroy()` remove listeners e conteúdo. Ele **não** envia envelopes nem faz polling: Sol liga `ritual.status/start/cancel`, atualiza o `RitualView` (inclusive até 1 Hz durante ritual), conecta o botão na tela da Bruxa e limpa na troca de sessão. A seleção e os limites no cliente são apresentação; servidor continua validando. O progresso depende exclusivamente de `active.remainingMs` recebido. Prévia simulada independente: `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\preview-ritual\index.html`, com capturas inicial/sobrecarga/em andamento.

**API visual do terror:** `C:\Code\Aetherius - New Systems\Sobrenatural\ui-core-module\modules\supernatural\veil-terror-presentation.js:4`, CSS adjacente. `createVeilTerrorPresentation({notify, document?, container?})` devolve `thought(index)` (índice 0–9; chama `notify(texto)` para o integrador usar a notificação vanilla), `blackout(durationMs)` (1000–1500 ms preto e 1200 ms de retorno CSS), `clear()` (cancela timers e remove overlay) e `destroy()`; valores inválidos retornam `false`. O componente não toca som, não detecta contexto seguro e não recebe eventos de rede: Sol deve fazer as proteções, reproduzir e interromper sons, coordenar exclusão mútua, chamar limpeza em interrupção e implementar o retorno em caso de queda da página/sessão. A variante heartbeat de 10 s e a cadência dos sussurros também pertencem à integração.

**Verificação:** `node --test overlays/sobrenatural/teste-visual-vinculo.cjs overlays/sobrenatural/teste-ritual-apresentacao.cjs` passou 7/7, cobrindo fronteiras/reversão, ausência de anel, seleção e quantidade dobrada, cancelamento, entrada inválida e limpeza de overlay. Prévia visual Orca em `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\preview-vinculo\`: valores 100/150/200/250/300 e 17 quadros 100→300→100, em 1280×720. O navegador local devolveu imagem corrompida quando solicitada captura maior; as capturas rotuladas 1920 foram retiradas, e o tamanho de 1920 não foi comprovado nesta rodada. As sequências são quadros de valores discretos, com transição CSS entre eles, não gravação contínua. `1561f68` contém tela, API de terror, prévia e testes. Revisão cruzada por outro modelo, verificação artística pelo dev, CEF real e testes em jogo ainda pendentes; não houve instalação nem jogo.


## 18/11 — Ritual, inventário e bloqueio de autoria do plugin

Candidato `4b16eb5` em `C:\Code\Aetherius - Servidor Local`. O patch 021 registra `ritual.status`, `ritual.start` e `ritual.cancel` em `source\server\ts\systems\aetheriusUiSystem.ts:153` e o serviço em `source\server\gamemode\supernatural-service.js:212`. `supernatural\veil-ritual.js:88,98,122,158` valida sessão, concessão, morte, movimentação (raio de 12 unidades a partir do início, incluindo troca de célula), combate observado e duração. Mantém uma reserva de FormIDs por execução, com nova verificação imediatamente antes da baixa. O limiar configurável `AETHERIUS_VEIL_OVERLOAD_START` começa em 99.

Em `source\server\gamemode\supernatural\veil-materials.js:13`, os FormIDs estão qualificados pelo master `Skyrim.esm`: Coração Daedra `03AD5B`, pedras pretas `02E500` e `02E504`, Nightshade `02F44C`, Nirnroot `059B86`. houseCARL encontrou no perfil ativo 33 referências ALCH ao marcador `0A0E56:Skyrim.esm` e 28 MISC ao marcador `0914EA:Skyrim.esm`. O código resolve KWDA do vencedor e exclui leite, tiras de couro, couro refinado, placas de quitina e placas de pele. Exemplos conferidos: carne bovina `065C99:Skyrim.esm`, salmão `065C9F:Skyrim.esm`, javali `03BD14:Dragonborn.esm`, pele de lobo `03AD74:Skyrim.esm`, pele de cervo do vale `011999:Dawnguard.esm`. Tomos cobrem os BOOK que ensinam magia (`DATA` flag `0x04`, conforme `source\libespm\include\libespm\BOOK.h:18`), incluindo `0B3165:Skyrim.esm` e `000AF0:ccBGSSSE025-AdvDSGS.esm`. O catálogo não usa índices de runtime fixos para mods e CC.

Em `veil-materials.js:86`, as baixas rastreadas usam uma transação PG e uma chave de ledger por Ritual e FormID. Uma única substituição de inventário nativo é conferida por leitura. Só a confirmação de baixa nativa **e** commit PG autoriza o crédito do Vínculo. PG, save nativo e JSON do Vínculo têm fronteiras de gravação distintas. Falha após a baixa pode perder material conforme D7, sem crédito nem replay. Cinco testes de Ritual invocam o serviço, incluindo disputas no mesmo Ritual, catálogo, fronteiras e resultado incerto.

Runa apurada por houseCARL: `00000F:MasterSpellsRunes.esp` MGEF `FXSummonMassBodyHolderRune` aponta ao ARTO `00000E:MasterSpellsRunes.esp`, que usa `Magic\Runes\RuneConjuration02.nif`. É um efeito constante Self/Script sem VMAD. A criação da habilidade visual no ESP existente foi recusada antes de qualquer escrita. A sessão houseCARL está no perfil ativo `AETHERIUS - GRAFICO - QUALIDADE`, onde o plugin alvo não está ativo. Em `C:\modOrganizer\profiles\AETHERIUS - LOCAL\plugins.txt:255` et seq., o plugin alvo está marcado no perfil LOCAL. Nenhum record foi criado até aqui. O Ritual candidato registra as ações, mas recusa iniciar enquanto a runa estiver indisponível.

## 18/11 — sobrecarga visual intensificada (Terra, pedido do dev)

O candidato `C:\Code\Aetherius - Servidor Local\client-source\client\ui-core\modules\supernatural\veil-seal.js` e `veil-seal.css`, cliente `46d86b3`, preserva a imagem original sem anel e reforça a sobrecarga. De 100 a 200, brilho e halo do cristal e raios longos aumentam progressivamente com pulso e leve tremor; acima de 200, as duas metades originais deslocam-se, giram e crescem progressivamente, com extensões curvas dos chifres, olhos, partículas e brilho roxos, revertendo ao reduzir o valor. A intensidade da pulsação aumenta com o valor; `prefers-reduced-motion` desliga movimento e transições. O brilho e os raios extrapolam o limite do desenho, sem garantia de que o espaço disponível em outros HUDs seja suficiente: validar visualmente em CEF e em jogo. O patch `C:\Code\Aetherius - Servidor Local\overlays\cliente-local\015-selo-sobrecarga-intensa.patch` sucede 014; `git apply --check --reverse` passou.

Prévia `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\preview-vinculo\index.html?value=300&zoom=4`: a opção `zoom=4` aumenta o componente via CSS na renderização, sem alterar a HUD. As cinco capturas `vinculo-1280x720-{100,150,200,250,300}.png`, as respectivas `vinculo-ampliado-4x-*.png` e os 17 arquivos `transicao-00-100.png` a `transicao-16-100.png` estão nessa pasta, laboratório `cfb896b`. Os quadros representam valores discretos em subida/descida, com a transição CSS entre atualizações, não uma gravação contínua. Nova tentativa de captura real 1920×1080 no navegador Orca retornou imagem repetida em blocos e sem selo; ela foi descartada, sem reivindicar validação em 1920. A avaliação ampliada usa um selo renderizado em 240 px na prévia 1280×720, quatro vezes os 60 px da HUD nessa resolução.

Verificação `node --test overlays/sobrenatural/teste-visual-vinculo.cjs` **5/5**, incluindo crescimento/reversão dos parâmetros em 100/150/200/250/300 e ausência do anel; 10 PNGs principais medidos em 1280×720 e 17 quadros presentes. São prévias de laboratório sem integração nova, instalação, CEF ou jogo; revisão cruzada por outro modelo e avaliação artística do dev permanecem pendentes.
