# Etapa 3 — investigação inicial das mecânicas de vampirismo

> **Residual R2 #2 — 17/11/2026, Sol High:** callback atrasado limitado a `min(now, offlineAt)` em `a35b0e8`; reaparecimento observado fecha o segmento anterior e reabre sem o marcador antigo. **314/314 testes Node + serviço/F7/revisão-F7 passaram**. Terra já reconfirmou #3/#4/retry; esta correção residual ainda aguarda confirmação independente. D7 permanece: crédito antigo da poção ativo sem flag e não atômico; alimentação default false. Criaturas e demais roteiros em jogo continuam obrigatórios, sem instalação, runtime ou jogo nesta rodada.


Data: 15/11/2026. Autor: Terra High. Estado: investigação preliminar; sem implementação, aprovação de números, revisão cruzada ou teste em jogo. O servidor local analisado é `C:\Code\Aetherius - Servidor Local\source`, HEAD `310cb598fd382cf4d6598cb34eada5db1a3fa9f8` (arquivos de trabalho podem mudar em paralelo). Perfil MO2 `AETHERIUS - LOCAL`, houseCARL 2.0.0+5e3c744862bc17c85ebe850830603a9a3e6f0779, epoch `e2-e8e31d9a3236c6a8`; 435 plugins ativos. Os caminhos abaixo são relativos ao servidor local indicado, salvo indicação contrária. `ETAPA-2_vampirismo.md` prevalece sobre o documento de referência quanto a XP, disciplinas, Lorde e inatos.

## Mapa de ganchos e limites

| Assunto | Evidência e viabilidade |
|---|---|
| Cura por magia | `server/gamemode/magic/healing.js`, métodos `spell`, `apply` e `restore`: o sistema JS conhece spell, caster, target, MGEF/archetype e AV; `apply` pode filtrar a restauração de Health de fonte convencional por alvo, preservando os demais efeitos. Isso cobre apenas o caminho desse módulo; não prova cobertura de todos os caminhos de magia/cliente/nativo. O Dreno exige identificar e liquidar sua própria origem sem duplicação. |
| Comida e poções | `server/gamemode/survival-service.js:807-837`: `mp.onEatItem(actorId, baseId)` recebe item após validação de posse, e retornar `false` bloqueia efeitos do item inteiro. O survival chama seu processamento assíncrono antes de retornar, portanto encadear um veto sem coordenar o ledger pode deixar fome/inventário inconsistentes. `server/cpp/server_guest_lib/gamemode_events/EatItemEvent.cpp:33-54` aplica TODOS os MGEFs ALCH depois de traduzir IDs; `MpActor.cpp:1970-2007` aplica Health/Stamina/Magicka sem expor ali a origem ao JS. Para bloquear **apenas** cura preservando buffs/necessidades num item misto, é necessário um hook nativo por efeito com item + MGEF + alvo, ou uma mudança nativa equivalente. Veto integral é possível em JS, mas não satisfaz a granularidade pedida. Ingredientes não têm efeitos habilitados nesse caminho nativo. Não foi demonstrado que efeitos locais do cliente fiquem bloqueados ou reconciliados. |
| Regeneração | Não alterar HealRate/HealRateMult nem reverter genericamente qualquer aumento de HP. Precisamos de teste em runtime para comprovar preservação da regeneração natural e exclusão de duplicação com a cura nativa. |
| Alimentação e Sedução | Ainda não foi demonstrado um evento autenticado de alimentação nem estado de sedução/hostilidade do NPC confiável no servidor. Posição e cell são consultáveis (`server/gamemode/core/range-utils.js`); isso não prova elegibilidade, hostilidade ou sincronização da animação. É preciso investigar canal de solicitação, autenticação, repetição, estado do alvo e dono de HP/AI antes de prometer implementação JS; se tais sinais não existirem, exigir ponte nativa ou desativar feeding. Nunca converter alvo. |
| Dano periódico | `server/gamemode/supernatural/powers.js:145-155` já mostra tick validado e `DamageActorValue`, mas não prova sozinho que o resultado é autoritativo contra cliente ou integrável à morte. Reusar owner e validar morte/relog, troca de cell e aplicação única. |
| Hora e clima | `server/ts/systems/environmentSystem.ts:73-93` publica `globalThis[Symbol.for('aetherius.environment.authority.v1')]`, incluindo `isReady`, `clockSnapshot`, `environmentSample(actorId)` com `weatherDesc` e `quality`. `server/gamemode/survival-service.js:446-515` demonstra uso e classificação do WTHR por flags. Não usar clima inventado se autoridade/região indisponível; confirmar formato e transições. |
| Interior/exterior | `server/gamemode/survival-environment.js:8-10,166-212` resolve CELL/WRLD e exceções de worldspace interior por records; `read` também usa o relógio próprio do survival. Verificar consistência com a hora da facade: não misturar duas fontes temporais sem prova. Água: `survival-service.js:534-588` registra evento de nado reportado pelo cliente; ainda não é prova de imersão autoritativa. |
| Resistências | `server/gamemode/supernatural/vampire-grants.js:10-16` concede `VampireVampirism`, `VampirePoisonResist`, `COTV_ResistFrost` e `COTV_WeaknessToFire`, além de Awareness, vigor e sol. HouseCARL confirma descrições 25% gelo e 25% fraqueza fogo nos vencedores `33EAC2` e `33EAD4` do COTV. A especificação exige 100% doença/veneno, 25% físico e queda e respiração aquática; não foi comprovado que esses grants cobrem cada propriedade nem que o cálculo real do servidor respeita os modifiers. Conferir records e fórmula de dano antes de acrescentar algo e evitar duplicação racial. |

## COTV: evidência direta e riscos

HouseCARL leu vencedores do `Curse of the Vampire.esp`. `33EAC6` é ability concedida pelo sistema atual; sua descrição informa 5 HP/s, mas seus dois efeitos são `083F26` e um placeholder, não diretamente o MGEF de dano. A ability base `126B1D` aponta para `126B1C`, magnitude 5, com condições `IsSwimming == 0`, `IsPleasant == 1`, hora 07–19 inclusive e exterior; há outras condições. O MGEF `126B1C` possui `COTV_VampireSunlightScript`. É preciso rastrear como o efeito secundário instala a ability base e confirmar execução/remoção no runtime antes de ligar o tick de servidor. A simples remoção do grant não comprova que nenhum outro caminho racial ou script conceda o efeito. Não alterar o modpack.

`7A93D7` (`COTV_VampireFeedDamageEffect`) é efeito Script ligado a `COTV_VampireBiteScript`, não um dano numérico já comprovado. A checagem houseCARL indica `VampireVictim` ligado a None, que pode ser preenchido em runtime; não significa necessariamente erro. A lógica dos scripts não foi ainda comprovada: é necessário resolver os arquivos vencedores e ler fonte ou decompilar em destino permitido, sem escrever no modpack. Não presumir valores de mordida ou cura a partir dos nomes. O MGEF `2834FF` aplica perk `283500` para cura mágica; `12BC22` aplica perk `283501` para comida. Sua eficácia no owner nativo/JS ainda não foi demonstrada. O mecanismo de verificação de scripts do servidor e a correção local constam de `Sobrenatural/docs/investigacoes/INVESTIGACAO_15-11_scripts-nao-assinados.md`.

## Propostas originais submetidas ao dev

Os números de alimentação abaixo são hipóteses de teste, não foram encontrados nos records COTV lidos. Não ativar nem gravar defaults até resposta do dev.

1. **Sol:** 5 HP/s, tick de 1 s, janela 07:00 inclusive a 19:00 inclusive como a condição base do COTV; confirmar fronteira exata e se o tick usa tempo real. Sem débito acumulado offline; descartar elapsed excessivo e parar imediatamente em morte/logout/mudança de contexto. Sem sombra local na primeira versão. Estas decisões de borda são propostas, não inferência de runtime.
2. **Clima:** proteger chuva/neve/nevasca; céu limpo e nublado não protegem. O COTV usa `IsPleasant`, cuja equivalência com estas classes ainda não foi demonstrada; proposta Aetherius, não resultado do record. Interior protege; WTHR/local desconhecido suspende dano e registra diagnóstico.
3. **Imersão:** proteger enquanto nadando, coerente com condição `IsSwimming == 0` do COTV; aguardar fonte confiável server-side, ou não habilitar esta exceção até haver prova.
4. **Feeding:** propor 25 HP de dano à vítima e 25 HP de cura ao vampiro por ação, cooldown 60 s; sem PvP e sem morte da vítima no primeiro teste; NPC elegível, vivo, próximo e não hostil sob Sedução validada. Os valores e até a viabilidade da validação são pendentes; não usar enquanto sinais de hostilidade/sedução não forem comprovados.
5. **Sangue/fome:** propor reservatório 0–100, começa em 100 e mordida restaura 25; sem drenagem temporal, efeitos ou persistência enquanto não houver política final. É hipótese de teste, sem base numérica COTV confirmada; alternativamente adiar o reservatório inteiro.
6. **Blood Potion:** propor recuperar 25 HP e 25 de sangue por unidade; identificar record e prova de posse e evitar dupla liquidação. É hipótese sem magnitude COTV verificada, não confundir com Potent Vampire Blood.
7. **Poções não curativas:** preservar efeitos de Magicka, Stamina, buff/resistência/utilidade; bloquear somente restauração convencional de Health em itens mistos; confirmar com dev. O hook atual só permite veto inteiro, e essa proposta exige granularidade nativa ou caminho equivalente verificado.
8. **Resistências solares e outros modificadores:** propor nenhum multiplicador adicional no teste, sem imunidades por rank; confirmar interação com mitigação e morte. Resistências passivas continuam pelos records existentes sujeitos à auditoria.

## Pendências técnicas e revisão

- Investigar os scripts solares e feeding, os winners de passivas, owner efetivo de dano/cura e o comportamento de cliente antes de concluir cobertura.
- Solicitar ao coordenador definição do ponto de integração com `supernatural-service.js`, que está sendo reescrito por outro worker; nenhum arquivo compartilhado de formas ou reconciliador foi alterado.
- O repositório Git raiz aqui solicitado não existe em `C:\Code\Aetherius - New Systems`; o Git localizado fica em `C:\Code\Aetherius - Servidor Local\source`. Resolver com coordenador como versionar este documento sem interferir em alterações concorrentes.
- Acesso/publicação ao Acervo ainda não foi realizado por este worker; informar coordenador para assegurar a obrigação documental e revisão por outro modelo.

## Decisões do dev — valores provisórios aprovados em 15/11

- Sol: 5 HP/s, tick 1 s, 07–19 inclusive; chuva, neve, nevasca, interior e nado protegem, nublado não; sem catch-up offline nem multiplicadores.
- Alimentação NPC: 25 dano, 25 cura, recarga 60 s, não matar, NPC não hostil sob Sedução validada. Sangue 0–100 inicial 100, NPC +25, sem drenagem ou efeitos neste teste.
- Poção de Sangue: 100 cura e +50 sangue. Mordida em jogador: 200 cura e +100 sangue; usar provisoriamente dano 25, sem matar, alvo vivo e próximo; **dano e consentimento/estado do jogador continuam pendentes de decisão do dev**. Não transformar vítima.
- Cura/comida: vetar somente itens de cura pura via `onEatItem`; itens mistos permanecem liberados e devem ser documentados. Poções não curativas preservam buffs. Sem implementação nativa nesta alternativa.
- Instalação via patch próprio após serviço, API pública `stateOf`/`keywordOf`; não editar reconciliador. Coordenador publica no Acervo; espelhar documento no overlay e commitar na raiz `C:\Code\Aetherius - Servidor Local` (correção: existe Git nessa raiz, além do checkout aninhado `source`).

## Entrega parcial de cura e bloqueios atuais

- `overlays/sobrenatural/files/server/gamemode/supernatural/vampire-healing.js` filtra o AV Health de efeitos processados por `magic/healing.js` em vampiros, mantendo Magicka/Stamina e permitindo o FormDesc explícito do Dreno. Não prova cobertura de outros owners ou efeitos de cliente. Veta ALCH cujos MGEFs são exclusivamente curativos Health reconhecidos; itens mistos, desconhecidos e outras origens permanecem liberados. A identificação depende dos records vencedores presentes no servidor.
- `onEatItem` chama o handler anterior antes do veto: o C++ `MpActor::OnEquip` remove o item mesmo se a aplicação dos efeitos for vetada, e survival pode precisar registrar o consumo. Isso NÃO devolve o item ao jogador. Necessita revisão independente e teste real de consumo, ledger e correção no cliente. O filtro não altera HealRate e não toca regeneração.
- `007-vampiro-mecanicas.patch` instala depois do Sobrenatural. Testes `node --test overlays/sobrenatural/teste-vampiro-cura.cjs`: 2 aprovados, emulação Node, sem jogo. Não houve reinício nem teste humano.
- `COTV_VampireSunlightScript` (fonte previamente extraída em `Sobrenatural/research/cotv-extracted/scripts/source/cotv_vampiresunlightscript.psc`) apenas mostra mensagem no início; o dano do record base é Health PeakValueModifier. O vencedor dos PEX solar e mordida é `Curse of the Vampire.bsa`, segundo houseCARL; a correspondência das fontes extraídas à versão vencedora ainda requer confirmação por hash. O código extraído `cotv_vampirebitescript.psc` aguarda 3 s e aplica 50 Health à vítima, de modo que uma implementação nova exige primeiro neutralizar/reconciliar o caminho COTV: não permitir duplo dano. Nenhuma nova mecânica solar ou feeding foi ativada.
- Ainda não foi identificado record inequívoco de Poção de Sangue, nem canal seguro comprovado para Sedução/hostilidade ou imersão; o evento de nado encontrado vem do cliente. Sem essas provas não há alimentação NPC segura, exceção solar por nado confiável ou aplicação única de poção. O tick solar também não pode ser habilitado até garantir supressão do efeito COTV sem alterar grants de outro worker.
- Cura, feeding, sol e passivas necessitam revisão por Sol High. Pendências são riscos reais, não funcionalidades concluídas; números aprovados não constituem prova de viabilidade técnica.

## 16/11 — Fase A: auditoria da Reserva de Sangue

Fonte: `C:\Code\Aetherius - New Systems\referencia\Reserva_de_Sangue_Aetherius.md` integral, com decisões posteriores do dev transmitidas pelo coordenador prevalecendo. Cancela alimentação NPC e modelo anterior: só jogadores pelo menu X, sangue +100/vida +200 ao concluir; vítima perde 20% da vida máxima sem morrer, alcance 2 m, recarga 60 min por vítima e PvP existente. Poção +50 sangue/+100 vida. Drenagem online real 13h30 de 100 a 0, morte mantém sangue, 5 por uso bem-sucedido. Pendentes: histerese (aplicar literalmente saída >30 por enquanto), raio da visão (25 m proposto), categorias/invisibilidade, e restauração de necessidades ao remover vampirismo.

### Contratos observados e viabilidade

- Estado: `source/server/gamemode/supernatural-service.js:50,68,270-272`: `stateOf(actorId)` e `keywordOf(actorId)` são consultas privadas do servidor, identificação persistente pelo `commands.getActiveCharacterData`. Qualquer armazenamento novo deve usar characterId e geração da maldição (`grantId`), evitar reset ao relogar e não tratar falha de leitura como personagem novo. API proposta `mp.__aetheriusBloodReserve` com get/add/trySpend; atomicidade de gasto e sucesso do poder requer contrato de reserva/commit/cancel ou rollback seguro com o dispatcher de Sol; `trySpend` sozinho não garante não cobrar uma ativação que falhou depois. Adiar integrar até acertar contrato e persistência/recuperação.
- Sobrevivência: `survival-service.js:263-319,341-416,431-443,733-798,824-889`: estado `_online` por characterId é carregado do PostgreSQL, `_settle` avança, `_syncEffects` aplica penalidades, `_pushHud` publica, `_restoreMany` e `onEatItem` também alteram necessidades. Apenas ocultar HUD não para progressão, zero-drain, reenvio tardio de efeitos ou consumo; integrar guardas em todos esses caminhos e remover somente penalidades do survival, respeitando ownership. Resolver transição de setagem enquanto online e guardar/congelar valores enquanto se aguarda decisão de reversão. Cliente `survival-hud.js:111-127` usa propriedade owner e `window.setSurvivalHud`; UI de sangue pode usar mesma superfície CEF, mas precisa limpar HUD antiga e evitar conflitos de renderização/reconexão. Não foi comprovado um switch público individual existente: requer patch do survival, não alteração global.
- Menu X: `skysocial/src/client.js:74-88`, `ui.js:219-221`, `system.js:24-32,204-228` já fornecem alvo, menu, token e ação; exige patch para nova opção/ação autenticada. `system.js:15-25` valida online, SkyDeath ALIVE, mesma célula e distância; 180 unidades padrão não demonstram equivalência a 2 metros. `canDamage` em `system.js:168` só impede grupo, não substitui todas as regras PvP; também auditar `core/action-policy.js:133-159`, hooks SkyDeath e zonas seguras antes da liquidação. Cooldown por identidade persistente da vítima e sessão exclusiva/nonce, revalidação aos 10 s, limpeza e transação idempotente são necessários; integração não comprovada ainda.
- Nome: `skysocial/src/identity.js:35-48` calcula nome para observador, com prioridade atual para fera, conhecido e máscara, sem gravar nome. É ponto de override Feral; decidir prioridade com Fera/Lorde, e forçar republicação a observadores inclusive quando entram em relevância. `skysocial/src/index.js:18-19` publica `skySocialActor` a vizinhos; o valor exato de sangue deve permanecer privado.
- Efeitos: `skydeath/src/client.js:17-39` já aplica/remove EffectShader em owner/vizinhos e trata recarga 3D/célula; `supernatural/powers.js:167-168` publica visual em propriedade replicada. Isso mostra um caminho JS para protótipo de aura, não comprova shader vermelho apropriado nem persistência visual em observadores. `skysocial/src/client.js:44` e `survival-hud.js:111-127` mostram projeção e CEF para tela preta/áudio; a limpeza em logout, morte, troca de cell e conflitos de foco ainda exige teste. Visão através de obstáculos em players/NPCs e som em loop requerem auditoria de assets/APIs e testes de visibilidade: não declarar equivalente a Aura Whisper.
- Animação: `source/skyrim-platform/src/platform_se/codegen/convert-files/skyrimPlatform.ts:2104` expõe `startVampireFeed`, e scripts COTV extraídos mostram `StartVampireFeed`/`PlayIdle`. Nenhuma evidência ainda prova sincronização de dois atores jogadores, orientação e reprodução por terceiro; pode exigir controlador/ponte nativa. Primeiro experimentar eventos/replicação e validar em multiplayer, não usar a função vanilla para liquidar dano ou sangue.

### Poção e caminho legado

HouseCARL no perfil `AETHERIUS - LOCAL`: `018EF3:Dawnguard.esm` é `DLC1BloodPotion`, ALCH vencedor `unofficial skyrim special edition patch.esp`; contém `018EF4:Dawnguard.esm` (script de ingestão), `013812:Dawnguard.esm` (MGEF vencedor COTV, ValueModifier Health, magnitude **100**) e `000831:ccQDRSSE001-SurvivalMode.esl` (necessidade survival). Já é um item misto e o filtro da entrega parcial não o veta. `EatItemEvent.cpp` aplica os efeitos no servidor; não adicionar mais 100 sem determinar resultado e reconciliação cliente. Alternativa proposta: identificar esse FormDesc, vetar a aplicação integral nativa pelo hook e liquidar +100 vida/+50 sangue exatamente uma vez no owner autoritativo, preservando baixa de uma unidade e ledger; antes comprovar sem duplicação local, falhas de persistência, idempotência, sem transformar não vampiros e sem afetar outros consumidores. O veto integral suprime também os demais efeitos desse item; validar aceitação e eventual projeção visual. Outra alternativa é deixar o efeito de 100 existente e creditar só sangue, se testes comprovarem a cura única. Nenhuma das alternativas foi implementada.

HouseCARL: `7A93D7:Curse of the Vampire.esp` é carregado pelo spell `7A93D8`, referenciado pela perk vanilla `0CF02C:Skyrim.esm` (`VampireFeed`, vencedora COTV). As fontes extraídas mostram `StartVampireFeed` e `PlayerVampireQuest.VampireFeed` com `RestoreAV(Health, 200)`; mordida extraída causa 50 após 3 s. Isso comprova a cadeia de records e o risco, mas não que a perk esteja concedida ou o caminho executável no runtime atual. Auditar grants/perks de raça e verificar em jogo. Alternativa sem nativo: remover somente perk legada de origem vampírica mediante ownership seguro ou patch de records revisável no overlay próprio; não remover fontes de outras origens e não mexer no modpack. Mesmo assim validar race regrant, scripts de quest e o próprio Lorde. Somente o menu X autorizado será canal de alimentação.

### Sol e limites de autoridade

Usar flag exata `AETHERIUS_VAMPIRE_SERVER_SUN`, default false; coordenador alinhou com Sol retirada/reconciliação do grant sobrenatural ao ligar. `COTV_WeaknessToSunlight` de outra origem ainda precisa ser detectado e impedir ativação de dano servidor até que seja possível neutralizá-lo sem revogar outra origem. O único nado localizado é `survival-service.js:534-588` informado pelo cliente; provisoriamente nado NÃO protege, divergência conhecida da regra final. Não reiniciar o servidor agora.

Saída desta fase é análise, não implementação de animação, tela preta ou aura. Necessárias revisão Sol, testes em runtime e resolução dos conflitos antes de marcar qualquer parte completa.

## 16/11 — núcleo inicial da reserva (revisão pendente)

`overlays/sobrenatural/files/server/gamemode/supernatural/blood-reserve.js`, instalado pelo patch `008-vampiro-sangue.patch`, fornece cálculo fracionário segmentado e persistência local por characterId e grantId em `runtime/server/data/aetherius-blood-reserve.json` (conforme diretório de trabalho do servidor). Ao trocar grantId, reinicia em 100; status deriva em NORMAL (>30), CRITICAL (0 < sangue ≤30) ou FERAL (0). Usa relógio monotônico em sessão e grava por troca temporária/rename. O consumo natural é calculado em ticks de 10 s e em chamadas da API; sessão usa referência estável da ficha em `commands`, evitando contar um intervalo offline em reutilização do mesmo ator. Ao reiniciar o processo não há catch-up offline. **Ainda falta integração explícita de saída/login para liquidar até os limites exatos da sessão**; a amostragem pode perder até um tick online, e não há recuperação do último intervalo não gravado em queda brusca.

Contrato `mp.__aetheriusBloodReserve`: `get(actorId)` retorna `{blood,available,state}` ou null; `add(actorId,amount)` retorna boolean; `reserve(actorId,cost,powerId)` retorna `{ok,token}` ou `{ok:false}`; `commit(token)` e `cancel(token)` retornam boolean. IDs de poder string ou inteiro seguro positivo. Hold bloqueia disponibilidade e bloqueia duplicação do mesmo ator/personagem e poder; expira após 15 s. Commit/cancel repetido retorna o mesmo resultado por até 60 s na mesma execução, não é idempotência durável após restart. Drenagem durante hold não reduz saldo abaixo do total reservado: esse limite pode suspender parte da drenagem nos últimos pontos durante no máximo 15 s por token; é uma limitação provisória que precisa ser revista. Erro de gravação faz `commit` retornar false sem debitar em memória; o chamador deve abortar o efeito ou compensá-lo, conforme contrato a combinar com Sol.

O gancho de `onEatItem` identifica `18ef3:Dawnguard.esm` + EditorID e acrescenta 50 somente a vampiros; mantém cura existente de 100 e deixa o handler anterior rodar. O evento do C++ valida posse antes de ocorrer e remove uma unidade depois; não adiciona outra cura. **Sem transação compartilhada entre gravação JSON e remoção/ledger, falha de disco ou crash pode consumir sem crédito ou creditar antes de a baixa completar.** Há log para falha e é necessário um mecanismo durável/idempotente com o owner do inventário antes de declarar esta operação confiável. A execução real de cura única e comportamento de dois clientes não foi testada.

Testes históricos `node --test overlays/sobrenatural/teste-reserva-sangue.cjs`: 5/5, curva completa 13h30 simulada, mudança de faixa, token, falha de disco, sessão e poção; não substituem execução no servidor/jogo. Ainda faltam: integração de custos por Sol, sol com flag, bloqueio por personagem da lógica de sobrevivência e HUD, alimentação pelo menu X, heartbeat/visão, nome e aura; não houve teste em jogo nem reinício.

### Roteiro para teste em jogo futuro — NÃO realizado nesta sessão

1. Após revisão cruzada, servidor iniciado somente com Skyrim fechado, confirmar boot dos overlays 007/008 e presença da API; usar personagem de teste vampiro e outro humano com inventário conhecido.
2. Cura: comparar poção pura, comida e item misto, Restoration, dreno e regen natural; confirmar que item vetado é baixado uma vez e ledger/inventário/UI não divergem. Repetir em humano e ao remover vampirismo.
3. Sangue: setar, gastar por poder, relogar, alternar personagem e grant, morrer e reviver, verificar limites 100/30/0 e tempo efetivamente online. Simular reserva concorrente/expirada e falha de execução do poder; verificar que efeito rejeitado não consome.
4. Poção `DLC1BloodPotion`: comparar saldo e HP antes/depois em vampiro com vida baixa; conferir +50 sangue limitado a 100, +100 HP uma única vez, exatamente uma unidade no inventário e ledger. Testar não vampiro, item ausente, persistência e reconexão; não causar falha de disco em dados reais sem ambiente descartável.
5. Após implementação das demais partes, exercitar sol de dia/noite, chuva/nublado, interior, transição de clima e detecção do efeito COTV de qualquer origem; nado ainda não protege. Confirmar que a flag `AETHERIUS_VAMPIRE_SERVER_SUN` só é ligada após reconciliação de grants pelo Sol e ausência de duplo dano.
6. Com pelo menos dois jogadores e idealmente um espectador, validar PvP, alcance, cooldown por vítima, cancelamento/desconexão, dano não letal de 20% da vida máxima e crédito único após 10 s; checar tela, som, animação e limpeza. Verificar se a perk `VampireFeed` chega ao personagem e se o feeding legado permanece acessível.
7. Após implementação da HUD/efeitos, verificar supressão individual de todas as necessidades e penalidades, nome Feral sem alteração do nome salvo, aura para observadores que entram depois, visão crítica e limpeza ao recuperar sangue, morrer, trocar célula ou perder a maldição. Aguardar decisões pendentes sobre retorno dos valores humanos, histerese e alcance da visão.


## 16/11 — exceções de custo decididas após revisão Terra 3

O dev definiu **Sedução e Reanimação SEM custo de sangue**, funcionando normalmente pelo caminho nativo. Força, Névoa, Celeridade, Enxame, Morcego, Visão e Lorde continuam custando 5 por uso aceito; a API ausente/incompleta ou commit sem `true` explícito bloqueiam somente os poderes cobrados. Isso elimina o débito antecipado da conjuração dirigida, cuja última recusa nativa ocorre depois do hook JS. Relatório e testes: `C:\Code\Aetherius - New Systems\Sobrenatural\docs\REVISAO-3_CORRECOES_sol.md`. O restante da Reserva de Sangue foi pausado para essa prioridade; seu núcleo/sol/feeding/HUD/poção não foram alterados nesta correção.

## 17/11 — item 1: dano solar autoritativo (candidato)

Base: `88cbbd0`; correção independente B1 já separada em `e86c0cb`. Código novo: `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\supernatural\vampire-sun.js:6` (fonte espelhada em `source\server\gamemode\supernatural\vampire-sun.js`). Instalação no bootstrap pelo incremento `overlays\sobrenatural\012-sol-servidor.patch`, após o módulo de sangue. Nenhum runtime foi preparado/iniciado e nenhuma flag foi ligada nesta sessão.

`AETHERIUS_VAMPIRE_SERVER_SUN=true` habilita tick de 1 s com **5 HP**, janela **07:00 <= hora < 19:00**, usando exclusivamente `aetherius.environment.authority.v1` (calendário e weatherDesc autoritativos) e posição/célula do host (`:12–26`). Esta interpretação encerra o dano às 19:00 e substitui o texto histórico “19 inclusive”; o roteiro testa a fronteira. WRLD exterior + clima válido são exigidos; CELL/interior, clima desconhecido, autoridade indisponível, chuva e neve/nevasca suspendem o tick. Nublado continua exposto; nado não é consultado. Não há catch-up offline nem rajada por event loop atrasado. Mortos e abatidos não recebem novo dano solar.

O `vampire-grants.js:89` já suprime o grant próprio `COTV_WeaknessToSunlight` com a mesma flag; o reconciliador por origem retira apenas o que concedeu. `vampire-sun.js:35–42` exige ausência desse spell no `knownSpells` antes de aplicar dano; fonte externa ou reconciliação incompleta suspende o dano servidor e registra aviso, sem remover fonte de terceiros. Isso evita sobrepor o tick com a habilidade COTV conhecida. Não garante ausência de qualquer efeito solar arbitrário de outro mod.

Validação: `node --test overlays/sobrenatural/teste-sol-servidor.cjs overlays/sobrenatural/teste-reserva-sangue.cjs` → **tests 9; pass 9; fail 0; cancelled 0; skipped 0; todo 0**, exit 0, log `evidence/reserva-sol-node.log`. Testes cobrem fronteiras 7/19, nublado, chuva/neve, interior, amostra desconhecida, morto/abatido, flag e grant legado. Chamada `Actor.DamageActorValue(String, Float)` conferida na skill papyrus-reference, schema CELL/WRLD/WTHR na mutagen-reference; reutilizado parser existente core/espm. São mocks Node, sem execução nativa/jogo. Revisão independente do item solar pendente.

Roteiro adicional (não executado): habilitar flag antes do boot; conferir retirada só do grant próprio COTV e ausência de dano duplo. Com HP conhecido, testar 06:59→07:00→18:59→19:00, claro/nublado/chuva/neve/nevasca e entrada/saída de interior; nadar continua recebendo dano. Confirmar redução de 5 HP por segundo, morte/abatimento, relog sem recuperação retroativa e fonte COTV externa suspendendo o tick; repetir com humano. Voltar flag false exige reinício e reconciliação. Restante dos itens da Reserva continua pendente, inclusive atomicidade da poção e efeitos/HUD/feeding.

## 17/11 — D7: atomicidade da Poção de Sangue BLOQUEADA

Decisão do dev transmitida pelo Opus neste despacho: **não ampliar agora o contrato nativo e não aceitar garantia menor**; registrar o bloqueio e continuar os itens independentes. O crédito antigo da poção não foi modificado nem declarado atômico. Duas opções expostas ao coordenador: (1) novo contrato nativo com identificador durável de consumo, confirmação da baixa, idempotência e replay/reconciliação após crash; exige arquitetura/revisão próprias e build/testes nativos; (2) manter o hook atual e aceitar apenas crédito de melhor esforço, com janelas de inconsistência. **A opção 2 foi expressamente rejeitada**; opção 1 adiada para novo escopo. D7 permanece bloqueio de entrega integral, não uma funcionalidade concluída.

Evidência na base local `C:\Code\Aetherius - Servidor Local\source\server\cpp\`: `server_guest_lib\gamemode_events\EatItemEvent.cpp:22` serializa só actorId/baseId (linhas 26/28), sem operationId; `addon\ScampServerListener.cpp:103` chama o handler e `:111` exige Boolean síncrono (não aguarda transação PG); `server_guest_lib\MpActor.cpp:533` chama EatItem e `:557–558` remove uma unidade de qualquer forma após a chamada. `GameModeEvent.cpp:38` veta os efeitos, sem mudar essa remoção no chamador. O hook atual `overlays\sobrenatural\files\server\gamemode\supernatural\blood-reserve.js:93–100` credita JSON antes de a baixa nativa completar; mover a ordem apenas muda a janela de perda/duplicação. Não foi alterado nativo, inventário, ledger, modpack ou comportamento da poção nesta etapa.

## 17/11 — item 2: necessidades individuais e barra de sangue (candidato)

D1 configurável por `AETHERIUS_VAMPIRE_NEEDS_RESTORE`: **`frozen` padrão**, retoma valores congelados; **`default`**, retoma 100/100/100. Não é decisão final de balanceamento. `source\server\gamemode\survival-service.js:56,222,283,341–372,435,476,576,715–745,841,1014` congela progressão/consumo/restauração/staff, limpa só spells gerenciadas pelo survival, impede reenvio tardio e drenos do cliente, publica HUD vazia e retoma sem cobrar intervalo vampírico. Humanos continuam usando o mesmo fluxo. Consumo alimentar ainda debita ledger; só não restaura necessidades. O marcador `character_survival.supernatural_suspended` (boolean; ALTER idempotente no próximo boot, **não executado nesta sessão**) conserva a decisão de retorno também em relog/remoção offline. O salvamento otimista inclui o marcador e usa snapshot para não apagar uma alteração durante await. Leitura de estado autoritativo indisponível pausa penalidades por segurança.

`overlays\sobrenatural\files\server\gamemode\supernatural-service.js:150` avisa a ponte survival no commit de estado; o callback atualiza imediatamente o personagem online sem publicar keyword. `supernatural\blood-reserve.js:63` adiciona peek sem I/O repetido/drenagem extra; `supernatural\blood-presentation.js:1` publica apenas `{v,blood,state}` em property privada do owner e limpa humanos. Reaplicação visual de 1 s e watchdog CEF de 3,5 s escondem estado antigo após desconexão. Sangue exato não é replicado a vizinhos.

Artefatos: incremento servidor `overlays\sobrenatural\013-vampiro-necessidades.patch` após 012; incremento cliente `overlays\cliente-local\006-reserva-sangue-hud.patch` após 005. Template alterado somente no checkout local `C:\Code\Aetherius - Servidor Local\client-source\client\index.html` (compatibilidade), barra vermelha/ícone no canto inferior direito. **Nada instalado/copied no MO2 e nenhuma pasta MeridianUI criada/sobreposta**; o coordenador pode aplicar o template só na cópia completa COMPATIBILIDADE (LOCAL) quando autorizar instalação. As fontes locais foram sincronizadas; nenhum runtime/build/servidor/jogo iniciado.

Testes: `node --test source/server/gamemode/survival-service.test.js` → **70/70**, mesmo comando no modo `AETHERIUS_VAMPIRE_NEEDS_RESTORE=default` → **70/70**. `node --test overlays/sobrenatural/teste-sangue-hud.cjs overlays/sobrenatural/teste-reserva-sangue.cjs` → **7/7**. Logs `evidence/reserva-necessidades-node.log`, `reserva-necessidades-default-node.log`, `reserva-hud-node.log`. Reverse-check 013/006 passou, cliente diff --check limpo; PG/CEF real e revisão independente pendentes. Testes cobrem congelamento, humano simultâneo, retorno online/offline nas duas políticas, ledger da comida, remoção só de penalidades survival, payload privado, limpeza/watchdog e limites da barra.

Roteiro humano futuro: com dois personagens, comparar fome/sede/frio antes e após setagem; vampiro perde as três barras e penalidades e ganha barra vermelha, humano continua normal. Comer/beber e tentar /survival definir não muda necessidades congeladas; confirmar uma baixa no inventário/ledger. Aguardar reenvios de login de 20/90 s e verificar ausência de penalidades/drenos. Gastar/recuperar sangue, relogar, morrer e perder a maldição; HUD deve atualizar/limpar e voltar necessidades pela política configurada. Remover offline, reiniciar e voltar; confirmar marcador PG e valores. Conferir posição/estilo/foco em CEF real, sem criar outra pasta MeridianUI.

## 17/11 — item 3: Sede Crítica e Feral (candidato)

Commits até aqui: B1 `e86c0cb`; solar `4c631e1`; necessidades/barra servidor `1f84be4`, cliente/template `f57ec7d`. Os arquivos de código novos deste item partem de `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\supernatural\`: `blood-effects.js` (limiar/configuração e seleção autoritativa), `blood-effects-client.js` (som/shaders e watchdog). Instalação e override de identidade em `014-sede-critica-feral.patch`, após 013. Fontes sincronizadas em `source`; sem runtime/instalação MO2/jogo.

D2: `AETHERIUS_VAMPIRE_CRITICAL_EXIT=30` padrão aplica literalmente entrada **≤30** e saída **>30**; configurando 35 mantém até **≥35** (alternativa ainda não aprovada). Raio de visão fixado em **25 m**, convertido por `AETHERIUS_SKYRIM_UNITS_PER_METER` (padrão 70 unidades/m, convenção aproximada configurável a validar no mundo). `blood-effects.js` seleciona MpActors vivos nos neighbors do host, na mesma célula/worldspace e até 1750 unidades; não confia em alvos enviados pelo cliente. Clientes remotos são mapeados por `ctx.getFormIdInClientFormat`; o cliente revalida distância/3D antes de aplicar a detecção. Lista/radius só vão ao dono, sem sangue/keywords para vizinhos. Cobertura está limitada a atores instanciados e relevantes no host; NPCs ambientais que não apareçam no conjunto de neighbors exigem validação de cobertura em jogo.

A visão automática não conjura a spell manual nem cobra sangue. O cliente aplica o shader vanilla `0DC209:Skyrim.esm` (`LifeDetectedEnemy`) por 1,1 s, renovando a cada 750 ms; houseCARL confirmou **ParticleZTest=AlwaysShow**, base para revelar através de obstáculos. Ao sair, deixa de renovar e o shader finito expira em até 1,1 s, sem chamar Stop global que apagaria visão independente. Prioridade/coesistência real com Aura Whisper e Predator Vision ainda exigem jogo; não foi declarado equivalente visual homologado. Batimento privado usa `000809:AETHERIUS - LOBISOMEM.esp`/descriptor `000808`, refeito a cada 9,6 s e parado por instanceId próprio na saída/morte/desconexão; watchdog de 2,5 s sem heartbeat servidor limpa o som.

Feral em 0: propriedade pública contém só `{feral:true,sample}`, repetida para owner/vizinhos e participantes entrando depois. O cliente usa o shader vermelho **`000800:SkyDeath-Ghosts.esp` (`SkyDeathGhost_red`)**, separado do `0FE68C:Skyrim.esm` usado pelo SkyDeath; nunca muda ghost/alpha/controles/raça. Duração finita 2,5 s, renovação e reaplicação em troca de célula/3D; Stop só desse asset ao limpar. `source\server\gamemode\skysocial\src\identity.js` devolve **VAMPIRO FERAL** temporariamente para quem está perto, incluindo o próprio jogador; nunca grava nome/appearance e retorna ao nome normal ao sair. Morto/abatido suspende os efeitos, mantendo sangue persistido.

Leituras houseCARL somente: perfil ativo reportado GRAFICO–QUALIDADE; perfil LOCAL foi inspecionado sem troca. O arquivo local `AETHERIUS - SOBRENATURAL.esp` foi lido explicitamente fora da ordem ativa, sem alegar winner LOCAL; sua Visão tem áreas 500/300 e duração 30 s, por isso não foi reutilizada para impor 25 m. Winner de 0DC209 é Skyrim.esm; 000800 é SkyDeath-Ghosts.esp; coração do kdu e descriptor foram conferidos. Nenhum record/asset/mod foi escrito. Schemas consultados via mutagen-reference e assinaturas Sound.Play/StopInstance, EffectShader.Play/Stop via papyrus-reference.

`node --test overlays/sobrenatural/teste-sangue-efeitos.cjs source/server/gamemode/skysocial-service.test.js` → **10/10**, log `evidence/reserva-efeitos-node.log`. Cobertura: fronteiras 30/30,01/0 e histerese 35, raio 1750/1751 e outra célula, privacidade, morte/remoção, identidade sem mudança do nome salvo, heartbeat privado e shader finito sem Stop da visão independente, asset Feral separado e limpeza por 3D. Reverse-check 014 e revisão independente pendentes de fechamento; execução de motor não foi simulada como prova de visual.

Roteiro: chegar exatamente a 30, recuperar a 30,01, chegar a 0 e recuperar a 1; conferir som, visão e nome/aura. Com terceiro observador, entrar em relevância já Feral, cruzar célula e recarregar 3D. Colocar jogador e criatura a 24/25/26 m, atrás de parede e em invisibilidade/furtividade; conferir limite, remote IDs e ausência de efeito em outra célula. Conjurar Visão manual durante a crítica, recuperar sangue e garantir que o efeito manual continua. Testar Predator Vision, morte/abatimento, desconexão e remoção da maldição: limpar som e efeitos sem alterar controles/ghost/nome salvo. Se houver falta de cobertura de NPC/remote ou conflito visual, manter como candidato parcial e investigar antes de aceitar.

## 17/11 — item 4 independente: precisão do relógio de sessão (candidato)

O bloqueio D7 permanece só na atomicidade da poção; este incremento não muda seu consumo/crédito. Commit anterior dos efeitos: `6fb08d1`. `overlays\sobrenatural\files\server\gamemode\supernatural\blood-reserve.js` agora abre a referência monotônica na ativação e fecha o intervalo fracionário exato antes da saída/substituição da ficha. `overlays\sobrenatural\015-sangue-relogio-sessao.patch` integra `source\server\gamemode\commands.js::registerActiveCharacter/removeActiveCharacter/discardActiveCharacter`; `supernatural-service.js` fecha a geração anterior antes de setar/remover maldição e inicia após commit. A criação de personagem não conta. PD descarta a sessão sem ressuscitar salvamentos da ficha excluída. Fechamento antigo com referência de sessão obsoleta não pode encerrar a sessão nova.

A cauda entre último tick (10 s) e logout gracioso passa a ser drenada com fração de milissegundos; desconexão não depende da próxima amostragem. Holds do ator são cancelados na saída. Se o disco falhar, uma fila em memória conserva **a quantidade de segundos já encerrada**, sem somar o período offline durante a retentativa; entrada/uso ficam recusados até persistência recuperar. A fila nunca transfere débito para grantId novo. Dispose gracioso também fecha a cauda. Não há promessa de recuperar uma cauda ainda não gravada se o processo/volume morrerem abruptamente: o último checkpoint continua sendo o limite durável. Caminhos de host que removam ator sem passar pelos hooks de commands ainda têm o fallback amostrado anterior e precisam ser detectados em integração.

`node --test overlays/sobrenatural/teste-reserva-sangue.cjs overlays/sobrenatural/teste-origem.cjs overlays/sobrenatural/teste-revisao-3.cjs` → **59/59**, log `evidence/reserva-relogio-node.log`. Três novos testes: cauda 2,3456 s exata e 24 h offline, falha de fechamento com 1 h offline e retorno no mesmo processo, sessão obsoleta/creation/dispose. Integração por patch inspecionada, sem execução do servidor ou evento de rede real; revisão independente pendente.

Roteiro adicional: deixar vampiro conectado por intervalo não múltiplo de 10 s, desconectar e comparar saldo persistido com a curva fracionária; aguardar offline, relogar e confirmar ausência de desconto offline. Trocar personagem no mesmo actorId, relogar repetidamente e remover/reconceder maldição. Em ambiente descartável, falhar a escrita no fechamento e recuperá-la depois; verificar que só a cauda online é liquidada e que tokens antigos não servem na sessão nova. Não usar falhas de disco em personagens reais.

## 17/11 — item 5: Beber Sangue no menu X (candidato DESLIGADO)

**D7 estendida por orientação expressa do Opus neste despacho:** a liquidação da alimentação compartilha a fronteira não atômica entre Health nativo e JSON da reserva. Foi autorizado preparar somente o candidato atrás de **`AETHERIUS_VAMPIRE_FEED_ENABLED` (padrão false)**, com journal durável e bloqueio conservador; **ativar a flag depende de decisão do dev**. Nenhuma alteração nativa ou garantia menor foi aceita para a Poção de Sangue. Alimentação também NÃO é atômica: a alternativa autorizada é um protótipo desativado e explicitamente não homologado, com evidência para reconciliação manual, sem replay automático de recompensas.

### Funcionamento e código

Todos os caminhos partem de `C:\Code\Aetherius - Servidor Local`:

| Arquivo:linha | Responsabilidade |
|---|---|
| `overlays/sobrenatural/files/server/gamemode/supernatural/feeding.js:7` | Flag default false; D3 `AETHERIUS_VAMPIRE_FEED_REQUIRE_CONSENT=true` por padrão, `false` apenas alternativa configurável do protótipo. Alcance 2 m (140 unidades com convenção 70/m), 10 s, 60 min por vítima persistente, dano 20% da vida máxima com piso de 1 HP, +100 sangue e cura até 200 limitada ao máximo. D3 continua aberta, não vira decisão final por existir esta constante. |
| `feeding.js:26` | Escrita JSON temporária, fsync e rename em `data/aetherius-vampire-feeding.json`. Preserva todas as operações não resolvidas e até 2.048 terminais; remove cooldowns vencidos na próxima escrita. Não é arquivo global de auditoria permanente. |
| `feeding.js:35` | Boot cancela INVITED/RUNNING sem recompensa; SETTLING/RECONCILE bloqueiam ambos characterIds e nunca são liquidados/revertidos automaticamente. Registro inválido recusa instalação em vez de criar banco vazio. |
| `feeding.js:57` | Só jogadores com binding ativo, vivos/ALIVE, distintos, sem sessão concorrente, transação social/profissão/forma; mesma célula e raio medido no host. Política central `vampire_feed` (gameplay+combat) para ambos com zonas seguras e SkySocial `canDamage`/hook; grupo protegido continua bloqueado. Vítima com ≤1 HP recusada. |
| `feeding.js:105` | Início exige alvo e token frescos do menu X autenticado pelo SkySocial. Sessão exclusiva por ambos, nonce UUID e referências de binding/grant capturadas. Convite de consentimento expira em 15 s. Aceite só da vítima e da sessão atual. |
| `feeding.js:126` | Revalida ao concluir 10 s monotônicos. Grava SETTLING, snapshots de HP/sangue/intenção e cooldown antes de qualquer mutação. Aplica dano não letal e cura com readback, credita sangue uma vez e grava SETTLED. Qualquer falha após a intenção gera RECONCILE, limpa a apresentação e bloqueia os envolvidos para nova alimentação/trocas/poderes que consultam o lock social; não congela genericamente vida/mundo. Não há promessa de rollback ou atomicidade. |
| `feeding-client.js:2` | Tela preta e mensagem subjetiva na vítima; vermelho temporário no vampiro; watchdog CEF de 3 s e do owner de 1,5 s. Ao concluir, a mensagem de memória em primeira pessoa usa o notice já existente do SkySocial. Nenhum dado/memória do jogo é apagado. |
| `feeding-client.js:21,41` | Protótipo pareado em owner/vizinhos, mapeando vítima para a ref local de cada cliente e chamando `PlayIdleWithTarget` com `00E6A8:Dawnguard.esm`. Cleanup com IdleStop nos dois, watchdog e diagnóstico `sp.storage.aetheriusFeedAnimationDiagnostic`. Nenhum StartVampireFeed, spell/script COTV, alteração local de HP, transformação, teleporte ou mudança de controles. |
| `source/server/gamemode/skysocial/src/system.js:187,209–214,230` | Modelo privado entrega elegibilidade/consentimento e roteia feed-start/feed-reply/cancel. Ações sociais concorrentes recusadas. |
| `source/server/gamemode/skysocial/src/ui.js:163,225` | Consentimento e opção Beber Sangue no menu X existente; oculta se flag/elegibilidade recusarem. |
| `source/server/gamemode/skysocial/src/index.js:16`, `client.js:36` | Lock de sessão/reconciliação integra às transações sociais, e foco do consentimento é devolvido ao cancelar/concluir. |
| `source/server/gamemode/commands.js:45,63,70` | Troca/saída/PD cancelam sessão e limpam apresentação sem atuar no personagem substituto. |
| `source/server/gamemode/phase0-basic.js:438` | Instalação após os owners anteriores. Journal/config inválidos deixam alimentação bloqueada e registram erro; não inicia registro novo silencioso. |

O payload público da animação contém somente IDs de atores, ID de interação, sample e tempo de apresentação. Sangue, snapshots de HP, characterIds, grantId e keywords NÃO são publicados a vizinhos. O cliente nunca concede maldição nem define resultado. Solicitação não autentica consentimento de outro jogador; referência de personagem/grant e políticas são revalidadas durante a sequência. Sessões canceladas não recebem sangue/cura/dano novo. Cooldown é por identidade persistente da vítima, compartilhado entre vampiros, e sobrevive ao relog/boot.

### Limites explícitos da animação e do som

houseCARL confirmou `00E6A8:Dawnguard.esm` = `IdleVampireStandingFeedFront_Loose`, evento `pa_VampireFeedStanding_Front`, sem conditions; schema IDLE e assinatura Actor.PlayIdleWithTarget conferidos nas skills. Isto prova disponibilidade do idle e da API, **não prova alinhamento, cancelamento ou sincronização de dois owners e espectador**. Não foi criado controlador nativo; não há reposicionamento forçado. O retorno local do idle fica só no diagnóstico e NÃO é ACK autoritativo: o sucesso mecânico do protótipo é o intervalo validado no servidor. Falha visual pode coexistir com liquidação e deve impedir homologação/ativação até testar/corrigir.

O áudio pretendido é o do grafo pareado vanilla: houseCARL confirmou SNDR `0FF984:Skyrim.esm` (`NPCHumanVampireFeed`) e seu caminho `Data\Sound\FX\NPC\Human\Vampire\NPC_Human_VampireFeed_01.wav`. A varredura VFS NÃO incluiu os BSAs base por falta da lista Archive no INI ativo: informou ausência incompleta, portanto **não foi comprovado áudio nem seu acionamento por este idle**. Não foi inventado SOUN, reutilizado heartbeat como som de alimentação ou copiado BSA para o modpack. Confirmar áudio do vampiro e ausência de duplicação por cliente em jogo continua requisito de homologação. O caminho legado de perk/quest COTV fora desta nova ação não foi removido/neutralizado; a nova ação evita chamá-lo, mas não declara que todos os caminhos legados foram extintos.

### Reconciliação manual e D7

Após crash/falha em SETTLING, manter a flag desligada e conferir, com servidor parado e backup, o journal da alimentação, `aetherius-blood-reserve.json`, HP autoritativo e logs pelo UUID. `mp.__aetheriusVampireFeeding.reconciliationRequired()` é leitura privada de diagnóstico. **Não apagar journal/bloqueio nem reaplicar +100/+200 às cegas**: pode já haver mutação parcial. A staff/dev determina o que realmente foi aplicado e registra a resolução antes de tornar a linha terminal; não há botão/F7 novo para resolver automaticamente. Se não for possível determinar, continuar bloqueado. D7 exige futuro contrato/receipt nativo ou outra arquitetura aprovada para uma garantia atômica; este candidato não substitui essa decisão.

### Artefatos e validação

Incremento `overlays/sobrenatural/016-alimentacao-menu-x.patch` após 015, mais os arquivos novos `feeding.js` e `feeding-client.js`. Não há bundle cliente novo para alimentar: snippets vêm do servidor; a barra já está no checkpoint cliente **f57ec7d**. Instalar o template/bundle no LOCAL continua tarefa futura do coordenador; nenhum mod/MO2, oficial, painel externo, addon ou runtime foi alterado/iniciado neste despacho.

`node --test overlays/sobrenatural/teste-alimentacao.cjs source/server/gamemode/skysocial-service.test.js` → **tests 30; pass 30; fail 0; cancelled 0; skipped 0; todo 0** (24 casos da alimentação + 6 do serviço social). Log `evidence/reserva-feeding-node.log`. Cobertura: flag, token, consentimento autenticado, exclusividade/NPC, limites 140/141, duração 9.999/10.000 ms, dano máximo/não letal, clamp de cura, crédito único/cooldown, PvP/política/grupo, morte/desconexão/distância/curse/binding/journal de inventário/invulnerabilidade, timeout/cancelamento, prepare falho sem HP, falhas em dano/cura/crédito/finalização, boot sem replay e bloqueios por characterId, JSON real em diretório temporário e corrupção recusada; cliente mockado sem StartVampireFeed/HP/controles e limpeza de telas.

Preview **somente de navegador**, no Orca: `evidence/reserva-preview.html` e cópia `C:\Code\Aetherius - New Systems\Sobrenatural\preview\reserva-candidato.html`. Cliques Menu X → Beber Sangue → Permitir/Recusar, overlays e barra conferidos; console sem erros. Capturas `evidence/reserva-preview-{menu,consent,vitima,vampiro}.png`. O navegador não rodou Skyrim/CEF do jogo, Papyrus nem multiplayer; não conta como prova da animação/áudio/HP nativos.

Roteiro futuro, somente após revisão e decisão do dev sobre a flag: usar três jogadores (vampiro, vítima e espectador), ativar menu X a 1,9/2/2,1 m, recusar e aceitar consentimento, tentar ação forjada/repetida/concorrente e repetir em grupo/zona segura/abatido/formas. Durante 10 s, validar tela preta/mensagens, vermelho/som no vampiro, alinhamento dos dois Actors e animação vista pelo espectador; checar áudio uma vez, alvo remoto correto e limpeza por morte/desconexão/célula/cancelamento. Confirmar +100 sangue, cura até 200, vítima −20% da máxima com piso 1 e cooldown de 60 min após relog. Repetir com vítima abaixo de 20%, vampiro quase cheio e humano sem maldição. Não testar falha de disco em dados reais: em cópia descartável, falhar cada fronteira, reiniciar e confirmar quarentena sem replay antes da resolução manual. Se o grafo/áudio/autoridade não estiverem comprovados, **não habilitar a flag para uso normal**.

## Fechamento do despacho de 17/11/2026

Autor: Codex, despachado como Sol High. Base raiz: `88cbbd0`; base cliente: `757c571`. Nenhum histórico foi reescrito.

| Entrega | Commit |
|---|---|
| B1 da confirmação Terra R1: preservar evidência/ambiguidade entre retentativas | `e86c0cb` |
| Item 1: dano solar sob flag | `4c631e1` |
| Item 2: necessidades individuais, marcador PG e HUD privada | `1f84be4` |
| Template local cliente da barra | `f57ec7d` (`client-source`) |
| Item 3: Sede Crítica/Feral | `6fb08d1` |
| Item 4 independente: cauda exata da sessão | `f5a4593` |
| Item 5: candidato desativado, consentimento, journal/quarentena e protótipo pareado | `e4133ac` |
| Guarda adicional de journal social/invulnerabilidade na alimentação | `3246967` |

Validação final executada na raiz `C:\Code\Aetherius - Servidor Local` (exclui os três scripts de integração, rodados separadamente com os argumentos próprios):

```powershell
$testFiles = @(Get-ChildItem overlays/sobrenatural -File -Filter '*.cjs' |
  Where-Object { $_.Name -notin @('teste-servico.cjs','teste-painel-f7.cjs','teste-revisao-f7.cjs') } |
  Select-Object -ExpandProperty FullName)
$testFiles += @('source/server/gamemode/werewolf/tests/werewolf.test.cjs',
  'source/server/gamemode/magic/tests/magic.test.cjs',
  'source/server/gamemode/survival-service.test.js',
  'source/server/gamemode/survival-effects.test.js',
  'source/server/gamemode/skysocial-service.test.js')
node --test @testFiles
```

Saída real, exit **0** (`evidence/reserva-final-node.log`):

```text
# tests 298
# suites 18
# pass 298
# fail 0
# cancelled 0
# skipped 0
# todo 0
```

Os três comandos abaixo também retornaram exit 0; logs `reserva-final-servico.log`, `reserva-final-f7.log`, `reserva-final-revisao-f7.log` em `evidence/`:

```powershell
node overlays/sobrenatural/teste-servico.cjs 'C:\Code\Aetherius - Servidor Local\source\server\gamemode'
node overlays/sobrenatural/teste-painel-f7.cjs
node overlays/sobrenatural/teste-revisao-f7.cjs
```

Composição 012→016: revertidos em ordem inversa e reaplicados em cópia descartável sob `backups/`, sem tocar no source em uso; os nove arquivos de patch ficaram textualmente idênticos, normalizando somente CRLF/LF. Arquivos JS completos do overlay conferidos byte a byte com source. Log `evidence/reserva-final-patches.log`; reverse-check direto 016 e cliente 006 passou; `git diff --check` e parse de bootstrap/commands/UI passaram. O modo D1 `default` foi testado separadamente (70/70); não depende de PG real. **Não houve build JS/TS, preparação de runtime, servidor, instalação MO2 ou jogo nesta entrega.** Mudanças são JS e template HTML; nenhum addon foi recompilado.

Revisão cruzada: Terra R1 (`REVISAO-3AB_CONFIRMACAO_terra.md`) confirmou A e reprovou B1 sobre `88cbbd0`; B1 foi corrigido pelo autor em commit separado e ainda precisa de nova confirmação. Os demais candidatos de 17/11 precisam de revisão Terra R2; nenhuma aprovação de R1 foi estendida a eles. Não foram usados subagentes neste despacho. D7 permanece aberta/bloqueada; ativar a alimentação depende do dev, da revisão e dos testes nativos/visuais descritos. Também permanecem: decisões finais D1–D3, medição 2/25 m, cobertura de criaturas, áudio e animação de três jogadores, PG real/migration do marcador, integração pareada com cliente e caminho legado COTV. Documentação .md e evidências prontas para o coordenador consolidar no Acervo; este worker não fez push concorrente no clone compartilhado.


Conferência final de elegibilidade em `3246967`: respeita o lock composto social antes de começar, o journal de inventário independente durante a própria sessão (sem recursão/autocancelamento) e `aetheriusInvulnerableUntil` de ambos os atores. Dois novos testes de cancelamento, sem HP/sangue, elevaram o total final a **298/298** e a rodada alimentação/social a **30/30**; investigação de 17/11 registra o achado.

## R2 — correções arbitradas pelo Opus (17/11/2026)

Autor das correções: Codex, despacho Sol High. Base desta rodada: raiz `C:\Code\Aetherius - Servidor Local` em `d0a1258`; cliente `client-source` continua **f57ec7d**, sem edição nesta rodada. Revisão independente lida e preservada: `C:\Code\Aetherius - New Systems\Sobrenatural\docs\REVISAO-R2_reserva-de-sangue_terra.md`. O coordenador arbitrou o candidato como **aprovado com ressalvas**, aceitando #2/#3/#4 para correção e #1 para investigação sem bloqueio; isso não transforma o relatório Terra em homologação integral nem constitui confirmação independente das versões novas.

### Tratamento dos apontamentos

| Achado R2 | Tratamento e razão | Commit |
|---|---|---|
| #2 — perda da cauda quando onlinePlayers falha/desaparece | Aceito: ausência de lista válida não prova encerramento; a referência precisa sobreviver até fechamento seguro. | `3015b91` |
| #3 — V regravações do JSON inteiro por ciclo | Aceito: agrupar a persistência periódica evita multiplicar cópia/serialização de N registros por cada vampiro. | `34fac30` |
| #4 — apresentação até 10 s atrasada | Aceito: projetar a integral em memória na leitura resolve o limiar sem escrever JSON a cada consulta de HUD/efeitos. | `7889be4` |
| #2 — fechamento pendente durante dispose | Complemento encontrado em teste de falha: preservar a entrada e iterar snapshot do mapa evita descartá-la ou repetir a iteração quando rollback restaura o mapa. | `3a4b70e` |
| #1 — criaturas | Investigado no código C++; mantido como teste em jogo obrigatório, sem bloquear esta entrega por arbitragem do Opus. | documentação desta rodada |
| D7 — crédito antigo da poção sem flag | Ressalva operacional aceita/documentada explicitamente; nenhum desligamento ou garantia atômica foi implementado. | documentação desta rodada |

### #2 — sessão preservada e ordem da saída

Código: `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\supernatural\blood-reserve.js`, espelhado byte a byte em `source\server\gamemode\supernatural\blood-reserve.js`.

- `onlineIds/identity` (`:31–41`) distinguem lista indisponível de lista vazia válida. Tick, get e peek não descartam a entrada por exceção/resultado inválido. Ausência conhecida marca a primeira observação `offlineAt`; o callback de lifecycle ainda pode liquidar até seu instante autoritativo.
- `close/flushClosed` (`:59–85`) conservam a entrada `closingAt` e o intervalo fixo enquanto gravação falha; repetir a saída não soma a cauda novamente. A remoção só ocorre após persistência ou descarte justificado de geração antiga, sem transferir dívida para novo grant.
- `ensure` (`:90–103`) trata reaparecimento/troca sem callback com o primeiro instante conhecido de ausência (ou último settlement observado) e reabre no tempo atual; não conta todo o intervalo não observado como online. Recarrega o saldo depois de fechar, evitando devolver um snapshot anterior à cauda.
- `dispose` (`:151`) usa snapshot de entradas, conserva fechamentos malsucedidos para retry e usa offlineAt quando falta callback. A injeção de falha de gravação revelou que reconstruir o Map durante iteração direta podia revisitar entradas indefinidamente; o teste foi interrompido localmente, corrigido e repetido com timeout do processo de teste. Não houve execução desse defeito no servidor/jogo.

A integração existente já fecha antes de `commands.activeCharacters.delete`: `source\server\gamemode\commands.js:68–73`. `phase0-basic.js:688–713` detecta saída e chama essa limpeza, preservando a proteção contra conexão antiga que perdeu o ator para outra. A detecção atual de conexões roda em polling de **2.000 ms** (`:608,713`); a precisão demonstrada é a do lifecycle do servidor, **não uma medição do instante físico do socket**. A ordem lista→callback e callback→lista foi exercitada com o mesmo timestamp fracionário. Se não houver callback, o fallback usa apenas observações conhecidas; não inventa a duração exata da sessão perdida.

### #3 — uma gravação por tick, com rollback do lote

`transaction/write/rowOf` (`blood-reserve.js:43–57`) criam o draft do mapa de personagens apenas na primeira alteração do ciclo. O tick usa uma única lista/Set de online, compõe inicializações, drenagem e fechamentos pendentes e chama `options.save` **uma vez se houve mudança, zero se nada mudou** (`:142–149`). Reduz a parcela de persistência de V×N para uma cópia/serialização de N por ciclo; não afirma que toda lógica seja O(N+V), pois validações e holds ainda têm seu custo próprio.

Só depois da gravação bem-sucedida o mapa durável e os relógios ficam confirmados. Se a gravação falha, os saldos duráveis e snapshots dos mapas active/closed são restaurados; a retentativa calcula o intervalo completo ainda não liquidado, sem desconto parcial, duplicação ou perda da cauda. Entradas runtime são substituídas imutavelmente para que o snapshot raso dos mapas seja suficiente. Operações de uso/adição/saída continuam com persistência imediata; o lote não adia o resultado de um poder/poção até o próximo tick.

`saveFile` (`:18–22`) usa temporário, **fsync do arquivo**, fechamento do descriptor e rename. A implementação anterior da reserva não chamava fsync explicitamente; esta rodada o acrescenta, sem alegar fsync de diretório/power-loss absoluto nem atomicidade distribuída. Testes de arquivo real verificam fsync antes do rename e que falhas injetadas em fsync/rename mantêm o arquivo anterior. Isso não resolve D7.

Medição sintética finita: `node overlays/sobrenatural/medir-reserva-lote.cjs`. Gera N registros, abre V sessões, avança 10 s e executa um tick com JSON real + fsync/rename em diretório temporário; não usa servidor, jogo ou PG. Atraso de callback é uma observação local do event loop, não um percentile/SLA de produção. Rodada isolada do lote (`evidence/reserva-r2-lote-medicao.log`):

| N registros | V vampiros | Saves/ciclo | Bytes | Tick (ms) | Callback agendado (ms) |
|---:|---:|---:|---:|---:|---:|
| 1.000 | 100 | 1 | 35.321 | 2,892 | 2,969 |
| 10.000 | 1.000 | 1 | 362.922 | 8,053 | 8,087 |
| 50.000 | 2.000 | 1 | 1.816.922 | 27,734 | 27,785 |

A coleta mede um ciclo local por tamanho, sujeita a cache/GC/disco; não mede rede, bridge nativa ou operação concorrente de jogadores. O teste de 1.000 vampiros verifica uma leitura de onlinePlayers e uma gravação por tick, além de saldo de todos os registros, e os testes de falha verificam rollback/retry do lote completo.

### #4 — projeção atual sem reduzir o checkpoint de 10 s

`peek` (`:119–125`) agora devolve `drain(saldoPersistido, tempoMonotônicoDesdeSettlement)`, respeitando holds, sem avançar o checkpoint nem gravar o sangue projetado. Depois de inicializar o binding e sem fila de fechamento, a consulta da apresentação não faz I/O. `get/add/reserve/commit` continuam liquidando o saldo real no contrato existente; apenas HUD/efeitos usam a projeção.

`blood-effects.js:29–34` e `blood-presentation.js:17` já consultam peek em polling de 1 s: cruzar 30 ou 0 passa a ser observado no próximo polling de apresentação, normalmente até cerca de **1 s mais agendamento**, em vez de aguardar o tick durável. O shader finito e os watchdogs do cliente continuam sujeitos a suas durações. Holds, falhas de identidade/leitura e pausa do event loop continuam limitando o tempo observável; não se prometeu efeito instantâneo no motor. Em queda abrupta ainda vale o último checkpoint salvo de 10 s, não a projeção que só foi exibida.

Os dois testes de integração usam a reserva real + módulos de HUD/efeitos para cruzar **30,001→≤30** e **0,001→0** em menos de 1 s, antes de qualquer tick de persistência, sem nenhuma escrita adicional. Medição com projeção (`evidence/reserva-r2-projecao-medicao.log`): no caso N=50.000/V=2.000, tick 22,801 ms, callback 25,337 ms, e uma passagem de 2.000 leituras projetadas 3,433 ms, mantendo **um único save**. Validações da bridge real não são simuladas como desempenho de produção. Custo observado permite manter a projeção e o checkpoint durável de 10 s.

### #1 — o que a leitura do servidor comprova sobre criaturas

Nenhum filtro de criaturas foi adicionado. Fontes C++ lidas em `C:\Code\Aetherius - Servidor Local\source\server\cpp\`:

1. `server_guest_lib\WorldState.cpp:403,697–699`: NPC_ materializado por AttachEspmRecord vira `MpActor`; não há distinção “humanoide versus criatura” nessa construção. `addon\ScampServer.cpp:1354–1356` faz o mesmo em mp.place; `gamemode\enemy-spawns-service.js:203` usa mp.place para inimigos gerenciados.
2. `addon\property_bindings\TypeBinding.cpp:10–11` devolve `MpActor` para AsActor, sem exigir profile/player; `IsDeadBinding.cpp:8–9` usa Actor.IsDead para esse mesmo tipo. Portanto, uma criatura materializada como ator satisfaz a representação usada pelo filtro.
3. `server_guest_lib\MpObjectReference.cpp:696–739` atualiza assinaturas do grid e chama Subscribe nos dois sentidos; `:1042–1072` insere ator/referência nas coleções sem exigir humanoide ou jogador, bastando ao menos um participante ser ator. `addon\property_bindings\NeighborsBinding.cpp:13–20` devolve união de listeners/emitters, não somente jogadores.
4. `WorldState.cpp:910–951` carrega registros nos chunks vizinhos e retorna o grid, porém `:479–501` e a política `WorldState.h:292–303` podem excluir referências/NPCs antes da materialização. Assim, a arquitetura permite criaturas em neighbors como MpActor, mas **não prova que cada criatura visível no cliente está instanciada/relevante no host**. Também não comprova que o addon instalado foi exercitado com esse caminho; não houve build/execução nativa nesta rodada.

**Teste em jogo obrigatório, antes de homologar a visão completa:** usar uma criatura viva do mundo (por exemplo lobo), uma criatura/invocação gerenciada no servidor e outro jogador, a 24/25/26 m, inclusive atrás de parede; registrar no host os IDs efetivos em neighbors, type, isDead, célula e posição e comparar com o payload privado e o destaque no cliente. Repetir entrada/saída de relevância/célula e criatura morta. Se uma criatura que o jogador vê não existir como MpActor/relevante no host, registrar a lacuna e corrigir a integração antes de aprovar cobertura; não tratar mock humanoide como prova. A arbitragem de não bloquear esta entrega não remove esse critério.

### D7 — ressalva operacional expressa sobre a Poção de Sangue

**O crédito antigo +50 continua instalado e ativo sem flag de habilitação da poção.** `phase0-basic.js:434` instala blood-reserve; o wrapper `blood-reserve.js:166–176` identifica `18ef3:Dawnguard.esm`/ALCH/DLC1BloodPotion e vampiro autoritativo e chama `api.add(actor,50)` se o handler anterior não vetou. Nem `AETHERIUS_VAMPIRE_FEED_ENABLED=false` nem a flag solar desligam esse caminho. A cura do item continua no efeito nativo; nenhum +100 extra foi acrescentado pelo wrapper.

D7 é bloqueio de homologação/atomicidade, **não desligamento técnico da poção**. Gravar o JSON com fsync/rename não o torna transação compartilhada com remoção nativa e ledger PostgreSQL. O caminho antigo não foi removido ou protegido por flag nesta arbitragem; ainda pode creditar sangue em runtime quando a instalação/evento forem executados. Alimentação permanece com default false e quarentena conservadora; nenhuma flag foi ativada e nenhuma garantia atômica foi assumida.


### Validação final R2 e reprodução

Executado na raiz **C:\Code\Aetherius - Servidor Local**, após `3a4b70e` e o teste adicional de falha em rename:

```powershell
$testFiles=@(Get-ChildItem overlays/sobrenatural -File -Filter 'teste-*.cjs' |
  Where-Object {$_.Name -notin @('teste-servico.cjs','teste-painel-f7.cjs','teste-revisao-f7.cjs')} |
  Select-Object -ExpandProperty FullName)
$testFiles+=@('source/server/gamemode/werewolf/tests/werewolf.test.cjs',
  'source/server/gamemode/magic/tests/magic.test.cjs',
  'source/server/gamemode/survival-service.test.js',
  'source/server/gamemode/survival-effects.test.js',
  'source/server/gamemode/skysocial-service.test.js')
node --test --test-timeout=10000 @testFiles
node overlays/sobrenatural/teste-servico.cjs 'C:\Code\Aetherius - Servidor Local\source\server\gamemode'
node overlays/sobrenatural/teste-painel-f7.cjs
node overlays/sobrenatural/teste-revisao-f7.cjs
```

Saída do runner (exit 0), registrada em `evidence/reserva-r2-final-node.log`:

```text
1..245
# tests 311
# suites 18
# pass 311
# fail 0
# cancelled 0
# skipped 0
# todo 0
# duration_ms 1103.1539
```

Os três scripts separados terminaram com exit 0 e mensagens `OK`; logs `evidence/reserva-r2-servico.log`, `reserva-r2-f7.log`, `reserva-r2-revisao-f7.log`. As mensagens de falhas injetadas no serviço são esperadas pelo teste. A bateria é a anterior de 298 mais **13 casos R2**, sem contar medição como teste. Usar o filtro `teste-*.cjs`: o novo `medir-reserva-lote.cjs` é benchmark finito separado, não um teste a somar artificialmente ao total. Esta bateria não executa PG real, bridge nativa ou o jogo.

Casos novos em `overlays/sobrenatural/teste-reserva-sangue.cjs`: leitura exceptional (`:63`); ordem lista/callback (`:67`); retries e troca de sessão (`:73`); ausência sem callback (`:77`); 1.000 vampiros/um save (`:84`); rollback/retry (`:90`); cauda + online no mesmo lote (`:95`); fsync/rename com arquivo real e falhas (`:100`); limiares projetados com HUD/efeitos reais (`:108`); dispose malsucedido/retry (`:115`). O arquivo contém 21 casos contando os testes anteriores e expansões dos loops. Fonte e overlay de blood-reserve conferidos byte a byte; nenhuma mudança de cliente nesta R2 (`client-source f57ec7d`).

### Roteiro complementar R2 em jogo — ainda não executado

1. Após revisão e autorização operacional, usar somente ambiente LOCAL e setagem F7/painel. Manter a alimentação desativada; a verificação da poção deve considerar explicitamente que o crédito antigo está ativo apesar de D7. Não instalar ou ativar automaticamente por causa desta entrega.
2. Desconectar em intervalo fracionário entre checkpoints e comparar o JSON com o fechamento do lifecycle; repetir troca de personagem, perda de concessão, reconexão e callback antigo. Diferenciar atraso de polling de 2 s do instante físico da conexão. Falhas de persistência só em cópia descartável, nunca em personagens reais.
3. Partir de valores próximos de 30 e 0, observar a passagem no próximo polling de 1 s de HUD/efeitos antes do checkpoint de 10 s; recuperar para >30 e >0, conferir áudio, aura e nome sem mudar nome salvo. Repetir com observador remoto e transição de célula.
4. Executar a matriz obrigatória de criaturas descrita em #1 (lobo do mundo, criatura gerenciada/invocação e jogador; 24/25/26 m, parede, morte e relevância), cruzando dados do host com o efeito visto. Cobertura estática não substitui essa prova.
5. Medir o tick e responsividade no host real com população representativa, sem extrapolar os números sintéticos para SLA. Alimentação/animação e sincronização de dois owners + espectador continuam com roteiro anterior e só podem ser ativadas por decisão do dev sobre D7.

Investigação do lote: `C:\Code\Aetherius - New Systems\Sobrenatural\docs\investigacoes\INVESTIGACAO_17-11_R2_reserva.md`. Próximo passo: Opus encaminhar confirmação Terra das correções e publicar os .md no Acervo; os arquivos estão prontos para essa consolidação, sem push concorrente do worker. Sem reescrita de histórico, mudança nativa, preparo de runtime, instalação MO2 ou jogo.


## Residual R2 #2 — callback posterior à ausência (17/11/2026)

Base `a2e8583`; correção **`a35b0e8`**, autor Sol High, arbitragem Opus após reconfirmação Terra no relatório `C:\Code\Aetherius - New Systems\Sobrenatural\docs\REVISAO-R2_reserva-de-sangue_terra.md`. Terra reconfirmou #3/#4/retry; #2 ainda debitava tempo offline porque `onCharacterInactive` não passava o limite observado. Este residual substitui a afirmação anterior de que o horário do callback sempre seria autoritativo.

**Instante arbitrado: `min(now, offlineAt)` se há ausência observada; caso contrário now.** Aplicado dentro de `close`, não só no wrapper, em `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\supernatural\blood-reserve.js:72–85` (limite em `:78`), com espelho byte a byte em `source\server\gamemode\supernatural\blood-reserve.js`. Intervalo pendente continua fixo em `closingAt` em retries. Assim, callback atrasado, dispose e fechamento explícito respeitam o teto, sem reabrir o débito já fixado. Nenhuma nova leitura de onlinePlayers é usada no callback para presumir reaparecimento.

Reaparecimento **observado** por tick/leitura segue o caminho seguro de `ensure:101–106`: liquida até o offlineAt anterior e reabre o segmento no instante atual, sem offlineAt antigo. Isso limpa o marcador para o próximo fechamento e preserva a exclusão do gap não observado; somente apagar o campo e continuar o relógio antigo cobraria esse gap. Uma sessão nova mantém as proteções contra callback antigo e troca de grant. Sem observação online intermediária, prevalece o primeiro limite de ausência conhecido.

Três regressões com relógio injetado em `overlays\sobrenatural\teste-reserva-sangue.cjs:121,126`:

- t=0 inicia; t=2.000 ms lista vazia/tick; t=3.602.000 ms callback: sangue **99,9888888888889**, equivalente a 2 s. Antes da correção o teste falhou com **79,99444444444444**; log `evidence/reserva-r2-residual-before.log` (exit 1 esperado na reprodução). Callback repetido não cobra novamente.
- Reaparecimento por tick e por get em t=3.602.000 ms: conserva apenas os 2 s anteriores; callback 1 s depois cobra também esse segundo novo, sem manter o offlineAt antigo e sem descontar a hora ausente. Ambos os casos já passavam antes e protegem a limpeza por fechamento/reabertura.

Depois da correção: **24/24** casos dedicados, exit 0 (`evidence/reserva-r2-residual-after.log`). Bateria completa com a mesma seleção `teste-*.cjs` e os cinco arquivos adicionais da seção anterior, `node --test --test-timeout=10000 @testFiles`, exit 0:

```text
1..248
# tests 314
# suites 18
# pass 314
# fail 0
# cancelled 0
# skipped 0
# todo 0
# duration_ms 1053.4729
```

Log `evidence/reserva-r2-residual-final-node.log`. Serviço, painel F7 e revisão F7 executados separadamente, todos com `OK` e **exit 0**; logs `reserva-r2-residual-servico.log`, `reserva-r2-residual-f7.log`, `reserva-r2-residual-revisao-f7.log` na pasta evidence do servidor local. O total anterior de 311 mais três casos novos é 314; o log de reprodução deliberadamente reprovado não é resultado da versão corrigida.

Roteiro futuro adicional em cópia descartável: atrasar a limpeza de personagem após observar ausência e verificar que o fechamento não ultrapassa esse registro; repetir reentrada observada antes do callback, com novo segmento cobrado e gap ignorado. Não equivale a medir o instante físico do socket nem a testar rede/PG/motor. Cliente continua `f57ec7d`, sem mudança nativa/runtime/MO2/jogo. D7 e matriz de criaturas permanecem; confirmação independente residual e publicação do Acervo ficam com o coordenador.

## 17/11 — correções candidatas da revisão R3 de cura

Autor Terra High; revisão independente Sol High `C:\Code\Aetherius - New Systems\Sobrenatural\docs\REVISAO-R3_bloqueio-cura_sol.md` reprovou os dois pontos e pediu correção. Base `15b8174`; alterações candidatas, **aguardam reconfirmação independente** e testes em jogo. `C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\vampire-healing.js` e `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\supernatural\vampire-healing.js` têm SHA-256 `B1335E18C664C44FB16445ECDE4FA22E67E6C2345BA3B1AA81813AE9A691BA1E`.

- **R3-1 aceito:** `66d42d5` exclui MGEF com flag Recover (`0x2`) da classificação de restauração pura, sem excluir indiscriminadamente PeakValueModifier. Os fixtures representativos de Fortificar Vida e de Restaurar+Fortificar permanecem liberados; cura pura ainda é vetada ao vampiro e permitida ao humano. Isto não prova comportamento real do buff/consumo no motor.
- **R3-2 aceito:** `190d4e7` conserva a definição integral na chamada original de `Healing.apply`, preservando o cálculo de alvo/área, e suprime apenas `restore` de Health para o alvo vampiro durante essa chamada síncrona; restaura a propriedade original mesmo se houver exceção. Os testes comparam humano e vampiro para Magicka e Stamina, Self/dirigida, área e NoArea, e preservam Dreno e as regras anteriores. O wrapper temporário depende de `Healing.apply` continuar síncrono; qualquer futura alteração assíncrona exige revisão desta integração. Não foi demonstrado feitiço instalado com a composição sintética da regressão.

`node --test --test-timeout=10000 @testFiles`, seleção da bateria final R2 nesta seção anterior, retornou exit 0: **324 testes, 18 suites, 324 pass, 0 fail/cancelled/skipped/todo**. A bateria suplementar `node --test source/server/gamemode/magic/tests/records-ctda.test.cjs source/server/gamemode/core/survival-consumables.test.js` retornou exit 0, **14/14**. Todos são testes Node com fixtures, sem PostgreSQL, cliente, rede, modpack ou motor; não houve teste humano em jogo.

Pendências: reconfirmação pelo revisor Sol, executar o roteiro LOCAL de R3 (pura, Fortificar, misto, área/direto/concentração, Dreno, regeneração e humano, comparando inventário/ledger/UI); o veto atual não garante devolução da unidade nem conciliação PG. D7, cliente, nativo e MO2 não foram alterados. O coordenador consolida Acervo e demais documentos.

## 17/11 — HUD vertical de Reserva de Sangue (candidato visual)

Autor Terra High. Pedido do dev: indicador vertical no canto inferior direito, cheio de baixo para cima, sangue e moldura estilizados conforme `C:\Code\Aetherius - New Systems\Sobrenatural\docs\DIRECAO-DE-ARTE.md`, `C:\Code\Aetherius - New Systems\referencia\icones estilizados.png` e `icone lobisomem bruxa e vampiro.png`. A versão anterior era horizontal; o novo desenho vetorial usa contorno fino marfim, presas/gota e pequenos losangos carmesim, reservatório escuro e sangue com camadas diagonais e brilho de superfície. Estado `<=30` realça detalhes carmesim e `0` mantém contorno seco, com realce Feral; estes são apenas sinais visuais, independentes dos efeitos existentes.

Arquivo fonte: `C:\Code\Aetherius - Servidor Local\client-source\client\index.html`, commit cliente `7c5601a`; patch incremental `C:\Code\Aetherius - Servidor Local\overlays\cliente-local\010-hud-sangue-vertical.patch`, após `009-racial-origin-conservative.patch`. O contrato `setBloodReserveHud({v:1,blood,state})`, validação, clamp, privacidade, timeout e lógica de servidor permanecem; foi alterada apenas a apresentação (altura em vez de largura, classes CSS). Não foram criadas nem copiadas pastas MeridianUI e não houve alteração nas cópias MO2; aplicar e instalar somente depois de revisão, aprovação e roteiro apropriado.

Prévia autocontida: `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\preview-reserva\index.html?blood=60` (trocar o parâmetro por 100, 60, 30, 10 ou 0). Capturas Orca em `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\preview-reserva\reserva-<largura>x<altura>-<sangue>.png`, para cada valor solicitado nas duas resoluções 1920x1080 e 1280x720. A prévia usa fundo neutro simulado e a mesma marcação/CSS/handler do HUD, não representa uma captura do jogo.

Validação: Orca renderizou dez combinações, confirmou `aria-valuenow`, altura 100/60/30/10/0 e classes critical/feral; as dimensões das dez imagens foram verificadas. `node --test overlays/sobrenatural/teste-sangue-hud.cjs`: **2/2**, sem falhas; `git apply --reverse --check` confirmou o patch no cliente e `git diff --check` passou. Compatibilidade com o CEF/escala real, legibilidade em cena clara/escura e colisões com outras HUDs ainda precisam de jogo; revisão de outro modelo e aprovação visual do dev pendentes. Não foi executado jogo, deploy ou instalação no MO2.


## 17/11 — D3: Beber Sangue direto, sem consentimento, ambos sem elmo

Autor Sol High, decisão explícita do dev transmitida pelo Opus (`msg_68491b35b30f`), após correção P2 de autoria racial. Commit raiz **`b4d161c8a63553f0dfeca9a3defd0d1ac7f0aae8`** no laboratório `C:\Code\Aetherius - Servidor Local`. Nenhum arquivo oficial/MO2/jogo/runtime foi escrito ou iniciado.

### Fluxo e migração

`source/server/gamemode/supernatural/feeding.js:105–123` valida menu/token/alvo e elegibilidade, confere equipamento dos **dois atores no servidor**, grava sessão RUNNING e inicia apresentação imediatamente. Não pede aceite da vítima. Elmo recusa com “Retire seu elmo...” ou “O alvo precisa retirar o elmo...”, dirigido ao vampiro; capuz permitido. Consulta/classificação indisponível recusa com orientação para retirar a peça e tentar novamente. A opção pode continuar visível antes de vestir/remover o elmo; a decisão final é feita no clique, com equipamento corrente. Falha inicial não consome token nem grava sessão/recompensa.

Removidos requireConsent/inviteMs/reply e UI/rota `feed-reply` nos scripts SkySocial (`source/server/gamemode/skysocial/src/{ui,system,client}.js`). Não há foco/estado de convite de alimentação novo; convite de grupo/negociação não foi removido. O boot ainda reconhece **apenas para migração** um journal antigo INVITED e o cancela com `legacy_consent_removed`, sem animação/recompensa ou participante órfão. RUNNING interrompido cancela; SETTLING/RECONCILE continuam em quarentena sem replay. A chave antiga `AETHERIUS_VAMPIRE_FEED_REQUIRE_CONSENT` não é mais lida e não reativa consentimento.

Conservados: flag **`AETHERIUS_VAMPIRE_FEED_ENABLED=false`**, alcance 2 m (140 unidades no padrão 70/m), 10 s de sequência, +100 sangue/+200 cura, 20% da máxima da vítima com piso não letal, cooldown de 60 min por characterId da vítima, PvP/zonas/política social, sessão exclusiva, token autenticado, identidade/grant, locks, dano/readback, intenção durável anterior à liquidação e quarentena manual. D7/atomicidade não foram alteradas. A regra de equipamento é validada **no início**; não se introduziu punição ou nova transação por equipar peça durante os 10 s.

Código novo `source/server/gamemode/supernatural/feeding-headgear.js`, idêntico ao overlay `overlays/sobrenatural/files/server/gamemode/supernatural/feeding-headgear.js`. `feeding.js` também idêntico ao espelho. Alterações SkySocial no patch incremental `overlays/sobrenatural/017-alimentacao-sem-consentimento.patch`, após 016; reverse-check passou.

### Critério verificável de equipamento

Somente entradas `worn/wornLeft` de `mp.get(actor,'equipment').inv.entries`, lidas do servidor. Para cada ARMO, lê o record vencedor via `mp.lookupEspmRecordById`, slots BOD2/BODT e referências KWDA convertidas por `toGlobalRecordId`; compara **EditorIDs de keyword/record**, não texto renomeado/localizado pelo jogador.

1. Capuz com EditorID contendo `Hood`/`Cowl`, **sem** `Helm`/`Mask`/`Masque`, e classificado como cabeça por slots/keyword, é permitido mesmo se tiver ArmorHelmet. A exceção é necessária para capuzes blindados reais do modpack.
2. Demais peças com keyword **ArmorHelmet** são elmos e recusadas, inclusive elmo com capuz (`ArmorDawnguardHelmetHooded`).
3. Sem ArmorHelmet, ClothingHead, ClothingCirclet ou ArmorJewelry permitem roupa de cabeça/tiara/joia. Equipamento de outra parte do corpo não impede. Peça em slot de cabeça/cabelo/tiara/orelhas/máscara sem classificação suficiente é recusada como desconhecida; record, slots, equipamento ou keyword indisponíveis falham fechados.
4. Cache só da classificação estática bem-sucedida por baseId; erros temporários não são cacheados. Não se confia em flags fornecidas pelo menu nem se muda equipamento.

**Casos ambíguos explicitados:** máscara marcada ArmorHelmet é recusada; capuz mascarado de roupa com ClothingHead é permitido, pois a regra autorizada trata elmo, não toda cobertura facial. Coroa/tiara classificada ClothingCirclet/ArmorJewelry/ClothingHead é permitida; coroa com ArmorHelmet é recusada. Um `Hood...Helmet` é elmo, não ganha a exceção lexical de capuz. Mods com EditorID/keywords incorretos ou outra convenção podem precisar de catálogo específico; a regra é reprodutível, não uma análise da geometria. Não se declarou cobertura de toda máscara/capuz do modpack nem keywords adicionadas somente em runtime fora dos records do servidor.

### houseCARL somente leitura — evidência dos vencedores

Instância `C:\modOrganizer`, perfil ativo **AETHERIUS - GRAFICO - QUALIDADE**, 436 plugins, epoch `e2-ac8a903449c25337`, houseCARL `2.0.0+5e3c744862bc17c85ebe850830603a9a3e6f0779`. Schema ARMO/BodyTemplate consultado na skill mutagen-reference antes dos records. Inventário em memória de 11.926 ARMO; amostras abaixo lidas integralmente com nomes resolvidos, sem mudar perfil/modpack:

| FormID estável e EditorID | Vencedor / evidência | Resultado |
|---|---|---|
| `012E4D:Skyrim.esm`, ArmorIronHelmet | ITEM BALANCE; ArmorHelmet; slots 31/42 | Recusa |
| `036A45:Skyrim.esm`, ClothesRobesGreybeardHood; `07BC1A:Skyrim.esm`, ClothesRedguardHood | WACCF; ClothingHead; slot 31 | Permite |
| `000884:ccvsvsse003-necroarts.esl`, HauntingSpiritHood; `000908:ccvsvsse003-necroarts.esl`, NecroHoodMask | Plugin definidor; ClothingHead; slots 31/42 | Permite roupa/capuz mascarado |
| `012AE9:Apachii_DivineEleganceStore.esm`, NightshadeHoodNE; `02619E:Apachii_DivineEleganceStore.esm`, Geel_HoodWhite | Plugin definidor; ClothingHead; slots 31/41 | Permite |
| `8F3CD4:HammetDungeon01.esm`, Gloomfall_NecromancerHood01 | Plugin definidor; ClothingHead; slots 31/42 | Permite |
| `0BA99B:NB-FurHoods.esp`, ArmorFurHoodLt; `0BA99C:NB-FurHoods.esp`, ArmorFurHoodHv | ITEM BALANCE; **ArmorHelmet**, leve/pesado, slots 31/41/43 | Permite pela exceção verificável de capuz |
| `000EB4:CeykyndArmor.esp`, ArmorCeykyndHood | ITEM BALANCE; **ArmorHelmet**, slots 31/41/42/43 | Permite capuz |
| `000807:[FB] Master Thief Armor.esp`, 000_Master_Thief_Hood | ITEM BALANCE; **ArmorHelmet**, slots 31/42 | Permite capuz |
| `000096:cheddars_mashups.esl`, ArmorDawnguardHelmetHooded | ITEM BALANCE; ArmorHelmet; slot 30 | Recusa elmo com capuz |
| `00005E:cheddars_mashups.esl`, ArmorSteeledVigilantHood | ITEM BALANCE; ClothingHead, HeavyArmor; slots 31/42 | Permite capuz |
| `012AF0:Apachii_DivineEleganceStore.esm`, OperatorMaskNE | ClothingHead; slot 55 | Permite máscara de roupa |
| `0166D6:Skyrim.esm`, ClothesCirclet01; `01A28A:Apachii_DivineEleganceStore.esm`, Flowercrown | WACCF / plugin definidor; ClothingCirclet+ArmorJewelry / ClothingHead; slot 42 | Permite tiara/coroa de roupa |

A relação documenta as amostras examinadas, não uma auditoria de assets ou de todos os ARMO. Sem BSA/NIF/DLL ou output gerado como base; nenhuma escrita houseCARL.

### Validação e hashes

`node --test overlays/sobrenatural/teste-alimentacao.cjs`: **52/52**, incluindo os 24 cenários anteriores adaptados, 26 combinações de peça × vampiro/vítima, equipamento alterado entre menu/clique e indisponibilidade. Inclui conversão de KWDA por mapa local→global, início RUNNING direto, recusa do reply antigo, migração INVITED, cooldown, dano não letal, origem/identidade, falhas de journal e quarentena. Bateria ampla integrada **381/381** e Core **39/39**, scripts serviço/F7/revisão F7, TypeScript e webpack sem deploy passaram. O teste não executa animação/áudio/CEF nem comprova atomicidade nativa.

SHA-256 bruto: feeding.js `d568108af5f268baba085af08c4a7090c3be3451f35eb60260f0df3267c2adc3`; feeding-headgear.js `95d718bdcf0db98def8687ef1c4e20c89cc81a31bd4c97f15da4cbe6ee3fd1c9`. Fonte e espelhos iguais. Revisão independente desta implementação e testes humanos de menu, elmos/capuzes dos dois jogadores e observadores permanecem pendentes. Ativação da flag segue exclusiva decisão do dev.

## 17/11 — Revisão independente Sol da HUD vertical Terra

**Veredito: APROVADA no escopo de código/apresentação e das prévias, com ressalvas de validação no CEF/jogo.** Revisor Codex, papel Sol High; autora Terra High. Fonte cliente `C:\Code\Aetherius - Servidor Local\client-source`, commit **`7c5601aa14814a05e006161b56f495bbac5b6a17`**; patch/prévia/testes/docs do Git raiz **`8060a1081bf2b3eeffd0d1c78279b610d92c07ba`**. Revisão somente leitura da implementação e capturas; esta seção é a única entrega da revisão. Nenhum arquivo de HUD/teste/prévia foi editado pelo revisor.

- **Escopo e contrato confirmados:** diff do cliente altera só `client/index.html` (24 adições/7 remoções), não pastas MeridianUI, servidor, permissões ou regra de sangue. Permanece `setBloodReserveHud({v:1,blood,state})`, clamp numérico, rejeição de valor inválido, aria progressbar, limpeza e timeout de 3,5 s. Propriedade continua apenas para o dono; a revisão de apresentação não altera nem audita todo o domínio de sangue.
- **Preenchimento:** `.blood-fill` ancorada bottom:0, height derivada de blood, transição de 250 ms; fluxo visual de baixo para cima. Em 30 ativa critical; em 0 altura 0 e feral com moldura seca. Os pequenos acentos vermelhos ainda visíveis em 0 são ornamentos, não líquido. `html.aetherius-browser-hidden ... !important` continua vencendo o display do handler e pointer-events:none não captura clique.
- **Prévias conferidas:** comparei em memória CSS, markup e handler de `overlays/sobrenatural/preview-reserva/index.html` com o recorte de `client/index.html`: três iguais. Verifiquei as dimensões das dez PNGs. Inspecionei visualmente 1280×720 em **0/30/100** e 1920×1080 em 60. Não vi corte, sangue vazando da moldura ou preenchimento invertido nessas amostras. Em 1280×720, caixa fixa 74×220 com right 32/bottom 34 ocupa x=1174–1248, y=466–686, dentro da tela. A prévia é fundo neutro; não demonstra colisão com minimapa/HUDs no jogo nem legibilidade em neve/cena clara.
- **CEF/performance:** SVG pequeno e estático, sem assets remotos, canvas, vídeo, observadores ou loop de animação novo. Handler cacheia dois nós e mantém um timeout, com atualizações já limitadas a aproximadamente uma por segundo pelo caminho existente. Gradientes, clip-path, sombras e transição height geram pintura/layout em área pequena; não medi FPS/GPU/CPU no CEF. O setInterval da prévia é exclusivo dela, não foi levado ao produto. É plausível custo baixo, não uma medição de desempenho ou prova de suporte do CEF instalado.
- **Testes:** o commit 8060a10 adapta o teste anterior horizontal para height/feral e preserva testes de privacidade/watchdog. A primeira bateria deste trabalho coincidiu com a edição paralela ainda não concluída e falhou na expectativa antiga flex; com o commit final presente, `teste-sangue-hud` passou **2/2** e está incluído nos **381/381** finais. Reverse-check do patch 010 passou; nenhuma expectativa foi relaxada pelo revisor. TypeScript/build passaram como validação geral, sem deploy.

Não encontrei achado bloqueante no delta de apresentação. Pendente aprovação visual do dev e teste do CEF real em 1280×720/1920×1080, cena clara/escura, escalas de UI e concorrência com os outros HUDs; ausência de teste em jogo não foi convertida em homologação. O `state` do payload não foi transformado em nova regra; classes numéricas 30/0 seguem apresentação existente. Bundle final deste trabalho também contém as correções P2 de Sol e a HUD Terra, SHA-256 `955b534355c38694860a123c45d721c15661987205636aaa0a8aff42309658c4`, compilado **não instalado**.

## 17/11 — vinheta compartilhada de Sede Crítica e alimentação

Autor Terra High; pedido do dev de aplicar à Sede Crítica a mesma vinheta da alimentação. Candidato `f4236da` em `C:\Code\Aetherius - Servidor Local`; revisão cruzada e jogo pendentes. O visual escolhido é o da alimentação (`feeding-client.js`, `radial-gradient(ellipse,rgba(130,0,15,.06),rgba(130,0,15,.55))`), não o shader público vermelho Feral de `blood-effects-client.js`. O gradiente existente foi movido para `supernatural\blood-vignette-client.js`, chamado pelo cliente privado da alimentação e pelo cliente privado `aetheriusBloodEffects`.

A camada única tem donos independentes `critical` e `feeding`, não soma duas vinhetas e não desaparece quando apenas uma origem encerra; cada dono tem watchdog de 3,5 s e alimentação também conserva watchdog de 3 s. Os pacotes de sede já vêm do servidor ao owner, com `peek` projetado e tick de 1 s; não houve novo payload público nem timer servidor. Entrada e saída seguem o limiar existente de batimento/visão: padrão entra em `<=30` e sai `>30`; o parâmetro de teste pré-existente `AETHERIUS_VAMPIRE_CRITICAL_EXIT=35` continua afetando todos esses efeitos igualmente, D2 ainda não foi encerrada. Feral vivo em 0 continua sob Sede Crítica conforme a regra anterior; morte/abatimento, ausência do ator/3D, remoção de maldição e watchdog desligam o dono de sede, sem desligar uma alimentação ainda válida. Reentrada em relevância renova a camada sem piscada intencional em cada tick.

Fontes idênticas no `C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\` e `overlays\sobrenatural\files\server\gamemode\supernatural\`: `blood-effects-client.js`, `feeding-client.js`, `blood-vignette-client.js`. A flag de alimentação continua desligada por padrão, e não se altera D7, HP, dados da reserva, cliente compilado, nativo ou modpack. `node --test --test-timeout=10000` na bateria R2 ampliada: **384/384**, 18 suites, 0 fail/cancelled/skipped/todo; recorte de efeitos/alimentação/vinheta **59/59**. Os testes simulam DOM/packets, watchdogs, morte, 3D, origem dupla e cancelamento; não comprovam CEF, cor em cena, sincronização real ou ausência de flicker no motor.

Roteiro após revisão: atravessar 30 e 30,01 com limiar padrão, manter >10 s; ir a 0 (continua crítica) e recuperar; testar morte, desconexão, remoção e perda de 3D. Em ambiente autorizado de alimentação, testar as duas ordens de término, cancelamento e vítima em tela preta, garantindo um único gradiente contínuo no vampiro e privacidade para observadores. Nenhum servidor/jogo/MO2 foi iniciado ou alterado.

## 17/11 — D7: decisão do dev e fronteiras de implementação

O dev aceitou a garantia conservadora. Falha, crash ou consumo ambíguo descartam a recompensa. A poção pode ser perdida. A alimentação interrompida deve ser encerrada automaticamente e jamais repetir dano, cura ou crédito em recuperação. A autorização da flag vale para `config/local.env` após a implementação. O padrão do código continua false.

### Triagem em `C:\Code\Aetherius - Servidor Local`, base `1088d0b`

- **Poção** — `source/server/gamemode/supernatural/blood-reserve.js:161–176` acrescenta sangue dentro do callback síncrono. `source/server/cpp/server_guest_lib/MpActor.cpp:533–558` só chama `RemoveItem` depois desse callback, efeitos e notificação dos vizinhos. `source/server/cpp/server_guest_lib/gamemode_events/EatItemEvent.cpp:22–30` fornece ator e item, sem ID ou recibo pós-baixa. `source/server/gamemode/survival-service.js:882–934` inicia uma baixa PG independente do resultado nativo, com chave de idempotência nula e condicionada à classificação do item. Um commit PG isolado prova alteração no ledger, mas não comprova remoção nativa daquele evento. O readback de `inventory` é da memória do actor, conforme `source/server/cpp/addon/property_bindings/InventoryBinding.cpp:8–10`. Comparar a contagem depois de um timer não fecha atribuição por evento nem persistência durante crash.
- **Beber Sangue** — `source/server/gamemode/supernatural/feeding.js:129–152` grava SETTLING e cooldown, aplica dano à vítima, confere readback, aplica cura ao vampiro, confere readback, grava sangue em outro arquivo e só então marca SETTLED. Se cair depois de alterar HP e antes de registrar a observação, o journal legado só tem o antes e a intenção. Mesmo com leitura em recuperação, HP posterior não permite atribuir a diferença à sessão. Se cair depois de gravar sangue e antes de SETTLED, também não há recibo legado que permita distinguir o crédito dessa sessão de outras alterações de saldo.
- **Arbitragem solicitada** — confirmar se é para preservar dano e cura já aplicados, manter o cooldown durável e registrar resultado desconhecido quando faltar readback confirmado, sem compensação de HP ou ajuste de saldo legado. Para novos eventos, o registro terminal de saúde deve preceder a gravação final do crédito, que encerra a operação. Se a base não fornecer comprovação durável e atribuível de consumo, suspender o +50 da poção preservando sua cura nativa satisfaz a garantia de segurança, com perda da recompensa até existir recibo.
- **Estado deste registro** — triagem documental, sem mudança executável, configuração, teste em jogo, modpack ou repositório oficial. O comportamento anterior continua até a correção. Revisão independente pendente após implementação.

## Reconfirmação Terra — alimentação D3

Revisão independente Terra High em `C:\Code\Aetherius - Servidor Local`, commit `b4d161c` e docs `8b7d7d4`, após `f4236da` da vinheta; **aprovado no escopo de protótipo desativado e testes, sem homologar D7 ou funcionamento em jogo**. `source\server\gamemode\supernatural\feeding.js:7` só ativa com `AETHERIUS_VAMPIRE_FEED_ENABLED` igual a `true`; o clique autenticado verifica equipamento de ambos antes de gravar RUNNING, e a rota antiga de consentimento foi removida. O headgear em `feeding-headgear.js` lê equipamento e records no servidor, traduz KWDA, permite Hood/Cowl sem Helm/Mask, veta ArmorHelmet e recusa cabeça sem classificação ou dados indisponíveis. É regra lexical/records, não exame da geometria; as exceções de máscara, capuz blindado e coroa, e ambiguidades mod específicas já constam na seção D3 acima. A checagem ocorre no início, não é reaplicada durante os 10 s; isso corresponde ao escopo documentado da decisão e precisa ser considerado nos testes em jogo.

O journal anterior permanece com `SETTLING`/`RECONCILE` bloqueando ambos characterIds e sem replay de recompensas; sessões RUNNING e INVITED legado são canceladas no boot. D7 segue sem atomicidade e não é resolvida por esta revisão. Os espelhos source/overlay `feeding.js` e `feeding-headgear.js` têm SHA-256 iguais respectivamente `D568108AF5F268BABA085AF08C4A7090C3BE3451F35EB60260F0DF3267C2ADC3` e `95D718BDCF0DB98DEF8687EF1C4E20C89CC81A31BD4C97F15DA4CBE6EE3FD1C9`; reverse-check do patch 017 passou.

Bateria integrada posterior à vinheta: **384/384**, 18 suites, 0 falhas; Core **39/39**, serviço/F7/revisão F7 OK e exit 0, TypeScript e webpack sem deploy passaram. Inclui os testes de ambos atores, capuzes/elmos, token, flag e falhas de journal, mas usa mocks. Ativar a flag e homologar efeitos, equipagem, rede, áudio/animação, HP e recuperação ainda requer decisão do dev e jogo; não houve instalação ou início de runtime nesta revisão.

## 17/11 — batimento vanilla contínuo na Sede Crítica

Autor Terra High, candidato `17ae47c`, revisão independente pendente. Por decisão do dev, `C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\blood-effects-client.js` e o espelho `overlays\sobrenatural\files\server\gamemode\supernatural\blood-effects-client.js` passaram a usar `04DAAF:Skyrim.esm` (`UIHealthHeartbeatALP`), em substituição a `000809:AETHERIUS - LOBISOMEM.esp` somente na Sede Crítica. Dados de record fornecidos pelo dev a partir de houseCARL: descritor `04DAAD`, arquivo `Sound\FX\UI\Health\Heartbeat\UI_Health_Heartbeat_A_LP.wav`, loop nativo. Não foi feita consulta nova ao MO2 ou gravação nele.

O cliente inicia uma instância no primeiro update válido, mantém seu handle enquanto o pacote crítico permanece válido e não reinicia aos 9,6 s; saída da sede, morte/abatimento, ausência de 3D, remoção ou watchdog chamam `stopInstance` apenas sobre essa instância. Se `stopInstance` lançar exceção, conserva o handle para retry e impede outra instância; caso o som não possa iniciar, novas tentativas ficam limitadas a uma por segundo. O servidor conserva o limiar e projeção existentes (padrão `<=30`, saída `>30`; 0 Feral continua crítico), sem mudança de contrato. A transformação forçada do lobisomem, seu som e seus arquivos não foram alterados.

`node --test overlays/sobrenatural/teste-sangue-efeitos.cjs overlays/sobrenatural/teste-vinheta-sede.cjs`: **13/13**, exit 0; cobre 25 s de pacotes sem reinício, formID, recuperação/reentrada, morte, 3D, watchdog, abatimento e falha de stop sem duplicação. Source e overlay têm SHA-256 bruto `6582F846A944681FA91DFA3C18CFC8A325916BF58A5C38DDE7B071EC6FBBDD3E`. Bateria ampla simultânea a outro desenvolvimento D7 retornou em duas execuções **383/390** e **394/400**, falhando testes de alimentação/contratos em alteração concorrente; não foi assumido que a bateria geral esteja aprovada nem que essas falhas se devam ao áudio. Runtime de som/loop, reconexão real, mudança de dispositivo e sincronismo com vinheta seguem por validar em jogo após revisão cruzada; servidor, jogo e MO2 não foram iniciados ou alterados.

## 17/11 — D7 implementada no servidor local

Arbitragem Opus após a triagem `2a5434e`, em nome do dev. Preservar alterações de vida e saldo legado, manter o cooldown já gravado e encerrar automaticamente com a evidência disponível. Na Poção de Sangue, aceitar o `EatItem` observado no processo vivo após a verificação de posse como prova operacional. Descartar crédito em falha ou ambiguidade e em qualquer recuperação. Candidatos `0ec39d2` (poção) e `2088225` (alimentação) em `C:/Code/Aetherius - Servidor Local`, com fonte base `310cb59` e overlays do laboratório.

### Poção de Sangue

- `source/server/gamemode/supernatural/blood-reserve.js:118–149,184–201` identifica `18ef3:Dawnguard.esm` como ALCH `DLC1BloodPotion` e captura sessão, personagem e grant do vampiro. Cada invocação nativa aceita tem um recibo privado em memória com UUID. O mesmo callback só pode solicitar uma baixa e terminar uma vez. Uma segunda unidade realmente consumida recebe outro UUID. O host não fornece ID de rede para deduplicar pacotes. Cada invocação nativa que passa a posse representa um consumo aceito pelo contrato. Veto ou retorno inválido na cadeia não autoriza o crédito.
- `source/server/gamemode/supernatural/vampire-healing.js:39–47` encaminha o recibo ao dono do ledger. `source/server/gamemode/survival-service.js:882–949` usa a transação existente com `blood-potion:<UUID>` no campo único `inventory_transactions.idempotency_key`. Para item rastreado, exige confirmação do commit PG. Se a consulta PG bem-sucedida informa item fora do estoque rastreado, como item recolhido do mundo, a prova é o próprio EatItem e não se fabrica baixa PG. Qualquer falha, inclusive resposta de commit perdida ou JSON indisponível depois do commit, descarta o recibo. O crédito exige a mesma sessão e o mesmo grant. Reinício não pesquisa o ledger para conceder sangue.
- A recompensa normal é +50 até 100. A cura de 100 permanece com o efeito do item e o JS não remove uma segunda unidade. Como `MpActor.cpp:533–558` emite EatItem antes da baixa nativa, essa prova operacional depende da decisão explícita do dev. Não há recibo nativo pós-baixa nem transação entre os três estados (inventário SkyMP, PG e reserva JSON).

### Beber Sangue

- `source/server/gamemode/supernatural/feeding.js:163–238` grava SETTLING e cooldown antes de alterar vida. Aplica o dano não letal e confere a vida da vítima. Quando precisa curar, grava o readback da vítima e a intenção de cura antes da chamada seguinte. Confere HP após cura ou registra `not_needed` quando a vida já estava cheia. `CLOSED` grava o resultado de vida e encerra a sessão antes da última gravação do sangue. Falhar antes do commit final permite conservar dano/cura sem prêmio. `CLOSED` indica a saúde finalizada e sangue permitido apenas naquele processo. Passar pela gravação de sangue encerra a operação, sem outra gravação necessária.
- `feeding.js:38–75` muda SETTLING ou RECONCILE legado para `INTERRUPTED`. Mantém cooldown e preserva o saldo legado, que pode ter sido alterado pelo fluxo anterior. Marca cada efeito como `confirmed`, `unknown`, `not_attempted` ou `not_needed` conforme o checkpoint, com HP observado quando disponível. Um readback divergente é registrado como observação de resultado desconhecido. Ao recuperar, não relê HP para imputar variação à sessão, não repete efeitos e não tenta compensar. Se o disco falha, os locks são liberados e apenas o encerramento do journal volta a ser tentado a cada 10 s. O SkySocial não tem mais quarentena persistente de participantes interrompidos.
- `config/local.env` (ignorado pelo Git) está com `AETHERIUS_VAMPIRE_FEED_ENABLED=true`, verificado via o mesmo parser dotenv do launcher. `feeding.js:9` conserva default false. O runtime existente precede este lote e precisa receber o gamemode atualizado para que a flag tenha efeito. Nenhum processo de servidor, jogo, build de cliente, deploy, MO2 ou código nativo foi executado nesta rodada.

### Validação da D7

- `node --test` na seleção integral da validação R2 acima, com os testes de `17ae47c` incluídos: **410/410, 19 suites**, com flag ausente e com flag true, exit 0 nos dois. Core **39/39** e scripts serviço/F7/revisão F7 deram OK. `teste-alimentacao.cjs` isolado **62/62**.
- Injeções de poção cobrem consulta de estoque, conexão, início de transação, delta, inserção no ledger, commit rejeitado, commit efetivo com resposta perdida, falha JSON, veto e troca de grant. Conferem que reiniciar a reserva não aplica crédito pendente, que a mesma confirmação não credita duas vezes e que o host pode consumir sem recompensa. Injeções de alimentação cobrem preparação, veto de dano, falhas e variação de readback antes/depois de dano e cura, checkpoints, gravação terminal e crédito final. Snapshots dos três checkpoints reproduzem reinício, migração legada, queda de disco no boot e retentativa de journal sem efeitos.
- Fonte e overlays espelhados com SHA-256 bruto igual. Patch `018-d7-consumo-pocao.patch` aplica após 013 às duas rotas de survival e reproduz o fonte byte a byte após normalizar a terminação de linha. Reverse-check no checkout local passou. Falta revisão independente da D7 pelo outro modelo e teste humano LOCAL com PG, inventário, dois participantes e um espectador. Os testes de falha e reinício usaram dados descartáveis.

## Revisão independente Sol High — vinheta e batimento da Sede Crítica

Revisados `f4236da`/docs `1088d0b` e `17ae47c`/docs `f02c8ab`, em `C:/Code/Aetherius - Servidor Local`. **Aprovado para cliente iniciado do zero no recorte de código e testes Node, com ressalva de atualização em cliente já aberto.** A revisão não alterou os efeitos.

- `source/server/gamemode/supernatural/blood-effects.js:8,27–39` publica a sede só ao dono, com entrada em 30, saída acima de 30 e Feral vivo em 0 ainda crítico. O parâmetro preexistente de saída pode mudar o limiar, mas não há override em `config/local.env`. Morte/abatimento e retirada de maldição limpam o estado no próximo tick. Em desconexão sem pacote de limpeza, o watchdog do cliente vence em 2,5 s e o CEF tem fallback por dono de 3,5 s.
- `blood-vignette-client.js:3–15` mantém um único div com watchdogs independentes. O pacote periódico só renova o prazo. Cessar alimentação não apaga a sede e cessar a sede não apaga a alimentação. Os testes cobrem ambas as ordens e a perda de pacotes. Cor, continuidade no CEF real e reconexão física ainda aguardam o jogo.
- `blood-effects-client.js:9–20` em sessão nova guarda um único handle do `04DAAF:Skyrim.esm`. Cobre 25 s de pacotes sem reinício aos 9,6 s, encerra ao sair da sede, morrer, perder o 3D, abater ou vencer o watchdog e mantém o handle se o stop lançar erro. A forçada usa armazenamento `aetheriusCompulsionFx`, handle `heartId` e `000809:AETHERIUS - LOBISOMEM.esp` próprios, em `forced-werewolf-client.js:9–27`. O candidato da sede não toca a instância da forçada.
- **Achado de atualização em sessão: armazenamento de versão anterior sobrevive à substituição de scripts.** `client-source/client/src/services/services/gamemodeUpdateService.ts:116–136` troca funções compiladas e não recria `sp.storage.aetheriusBloodCriticalFx`. Como `blood-effects-client.js:6–14` só registra helpers e callback se o objeto não existe, simulei a substituição preservando storage e registro de update. De `f4236da^` para a fonte atual, com amostra nova, houve `TypeError: fx.vignette is not a function`, sem chamada de vinheta. De `1088d0b` para `17ae47c`, ficaram um callback antigo e duas reproduções de `0x809` após 12 s, com stop entre elas. Controle em storage vazio emitiu a vinheta e tocou `0x4DAAF`. Rodar o teste humano em uma sessão nova do cliente. Se atualização com cliente aberto for necessária, a versão do estado/callback e a limpeza da instância anterior precisam de correção e nova revisão.
- **13/13** no recorte `teste-sangue-efeitos.cjs` + `teste-vinheta-sede.cjs` e **410/410** na bateria integral final, incluindo a suíte da forçada e a D7, em ambos os modos da flag. O teste manual Node de substituição foi leitura de commits e simulação, sem CEF, áudio real ou alterações no cliente.

## Revisão independente Terra — D7 e migração de efeitos no cliente aberto

Revisora Terra High, 18/11/2026, base `C:\Code\Aetherius - Servidor Local` `8225ac6`, código D7 `0ec39d2`/`2088225`; `source` é checkout `310cb59` com overlays aplicados. **D7 aprovada com ressalvas no escopo do contrato operacional autorizado e testes**, sem alegar atomicidade real ou validação no jogo. A Poção de Sangue tem recibo UUID volátil por `EatItem`, claim único e `spent` antes da conclusão; ledger confirmado ou consulta positiva de item não rastreado permite +50 somente na mesma sessão/grant (`source\server\gamemode\supernatural\blood-reserve.js:118`–`:144`, `:184`–`:197`; `source\server\gamemode\survival-service.js:898`–`:945`). O evento aceito é prova operacional expressamente autorizada, não confirmação nativa pós-baixa; em reinício não existe replay de recibos. Falha de consulta/PG, commit ambíguo ou gravação da reserva é descartada nos testes; consumo e cura nativos podem ocorrer sem prêmio.

`source\server\gamemode\supernatural\feeding.js:38`–`:73,157`–`:229` conserva cooldown durável no preparo, registra intenção/readbacks, fecha HP antes do último commit de sangue e encerra interrupções sem replay/compensação; `CLOSED` não causa crédito no boot. RESULTADOS `unknown` em cenários de queda são informação de incerteza, não prova de HP. O `config/local.env` ignorado pelo Git contém a flag true, enquanto default do código é false; não foi alterado nesta revisão. O callback nativo e o ledger PG seguem separados, e falhas físicas ou de persistência fora dos modelos de teste não foram homologadas.

Executei as injeções D7 já existentes em `survival-service.test.js` e `teste-alimentacao.cjs` com `--test-name-pattern D7`: **24/24**, exit 0. Incluem hasItem, conexão, begin, delta, ledger, commit, commit com resposta perdida, JSON, falhas de HP/readback e checkpoints/boot; as outras condições, incluindo veto e troca de grant, foram executadas na bateria completa. Nas duas configurações explícitas de flag false e true, bateria integral posterior à correção de hot-reload: **413/413** em cada, 19 suites, exit 0 (410 anteriores + 3 testes novos); Core **39/39**, serviço/F7/revisão F7 OK e exit 0. São mocks e dados descartáveis; resta teste humano com PG/host reais, inventário, cliente e observador.

### Correção candidata de hot-reload do storage legado

Autor Terra High, commit `93d991a` em `C:\Code\Aetherius - Servidor Local`; requer revisão por outro modelo. `source\server\gamemode\supernatural\blood-effects-client.js` e o overlay são idênticos, SHA-256 `8392CF48D8DEB4549FA066694C568FAD3AE2C2791B2A0B243DC355F70DE27150`. O objeto de storage passa a ter `version:2`. Ao receber objeto antigo, o novo handler interrompe o handle antigo, esconde sua camada crítica, invalida o pacote e substitui o objeto; callbacks antigos observam identidade diferente e não voltam a iniciar o som. Se o stop lançar erro, mantém o objeto anterior e não cria nova instância, tentando na próxima atualização.

Os três testes novos cobrem estados anteriores à vinheta e ao heartbeat vanilla, callback antigo vivo, stop falho e retry; recorte efeitos/vinheta **16/16**. Simulação adicional em memória carregou as implementações reais `f4236da^` e `1088d0b` via `git show`, reteve storage/callbacks, iniciou 0x809, migrou, parou o handle antigo e iniciou 0x4DAAF com storage v2 e vinheta ativa. Nenhum cliente, servidor ou jogo foi iniciado; é necessária validação em CEF/áudio real com atualização de scripts durante sessão, logout e falha de stop, além da revisão independente.

## 18/11 — Reconfirmação Sol High da migração de Sede Crítica

Revisão independente de `93d991a` e da documentação `a4b1450`, em `C:/Code/Aetherius - Servidor Local`. **Reprovada para atualização em cliente aberto: a migração normal passou, mas uma exceção no stop pode deixar dois sons ativos.** O fonte `source/server/gamemode/supernatural/blood-effects-client.js` e o overlay têm SHA-256 bruto `8392CF48D8DEB4549FA066694C568FAD3AE2C2791B2A0B243DC355F70DE27150`.

- Repeti a reprodução original com o JavaScript de owner serializado de `f4236da^`, `1088d0b` e `17ae47c`, obtido com `git show`. Mantive o mesmo `sp.storage`, os callbacks de update e o mesmo número de amostra ao trocar a fonte para `93d991a`. Com stop bem-sucedido, o estado passou a v2, a instância antiga parou antes de iniciar `04DAAF`, a vinheta ficou ativa por mais de 12 s de pacotes e nenhum callback antigo voltou a tocar som ou ocultar a nova vinheta. Ao limpar a Sede Crítica, a camada da alimentação permaneceu ativa.
- **Bloqueio reproduzido com os callbacks reais anteriores a `17ae47c`.** Iniciei `0x809` com handle 1. Fiz `stopInstance(1)` lançar exceção sem interromper o som. No candidato, `blood-effects-client.js:7–12` retorna e deixa o objeto e o callback legados ativos. Aos 2.501 ms desde o último pacote aceito pelo legado, seu watchdog chama `clean()`. Em `f4236da^`, linhas 9–12, e `1088d0b`, linhas 12–15, `clean()` engole a exceção e zera `fx.heart` mesmo quando o stop falha. Na atualização seguinte, `93d991a` vê o handle nulo, instala v2 e toca `0x4DAAF` com handle 2. Os dois sons ficam ativos. Quando o stop volta a funcionar e chega `null`, só o handle 2 para. A instância 1 ficou sem referência. No controle partindo de `17ae47c`, o callback legado retém o handle na exceção e a migração espera o stop bem-sucedido, sem duplicar.
- O teste de retry em `overlays/sobrenatural/teste-sangue-efeitos.cjs:77` semeia o storage antigo, mas não executa seu watchdog após a falha. A correção precisa conservar o handle pendente sob responsabilidade da migração, fora do alcance de `clean()` legado, e só iniciar o novo batimento após o stop confirmado. A regressão deve manter ambos os callbacks no cenário e atravessar o watchdog. Assim se preserva a prova de qual instância precisa parar.
- Bateria integral R2 **413/413** em cada modo explícito da flag, 19 suites e zero falhas. Core **39/39** e scripts serviço/F7/revisão F7 deram OK. A reprodução adicional usou relógio, bridge do navegador e áudio simulados. Só esta seção foi alterada. Ficam pendentes a correção do caso de falha e a validação em CEF/áudio no jogo.

## 18/11 — correção candidata do watchdog legado de Sede Crítica

Autor Terra High; achado da revisão Sol em `59af633` aceito. O candidato `2fffaf9` no servidor local `C:\Code\Aetherius - Servidor Local` guarda o handle do áudio antigo em `sp.storage.aetheriusBloodCriticalLegacyStop` **antes** de zerar `heart` e `packet` no objeto anterior e substituí-lo. O callback antigo observa identidade diferente, não reinicia áudio nem esconde novamente a camada já limpa. Se `stopInstance` lançar, o handle separado permanece cercado e o heartbeat vanilla não inicia; um callback independente tenta parar novamente no máximo uma vez por segundo, inclusive sem novos pacotes. Após um stop sem exceção, o próximo pacote pode iniciar o novo som. Stop sem exceção é a confirmação disponível da API, não uma prova acústica física.

O teste reproduz os dois callbacks reais salvos em `overlays\sobrenatural\fixtures\critical-pre-vignette.txt` (`f4236da^`) e `critical-pre-vanilla.txt` (`1088d0b`): mantém falha de stop por mais de 2.501 ms, executa os watchdogs antigos e retentativas, verifica handle 1 preservado e nenhum 0x4DAAF enquanto a falha persiste; após recuperação, confirma stop anterior ao novo som, depois cleanup do novo handle. Atualizado também o teste da migração normal/retry; recorte efeitos/vinheta **18/18**. Bateria integral **415/415** com `AETHERIUS_VAMPIRE_FEED_ENABLED=false` e **415/415** com true, 19 suites e exit 0 em ambos. As duas cópias de `blood-effects-client.js` têm SHA-256 `3E0DACB621038B9B1B35916A028683D0487A094C67F5B909F8831CEA73DF7EF7`.

Pendente revisão independente de Sol e teste real de atualização em cliente aberto, áudio/CEF e falha de stop; testes simulam callbacks e relógio. Se stop nunca tiver sucesso, a instância antiga pode permanecer audível, mas não se inicia uma segunda. Nenhuma instalação, alteração de MO2 ou jogo foi realizada.

## 18/11 — Reconfirmação Sol High do retry de áudio no hot-reload

**Aprovado no recorte de código e testes Node para o problema de sobreposição.** Revisão de `2fffaf9` e da evidência `3e8a8d3`, em `C:/Code/Aetherius - Servidor Local`. Fonte `source/server/gamemode/supernatural/blood-effects-client.js` e overlay com SHA-256 bruto `3E0DACB621038B9B1B35916A028683D0487A094C67F5B909F8831CEA73DF7EF7`.

- Repeti a reprodução de `59af633` com scripts serializados lidos por `git show` das revisões `f4236da^` e `1088d0b`, preservando o mesmo storage e executando os callbacks históricos. Iniciei `0x809` no handle 1 e fiz `stopInstance(1)` lançar exceção sem alterar a lista de sons ativos. Aos 2.501 ms, `93d991a` voltou a deixar `0x809` e `0x4DAAF` simultâneos. Com `2fffaf9`, o mesmo relógio e a mesma ordem de callbacks conservaram somente o handle 1.
- `blood-effects-client.js:7–31` separa o handle em `aetheriusBloodCriticalLegacyStop` e invalida `heart`, pacote e referência do objeto legado. Os watchdogs anteriores seguem executando e já não têm acesso ao handle pendente. Testei recuperação após mais de 3.500 ms, recebimento continuado, ausência de chamadas do owner, chegada de `null` e falha persistente. Em todos os casos, houve no máximo uma instância ativa. Após `stopInstance(1)` retornar normalmente, novo pacote crítico abriu apenas o handle 2 em `0x4DAAF` e restaurou a vinheta. Sem execução do owner ou com `null`, a recuperação deixou zero instâncias. Com falha persistente, a nova instância ficou bloqueada e o handle antigo continuou rastreado. A migração sem falha também passou com a mesma amostra a partir de `f4236da^`, `1088d0b` e `17ae47c`, preservando o dono da vinheta de alimentação.
- **Ressalva de desempenho.** O callback autônomo tem intervalo de 1 s entre tentativas, mas `blood-effects-client.js:19–22` tenta em toda chamada do owner enquanto houver pendência. Em cinco frames simulados a 16 ms, contei cinco chamadas que lançaram exceção. Isso não reabriu o som ou perdeu o handle na reprodução. O custo real sob falha prolongada depende da bridge no jogo.
- Bateria R2 integral **415/415** tanto com a flag false quanto true, 19 suites sem falhas. Core **39/39** e scripts serviço/F7/revisão F7 OK. A confirmação de stop é o retorno sem exceção do mock da API. A atualização em sessão aberta, a resposta real do áudio e a vinheta no CEF continuam pendentes de teste em jogo.
