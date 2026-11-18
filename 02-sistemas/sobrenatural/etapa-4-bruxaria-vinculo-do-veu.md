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

