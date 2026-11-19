# Teste em jogo — 18/11/2026 (dev, perfil AETHERIUS - LOCAL, conta 1)

Ambiente: servidor local PID 14496 (log `C:\Code\Aetherius - Servidor Local\logs\server-20261118-222056.stdout.log` / `.stderr.log`), cliente `skymp5-client.js` SHA-256 5eec8f8c… (client-source 7fb11d8 + perfil 6e011b3), UI Core (LOCAL) sincronizada do client-source. Screenshots do dev em `C:\Users\pablo\AppData\Local\Temp\claude\C--Code-Aetherius---New-Systems\25a0029f-cff2-4576-bc6c-975bce6a82ef\images\5.png` a `8.png`. Jogo e MO2 fechados ao fim.

## Console do cliente (repetido em loop)
```
[Exception] SP3NativeValueCasts::JsObjectToNativeObject - Invalid __skyrimPlatform__indexInPool: 738, expected to be in [436393, 436427) range
[Exception] failed to get key 'argsJsonDumps': couldn't get array index 0: simdjson (type in:n out:string): INCORRECT_TYPE: The JSON element.
```
(índice 738 fixo; faixa crescendo a cada frame → objeto nativo de skyrim-platform guardado entre frames/`sp.storage` e reutilizado; e um evento com argsJsonDumps não-string.)

## Lobisomem
- L1: Beast Form — equipar o poder e apertar Z: o ícone do efeito aparece no canto superior direito, mas o personagem NÃO se transforma.

## Vampiro
- V1: ao virar vampiro, a HUD da Reserva de Sangue NÃO apareceu (fome/frio/sede continuaram — print 7).
- V2: Bat Form: efeito visual dos morcegos e invisibilidade, mas NÃO desloca o jogador para frente (dash). Verificar se o efeito aparece para outros jogadores.
- V3: Mist Form funcionando; verificar se a invisibilidade vale para todos.
- V4: Devouring Swarm sem som; verificar dano/dreno em NPCs e jogadores próximos e visibilidade para outros.
- V5: Vampiric Vision revela NPCs invisíveis; verificar NPCs aparecendo e jogadores, no servidor oficial.
- V6: Vampire Lord: igual a L1 — efeito aparece à direita (print 6), personagem não se transforma.
- V7: nenhum poder toca som de ativação; usar o som de ativação que cada poder já possui.

## Bruxa
- B1: ao setar Bruxa, o selo (gauge) NÃO apareceu na HUD.
- B2: Ritual: a UI fecha ao iniciar (ok), mas deve haver na HUD um timer com tempo decorrido e restante.
- B3: durante o ritual o jogador deve ficar imóvel; usar comandos de movimento encerra a runa e cancela o ritual com mensagem ao jogador. Animações pela tecla K são permitidas.
- B4: durante o ritual, tocar o som vanilla de conjurar feitiço mestre (ritual spell), audível a todos.
- B5: a runa do chão NÃO some ao concluir ou interromper o ritual.
- B6: olhos violeta normal (sem glow) OK fora da sobrecarga; glow OK na sobrecarga (print 7).
- B7: após um ritual, TODO feitiço conjurado invoca a Master Spell Rune (indesejado).
- B8: vários minutos em Sobrecarga sem nenhum sussurro.
- B9: habilidades (Conjuração etc.) não sobem com o estágio da Bruxa; no estágio final ficaram em 15 (print 8).
