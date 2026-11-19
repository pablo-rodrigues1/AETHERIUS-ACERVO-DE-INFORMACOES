# Investigação T9 — Runa de Conjuração no Ritual do Véu

**Data** 19/11/2026. **Autor** Codex. **Candidato** `47835dc0a8aa395eb21f4d08906b4f5de9ccb03b` no laboratório `C:\Code\Aetherius - Servidor Local`. A base do teste solo foi `5effbd8`, com `client-source` em `295f02eed3addda81900ba3589784b11f2bf6fc7`.

## Evidência e causa

- Em `C:\Code\Aetherius - Servidor Local\logs\server-20261119-003042.stdout.log:254`, o servidor recebeu `ritual.start` às 00:35:03.227. A linha 255 registra `ritual.cancel` por movimento às 00:35:10.683, e a linha 261 registra outro pedido de início às 00:35:31.793. O jogador viu o HUD e o timer, mas relatou a ausência da runa. O stdout comprova o caminho de requisição e o timer confirma a execução na apresentação. O log não mede o desenho da runa. [Registro do teste](../TESTE-JOGO_19-11.md)
- A leitura houseCARL no perfil `AETHERIUS - LOCAL`, epoch `e2-22a0c680484df11f`, encontrou o SPEL `00083A:AETHERIUS - SOBRENATURAL.esp` (`SN_Veu_RitualRune`) como Ability `ConstantEffect`, `Self`. Ele referencia o MGEF `00000F:MasterSpellsRunes.esp`, com `HitEffectArt = 00000E:MasterSpellsRunes.esp`. O ARTO aponta a `Magic\Runes\RuneConjuration02.nif`. A cópia loose vencedora de **Master Spells Runes** contém `BSBehaviorGraphExtraData`, seis `NiControllerSequence` e as strings `Magic\CastingRitualBody.hkx`, `AttachT`, `mIntro`, `mCharge`, `mReady`, `mReadyTrans`, `mCast` e `mIdle`. A malha pertence ao grafo do ritual de conjuração. [Coleta anterior de records e referências](DADOS-HOUSECARL_19-11_ritual.md)
- Antes do lote B5/B7, o código adicionava `00083A` e enviava `MLh_SpellAimedConcentrationStart`. A runa apareceu no teste de 18/11, mas a saída guardava `Actor` e `Spell` entre frames. A limpeza chamava `removeSpell` e `MLh_Equipped_Event` com wrappers vencidos. Isso explica a permanência da Ability e é compatível com a runa reaparecer ao conjurar depois do Ritual. No arquivo `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\supernatural\veil-ritual-fx.js`, consulte `42315d1^:15–16,41–45`. [Teste de 18/11, B5/B7](../TESTE-JOGO_18-11.md)
- `42315d1` retirou os dois eventos e deixou `addSpell` como único acionamento visual (`veil-ritual-fx.js:82–87` naquele commit). A hipótese de que a Ability sozinha exibia a arte foi contrariada pelo teste de 19/11 e pela malha vinculada ao grafo. Em `5effbd8`, o caminho continuava só com `addSpell` (`veil-ritual-fx.js:86–90`). `65edf8c` separou o áudio em outra propriedade e preservou esse caminho. A regressão de código veio de `42315d1`. [Revisão Neon de B5/B7 e B4](../REVISAO-BRUXA-HUD-RITUAL_neon.md)

## Correção candidata

`veil-ritual-fx.js:70–98` adiciona somente a Ability visual do Ritual e envia `MLh_SpellAimedConcentrationStart` uma vez por montagem, tanto no dono quanto na réplica. A cada heartbeat, a amostra renova a vigência sem reenviar o evento. O servidor continua publicando `{ id, sample }` pelo mesmo caminho e o Charge segue em `aetheriusVeilRitualCharge`, com as janelas de início e conclusão de B4.

Em `veil-ritual-fx.js:17–37`, a saída resolve o ator e o feitiço pelo FormID daquele frame. Primeiro chama `removeSpell`, depois `dispelSpell`, e envia `MLh_Equipped_Event` no `finally` se este ciclo armou a pose. Isso retira a origem do efeito antes de dispensá-lo e tenta encerrar o grafo mesmo se uma chamada de limpeza lançar exceção. O estado guarda valores primitivos. A janela de 2,5 s e as tentativas com throttling permanecem (`:75–92`). As versões antigas do estado são desarmadas antes de montar a nova (`:42–51`), com reset da pose daqueles estados que vinham da lógica anterior a B5/B7.

Paridade do JS do overlay com `C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\veil-ritual-fx.js` confirmada por SHA-256 `714381699727845F3CC36A4C6517C02C5531B7769D467E05F5FF45B0B6E78445`. O cliente recebe o JS pela propriedade SkyMP e o executa no Skyrim Platform. O checkout `client-source` permanece em `295f02e` para T9.

## Testes e aceitação em jogo

| Verificação | Resultado |
|---|---|
| Três testes da runa em `overlays/sobrenatural/teste-veu-mundo.cjs:162`, `:203` e `:221`, na revisão `47835dc` | **3/3** com o candidato. **0/3** ao carregar apenas `veil-ritual-fx.js` de `5effbd8` no mesmo processo de teste. O dublê exige a Ability e a pose para mostrar a runa, simula reativação do efeito se ele for dispensado antes de remover a Ability e rejeita wrappers de frames anteriores. |
| Testes direcionados de replicação, Charge e serviço do Ritual | **15/15** ao considerar os nove de `teste-veu-mundo.cjs` e os seis de `teste-ritual-servidor.cjs` na rodada integrada. |
| Bateria Node 22.14.0, `AETHERIUS_VAMPIRE_FEED_ENABLED=false` e `=true` | **503/503 por modo** no workspace integrado. Inclui `overlays/sobrenatural/teste-*.cjs` (exceto os três scripts abaixo), `werewolf.test.cjs`, `magic.test.cjs`, `survival-service.test.js`, `skysocial-service.test.js` e `survival-effects.test.js`. A primeira rodada teve uma falha na asserção da aura violeta durante a alteração concorrente de T8; a rodada integrada com o teste correspondente passou. |
| `node --check` no JS, `teste-servico.cjs` com caminho absoluto do gamemode, `teste-painel-f7.cjs`, `teste-revisao-f7.cjs` | Aprovados. |
| `npx --no-install tsc --noEmit` e webpack 5.94.0 em `C:\Code\Aetherius - Servidor Local\client-source\client` | Aprovados. Build temporário sem instalação, SHA-256 do bundle `FD61C198FAA52E1A821106DFBCCC23CCBFCCA95F3B9A63AFFD9F900AD2935709`. |

O cenário de B7 no teste verifica o estado simulado após conclusão, cancelamento e perda da réplica. A animação real e a interação com mods permanecem para o teste em jogo. No LOCAL, iniciar o Ritual, verificar a runa, usar um emote K e cancelar por movimento. Em outra tentativa, concluir. Em cada saída, conjurar um feitiço comum e verificar que a runa do Ritual desapareceu e não reaparece. No oficial, repetir com observador presente e tardio, saída e reentrada no raio e desconexão. Verificar que o Charge toca somente nas duas janelas previstas. O runtime do teste solo permaneceu com o script anterior e o servidor não foi reiniciado.

A revisão independente por Gemini 4 Neon do candidato T9 segue pendente com o coordenador. A revisão anterior de `42315d1` e `65edf8c` pertence aos lotes originais.
