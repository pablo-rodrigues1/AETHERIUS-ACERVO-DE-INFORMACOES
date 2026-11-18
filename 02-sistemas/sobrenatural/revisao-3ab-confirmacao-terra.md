# Revisão R1 — confirmação independente das pendências 3A/3B

Data: 17/11/2026. Revisora: Codex, despachada como Terra High; autor do candidato: Sol High. Laboratório: `C:\Code\Aetherius - Servidor Local`, commit raiz `88cbbd0a24f59a22cb5c80cf7167b4d6ee678a00`; `client-source` em `757c57114a0ebe8578eac4801a5f274357b9aa2a`. Parecer estático e testes automatizados; não executei servidor nem jogo. Esta revisão não altera o parecer anterior nem revisa toda a Reserva de Sangue.

## Veredito: REPROVADO

A correção A está confirmada dentro do protocolo dos dois provedores. A fila B resolve os cancelamentos cobertos, mas uma tentativa anterior de transformação comprovada pode ser abortada por uma leitura ou escrita posterior falha, eliminando a prova durável. O achado abaixo deve ser tratado e novamente revisado antes de declarar B resolvida.

## Achado B1 — média: nova tentativa pode apagar intenção de transformação já observada

Em `C:\Code\Aetherius - Servidor Local\source\server\gamemode\werewolf-service.js:190`, `becomeBeast` reutiliza `session.transformIntent`. Após `setAppearance` bem-sucedida, a linha 202 marca `raceApplied=true`; se `confirm` falhar na linha 203, a sessão continua em `turning` e o tick da linha 355 chama `becomeBeast` novamente. Se nessa chamada posterior `setAppearance` falhar e a leitura da aparência não retornar raça de fera, as linhas 195–198 chamam `abort` sem consultar a evidência anterior `raceApplied`. Com o disco recuperado, `werewolf-transform-journal.js:50` remove o pending sem confirmar a transformação já observada; `last` nas linhas 54–56 não oferece o stamp e, após cancelamento ou crash, o contador pode deixar de reconhecer a transformação real. Além disso, `get` em `werewolf-service.js:72` converte exceção de leitura em `null`, que satisfaz a condição da linha 196: leitura indisponível não comprova no-op.

Sequência reproduzível por injeção a acrescentar aos testes: aplicar raça com readback e fazer falhar `confirm`; antes do tick seguinte recuperar o disco, falhar `setAppearance` e fazer `mp.get(appearance)` lançar ou retornar aparência não fera; cancelar e verificar que o stamp original segue confirmável sem reinício e sobrevive ao boot. Testar também leitura indisponível na primeira tentativa: a decisão deve conservar a intenção ambígua até haver evidência suficiente, segundo a política conservadora já documentada. `overlays/sobrenatural/teste-revisao-3.cjs:141` cobre cancelamentos após falha de confirmação; `:153` cobre no-op comprovado, mas não esta sequência de tentativas ou leitura indisponível. Este é um achado por análise de fluxo, não um teste de reprodução executado.

## Confirmação A

`source/server/gamemode/supernatural/perk-origin.js:49` distingue `CLAIM_RELEASED` quando existe outra intenção e o autor está atestado. `source/server/gamemode/supernatural-service.js:229` aceita o status e só apaga o ledger nos resultados de ausência ou remoção nas linhas 235–240; `client-source/client/src/services/services/gameplayGrantService.ts:211` também só limpa autoria nesses dois resultados. No Core, `source/server/gameplay/core/runtime/src/projection/perks.ts:176` valida o status para remoção, `:195–201` mantém a linha ativa e a autoria, e `:208–215` informa resultado parcial. As duas direções e ACK perdido são exercitados; o executor SQL é simulado, não PostgreSQL real. A proteção não cobre fontes externas que não participem do protocolo, limitação previamente aceita pelo dev.

## Confirmação parcial B e demais riscos

`werewolf-service.js:235–249` transfere a intenção cancelada para mapa independente da sessão e tenta confirmação ou aborto; `:337–339` retenta em runtime, e `:156–158` impede nova sessão enquanto o diário aponta pending. O teste cobre morte, saída, remoção de maldição, F7 e trocas de concessão/personagem, além de recuperação do disco no mesmo processo. O mapa é volátil: falha de escrita seguida de queda ainda depende da política conservadora de boot do diário; não há garantia de gravar cancelamento em volume indisponível.

A retentativa de cada item a cada 200 ms (`werewolf-service.js:337–339,367`) faz escrita síncrona, fsync e rename (`werewolf-transform-journal.js:18–23`); falha de volume persistente pode afetar latência proporcionalmente à fila. Medição sob falha e carga real permanece pendente. Não identifiquei neste recorte envio das keywords ao cliente: a publicação de perks contém scope, nonce, perks e owned em `supernatural-service.js:99–100`, e a keyword é consultada no servidor (`:272`). Não há evidência de crash ou conflito de mods adicionais neste recorte, mas mocks não comprovam comportamento do motor, sincronização visual de dois clientes ou load order real.

## Validação executada

Na raiz do laboratório:

```powershell
node --test overlays/sobrenatural/teste-revisao-3.cjs overlays/sobrenatural/teste-transformacao-forcada.cjs overlays/sobrenatural/teste-origem.cjs overlays/sobrenatural/teste-poderes.cjs overlays/sobrenatural/teste-formas.cjs overlays/sobrenatural/teste-aparencia.cjs source/server/gamemode/werewolf/tests/werewolf.test.cjs source/server/gamemode/magic/tests/magic.test.cjs
```

Saída final: `tests 158; pass 158; fail 0; cancelled 0; skipped 0; todo 0`, exit 0.

Em `C:\Code\Aetherius - Servidor Local\source\server\gameplay\core`:

```powershell
node --test runtime/tests/perk-client.test.mjs runtime/tests/perk-origin-barrier.test.mjs runtime/tests/perk-ownership-retention.test.mjs runtime/tests/client-delivery.test.mjs runtime/tests/class-skills-delivery.test.mjs
```

Saída final: `tests 39; pass 39; fail 0; cancelled 0; skipped 0; todo 0`, exit 0. Total: **197/197**. Node emitiu aviso de API experimental MockTimers.

`git apply --reverse --check` passou para patch Core 011 em `source` e cliente 005 em `client-source`; os quatro arquivos alterados do gamemode coincidem por SHA-256 entre source e overlay. Sem PostgreSQL real, build, instalação no MO2, servidor, jogo ou teste humano. A instalação pareada do cliente 757c571 e do servidor candidato permanece necessária após correção e revisão.

## Tratamento

B1 é apontamento desta revisora, ainda não arbitrado nem corrigido. Nenhuma versão corrigida foi analisada; o coordenador decide o tratamento e solicita nova confirmação independente.

## Reconfirmacao B1 (e86c0cb)

Data: 17/11/2026. Revisora independente: Codex, despachada como Terra High; autor: Sol High. Escopo: `C:\Code\Aetherius - Servidor Local`, diferença `88cbbd0..e86c0cb`, sem revisão do commit solar `4c631e1`. **Veredito B1: APROVADO no recorte estático e nos testes automatizados**, com as limitações abaixo; este parecer não substitui teste em jogo nem reabre o parecer A.

Em `source/server/gamemode/werewolf-service.js:151–165,202–220`, a leitura válida de raça fera fixa `raceApplied`; leitura inválida/indisponível ou exceção fixa `raceUncertain`. Ambos são conservados nas tentativas seguintes. O aborto imediato exige leitura válida de raça não fera, sem `raceApplied` nem `raceUncertain`; assim, leitura posterior mortal não apaga prova nem ambiguidade anteriores. A fila em `:251–265` confirma conservadoramente a mesma intenção quando há qualquer dessas evidências, e só aborta sem elas. Confirmar uma intenção ambígua pode dar um reset conservador mesmo sem troca visual comprovada, conforme política registrada pelo autor; não é prova de que o motor aplicou a raça.

O diário `source/server/gamemode/supernatural/werewolf-transform-journal.js:30–56` confirma o stamp original e recupera pending no boot. Os testes `overlays/sobrenatural/teste-revisao-3.cjs:160–183` injetam confirmação falha após raça observada, depois leitura lançando/null/mortal, cancelamento, confirmação do mesmo stamp em runtime e leitura após restart; cobrem ainda leitura indisponível na primeira tentativa seguida de leitura mortal e recuperação conservadora após interrupção. O teste anterior em `:153` cobre no-op comprovado sem crédito. São mocks de `mp` e filesystem, sem comprovação do comportamento do motor real ou de crash físico; o risco de fsync síncrono por tick e as demais limitações do parecer R1 permanecem.

Validação executada com os comandos integrais da seção Validação executada acima, no laboratório e Core indicados: gamemode `tests 164; pass 164; fail 0; cancelled 0; skipped 0; todo 0`, exit 0; Core `tests 39; pass 39; fail 0; cancelled 0; skipped 0; todo 0`, exit 0; total **203/203**. O serviço do source e o overlay têm SHA-256 `C785CCEC45335BA797AEE6392D0101DBBAF054A7861417117DBD7A1AFD2DF493`; o arquivo do serviço e o teste do overlay coincidem com `e86c0cb`, e o commit solar altera outros arquivos. Source e Core são árvores de trabalho com alterações locais preexistentes: os números são da configuração de laboratório atual, não de uma reconstrução isolada de todo o commit. Nenhum servidor, jogo, PostgreSQL real, build ou instalação no MO2 foi executado nesta reconfirmação.
