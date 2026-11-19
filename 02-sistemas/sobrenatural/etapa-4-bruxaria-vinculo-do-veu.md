# ETAPA 4 — Bruxaria: Vínculo do Véu e Sobrecarga

**Data:** 18/11/2026 · **Autor da especificação:** Opus 5.5 (orquestrador) · **Status:** candidato local de código entregue por Sol High e Terra High. Compilação do addon nativo, revisão cruzada e teste no jogo pendentes.

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
- **Decidido (18/11, S7):** o jogador escolhe as 3 categorias entre as que possui; o servidor valida.

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

## 8. Sobrecarga do Vínculo

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

### 8.5 Decisões do dev sobre S1–S7 (18/11)
- S1 ✅ mesma taxa linear acima de 100 (300→100 em 24 h online).
- S2 ✅ blocos completos: `piso((vínculo−100)/50)`, limitado a 0–4.
- S3 ✅ criaturas acima do novo limite permanecem até expirar; novas bloqueadas.
- S4 ✅ sussurros = `whisper2_witch` + `whispering_witch`, só para a própria bruxa.
- S5 ✅ mod de olhos não está no modpack: instalar **Miggyluv's Eyes v2.2 (Main Mod)**, Nexus 155332, e ativar nos perfis de teste. O autor bloqueou download direto (só via gerenciador/nxm): depende de o dev clicar "Mod Manager Download" no MO2; depois o orquestrador verifica (houseCARL) e ativa nos perfis.
- S6 ✅ modo "Sobrecarga" na tela do Ritual com receita dobrada quando vínculo ≥100.
- S7 ✅ **o jogador escolhe**: a tela do Ritual lista as categorias que ele possui (das 7) e ele seleciona quais 3 usar (na sobrecarga, as mesmas 3 categorias em dobro). Substitui a suposição "servidor automático"; o servidor valida a escolha.
- **Olhos por raça (dev, 18/11):** quando a raça não tiver a variante pedida (ex.: Glow ausente para Orc/Khajiit/Argonian; Magic02 ausente para Argonian), usar a variante Magic mais próxima que existir para aquela raça. Detalhes do mod em `docs/INSTALACAO_olhos-miggyluv.md`.

### 8.6 Elegibilidade da Sobrecarga com drenagem fracionária

Opus autorizou em 18/11 avaliar `99 < vínculo < 300` no início do ritual. O limiar inferior é configurável e resolve a janela instantânea do valor 100 em tempo online. A drenagem segue durante os 300 s e o resultado credita até 50 sobre o saldo projetado ao concluir, com teto 300. Blocos, bônus e apresentação continuam com as fronteiras fracionárias da §8.5. A UI usa `modes.overload.eligible` do [contrato v1](CONTRATO_vinculo-do-veu.md).

## 18/11 — Servidor candidato, relógio e agenda de terror

Sol High implementou a base persistida `7dc6c23` e o agendamento `944b0c3` no repositório `C:\Code\Aetherius - Servidor Local`. Os arquivos abaixo ficam em `source\server\gamemode`, com os módulos Sobrenatural espelhados em `overlays\sobrenatural\files\server\gamemode` e os pontos de entrada transferíveis pelos patches 019 e 020.

- `supernatural\blood-reserve.js:26,52,75,149` permite um recurso adicional com as mesmas fronteiras de sessão, lote e confirmação de gravação. O esquema existente do sangue continua com `blood`. `supernatural\veil-bond.js:11,23,46` usa `bond` e grava `data\aetherius-veil-bond.json` por personagem e concessão. O projetor expõe um estado privado v1; `commands.js:25,46,67,75` e `supernatural-service.js:186,190` fazem o fechamento da sessão e a limpeza ao perder a concessão.
- `supernatural\veil-terror.js:24,61` agenda IV a cada 12–20 min online, com 35% sem evento, e V a cada 5 min, com evento. Guarda o último áudio e pensamento por sessão e grava o cooldown da tela preta antes de publicá-la. Uma gravação ambígua, cooldown vigente ou veto local da tela preta exige retorno seguro. A caixa de eventos é privada e transitória. A reprodução, a restauração da tela e os sussurros dependem da integração nativa do cliente.

Resultados Node desta rodada: 24/24 de Reserva de Sangue, 4/4 de Vínculo e 3/3 do agendador. Os pontos de integração permanecem candidatos até revisão independente e rodadas de teste no jogo.

## 18/11 — Ritual, inventário e bloqueio de autoria do plugin

Candidato `4b16eb5` em `C:\Code\Aetherius - Servidor Local`. O patch 021 registra `ritual.status`, `ritual.start` e `ritual.cancel` em `source\server\ts\systems\aetheriusUiSystem.ts:153` e o serviço em `source\server\gamemode\supernatural-service.js:212`. `supernatural\veil-ritual.js:88,98,122,158` valida sessão, concessão, morte, movimentação (raio de 12 unidades a partir do início, incluindo troca de célula), combate observado e duração. Mantém uma reserva de FormIDs por execução, com nova verificação imediatamente antes da baixa. O limiar configurável `AETHERIUS_VEIL_OVERLOAD_START` começa em 99.

Em `source\server\gamemode\supernatural\veil-materials.js:13`, os FormIDs estão qualificados pelo master `Skyrim.esm`: Coração Daedra `03AD5B`, pedras pretas `02E500` e `02E504`, Nightshade `02F44C`, Nirnroot `059B86`. houseCARL encontrou no perfil ativo 33 referências ALCH ao marcador `0A0E56:Skyrim.esm` e 28 MISC ao marcador `0914EA:Skyrim.esm`. O código resolve KWDA do vencedor e exclui leite, tiras de couro, couro refinado, placas de quitina e placas de pele. Exemplos conferidos: carne bovina `065C99:Skyrim.esm`, salmão `065C9F:Skyrim.esm`, javali `03BD14:Dragonborn.esm`, pele de lobo `03AD74:Skyrim.esm`, pele de cervo do vale `011999:Dawnguard.esm`. Tomos cobrem os BOOK que ensinam magia (`DATA` flag `0x04`, conforme `source\libespm\include\libespm\BOOK.h:18`), incluindo `0B3165:Skyrim.esm` e `000AF0:ccBGSSSE025-AdvDSGS.esm`. O catálogo não usa índices de runtime fixos para mods e CC.

Em `veil-materials.js:86`, as baixas rastreadas usam uma transação PG e uma chave de ledger por Ritual e FormID. Uma única substituição de inventário nativo é conferida por leitura. Só a confirmação de baixa nativa **e** commit PG autoriza o crédito do Vínculo. PG, save nativo e JSON do Vínculo têm fronteiras de gravação distintas. Falha após a baixa pode perder material conforme D7, sem crédito nem replay. Cinco testes de Ritual invocam o serviço, incluindo disputas no mesmo Ritual, catálogo, fronteiras e resultado incerto.

Runa apurada por houseCARL: `00000F:MasterSpellsRunes.esp` MGEF `FXSummonMassBodyHolderRune` aponta ao ARTO `00000E:MasterSpellsRunes.esp`, que usa `Magic\Runes\RuneConjuration02.nif`. É um efeito constante Self/Script sem VMAD. A criação da habilidade visual no ESP existente foi recusada antes de qualquer escrita. A sessão houseCARL está no perfil ativo `AETHERIUS - GRAFICO - QUALIDADE`, onde o plugin alvo não está ativo. Em `C:\modOrganizer\profiles\AETHERIUS - LOCAL\plugins.txt:255` et seq., o plugin alvo está marcado no perfil LOCAL. Nenhum record foi criado até aqui. O Ritual candidato registra as ações, mas recusa iniciar enquanto a runa estiver indisponível.

## 18/11 — protótipo da sobrecarga do selo e decisão de cadência (Terra)

Correção do dev: o capacete aberto deve evoluir de `witch gauge.png`, sem troca de sprite; placas, chifres e asas pertencem à mesma arte, com transições suaves em ambas as direções. A versão candidata em `C:\Code\Aetherius - Servidor Local\client-source\client\ui-core\modules\supernatural\veil-seal.js` e `veil-seal.css` separa a imagem original em duas metades e interpola deslocamento e expansão acima de 200, brilho violeta e raios acima de 100; `setValue` aceita 0–300. É protótipo visual: não está ligado à projeção v1, não foi aprovado em revisão nem em CEF/jogo e depende de validação artística quanto à abertura das placas e crescimento dos chifres. Commits do cliente: `850c082` (primeiro desenho, substituído), `d6ae059` (arte original em camadas). Patch incremental `C:\Code\Aetherius - Servidor Local\overlays\cliente-local\014-selo-sobrecarga.patch`; capturas `vinculo-1920x1080-{100,150,200,250,300}.png` e sequência `transicao-00-100.png` a `transicao-16-100.png` em `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\preview-vinculo`. Teste `node --test overlays/sobrenatural/teste-visual-vinculo.cjs`: 3/3, cobrindo limites e reversão; os quadros são valores discretos, não uma prova visual de cada quadro interpolado. A prévia usa cenário simulado, sem jogo.

Cadência inicial dos sussurros, decidida por Opus em resposta ao bloqueio e configurável para teste: primeiro após 30–90 s, depois a cada 3–6 min online, alternando `whisper2_witch` e `whispering_witch` sem repetir, volume baixo, somente a bruxa; adiar enquanto houver terror ou tela preta e parar ao chegar a ≤100, perder maldição ou encerrar sessão, sem acumular em reconexão. Ainda não implementado.

O contrato `C:\Code\Aetherius - New Systems\Sobrenatural\docs\CONTRATO_vinculo-do-veu.md` v1 foi publicado por Sol após o início do protótipo. Conversão: FFmpeg disponível gera WAV PCM normalizado, mas seu formato xWMA é somente leitura (`D xwma`); tentativa de multiplexar retornou `Requested output format 'xwma' is not known`. Um WAV experimental `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\Sound\FX\Aetherius\Witch\whisper2_witch.wav` foi gerado; nenhum XWM ou record novo foi criado. houseCARL identificou `04DAAF:Skyrim.esm` como SoundMarker (SOUN), apontando para o SoundDescriptor `04DAAD:Skyrim.esm`; a reprodução e o encerramento não foram testados em jogo. Restam tela do Ritual, integração HUD/contrato, áudio, terror e sussurros; nenhuma integração funcional deve ser presumida.

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

## 18/11 — sobrecarga visual intensificada (Terra, pedido do dev)

O candidato `C:\Code\Aetherius - Servidor Local\client-source\client\ui-core\modules\supernatural\veil-seal.js` e `veil-seal.css`, cliente `46d86b3`, preserva a imagem original sem anel e reforça a sobrecarga. De 100 a 200, brilho e halo do cristal e raios longos aumentam progressivamente com pulso e leve tremor; acima de 200, as duas metades originais deslocam-se, giram e crescem progressivamente, com extensões curvas dos chifres, olhos, partículas e brilho roxos, revertendo ao reduzir o valor. A intensidade da pulsação aumenta com o valor; `prefers-reduced-motion` desliga movimento e transições. O brilho e os raios extrapolam o limite do desenho, sem garantia de que o espaço disponível em outros HUDs seja suficiente: validar visualmente em CEF e em jogo. O patch `C:\Code\Aetherius - Servidor Local\overlays\cliente-local\015-selo-sobrecarga-intensa.patch` sucede 014; `git apply --check --reverse` passou.

Prévia `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\preview-vinculo\index.html?value=300&zoom=4`: a opção `zoom=4` aumenta o componente via CSS na renderização, sem alterar a HUD. As cinco capturas `vinculo-1280x720-{100,150,200,250,300}.png`, as respectivas `vinculo-ampliado-4x-*.png` e os 17 arquivos `transicao-00-100.png` a `transicao-16-100.png` estão nessa pasta, laboratório `cfb896b`. Os quadros representam valores discretos em subida/descida, com a transição CSS entre atualizações, não uma gravação contínua. Nova tentativa de captura real 1920×1080 no navegador Orca retornou imagem repetida em blocos e sem selo; ela foi descartada, sem reivindicar validação em 1920. A avaliação ampliada usa um selo renderizado em 240 px na prévia 1280×720, quatro vezes os 60 px da HUD nessa resolução.

Verificação `node --test overlays/sobrenatural/teste-visual-vinculo.cjs` **5/5**, incluindo crescimento/reversão dos parâmetros em 100/150/200/250/300 e ausência do anel; 10 PNGs principais medidos em 1280×720 e 17 quadros presentes. São prévias de laboratório sem integração nova, instalação, CEF ou jogo; revisão cruzada por outro modelo e avaliação artística do dev permanecem pendentes.

## 18/11 — transições suaves do selo (Terra; após aprovação visual do dev)

O componente `C:\Code\Aetherius - Servidor Local\client-source\client\ui-core\modules\supernatural\veil-seal.js:30` recebe a primeira projeção sem transição; depois de dois `requestAnimationFrame` únicos, habilita easing. A chamada opcional `setValue(valor, {immediate:true})` força reconstrução imediata e rearma a transição depois dos quadros iniciais; Sol deve usá-la ao restaurar uma HUD que tenha sido ocultada e reutilizada após troca de personagem/sessão, pois o atual `client/index.html` apenas oculta e reutiliza a instância. Para saltos de pelo menos 1 ponto o alvo interpola por 1500 ms; para atualizações menores, 1100 ms, sempre retargetando a partir da apresentação corrente, e uma repetição idêntica não altera estilos. A pontuação continua vinda do servidor.

`veil-seal.css:31–34` interpola o preenchimento com `transform:scaleY` em torno da base, a abertura por transform e as demais camadas por opacidade, incluindo halo, raios, cor, chifres e partículas. Para evitar animar filtros, a cor e o brilho usam cópias com filtros estáticos cuja opacidade é interpolada; os filtros e as camadas ainda exigem medição de custo no CEF real, portanto não há alegação de desempenho comprovado. A pulsação de sobrecarga só roda enquanto ela está ativa, e o modo `prefers-reduced-motion` desliga animações e transições. O aspecto visual aprovado foi preservado como referência, mas a mistura de camadas precisa de confirmação artística e revisão independente.

Prévia interativa: `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\preview-vinculo\animacao.html` (slider, botões das três transições e ampliação). Ela respeita a preferência de movimento do sistema; para demonstração explícita apenas nessa prévia, usar `animacao.html?value=100&zoom=4&forceMotion=1`. O Windows local reportou redução de movimento, confirmada pelo navegador; a opção de demonstração é restrita à página de prévia e não altera o CSS do jogo. Os 15 PNGs em `preview-vinculo\animacoes\` representam 100→150, 150→250 e 250→95, cada um nos tempos 0/375/750/1125/1500 ms: o relógio das CSS transitions foi pausado e posicionado nos tempos indicados para uma sequência determinística, não é gravação contínua. A prévia de 1920 permanece limitada pelo defeito previamente documentado do navegador.

Teste `node --test overlays/sobrenatural/teste-visual-vinculo.cjs`: 5/5 (primeira projeção imediata, fronteiras e reversão, atualização fina sem escrita redundante, reconstrução explícita, acessibilidade/redução de movimento). Em Orca verificou-se slider 225 → abertura 0,25 e `transition:none` sob redução de movimento, além de transições CSS intermediárias quando a página de demonstração força movimento. Patch do selo `C:\Code\Aetherius - Servidor Local\overlays\cliente-local\016-selo-transicoes.patch`, após 015, validado com `git apply --check --reverse`; prévia e testes no laboratório `94f4313`. Não houve teste no CEF do jogo, benchmark ou execução do Skyrim.

**Autoria mista do commit cliente `8bcc121`:** o índice compartilhado já continha trabalho preparado por Sol quando Terra commitou os dois arquivos do selo; o commit foi mantido intacto por orientação do Opus, e não é uma entrega exclusiva de Terra. Além de `client/ui-core/modules/supernatural/veil-seal.js` e `veil-seal.css` (Terra), inclui trabalho de Sol em `client/index.html`, `client/src/services/services/aetheriusUiService.ts`, `client/src/services/services/spSnippetService.ts`, `client/src/services/services/veilEffects.ts`, `client/src/sync/actorvalues.ts`, `client/ui-core/modules.json` e, dentro de `client/ui-core/modules/supernatural/`, `icons.js`, `ritual-screen.css`, `ritual-screen.js`, `supernatural-data.js`, `supernatural-module.css`, `supernatural-module.js`, `veil-terror-presentation.css` e `veil-terror-presentation.js`. A revisão cruzada deve cobrir cada autoria; não se presume aprovação dos arquivos do Sol por este registro.

## 18/11 — Integração da Etapa 4 para revisão cruzada

Repositório local em `C:\Code\Aetherius - Servidor Local`. Terra High implementou a apresentação e as transições do selo. O commit de cliente `8bcc121` contém as alterações do selo do Terra e também os arquivos de integração que Sol High havia colocado no índice compartilhado. Os commits posteriores `480f19d` e `8d9fc09` são de Sol High. O patch de distribuição da integração é `overlays\cliente-local\017-veu-integracao.patch`, após o 016 do selo, com reverse-check conferido. No fechamento, o espelho de servidor está em `f69c438`. As mudanças são candidatas e aguardam revisão de outro modelo.

### Pontos de integração

- UI Core. `client-source\client\ui-core\modules\supernatural\supernatural-module.js:59` conecta o botão Ritual à tela Terra, consulta o servidor até 1 Hz com a tela aberta, ignora respostas após desmontagem ou troca de concessão e recebe os resultados do contrato v1. O manifesto local registra o módulo e a ordem dos scripts. `client-source\client\index.html:1347` aplica a primeira projeção e a restauração da HUD com `setValue(bond, {immediate:true})`, usa transição nas atualizações seguintes e oculta a barra de sangue enquanto o selo estiver ativo.
- Efeitos privados. `client-source\client\src\services\services\veilEffects.ts:28` consome a caixa de terror v1, deduplica por ID, controla uma instância sonora por vez e preserva o handle quando o stop falha. O heartbeat nativo recebe uma janela de 10 s. A tela preta só inicia com contexto permitido, retorna pelo componente visual do Terra e limpa no cliente se houver combate, menu, salto, queda, cena, saída do estágio, morte, troca de célula, desconexão ou recarga. A respiração para no fim do retorno. Os sussurros alternam após 30–90 s e depois a cada 3–6 min, exclusivamente na sessão da bruxa.
- Runa e animação, `1537e23`. `source\server\gamemode\supernatural\veil-ritual-fx.js:8,63` replica uma habilidade visual por propriedade SkyMP ao dono e aos vizinhos, incluindo entrada tardia. O cliente aplica a pose `MLh_SpellAimedConcentrationStart`, encerra com `MLh_Equipped_Event` e usa uma vigência curta para limpar se a publicação parar. Relatos locais de combate são positivos e complementam o gancho de hit do servidor. A qualidade da pose, a remoção da runa no cliente real e a entrega para vizinhos dependem de jogo.
- Aura e identidade, `42d5832`. `source\server\gamemode\supernatural\veil-visuals.js:7` usa o mesmo ciclo da aura Feral com `000807:SkyDeath-Ghosts.esp` (`SkyDeathGhost_violet`). O SkyDeath efetivamente usa `103129:Skyrim.esm` para o próprio fantasma violeta. `source\server\gamemode\skysocial\src\identity.js:44` devolve “BRUXA” à identidade de apresentação dos vizinhos e o nameplate nativo usa tom violeta. O nome persistido do personagem continua vindo de sua ficha original.
- Olhos. `source\server\gamemode\supernatural\veil-eyes.js:20,59` escolhe a variante pelo `raceId`, sexo e Vínculo projetado. A gravação antecipada do olho original e do último projetado permite restaurar só o headpart de autoria da mecânica, preservando o restante do rosto. O arquivo `data\aetherius-veil-eyes.json` usa descritores de FormID estáveis. O servidor replica a mudança pela propriedade nativa `appearance`, que o cliente existente aplica ao dono e aos vizinhos.
- Magicka. `source\server\gamemode\supernatural\veil-magicka.js:4,16` projeta multiplicadores nos limiares. `overlays\sobrenatural\025-veu-magicka-nativa.patch` altera o addon em `source\server\cpp\server_guest_lib\aetherius_combat\DevAttributes.cpp:112`, `MpActor.cpp:1725` e `CropRegeneration.cpp:78`. O servidor aplica o máximo ao resultado de raça/classe e preserva a quantidade corrente absoluta ao mudar o máximo, limitada ao novo teto. No estágio V o máximo é zero e a taxa nominal mantém a penalidade de 50%. `client-source\client\src\services\services\spSnippetService.ts:68` aplica máximo, taxa e a quantidade corrente no mesmo update. Os modificadores são vinculados ao perfil, com remoção por sessão e ACK do cliente. O addon do kit existente precisa ser recompilado para reconhecer a propriedade.
- Invocações, `3831f69`. `source\server\gamemode\magic-service.js:81,98,104,146` adiciona blocos ao limite de classe/perks, conta criaturas por dono e revalida antes de criar após o callback já agendado. Acima do novo limite, as criaturas mantêm seu ciclo e novas invocações são recusadas. Ao chegar ao limite, a substituição FIFO do serviço existente volta a valer. **R-ANIM** pertence a despacho próprio por decisão do Opus. As duas portas de conjuração existentes reconhecem apenas o arquétipo 18 `SummonCreature`. O produtor de reanimações de cadáver precisa registrar e expirar criaturas por autoridade nativa/servidor antes de preencher a contagem compartilhada.

### Records criados no ESP

Fonte de autoria `C:\modOrganizer\mods\AETHERIUS - SOBRENATURAL\AETHERIUS - SOBRENATURAL.esp`. Após o dev selecionar `AETHERIUS - LOCAL`, houseCARL criou e releu os nove records abaixo. Cópia idêntica no commit `a00899a` em `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\plugin\AETHERIUS - SOBRENATURAL.esp`. SHA-256 `B9C4F8BF3EFBBDB1B730539118D31EE21C3FD8B879DA945443D3F5C99129F8E6`. O master `MasterSpellsRunes.esp` carrega antes do Sobrenatural no perfil LOCAL. A verificação de integridade do plugin retornou zero referências soltas, masters faltantes e falhas de parse.

| FormID local no AETHERIUS - SOBRENATURAL.esp | Tipo | EditorID | Recurso |
|---|---|---|---|
| `00083A` | SPEL | `SN_Veu_RitualRune` | Ability, ConstantEffect, Self, `00000F:MasterSpellsRunes.esp` |
| `00083B` | SNDR | `SN_Veu_BreathingSD` | `breathing_witch.wav` |
| `00083C` | SNDR | `SN_Veu_ScreamSD` | `scream_witch.wav` |
| `00083D` | SNDR | `SN_Veu_Whisper2SD` | `whisper2_witch.wav` |
| `00083E` | SNDR | `SN_Veu_WhisperingSD` | `whispering_witch.wav` |
| `00083F` | SOUN | `SN_Veu_Breathing` | `00083B` |
| `000840` | SOUN | `SN_Veu_Scream` | `00083C` |
| `000841` | SOUN | `SN_Veu_Whisper2` | `00083D` |
| `000842` | SOUN | `SN_Veu_Whispering` | `00083E` |

Todos os WAVs são da pasta `Sobrenatural\Bruxas\Audio\wav` e estão no overlay em `files\Sound\FX\Aetherius\Witch`. Os SNDR usam categoria e saída UI de `064451:Skyrim.esm` e `07EDCA:Skyrim.esm`, sem loop. Os sussurros têm atenuação estática de 12 dB, e os outros dois sons de 8 dB. O heartbeat é o SOUN vanilla `04DAAF:Skyrim.esm` com seu loop nativo.

### Eye parts do Miggyluv v2.2

FormIDs de `Miggyluv's Eyes.esp`, na ordem Light / Dark / Glow. A leitura das DDS humanas no arquivo do mod encontrou luminância média de 51,38 no conjunto de pixels violetas de Magic01 e 36,99 no mesmo critério de Magic02. Os Glow usam textura Magic01. A variante disponível mais próxima de Glow é Light. Se só existir uma variante compatível, ela atende às três faixas. Sem variante de raça/sexo compatível, a aparência original é preservada. No perfil LOCAL efetivamente reaberto, o mod foi observado desabilitado e o plugin ausente da ordem ativa. A implementação resolve os IDs com segurança quando o plugin está presente. A validação do perfil depende da reativação por quem controla o MO2.

| Raça | Masculino | Feminino |
|---|---|---|
| Humanos (`0A8023:Skyrim.esm`) | `09D / 09E / 2D5` | `0F7 / 0F8 / 2D4` |
| Bosmer | `1A9 / 1AA / 2DF` | `1D1 / 1D2 / 2DE` |
| Altmer | `23A / 23B / 2E8` | `27E / 27F / 2E9` |
| Dunmer | `260 / 261 / —` | `2A4 / 2A5 / 2EE` |
| Orc | `0F6 / 0FB / —` | `2F9 / 2FA / —` |
| Khajiit | `342 / 394 / —` | `3C6 / 3C7 / —` |
| Argonian | `337 / — / —` | nenhuma |

### Verificação e pendências

- Bateria Node do gamemode/projeções e regressões do Sobrenatural **445/445** com `AETHERIUS_VAMPIRE_FEED_ENABLED=false` e **445/445** com true. A seleção de cinco arquivos Core usada nas revisões anteriores passou **39/39**. Serviço, painel F7 e revisão F7 deram OK. A execução suplementar de `npm test` integral do Core teve **184/205** no primeiro estágio, com casos que exigem o banco isolado `aetherius_test`, exports `gameplay\_work\exports` e load order de outra árvore, além de asserts dos fixtures reais. Essa execução não valida o Core inteiro no laboratório atual.
- TypeScript do servidor e do cliente e webpack do cliente passaram sem deploy. Bundle em `client-source\build\dist\client\Data\Platform\Plugins\skymp5-client.js`, SHA-256 `F935C118CFFFC38FC138475266F4620239EBD3D3C884DCCA6D812259A532AB62`. Os sete patches 019–025 foram revertidos e reaplicados em cópia descartável, com igualdade de conteúdo de 17 arquivos e conferência SHA-256 dos 11 espelhos de módulos.
- A configuração CMake offline encontrou MSVC e Windows SDK, mas parou em `find_package(CMakeRC)` em `source\server\CMakeLists.txt:9`. O addon novo não foi compilado. O arquivo de cache da tentativa foi retirado da pasta de build para não reutilizar `VCPKG_MANIFEST_INSTALL=OFF` em uma configuração posterior. Restam a compilação com as dependências presentes, revisão cruzada, reativação de Miggyluv no perfil de teste e a rodada em jogo/CEF com uma bruxa, um observador e um terceiro entrando depois. O servidor, o cliente de jogo e a instalação de ativos não foram executados.

## 18/11 — Fechamento após aceite e ganho gradual no selo

Decisão do dev repassada por Opus. Somente `ritual.start` com `ok: true` e `ritual.active.id` autoriza a interface a fechar. A apresentação do Terra faz o fechamento. As interrupções e os 300 s seguem no servidor. A interface pode ser reaberta para consultar a execução. Erro ou timeout mantém a tela e consulta o status para recuperar o caso de resposta perdida.

O servidor credita de uma vez depois de confirmar o consumo, preservando D7. O valor confirmado determina olhos, aura, Magicka e limite de criaturas desde o commit. A ligação do Sol continua em `C:\Code\Aetherius - Servidor Local\client-source\client\index.html:1347`. Cada novo alvo, inclusive cada fração da drenagem, usa `setValue(bond)`. A API do selo de Terra sobe em passos de 1 ponto quando o alvo supera o exibido em pelo menos 2 pontos, à velocidade inicial de cerca de 10 pontos/s. Durante os passos, atualizar o alvo mantém a subida enquanto ele superar o exibido. O primeiro snapshot e a restauração usam `setValue(bond, { immediate: true })` para cancelar qualquer animação e aplicar a projeção diretamente. O [contrato v1](CONTRATO_vinculo-do-veu.md) mantém os mesmos campos.

O teste da ponte CEF passa os valores 260, 249, 299 e 298,997 ao mesmo selo. A sequência confirma que ganho e drenagem continuam pelo caminho comum, com aplicação imediata somente na hidratação inicial e na restauração. Os nove testes de `teste-efeitos-veu-cliente.cjs` e `teste-ritual-servidor.cjs` passaram. A curva e o fechamento são verificados no lote de apresentação de Terra.

## 18/11 — Ritual no padrão da Bruxa e ganho gradual (Terra, pedido do dev)

**Ritual:** `C:\Code\Aetherius - Servidor Local\client-source\client\ui-core\modules\supernatural\ritual-screen.js:9` usa `sn-breadcrumb`, `sn-workspace`, `sn-identity`, `sn-hero`, `sn-medallion`, `sn-content`, `sn-section-head`, `sn-choice-grid` e `sn-choice` da tela da Bruxa; a entidade permanece na moldura e as sete oferendas exibem disponibilidade conforme o modo. O CSS específico restante está em `ritual-screen.css:1`. `supernatural-module.js:59–80` conserva a tela e exibe a mensagem quando `ritual.start` retorna erro; somente `ok:true` com execução ativa confirmada chama `context.close()`. O fechamento em `client\ui-core\frontend\shell.js:434` desmonta o módulo, retorna ao estado gameplay e libera o foco nativo, condicionado à mesma instância ainda ativa. Após iniciar, a tela não exibe progresso nem botão cancelar; cancelamento e condução no jogo pertencem à integração autoritativa do servidor. O botão do Ritual na tela padrão recebeu as mesmas regras visuais do botão Atualizar (`supernatural-module.css`).

**Ganho:** `client\ui-core\modules\supernatural\veil-seal.js:55–70` mantém a API `setValue(v, options)` existente, sem exigir novo evento ou alias do Sol. Depois da primeira projeção, aumento de pelo menos 2 pontos inicia passos de 1 ponto a cada 100 ms por padrão (~50 pontos em 5 s); `setValue(v,{stepMs:...})` permite configurar o intervalo, limitado a 20–1000 ms. Uma atualização durante a subida muda o alvo, inclusive uma pequena redução por drenagem, enquanto o alvo for maior que o valor lógico mostrado; se o alvo ficar igual ou abaixo, cancela a subida e interpola até o novo valor. `setValue(v,{immediate:true})` cancela timer e restaura imediatamente, inclusive login/reconexão; redução de movimento aplica o alvo imediatamente. `client\index.html:1343` chama `cancelGain` ao ocultar o selo, tolerando componente antigo sem esse método. A animação é somente apresentação: o saldo e a conclusão continuam autoritativos no servidor.

**Arquivos e revisão:** cliente `b007e6e` (ganho), `82280b2` (tela/fechamento), `5589c91` (botão), `c27afd4` (compatibilidade ao ocultar); patches locais `017-ganho-vinculo.patch`, `018-ritual-painel-bruxa.patch`, `019-botao-ritual.patch`, `020-ganho-hide-compat.patch` em `C:\Code\Aetherius - Servidor Local\overlays\cliente-local\`, cada um validado com reverse-check. Laboratório `ca52c0b`, `e081b52`. Capturas lado a lado de Bruxa + normal/sobrecarga/recusa e `comparacao.html` em `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\preview-ritual\`. Quadros reais aproximados e um quadro final dos ganhos 80→130 e 180→230 em `overlays\sobrenatural\preview-vinculo\ganho\`; preview `animacao.html?value=80&zoom=4&forceMotion=1` força movimento apenas na prévia para o Windows local configurado com redução de movimento.

**Verificação:** `node --test overlays/sobrenatural/teste-visual-vinculo.cjs overlays/sobrenatural/teste-ritual-apresentacao.cjs` passou 11/11, incluindo drenagem a cada segundo durante ganho, retarget, queda abaixo do exibido, cancelamento imediato, redução de movimento e ausência de Cancelar na tela; `node --test overlays/sobrenatural/teste-efeitos-veu-cliente.cjs` passou 4/4. Inicialmente um dublê do teste de integração não tinha `cancelGain`, gerando falha de teste; `c27afd4` tornou a chamada opcional e o teste passou, sem alegar falha em runtime. `npx tsc --noEmit` e `node --check` dos JS alterados passaram. No navegador Orca com servidor simulado, resposta negativa manteve a tela e exibiu motivo, resposta positiva chamou `close` uma vez; um teste isolado com o shell real confirmou workspace→gameplay, unmount e pedido de perda do foco. Revisão cruzada por outro modelo, benchmark/CEF, validação do contrato com servidor e jogo continuam pendentes; não houve instalação no MO2 nem execução do jogo.

## Correções da revisão Opus (A1, B2, B3) e verificação C1 — 18/11/2026

**Autor:** Opus 5.5 Low (contingência, `task_0890c3c11f2b`). **Revisão de referência:** `docs/REVISAO-ETAPA-4_opus.md`. Sem servidor, jogo ou MO2; houseCARL só em leitura. Commits: cliente `b7f92f1` em `C:\Code\Aetherius - Servidor Local\client-source` (sobre `c27afd4`) e laboratório `c65b1e7` em `C:\Code\Aetherius - Servidor Local` (sobre `a533441`). Commits feitos com pathspec explícito; o trabalho paralelo do Sol (addon 025) não foi tocado.

### A1: isolamento do cliente
- Novo `client/src/sobrenatural/`:
  - `sobrenaturalService.ts` (ClientListener): é dono de `VeilEffects` e `VeilMagicka`. Ouve os próprios eventos (`update`, `loadGame`, `aetheriusUiFocusChanged`, `customPacketMessage` de sessão/`uiDisconnect`, `connectionDisconnect/Failed/Denied`) e reproduz os resets que antes ficavam no `aetheriusUiService`.
  - `veilEffects.ts`: movido de `services/services/` sem mudança de lógica.
  - `veilMagicka.ts`: o snippet `AetheriusVeil.ApplyMagicka`, mais B2 e B3.
- Revertidos ao base `e82ac03`: `ui-core/frontend/shell.js` (sem `close()`), `src/sync/actorvalues.ts` (sem a proteção contra NaN) e `aetheriusUiService.ts` (sem nenhuma referência ao Véu).
- Integração mínima que fica no núcleo (diff total contra `e82ac03`: 3 arquivos, +12/−1):
  - `src/index.ts`: import e registro de `SobrenaturalService` (2 linhas).
  - `spSnippetService.ts`: import e delegação `lookupListener(SobrenaturalService).runSnippet(...)` (3 linhas).
  - `index.html`: 2 `<link>` e 3 `<script>` do módulo. O bloco inline foi para `ui-core/modules/supernatural/veil-hud.js`, e a regra `html.aetherius-browser-hidden .veil-seal` foi para `veil-seal.css`. A linha `aetheriusVeilHudActive` em `setBloodReserveHud` pertence à HUD de sangue, que também é do Sobrenatural (`f57ec7d`).
  - `modules.json`: registro, sem alteração.
- Fechamento do Ritual (`supernatural-module.js`): `closeShell()` local executa o `cleanup()` do próprio módulo, depois `AetheriusUI.nativeFocusChanged(false)` (API pública) e `window.aetheriusUiSetFocus("false")`. É o mesmo caminho da perda de foco nativa. Diferença em relação ao `close()` removido: o shell não chama o `unmount` (o módulo já se limpou, e `cleanup` é idempotente) e não há animação. **Validar no CEF.**
- NaN no estágio V: sem `actorvalues.ts`, o cliente mantém um máximo efetivo de `EMPTY_MAXIMUM = 0.01`, com Magicka atual 0 e `MagickaRate` 0. O percentual fica finito (0) e a barra fica vazia. O descarte de NaN no `ActionListener.cpp` (patch 025) continua como defesa no servidor.

### B2: base × Fortificar
`VeilMagicka.enforce` define a base como `max(máximo do servidor, 0.01) − (getActorValueMax − getBaseActorValue)`. Assim, o máximo **efetivo** é igual ao do servidor, com qualquer Fortificar já compensado. A Magicka atual é ajustada só até o `current` enviado (restore/damage do delta), sem preenchimento. A cada 250 ms o valor é reforçado, para acompanhar a troca de equipamento. Quando o servidor devolve o perfil vanilla (máximo e taxa iguais aos valores capturados do cliente), a base e a taxa originais são restauradas e o Fortificar volta a somar. **Risco a validar em jogo:** com Fortificar no estágio V, a base fica negativa (ex.: 0.01 − 50). O Skyrim aceita AV base negativo, mas isso precisa ser conferido junto com a reprodução B2 da revisão.

### B3: loadGame sem desconexão
- **Magicka:** o alvo sobrevive ao `loadGame`. `onLoadGame` descarta a base vanilla capturada (o motor a restaura), e o `update` reaplica o alvo assim que o jogador carrega o 3D. Não é preciso nenhum reenvio do servidor nem mudança no C++. Uma sessão nova ou uma desconexão restaura a base e a taxa vanilla e limpa o alvo.
- **Selo e efeitos:** o `updateOwner` do `veil-bond.js` regrava `aetheriusVeilPresentationV1` a cada frame e reenvia a HUD em até 1 s após o `reset` do `loadGame`. O terror também é regravado. O id do último evento é marcado como visto enquanto o 3D não carregou, então não há replay.
- **Olhos:** são `appearance` do núcleo, reaplicada pelo carregamento do núcleo.
- **Aura:** o `auraScript` repete o shader a cada 1,5 s.
- Nenhuma alteração no servidor foi necessária.

### C1: plugins (houseCARL, leitura)
- `MasterSpellsRunes.esp`:
  - **ativo** no perfil MO2 `AETHERIUS - LOCAL` (linha 133 de `plugins.txt`, antes de `AETHERIUS - SOBRENATURAL.esp`, que está na 255);
  - presente e ativo também em `AETHERIUS - GRAFICO - QUALIDADE` e `AETHERIUS - PERFORMANCE` (linha 133), e nas cópias de `client-source/client/profiles/*/plugins.txt`;
  - presente no servidor, em `runtime/server/data/aetherius-loadorder.json` (posição 149, antes do Sobrenatural na 271).
- `SkyDeath-Ghosts.esp` está presente em todos os perfis e no servidor (linha 418). Não é master: é resolvido em runtime por `getFormFromFile`. Linhas: 418 nos `plugins.txt` do MO2, 416 nos espelhos QUALIDADE/PERFORMANCE do `client-source`, 434 no `aetherius-loadorder.json` do servidor (correção da revisão Neon R6).
- **Nenhuma ação necessária.** Observação: os perfis oficiais ainda não listam `AETHERIUS - SOBRENATURAL.esp`, o que é esperado até a compilação final. Ao incluí-lo, ele deve ficar depois de `MasterSpellsRunes.esp`.

### Testes (`C:\Code\Aetherius - Servidor Local`, Node v22.14.0)
- Bateria da ETAPA-3 (todos os `overlays/sobrenatural/teste-*.cjs`, exceto serviço/F7/revisão F7, mais 5 Core):
  - `AETHERIUS_VAMPIRE_FEED_ENABLED=false`: **454/454**;
  - `=true`: **454/454**.
- `teste-efeitos-veu-cliente.cjs`: **10/10**, sendo 4 antigos e 6 novos (B2 efetiva/Fortificar, estágio V finito, retorno ao vanilla, B3 `loadGame`, entradas inválidas, isolamento do núcleo).
- Divergência de contagem: os outros arquivos somam 444. A base antes desta mudança seria, portanto, 448, e não os 449 da revisão. Essa diferença de 1 é anterior a esta tarefa e não foi investigada.
- Os 5 arquivos Core sozinhos: **151/151**. O recorte "Core 39" citado em documentos antigos não corresponde a esses arquivos.
- `teste-servico` (caminho absoluto), `teste-painel-f7` e `teste-revisao-f7`: exit 0.
- `npx tsc --noEmit`: exit 0.
- `node --check` de `supernatural-module.js` e `veil-hud.js`: OK.
- Webpack não executado.

### Pendente
- Revisão cruzada por outro modelo.
- Fechamento do Ritual pelo novo caminho, no CEF.
- Reprodução B2 em jogo (anel de Fortificar nos estágios IV e V, base negativa).
- B3 em jogo (carregar o save sem relogar).
- Patch incremental: gerado depois como `021-isolamento-veu.patch` (`1ba58ee`).

**Patch incremental (18/11/2026):** `C:\Code\Aetherius - Servidor Local\overlays\cliente-local\021-isolamento-veu.patch` = `git diff --binary c27afd4 b7f92f1` no `client-source`, aplicado após o 020. Verificação feita num worktree descartável em `c27afd4`, já removido: `git apply --check` passou, e a árvore aplicada ficou idêntica a `b7f92f1` (diff vazio). `git apply --check --reverse` também passou no HEAD. Commit do laboratório `1ba58ee`.

## Correções da revisão Neon (R1–R6) — 18/11/2026

**Autor:** Opus 5.5 Low (`task_4d4adf415d9c`). **Revisão:** `docs/REVISAO-ETAPA-4_correcoes_neon.md` aprovou A1 e C1 e bloqueou B2/B3. Commits: cliente `7fb11d8` (sobre `b7f92f1`), laboratório `f4b9303` (testes e patch `overlays\cliente-local\022-veu-magicka-total.patch`). Sem servidor, sem jogo e sem MO2.

### Premissa do dev: não existe save game
Os personagens vêm do banco de dados. O único `loadGame` do cliente é o interno do spawn: `remoteServer.ts:786` (`onCreateActorMessage`) chama `LoadGameService.loadGame` (`loadGameService.ts:18/22`), ao conectar e ao reconectar. Não há outra chamada em `client/src` (busca por `loadGame(`).

Consequência: B3, como "recarga de save sem desconexão", não tem caso real. O que existe é o spawn interno:
- Antes do 3D, `apply` devolve `false`. Sem ACK, o servidor mantém `pending` e reenvia a cada 2 s.
- O template carregado não traz o modificador do Véu, por isso `onLoadGame` zera a contagem aplicada. Esse código continua necessário e não é morto.

A reaplicação por alvo guardado foi removida.

### R4: política (decisão Opus)
O Fortificar Magicka **nunca** é anulado. Os multiplicadores do Véu valem sobre o máximo **total** (base com bônus de classe, mais Fortificar). O estágio V mantém o máximo finito mínimo de 0,01, com regeneração 0.

### Novo desenho (`client/src/sobrenatural/veilMagicka.ts`)
- **Base:** pertence ao servidor/núcleo. `ApplyMagicka` define `base = maximum`, como fazia o código original, e `SendAttributeMaximumValues` também define a base. O Sobrenatural **nunca** reforça a base, o que resolve a sobrescrita apontada em R2.
- **Fortificar:** é escalado por um **modificador permanente próprio** (`modActorValue`), igual a `(fator − 1) × Fortificar`, recalculado a cada frame.
  - O fator vem de uma **fonte explícita do servidor**: a projeção do dono `aetheriusVeilPresentationV1` (`veil-bond.js` `updateOwner`), mapeada pela mesma tabela de `veil-magicka.js` (Sobrecarga 1,5; V 0; IV 0,5; demais 1).
  - Não há mais heurística de base vanilla (R2).
- **Estágio V:** o modificador leva o total a 0,01, a Magicka atual a 0 e a taxa a 0. O ACK exige `< 0.001` (R6.1).
- **Magicka atual:** mantém a fração do servidor sobre o total do cliente, sem preencher.
- **R3:** no estágio IV a base é sempre `0,5 × máximo do servidor`, que é maior que 0. Por isso `getMaximumActorValue` (`actorvalues.ts:28`) devolve um valor positivo com a Magicka em 0.
  - Ao desequipar Fortificar no estágio V, o máximo pode ficar negativo por no máximo 1 frame, porque a correção agora roda a cada frame em vez de a cada 250 ms.

### Sessão (`sobrenaturalService.ts`)
- **R1:** só há reset quando uma sessão anterior troca por outra. O primeiro `PACKET_SESSION` não reseta mais.
- **R5:** `connectionAccepted` agora reseta, o que cobre a reconexão do RaceMenu.
- **R6.2:** um filtro de tamanho e de prefixo `aetherius-ui:v1:` roda antes do `JSON.parse`.
- **R6.3:** corrigido o comentário de `closeShell()`.
- **R6.4:** corrigidas as linhas citadas da C1 e a nota do patch 021.
- **R6.5:** a auditoria de `.shell.is-supernatural-workspace` continua pendente para a compilação final.

### Testes (cenários da revisão automatizados em `teste-efeitos-veu-cliente.cjs`: 15/15)
- Tabela de fatores.
- R4: estágio III mantém o Fortificar inteiro; estágio IV reduz o total à metade; Sobrecarga multiplica o total por 1,5; volta ao estágio II.
- Fração atual sem preenchimento.
- Estágio V finito, com regeneração 0 e ACK estrito.
- R2: a base com bônus de classe enviada pelo núcleo não é sobrescrita, e o estágio I libera o Fortificar.
- R2/B3: o spawn interno recalcula sem heurística.
- R3: estágio IV com Fortificar maior que o máximo e `setActorValuePercentage` real de `actorvalues.ts`, partindo de 0.
- Snippets inválidos.
- R1: o primeiro pacote de sessão após o ACK não reseta, e a troca real de sessão reseta.
- R5: `connectionAccepted` reseta, e pacotes de outros sistemas são ignorados.
- Isolamento do núcleo.
- `closeShell()` ainda não tem teste automatizado: depende do shell no CEF.

Resultados:
- Bateria: **459/459** com a flag false e com a flag true.
- Core (5 arquivos): **151/151**.
- `teste-servico`, `teste-painel-f7` e `teste-revisao-f7`: exit 0.
- `tsc`: exit 0. `node --check` do módulo: OK.
- Patch 022: `git apply --check` em worktree descartável em `b7f92f1` passou, e a árvore aplicada ficou idêntica a `7fb11d8`. `--reverse` no HEAD também passou.

### Pendente
- Nova revisão cruzada.
- Validação em jogo de que `modActorValue` em Magicka se comporta como Fortificar e não vaza para o banco. A persistência é do servidor, que guarda percentuais e não os AVs do cliente.
- Fechamento do Ritual no CEF.

## Compilação nativa com o patch 025 (Opus 5.5 Low, 18/11/2026)

- **Estado prévio:** o build do Sol (configure + `vcpkg install`, iniciado às 19:25) foi encerrado por ordem do dev no meio do CommonLibSSE Release. Não ficou nenhum processo cmake/vcpkg rodando; sobrou `vcpkg-running.lock`, que o vcpkg tratou sozinho. Nada foi apagado.
- **Comando:** `X:\scripts\Build-Native.ps1 -AllowDependencyDownloads` (X: = `subst` de `C:\Code\Aetherius - Servidor Local`, para evitar espaços no caminho). Log em `C:\Code\Aetherius - Servidor Local\evidence\etapa4-build-native-opus.log`. Tempo total **3 min 58 s**, porque o vcpkg reaproveitou o cache binário da rodada anterior. Saída 0.
- **Patch 025:** confirmado aplicado em `source\` com `git -C source apply --reverse --check ..\overlays\sobrenatural\025-veu-magicka-nativa.patch`. **Não houve erro de compilação**, então não foi preciso corrigir nada nem fazer commit, e source e overlay seguem idênticos.
- **Warnings:** 0 linhas `warning` no log, inclusive em `DevAttributes.cpp`, `MpActor.cpp` e `CropRegeneration.cpp`.
- **Artefato:** `C:\Code\Aetherius - Servidor Local\tools\native\scam_native.node` (cópia de `source\build\dist\server\scam_native.node`), 11 714 048 bytes, SHA-256 `77f28d98f4dda3a256a0a0e1bab0a1f4cea25cebaaa21336b0399475dd05c305`.
- **Testes Node:** `node --test overlays/sobrenatural/*.cjs` deu 311/312. A única falha é `teste-servico.cjs`, que exige o diretório do gamemode em `argv[2]`. Rodado como documentado (`node overlays/sobrenatural/teste-servico.cjs 'C:/Code/Aetherius - Servidor Local/source/server/gamemode'`), deu `OK`, exit 0.
- **Não feito:** Prepare/Start do servidor, jogo e MO2 (fora do escopo). O addon só vale em execução depois de `Prepare.ps1`.
- **Alterações do Sol não commitadas (preservadas, nem commitadas nem descartadas):** `source\server\gamemode\phase0-basic.js` e `source\server\gamemode\magic\tests\magic.test.cjs`. Nenhuma alteração pendente em `veil-ritual-fx` foi encontrada em `.`, `source`, `client-source` ou `client-local-mod`.

### Revisão das alterações do Sol em `phase0-basic.js`/`magic.test.cjs` (Opus 5.5 Low, 18/11/2026)
- **Conclusão: nada a aproveitar nem a descartar — já estava integrado.** `source/` é ignorado pelo git do laboratório (`.gitignore:8: source/`), por isso as alterações pareciam "não commitadas". O conteúdo já está versionado nos overlays:
  - `phase0-basic.js` (instala `veil-ritual-fx` e injeta `rune` em `veil-ritual`): `overlays/sobrenatural/023-ritual-runa.patch`, commit `1537e23`. `veil-ritual-fx.js` e `veil-ritual.js` em `source/server/gamemode/supernatural/` são idênticos aos de `overlays/sobrenatural/files/server/gamemode/supernatural/` (versionados). O `git apply --check --reverse` do 023 isolado falha só por deslocamento de contexto causado pelo 024 (mesma região de `phase0-basic.js`); o código resultante é o do patch.
  - `magic.test.cjs` (testes de capacidade na Sobrecarga): `overlays/sobrenatural/022-veu-limite-invocacoes.patch`, commit `3831f69`; reverse-check OK. Os dois testes conferem com a S3 do DECISOES.md: limite de classe + blocos; ao perder bloco ou maldição os excedentes ficam e novos casts são recusados; após mortes, a reposição volta ao limite base substituindo o mais antigo; spawn adiado reavalia a capacidade depois do decaimento (não cria além do restante).
- **Verificação (Node v22.14.0):** bateria (`overlays/sobrenatural/teste-*.cjs` exceto serviço/F7/revisão F7, 308 testes + Core) **459/459** com `AETHERIUS_VAMPIRE_FEED_ENABLED=false` e `=true`. Core = `werewolf.test.cjs` (+`magic.test.cjs`) 51, `survival-service.test.js` 80, `skysocial-service.test.js` 6, `survival-effects.test.js` 14 → **151/151**. `teste-servico` (dir do gamemode), `teste-painel-f7`, `teste-revisao-f7`: exit 0. `npx tsc --noEmit` (client-source/client): exit 0. `node --check phase0-basic.js`: OK.
- Sem servidor, jogo ou MO2. A pendência da seção anterior ("alterações do Sol preservadas") fica encerrada.

## 18/11 — rodada pós-teste, HUD Meridian e sincronização pública (Sol High)

O resultado do dev está em TESTE-JOGO_18-11.md. Evidências, limitações e causas por item estão em [INVESTIGACAO_18-11_excecoes-hud-ritual-pisos.md](investigacoes/INVESTIGACAO_18-11_excecoes-hud-ritual-pisos.md). O contrato de Ritual passou para a [revisão v2](CONTRATO_vinculo-do-veu.md), com cancelamento autenticado por movimento e motivo final sempre atribuído pelo servidor. Mantém os campos v1 do wire. B2 e B3 usam a propriedade privada do Ritual e o temporizador do HUD após fechar o TAB. O servidor cancela também por deslocamento, célula, morte, combate, perda da maldição ou desconexão.

### B4 e B5 — FormIDs, réplicas e deploy

- O marcador de som é **000843:AETHERIUS - SOBRENATURAL.esp** (SN_Veu_RitualCharge), criado in-place via houseCARL com autorização registrada do coordenador e verificado como vencedor no perfil AETHERIUS - LOCAL. Usa o SNDR 073FD9:Skyrim.esm (MAGDestructionFireCharge300) do Charge de Fire Storm e SOPM 07E5DC:Skyrim.esm (SOMMono01800), atenuação de 150–1.800 unidades. O áudio espacial segue a decisão de 19/11 registrada abaixo. Cada observador recebe a runa 00083A pelo updateNeighbor, inclusive na entrada tardia. A runa é dispensada e removida no fim ou na expiração local. A janela de expiração é 2,5 s sem amostra. A apresentação evita forçar eventos do grafo de conjuração para permitir emote K e prevenir a interferência de B7.
- Plugin ativo e cópia do laboratório em C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\plugin\AETHERIUS - SOBRENATURAL.esp, SHA-256 65bb3aae89f199c7a8c18a2eba1892716b77b791268db07009f2ccc7924e45a0. Backup do ESP anterior em C:\Code\Aetherius - Servidor Local\backups\ritual-hud-20261118-sol\AETHERIUS - SOBRENATURAL.esp, SHA-256 b9c4f8bf3efbbdb1b730539118d31ee21c3fd8b879da945443d3f5c99129f8e6.
- **V1/B1/B2:** frontend, modules e modules-config.js copiados do client-source\client\ui-core para C:\modOrganizer\mods\AETHERIUS - UI CORE (LOCAL)\MeridianUI\aetheriusui. Os 107 arquivos da árvore combinada coincidem por SHA-256. Backup prévio C:\Code\Aetherius - Servidor Local\backups\ritual-hud-20261118-sol\MeridianUI-aetheriusui-before.
- **B8:** breathing_witch.wav, scream_witch.wav, whisper2_witch.wav e whispering_witch.wav foram copiados de C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\Sound\FX\Aetherius\Witch para C:\modOrganizer\mods\AETHERIUS - SERVIDOR LOCAL\Sound\FX\Aetherius\Witch. Os hashes correspondem ao overlay e houseCARL confirmou os quatro como vencedores. Os sussurros são privados e alternam os SOUN 000841 e 000842. A ausência dos arquivos explica a lacuna de deploy.
- **Código candidato:** laboratório 42315d1 e cliente 61d7547, patch 030. Webpack gerou C:\Code\Aetherius - Servidor Local\client-source\build\dist\client\Data\Platform\Plugins\skymp5-client.js com SHA-256 3f6bc76753024576e3a2b73cedc4c3b2fa16b431fd4450caa5080b77506cf78b. O bundle não foi copiado ao mod e o runtime do servidor aguarda Prepare.ps1 para atualizar gamemode e Data do perfil com o ESP. A execução de MO2, jogo e servidor não fez parte deste lote.

### Roteiro no local com um jogador

1. Após revisão, preparar o servidor e instalar o bundle compilado para a mesma revisão. Conectar a partir do menu principal. Em logs\server-*.stdout.log, procurar o canal aetherius.gameplay.grants com begin e ready para o mesmo ciclo. Em %USERPROFILE%\Documents\My Games\Skyrim Special Edition\SKSE\skyrim-platform.log, procurar eventSource argument encoded as null e ausência de INCORRECT_TYPE e indexInPool. Se ready faltar novamente, anotar se o carregamento começou do menu principal, se houve troca de aparência no spawn e se apareceu Class skills: application failed after spawn.
2. Setar vampiro por F7, verificar a Reserva de Sangue e o desaparecimento de fome/sede/frio. Conferir atualização pelo gasto e remoção da maldição. Setar Bruxa, verificar selo privado, recarga da HUD e retorno do estado após relog. Confirmar as quatro skills com getav Alteration, getav Conjuration, getav Destruction e getav Illusion no estágio inicial, intermediários e final. Conferir pisos 30, 40, 50, 60, 60, 60, remoção e composição com valor de classe maior.
3. Iniciar Ritual, confirmar fechamento do TAB e timer de 5:00 fora do menu. Usar K para acionar um emote sem cancelar. Em outra tentativa, apertar uma tecla mapeada de movimento e conferir travamento durante o Ritual, aviso de interrupção por movimento e desbloqueio. Repetir com conclusão, combate, morte e desconexão. Em cada fim, confirmar que a runa some e conjurar um feitiço comum. Em movimento, confirmar request supernatural ritual.cancel no log do servidor, e que os ingredientes permanecem quando o cancelamento precede o consumo.
4. Entrar na sobrecarga e permanecer vivo na mesma célula por 90 s. Ouvir o primeiro sussurro e aguardar o seguinte por até 360 s. Sair da sobrecarga durante a fala e conferir que o áudio termina. Comparar o arquivo skyrim-platform.log antes/depois por exceção de Sound.play ou Sound.stopInstance.

### Validar no servidor oficial com observadores

O dev dispensou teste com dois jogadores no laboratório. Os dublês Node verificaram o dono, um vizinho presente, um vizinho tardio, saída, reentrada e limpeza com amostra vencida. A execução real do motor precisa das verificações abaixo.

| Item | Cena e resultado esperado | Logs e evidência |
| --- | --- | --- |
| Runa B5 | A inicia com B já perto. B vê a runa sob A. C entra no mesmo raio durante o Ritual e vê a mesma arte. B sai da área, retorna e confere que ela volta somente se o Ritual ainda estiver ativo. A conclui ou interrompe. B e C veem a runa desaparecer. Repetir cancelamento por movimento, troca de célula, morte e desconexão. | Conferir no stdout request supernatural ritual.start e ritual.cancel quando houver comando. Conferir ausência de [veil] limpeza da runa será reconciliada e, nos logs Skyrim Platform dos observadores, ausência de indexInPool ao sair. |
| Som B4 | Colocar B a menos de 150 unidades de A. A e B ouvem uma vez no início e uma vez na conclusão confirmada. C entra na área durante cada trecho em tentativas separadas e ouve somente dentro da janela. Em outra tentativa C entra no meio do Ritual, já fora da janela inicial, e vê a runa em silêncio. Conferir a atenuação entre 150 e 1.800 unidades. Cancelar durante o trecho inicial e verificar que ele cessa sem emitir o Charge de conclusão. | Conferir SOUN 000843 e SNDR 073FD9 como vencedores com houseCARL no perfil oficial. Conferir logs Skyrim Platform de A, B e C por exceções de Sound.play/stopInstance; usar gravação ou relato por observador para confirmar áudio. |
| Olhos, aura, BRUXA | Com bruxa Light e B perto, anotar a cor e o nome. A passa para Dark e para Glow, usando raça com as três variantes. Na sobrecarga B e C tardio veem olhos Glow, aura roxa e BRUXA em violeta a até 4.096 unidades. B sai e retorna. Quando A volta a ≤100, confirmar perda de aura e nome especial, com Light ou Dark conforme o saldo. Remover a maldição e conferir restauração dos olhos originais após relog ou reinício. | Conferir ausência de [veil] Olhos aguardam reconciliação, Olhos indisponíveis e Projeção pública indisponível no stdout, bem como falhas de appearance ou shader nos logs Skyrim Platform de cada cliente. Comparar variante visível com a raça e sexo válidos pelo fallback. |

Verificação local sem runtime: overlay Node 335/335 por modo, Core skills e entrega 13/13, survival/magia 102/102 em cada modo, serviço e F7 aprovados. TypeScript, webpack e reverse-check do patch 030 aprovados. Revisão cruzada do novo lote ainda pendente com o coordenador.

## 19/11 — retorno à revisão Neon da HUD e do Ritual (Sol High)

O Gemini 4 Neon aprovou o lote 61d7547 / 42315d1 com duas correções de cliente em [REVISAO-BRUXA-HUD-RITUAL_neon.md](REVISAO-BRUXA-HUD-RITUAL_neon.md). Aceitei as duas e a observação do UUID do terror. O candidato é o cliente 295f02e e o laboratório 1887fd0. O incremento C:\Code\Aetherius - Servidor Local\overlays\cliente-local\031-ritual-foco-blackout.patch aplica após 030.

- **R1, blackout.** C:\Code\Aetherius - Servidor Local\client-source\client\src\sobrenatural\supernaturalHud.ts envia o tempo nominal de 1.000–1.500 ms, mesmo quando o throttle adia o envio. veilEffects.ts publica null no fim natural, e zero na interrupção ou reset. client\ui-core\modules\supernatural\supernatural-hud.js aceita o início nominal e deixa o CEF completar o fade de 1.200 ms quando recebe null. Zero e desconexão limpam a tela. Assim, a fase preta e o fade começam no relógio do CEF mesmo quando o bridge recebe o início atrasado. O áudio do terror conserva o relógio do efeito em jogo.
- **R2, foco.** O HUD usa o foco de sp.browser, o modo menu nativo, MeridianUI_FocusMenu e Console tanto ao capturar teclado ou analógico quanto antes de enviar ritual.cancel. Se o contexto se tornar ocupado, descarta a intenção pendente. O bloqueio autoritativo por deslocamento no servidor continua em vigor. K conserva o comportamento do lote anterior.
- **UUID.** C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\veil-terror.js e seu espelho em overlays aceitam UUIDs de personagem e chaves numéricas legadas no arquivo do cooldown. Uma tela preta pode gravar um UUID e o reinício continua aplicando o cooldown de 90 min.

Os testes de foco abrem e fecham cada tipo de menu antes do update e entre a captura e o envio. O teste do blackout injeta 150 ms de atraso, roda o renderer CEF com relógio simulado, mede a fase preta e o fade completos e verifica interrupção. O teste do servidor reinicia o agendador após persistir um UUID. Passaram 27 testes direcionados, 343 testes do overlay em cada modo de alimentação, TypeScript, webpack e o reverse-check do 031. SOUN e áudio do Ritual B4 seguem aguardando decisão do dev. Reconfirmação Neon e reteste em jogo pendentes.

Ao consolidar, distribuir juntos skymp5-client.js e o arquivo modules\supernatural\supernatural-hud.js da UI CORE (LOCAL). O bundle gerado tem SHA-256 fd61c198faa52e1a821106dfbccc23ccbfcca95f3b9a63affd9f900ad2935709. A fonte e o overlay do terror coincidem por SHA-256. Nesta rodada, compilação e testes foram executados com o jogo e o servidor parados.

## 19/11 — B4 decidido pelo dev e disparado pelo servidor (Sol High)

O Charge vanilla de Fire Storm, SOUN 000843:AETHERIUS - SOBRENATURAL.esp, toca uma vez no início aceito e uma vez na conclusão confirmada. O SNDR 073FD9:Skyrim.esm permanece sem loop, com trecho medido de 3,351 s e saída espacial SOMMono01800 de 150–1.800 unidades. Cancelamento ou falha encerram o trecho inicial ainda em execução e não geram disparo de conclusão. Referência da decisão em DECISOES.md. O candidato está no commit 65edf8cb6ea2443ae1f84d1cfec1217cf44bd93c do laboratório. O perfil, o ESP, o WAV e o bundle de cliente permanecem os do lote anterior.

Em C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\veil-ritual.js, a emissão do fim exige outcome === 'completed', alcançado após consumo confirmado e crédito positivo de Vínculo. Em veil-ritual-fx.js, a propriedade aetheriusVeilRitualCharge separa o áudio da vida da runa. Para o dono e vizinhos, cada entrada publica { id: "<ritualId>:start" ou "<ritualId>:complete", remainingMs } por até 3.351 ms, com amostra atualizada pelo servidor e expiração independente. A runa sai imediatamente no fim. O listener guarda o identificador e o handle numérico e resolve o ator novamente em cada update que precisa dele. Uma entrada tardia pode começar a ouvir se a janela ainda estiver aberta. Sound.Play inicia do começo, e o cliente corta no tempo restante projetado. A entrega e a atenuação efetiva dependem da réplica 3D e da latência. Heartbeats, montagem da runa após a janela e remount durante o mesmo trecho não disparam sons adicionais para quem já o ouviu.

**Testes:** 13/13 direcionados em teste-ritual-servidor.cjs e teste-veu-mundo.cjs. Passaram 345/345 testes de overlay em cada modo de alimentação. Os testes distinguem conclusão confirmada, cancelamento e falha de consumo, e simulam vizinhos presentes e tardios, saída e remount, expiração e propriedade desatualizada. Cópias source/overlays idênticas e sintaxe Node aprovada. Revisão Neon e validação oficial do motor pendentes. Nenhum servidor ou jogo iniciado.
