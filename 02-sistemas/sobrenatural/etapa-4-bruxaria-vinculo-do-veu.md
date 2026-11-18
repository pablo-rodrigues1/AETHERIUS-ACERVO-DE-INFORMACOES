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
- ~48–56 px, baseado em `witch gauge.png`; **barra central em degradê** como na referência, enchendo de baixo para cima; violeta; segmentos apagados em cinza/branco/dourado; rachaduras e perda de brilho progressivas; pulsação sutil no crítico; **sem número nem tooltip**; só para Bruxa.
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
