# Revisão 3 — segunda correção: autoria e intenções canceladas

Data: 16/11/2026. Autor: Sol High; revisão: Terra High; arbitragem: Opus 5.5. Base do laboratório `C:\Code\Aetherius - Servidor Local`: código `9b4230a0b9e22d7631aae312da9977c53a062f83`, documentação `b6a3cf1`, cliente `79303d57cc0a75246f45ca2ff2c83ba95dceb824`. A confirmação Terra aceitou as correções iniciais conforme decisões do dev, mas abriu as pendências A/B, ambas aceitas pelo coordenador. Este candidato separado trata essas duas pendências; **nova confirmação independente pendente**, sem teste em jogo.

O parecer completo está em `C:\Code\Aetherius - New Systems\Sobrenatural\docs\REVISAO-3_origem-bruxa-forcada_terra.md`. As decisões permanecem: Sedução/Reanimação isentas, sete poderes cobrados falham sem reserva, origem externa posterior não integrada pode ser removida junto e nenhum nativo. A Reserva de Sangue continua pausada, sem alteração de seu núcleo neste lote.

Checkpoint do cliente corrigido: **`757c57114a0ebe8578eac4801a5f274357b9aa2a`**.

## A — liberar um claim não descarta a autoria

Problema: quando o outro provider apenas publicava `wanted`, o helper retornava `ALREADY_ABSENT`; o autor original apagava a sua prova, mesmo sem o outro ter persistido qualquer ownership. Uma reinicialização nesse intervalo podia transformar uma perk do sistema em presença aparentemente externa permanente.

Agora `source/server/gamemode/supernatural/perk-origin.js` retorna **`CLAIM_RELEASED`** se o claim foi liberado, a autoria está atestada e outro claim mantém a perk. O estado significa “minha fonte não deseja, mas ainda sou responsável pela prova do item presente”; não é confirmação de transferência de autoria e não é ausência física.

O cliente já conservava autoria para qualquer status diferente de `REMOVED`/`ALREADY_ABSENT`. A property do Sobrenatural transmite o status novo, e `source/server/gamemode/supernatural-service.js` aceita o ACK sem apagar seu ledger. `source/server/gameplay/core/runtime/src/projection/perks.ts` também aceita o status apenas para `remove`, conserva `owned`, mantém a linha `active=true` para nova tentativa de retirada, e projeta `PARTIAL`/`SENT_UNCONFIRMED`, razão `CLAIM_RELEASED_OWNERSHIP_RETAINED`. Assim, essa retirada ainda pendente não vira entrega concluída que seria ignorada no mesmo revision.

Não existe transferência em duas fases a esperar: **o autor original continua responsável até observar ausência/remoção**, mesmo se o outro provider nunca enviar ensure, perder seu ACK ou não salvar autoria. Ao reiniciar, o ledger original reaparece no snapshot; quando o último claim acaba, a remoção pode ocorrer e somente então a prova é descartada. Se ambos já têm autoria durável, ambos podem conservá-la: primeiro a remover fisicamente relata `REMOVED`; o outro observa ausência e limpa a própria linha. Preexistentes continuam protegidos e não viram autoria nova por mera presença.

Testes novos:

- Duas direções (`gameplay`/`supernatural`), outro desejo sem ensure, ACK de ensure perdido e ACK de liberação perdido, reinício do storage e retirada do último claim.
- API real do Sobrenatural gravando `aetherius-supernatural.json`: `CLAIM_RELEASED` conserva a linha em disco, restart a republica e só `REMOVED` a apaga.
- `runtime/tests/perk-ownership-retention.test.mjs`: executa **PerkDelivery e o código transacional reais**, com executor SQL de teste e ledger gravado em arquivo. Verifica `owned/active` após ACK, reinicia a classe de entrega, comprova nova emissão de remove, ACK perdido e falha de COMMIT. O executor é mock transacional, **não PostgreSQL**; não substitui integração no banco.

## B — nenhuma intenção cancelada fica sem responsável em runtime

Problema: confirmação do diário falhava depois da raça, mas `finish` restaurava a aparência e apagava a sessão, deixando `pending` sem rotina de resolução até o próximo boot. `prepare` recusava toda tentativa posterior da identidade.

`source/server/gamemode/werewolf-service.js` agora conserva evidência em memória de que a raça passou no readback (`raceApplied`). `finish`, cancelamento por identidade obsoleta e dispose entregam a intenção não resolvida a uma **fila independente da sessão de forma**, antes de descartar sua responsabilidade. O tick tenta concluir essa fila enquanto o processo vive:

| Evidência do cancelamento | Decisão durável |
|---|---|
| Raça de fera foi aplicada e passou no readback | Confirmar aquela transformação real, mesmo após restaurar o mortal; o contador consulta essa prova e pode zerar uma vez. |
| A aparência não mudou, no-op comprovado, e o rollback foi conferido | Abortar a intenção; não produzir stamp nem reset. |
| Disco continua recusando a gravação | Conservar trabalho na fila e intenção em `pending`; retentar no tick. |

`werewolf-transform-journal.js::hasPending` recusa uma nova transformação **antes de criar turning, gravar recuperação ou publicar visual**, enquanto houver intenção anterior pendente na identidade. Assim, não se inicia outra sessão presa pelo `prepare`. Com o disco recuperado, a fila confirma/aborta, libera a pendência e a próxima transformação funciona **sem reiniciar o servidor**. A fila grava somente o diário; nunca aplica a aparência antiga em outro personagem. `characterId`/`grantId` continuam delimitando a prova consultada pelo contador.

O retorno `finish: restored` continua informando restauração da aparência; a fila de persistência é consultável apenas no servidor por `pendingIntentCount()`. Falha de disco não deixa overlays turning nem callback de animação da sessão encerrada. O dispose faz mais uma tentativa de resolver a fila; se o processo morrer enquanto o disco ainda falha, a intenção durável remanescente continua sujeita à política conservadora de crash da entrega anterior. **Não é possível persistir um cancelamento enquanto o volume recusa toda escrita**; não se prometeu eliminar essa ambiguidade física.

Cobertura: falha de confirmação após raça e cancelamento por morte, desconexão, remoção da maldição, F7, troca de concessão e troca de personagem; nenhuma sessão/turning órfão, bloqueio anterior a nova sessão enquanto pendente, recuperação de disco e nova transformação no mesmo processo, boot posterior sem novo stamp; no-op com falha de aborto não ganha crédito após recuperação e reinício.

## Artefatos e integração

Todos os caminhos abaixo partem de `C:\Code\Aetherius - Servidor Local`:

- Fontes prontas em `overlays/sobrenatural/files/server/gamemode/`: `supernatural/perk-origin.js`, `supernatural-service.js`, `werewolf-service.js`, `supernatural/werewolf-transform-journal.js` e arquivos anteriores conservados.
- **`overlays/sobrenatural/011-revisao-3ab-ownership.patch`** complementa 010: reconhecimento/armazenamento do status no Core, expectativa atualizada do receptor e teste da autoria do ACK/ledger.
- **`overlays/cliente-local/005-perk-ownership-retention.patch`** complementa 004: helper novo e finalização de espera com `CLAIM_RELEASED`. Não substituir 004 nem aplicar isolado sobre a base oficial. `.gitattributes` conserva os bytes desses patches.
- O bundle cliente foi compilado em `client-source/build/dist/client/Data/Platform/Plugins/skymp5-client.js`, SHA-256 **`24284D9C8C0CCD59443425C86743F46D4FAEE625F09F34B99CB2E5D26C3EDCC5`**. Helper cliente/property byte-idêntico, SHA-256 `F812FA4020FB61FECA78DA0E9201478FC3FF2638956A822FF3A14CD64C62E34E`.
- Build JS/TS e staging do Core concluídos, `failures=0`; `Prepare.ps1 -SkipPluginSync` preparou o runtime. **Servidor parado; bundle NÃO instalado no MO2**, conforme instrução do coordenador; nenhuma árvore MeridianUI, addon, OFICIAL ou modpack alterada. O Acervo será consolidado pelo coordenador.

Os dois lados precisam do status novo. Depois da confirmação Terra, instalar o bundle novo apenas no mod LOCAL conforme roteiro anterior, junto do Core e das properties deste lote. Nenhum teste em jogo foi pedido nem executado. Reverse-check desta entrega usa **011 no source e 005 no client-source**; para verificar versões anteriores, reverter os incrementos novos numa cópia descartável antes de testar 010/004.

## Validação

Na raiz do laboratório:

```powershell
node --test overlays/sobrenatural/teste-revisao-3.cjs overlays/sobrenatural/teste-transformacao-forcada.cjs overlays/sobrenatural/teste-origem.cjs overlays/sobrenatural/teste-poderes.cjs overlays/sobrenatural/teste-formas.cjs overlays/sobrenatural/teste-aparencia.cjs source/server/gamemode/werewolf/tests/werewolf.test.cjs source/server/gamemode/magic/tests/magic.test.cjs
```

**158/158**. Serviço, F7 e revisão F7 passaram nos mesmos comandos do relatório anterior. Em `source/server/gameplay/core`:

```powershell
node --test runtime/tests/perk-client.test.mjs runtime/tests/perk-origin-barrier.test.mjs runtime/tests/perk-ownership-retention.test.mjs runtime/tests/client-delivery.test.mjs runtime/tests/class-skills-delivery.test.mjs
```

**39/39**, total **197/197**. Logs `C:\Code\Aetherius - Servidor Local\evidence\review3ab-*.log`: node/core/service/F7/review-F7/client-build/server-build/prepare. O teste com executor SQL de arquivo não abre conexão com PG; a rodada PG real continua pendente de banco de teste configurado. Parse, reverse-check dos artefatos versionados e igualdade de fontes/property/runtime conferidos. Confirmar a correção do autor com Terra antes de marcar A/B resolvidos no parecer independente.

Roteiro humano adicional, somente depois da revisão: com perfil LOCAL e ambas as versões instaladas, relogar entre liberação do claim e ensure da outra origem, depois terminar o último claim e verificar retirada; em laboratório descartável, injetar falha de confirmação, cancelar a forma, recuperar a escrita e transformar novamente sem restart, conferindo aparência e reset uma única vez. Não simular falhas apagando arquivos de personagens reais.
