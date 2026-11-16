# Etapa 2 — origem dos grants, Bruxaria e poderes vampíricos

Atualização: 16/11/2026, Sol High, despacho de Opus. Base: commits locais `4fc0034` (formas), `603dea7` (Argon) e `68a6e2b` (assinatura). Raiz de implementação: `C:\Code\Aetherius - Servidor Local`; servidor fonte `source` em `310cb598fd382cf4d6598cb34eada5db1a3fa9f8`, cliente fonte `client-source` em `e39163cc952169c148b2fbc00002637fe7a88fe3`. **Candidato local, revisão Terra pendente e nenhum teste em jogo neste lote. O dev decidiu não testar em jogo hoje.**

## Resultado atual

- `source/server/gamemode/supernatural/grant-origin.js` substitui a remoção pela união dos catálogos: somente magias adicionadas e confirmadas pelo serviço podem ser retiradas. Registro estável por personagem, plugin definidor e id local em `runtime/server/data/aetherius-supernatural.json`, seção `origins`.
- `supernatural-service.js` liga `witch-grants.js`, valida seus 60 records no boot e entrega perks, magias e upgrades por estágio/vertente. GameplayCore conserva os pisos do patch `005-gameplay-core-witch-floors.patch`.
- `supernatural/perk-origin.js` é compartilhado byte a byte com `client-source/client/src/services/services/perk-origin.js`. A união de claims `gameplay`/`supernatural` conserva perks externas e só remove uma perk introduzida pela união quando a última fonte a libera. O cliente do GameplayCore mantém ACK, verificação tardia de remoção e proteção contra callbacks obsoletos.
- Sobrenatural persiste os ACKs de autoria de perks, vinculados ao personagem, publicação/nonce e catálogo projetado. O hash de personagem (`originScope`) chega também no hello e nas entregas de perks do Core; este host usa `profile:N` na persistência de classes, portanto o hash usa o UUID do diretório ativo quando disponível. Não houve migração do banco de classes por perfil.
- Magias antigas sem prova de origem e intenções de AddSpell interrompidas são preservadas. Não existe adoção automática de tudo que um portador já conhecia. Ações externas que concedam a mesma magia depois da adição do serviço sem registrar origem continuam inobserváveis.

## Poderes e decisão de duração/recarga

Medição antes dos overrides, via houseCARL no perfil `AETHERIUS - LOCAL`: os quatro poderes **não tinham todos 20 s**. A decisão do dev foi aplicada como duração absoluta de 40 s e reutilização aos 60 s do uso.

| Poder | Efeito anterior / marcador de recarga | Candidato atual | Cobertura |
|---|---|---|---|
| Força Imensurável | 10 / 30 s | 40 / 60 s | **PARCIAL**: record e janela aplicados; bônus de dano/perk dependente de Papyrus não reconstruído nem certificado no servidor |
| Forma de Névoa | 10 / 30 s | 40 / 60 s | alpha e arte públicos, bloqueios de combate/interação e veto de dano pelos hooks; **PARCIAL**: regeneração especial magicka/vigor pendente, invisibilidade frente à IA não homologada |
| Celeridade Não Natural | 20 / 80 s | 40 / 60 s | SpeedMult nativo do record, projeção para dono/vizinhos; efeito real no motor pendente |
| Enxame Devorador | 15 / 35 s | 40 / 60 s | baseline COTV provisório: 10 vida/s, raio 10 pés = 213,333 unidades; dreno/cura pelo servidor |
| Morcego | 1 / 3 s | 1 s / **zero recarga** | dash via animação e ocultação/arte; não há teleporte arbitrário; cadência antirreplay de 1 s permanece |
| Visão própria | novo record | Aura Whisper 30 s | `000836:AETHERIUS - SOBRENATURAL.esp`, 3 efeitos vanilla; não depende de sobrescrever o vencedor Predator Vision |
| Sedução / Reanimação | 30 / 60 s de efeito | regras próprias preservadas | delimitação do orquestrador, **a confirmar com o dev**; não entraram na mudança 40/60 |

`VAMPIRE_POWER_COOLDOWN_SECONDS=60` é o padrão e o valor explícito de `config/local.env` (arquivo ignorado); admite 40..86400 s. A duração é do ESP. Os quatro overrides copiam os vencedores COTV para `AETHERIUS - SOBRENATURAL.esp`: efeitos principais 40 s, marcadores 60 s. As artes locais da Névoa/Enxame também duram 40 s. A recarga autoritativa fica em `aetheriusPowerCooldowns`, invisível, com personagem e prazos absolutos; não é apagada ao cancelar a apresentação. Fera conserva sua configuração própria `WEREWOLF_COOLDOWN_SECONDS` (1800 padrão; 0 local).

Os poderes simultâneos têm entradas independentes por tipo, sem apagar buffs anteriores. Névoa bloqueia novas ações enquanto ativa; formas de Fera/Lorde bloqueiam esses usos. A aceitação dos poderes próprios exige geração/nonce/seq, proposta nativa em até 750 ms, spell conhecido/equipado, estágio, vida/SkyDeath, locks e cadência. Relatos de efeito/raça continuam sendo apenas evidência. A entrada é um comando validado, não prova de um cast genuíno do motor.

Enxame: até 128 vizinhos por tick de 250 ms, mesma célula/mundo, distância, exclusões de keywords e permissões sociais. Usa evidência recente de golpe físico aceito como substituição conservadora da condição IsHostileToActor; não inicia dano sobre todos os vizinhos neutros. Tempo acumulado por tick é limitado a 0,5 s; não descarrega um pico após travamento. Cura apenas o dreno efetivamente observado no servidor. Mitigações exatas de todos os efeitos mágicos COTV não foram reproduzidas; os números não são balanceamento final.

## Reserva de Sangue — contrato coordenado com Terra

`power-input.js` chama a API real acordada: `mp.__aetheriusBloodReserve.reserve(actorId, 5, powerId)` antes da execução, `commit(token)` somente na aceitação bem-sucedida e `cancel(token)` na recusa/falha. `powerId` é string `supernatural:0x<FormID-runtime>`. Não implementa o saldo. Sem módulo, segue sem custo e avisa uma vez por instalação.

Lista custeada, **pendente de confirmação final do dev**: Força, Névoa, Celeridade, Enxame, Morcego, Visão, Lorde, Sedução e Reanimação. Lorde mantém a reserva durante turning e só comita depois de `isLord` (raça/fase confirmadas); desconexão, troca de personagem/concessão, encerramento ou 14 s sem confirmação cancelam antes do TTL de ~15 s da reserva. Sedução/Reanimação usam seu caminho nativo com alvo: cobram a aceitação do comando no hook, sem inventar um replay de magia dirigida nem afirmar comprovação de efeito na vítima. Sangue não é cobrado por replay, interrupção ou falha de recarga. Falha no commit desfaz a entrada gerenciada; o prazo de recarga pode permanecer conservador.

Uma recusa de poder próprio publica limpeza do efeito local original sem limpar uma instância já aceita daquele poder. A descoberta da assinatura não torna Papyrus funcional: só libera os event sources/propriedades JS; OnEffectStart/quests continuam bloqueados no cliente.

## Sol — preparação, sem ativação

`vampire-grants.js::spellsFor` conserva `COTV_WeaknessToSunlight` por padrão. Só o exclui quando `AETHERIUS_VAMPIRE_SERVER_SUN=true`, nome transmitido e aceito pelo orquestrador para Terra. A flag permanece desligada; nenhum tick solar foi implementado aqui. O reconciliador remove apenas uma concessão solar própria. Se o personagem já tinha esse spell por outra origem, ele fica preservado: o dono do tick solar precisa tratar essa duplicidade antes de habilitar sua flag. Dano de 5/s pertence ao lote Terra.

## Auditoria, correções e artefatos

- A primeira versão guardada do Enxame ignorava a condição HasMagicEffect do marcador de recarga e se desativou no boot. Validada a cadeia completa: cloak com HasMagicEffect 214, exclusões HasKeyword 560 e IsHostileToActor 719; o servidor substitui o marcador por seu prazo. Boot posterior confirmou 10/s, 40 s, 10 pés.
- `magic-service.js` tratava o modificador Health da Força como cura antes dos gates do Sobrenatural. `007-managed-power-magic.patch` deixa esses records com seu domínio depois do veto anterior; a cura normal continua pelo caminho existente. Teste de regressão incluído.
- Plugin: 57 records (46 anteriores + 7 novos + 4 overrides); os novos MGEFs de arte não têm VMAD. houseCARL confirmou os quatro vencedores, a Visão própria e zero referências quebradas/masters ausentes/records ilegíveis.
- ESP instalado/overlay SHA-256: `7F467CBE2EC3B38CA42F10DB9E1CA9A0013C22377527D9D752A773A4F02ECE29`.
- Cliente instalado SHA-256: `387A7471FC4F8DF3471B118C68B318791DD2D1EC2F08048FD49187763815F517`.
- `006-perk-origin-core.patch` complementa o patch 005; `overlays/cliente-local/003-perk-origins.patch` complementa o 002 sobre e39163c. Reverse-check dos patches passou. Arquivos JS do serviço e catálogo ficam espelhados em `overlays/sobrenatural/files/server/gamemode/`; ESP em `overlays/sobrenatural/plugin/`.
- Servidor/cliente passaram por Build.ps1/webpack sem addon. Nenhuma alteração em OFICIAL, mods de terceiros, nativo ou raízes MeridianUI. Mantidos os 435 plugins, sem reorder. A chave de teste da correção 68a6e2b foi preservada sem publicar segredo.

## Validação automatizada e limites

Na raiz `C:\Code\Aetherius - Servidor Local`:

```powershell
node --test overlays/sobrenatural/teste-origem.cjs overlays/sobrenatural/teste-poderes.cjs overlays/sobrenatural/teste-formas.cjs overlays/sobrenatural/teste-aparencia.cjs source/server/gamemode/werewolf/tests/werewolf.test.cjs source/server/gamemode/magic/tests/magic.test.cjs
node overlays/sobrenatural/teste-servico.cjs 'C:/Code/Aetherius - Servidor Local/source/server/gamemode'
node overlays/sobrenatural/teste-painel-f7.cjs
node overlays/sobrenatural/teste-revisao-f7.cjs
# source/server/gameplay/core:
node --test runtime/tests/perk-client.test.mjs runtime/tests/client-delivery.test.mjs runtime/tests/class-skills-delivery.test.mjs
```

**93 + 29 = 122 testes**, além dos scripts serviço/F7. Cobrem origem preexistente, remoção/troca/login, falhas/no-op, upgrades/decréscimos, claims concorrentes nas duas ordens, transporte real compilado, bloqueios/replay, 40/60/zero, confirmação/cancelamento de custo, Lorde e isolamento. Logs sem dados de jogadores: `evidence/origem-formas-tests.log`, `evidence/gameplay-origin-tests.log`, `evidence/magic-power-tests.log`. Os fixtures de Core tinham caminhos de outro monorepo; só os caminhos foram adaptados para esta cópia local.

Boot confirmado em `logs/server-20261116-000848.stdout.log`: 16 ativos, 2 desativados, 0 falhas; 162/162 forms; Enxame habilitado. Boot final com os sources fechados: PID 7980, `logs/server-20261116-001610.stdout.log`, mesmos 435 plugins/162 forms/0 falhas; SkyrimSE/VR fechados por tasklist antes de Stop/Prepare/Start. Acervo publicado em `be29f35`. Nenhum mock comprova visual, scripts aceitos no jogo, dreno sobre jogadores reais ou replicação para observador. Revisão de Terra sobre este lote **pendente**; a revisão antiga rejeitada não foi convertida em aprovação pelo autor.

## Roteiro humano preparado para depois

1. Usar somente o perfil MO2 `AETHERIUS - LOCAL`. F7: consultar, remover a maldição antiga se necessário e setar a desejada. Não limpar magias antigas em massa: o histórico anterior sem origem é preservado por segurança.
2. Antes da concessão, registrar uma magia própria e uma perk própria. Conceder/remover cada maldição, reconectar e trocar entre elas: as próprias permanecem; só as concessões novas desaparecem. Testar uma perk compartilhada por classe e Bruxa nas duas ordens.
3. Bruxa → TENEBRAE: XP 0, 20k, 60k, 190k, 610k, 2M. Conferir escolas 30/40/50/60/60/60, Shadowbolt inicial→50→75, Dark Arts no estágio 3 e Atronach Expert no 5. Reduzir XP e remover: restaurar classe e remover somente grants próprios. Repetir MORTUI/DAEMONES para livros de magia e perks.
4. Vampiro: Visão própria deve revelar via Aura Whisper, mesmo com Predator Vision ativo. Morcego: dash/ocultação 1 s e novo uso após terminar; nenhuma espera de 3 s. Força/Névoa/Celeridade/Enxame: cronometrar 40 s de efeito e tentativa aos 40/59 s recusada, aos 60 s aceita.
5. Névoa: arte/ocultação vistas por outro cliente, bloqueio de ataque/interação e recuperação ao acabar/remover/morrer; não exigir a regeneração ainda parcial. Força: registrar efeitos observáveis sem declarar dano aumentado. Celeridade: medir deslocamento com/sem efeito. Enxame: vítima hostil aceita dentro de 10 pés perde ~10/s e só o dano efetivo cura; grupo, neutro, fora do raio e caído não recebem dano indevido.
6. Com o módulo Terra instalado: saldo 10→5→0 em dois usos aceitos; saldo 4 recusa sem negativo; recarga/replay/interrupção não cobram. Lorde só cobra ao confirmar a transformação; cancelar durante turning libera a reserva. Sedução/Reanimação mantêm suas regras e devem ter aceitação/custo correlacionados no log.
7. Repetir o roteiro anterior de Fera/Lorde/raça abaixo, agora com scripts assinados. Observador verifica apresentação de todos os efeitos públicos; proprietário verifica controles e limpeza. Registrar horário/poder/saldo/sintoma para correlacionar os logs.

---

## Histórico do lote menor 4fc0034 (preservado)

# Etapa 2.x — lote menor: Fera, Lorde, raça e recarga

Data: 15/11/2026. Autor: Sol High; coordenação: Opus 5.5. Base do retorno: `3299cf1`, rejeitado por Terra High. Este candidato resolve os quatro apontamentos que afetam as formas/raça; **não recebeu confirmação da revisora nem teste humano**. Decisão de escopo do dev transmitida pelo orquestrador: testar primeiro somente estes itens, depois retomar poderes e origem de grants.

## Escopo instalado

- Forma da Besta: comando validado no servidor, sequência kdu, aparência pública e fase somente após readback.
- Lorde Vampiro: entrada pelo poder, animação inicial, fase pública e troca de raça para dono/vizinhos. A quest completa de menu/levitação/roupas/spells próprios não foi reimplementada; usar F7 para encerrar neste teste.
- Raça vampírica: projeção ao setar/login, SetRace também em humanoides, olhos derivados das dez variantes vanilla, reversão mantendo original até confirmação.
- Recarga real da fera: `WEREWOLF_COOLDOWN_SECONDS=1800` por padrão; `config/local.env` do laboratório usa 0. Cadência mínima antirreplay de 1 s continua existindo.
- Morcego, Névoa, Enxame, Força e Visão seguem no caminho anterior. Nenhum novo hook de combate/saúde/alpha/dash foi instalado. A Visão continua vencida por Predator Vision. O novo patch VISOES foi retirado dos dois perfis LOCAL, sem reordenar os 435 plugins anteriores.
- Bruxaria não pertence a este worker. Reconciliador por origem para lobisomem/vampiro fica para a continuação; nenhum código dessa correção foi incluído neste lote.

## Ambiente e artefatos

Raiz Git: `C:\Code\Aetherius - Servidor Local`. Fonte ativa `source`, base `310cb598fd382cf4d6598cb34eada5db1a3fa9f8`; cliente fonte `client-source`, base `e39163cc952169c148b2fbc00002637fe7a88fe3`. MO2: `C:\modOrganizer`, perfil **AETHERIUS - LOCAL**, 435 plugins na ordem anterior. Não houve alteração de OFICIAL, nativo/addon, mods de terceiros ou pastas MeridianUI.

| Artefato instalado | SHA-256 |
|---|---|
| `C:\modOrganizer\Mods\AETHERIUS - SERVIDOR LOCAL\Platform\Plugins\skymp5-client.js` | `2DAEBD6C0679FF7865024B47B617CF19ADA210F4AAEEC146BCD8137CCDE0F901` |
| `C:\modOrganizer\Mods\AETHERIUS - SOBRENATURAL\AETHERIUS - SOBRENATURAL.esp` | `57CF1A2BC4C83C335336F49B302BDE36763EB53DDF443AED0F21460B62B3688C` |

Plugin principal: 46 records, os 45 da base + override `092C48:Skyrim.esm` (`WerewolfChange`, `Type=LesserPower`). Foram removidos o override experimental de Visão e os seis clones visuais do próximo lote. houseCARL confirmou vencedor LesserPower e zero refs quebradas/masters ausentes/records ilegíveis. O patch tardio de Visão permanece inativo, fora do pacote entregue.

Trabalho adiado preservado em `C:\Code\Aetherius - Servidor Local\backups\formas-proximo-lote-20261115` (código, docs e ESP com clones). O arquivo `powers.js` pode existir na cópia de desenvolvimento, mas não é requerido/instalado pelo serviço deste candidato nem acompanha seu overlay. Perfis MO2/client-source conferidos byte a byte contra seus backups pré-VISOES.

Documentação oficial: https://github.com/pablo-rodrigues1/AETHERIUS-ACERVO-DE-INFORMACOES/tree/main/02-sistemas/sobrenatural . Publicação inicial `998fd05`; o diário do acervo registra este retorno. Decisões recentes da pasta prevalecem sobre a referência antiga de vampirismo.

## Causa e fronteira de confiança

`client-source\client\src\services\services\blockPapyrusEventsService.ts:12,16` bloqueia eventos Papyrus. `source\skyrim-platform\src\platform_se\skyrim_platform\FridaHooks.cpp:43` substitui o evento bloqueado por string vazia, salvo exceções do host. Um MGEF em Active Effects não comprova OnEffectStart/quest executados. `source\server\cpp\server_guest_lib\ActionListener.cpp`, final de OnSpellCast, deixa a aplicação genérica de autoalvo como TODO.

O evento `spellCast` possui `caster` e `spell`: a hipótese de campo errado foi descartada. O relato original não permitia localizar onde a entrada se perdia; este candidato registra comando/evidência/aceitação para o reteste. O motor ainda precisa confirmar o transporte real.

O protocolo é um **comando de uso validado pelo servidor**, não atesta um cast genuíno. Pacote nativo e evento customizado são entradas não confiáveis. Um cliente modificado pode solicitar uma forma que realmente possua/equipe dentro dos gates e recarga; não pode atribuir maldição/estágio nem antecipar fases com relatos de efeito/raça.

## Achados Terra tratados neste lote

| Achado aceito | Correção |
|---|---|
| 1 — entradas forjáveis/gates divergentes | Dispatcher único `supernatural/power-input.js`. `activate` exige geração do personagem, nonce de uso único, sequência e proposta do caminho nativo correspondente em até 750 ms. Consome as duas entradas, revalida gates e só então chama start. Relatos antigos/novos `cast/effect/race/reached` são evidência limitada por frequência e nunca mudam fase. |
| 2 — perda de raça original | `supernatural-service.js::projectRace` persiste original antes de converter. Mantém `restoreRace` após throw/no-op e somente apaga depois de readback da raça restaurada. Se o save falhar antes da conversão, não troca raça. Falha no save da limpeza mantém a recuperação. |
| 3 — aparência obsoleta em callbacks | `appearance.ts` reserva uma geração por atualização e confere cada callback/espera. `remoteServer.ts` invalida em novo createActor do dono, logout, destruição e aceitação/recusa/falha de conexão. Callback de spawn captura a identidade original. SetRace precisa conferir raça antes de aplicar rosto; falha não cria espera infinita. |
| 5 — fase beast antes da aparência | `werewolf-service.js` inicializa turning inclusive na entrada direta sem animação; becomeBeast só marca beast/duração/publicação depois de set/readback. Falha conserva recuperação e tenta novamente no tick. Prazo e registro também precisam gravar antes da publicação. Lorde aplica o mesmo critério de aparência antes da fase lord. |

O achado **4 (Visão perde para Predator Vision)** foi adiado junto da Visão. Seu candidato tardio foi desligado; não há afirmação de correção/homologação da Visão neste lote.

Gates comuns: ator online e personagem atual; vivo/SkyDeath ALIVE; inventário social/profissão sem lock; sem negociação; sem outra forma ativa; spell conhecido e equipado; maldição correta, estágio do Lorde conferido pelo domínio; cadência e recarga autoritativas. Permit e propriedades públicas não contêm keyword.

O hook nativo roda depois da conferência de host/vínculo/spell equipado, antes de OpenBoundWeaponCast. Respeita o veto anterior social/cura. Para formas gerenciadas, retorna false para consumir o replay mecânico original; os demais poderes passam intactos ao hook anterior. A política de comando não depende de confiar no relato de Active Effects.

## Raça e sincronização

O teste salvo usava Nord vanilla; não se confirmou raça custom. O servidor resolve a raça atual de appearance pela tabela `RACE_TO_VAMPIRE` e salva a mortal antes de projetar. Custom desconhecida permanece intacta e gera log, sem equivalente inventada. No cliente, SetRace agora contempla humanoides no update e no spawn/login. Olhos são derivados somente na apresentação das dez raças vanilla, sem modificar HDPTs originais persistidos. Fera/Lorde continuam evitando partes de rosto humano.

O kdu mantém aparência/nome/animação/cronômetro/prata/recuperação da fera. Em produção seus relatos antigos delegam ao dispatcher como evidência. Lorde publica `aetheriusVampireLord` para dono e vizinhos; dono dispara `IdleVampireLordTransformation`, e a aparência autoritativa chega aos demais pelo fluxo normal. Replicação da animação/olhos/retorno ainda precisa de observador humano.

## Validação e boot

```powershell
# C:\Code\Aetherius - Servidor Local
node --test source/server/gamemode/werewolf/tests/werewolf.test.cjs overlays/sobrenatural/teste-formas.cjs overlays/sobrenatural/teste-aparencia.cjs
node overlays/sobrenatural/teste-servico.cjs 'C:/Code/Aetherius - Servidor Local/source/server/gamemode'
node overlays/sobrenatural/teste-painel-f7.cjs
node overlays/sobrenatural/teste-revisao-f7.cjs
```

**50/50** no primeiro comando: 29 kdu +17 formas +4 aparência. Três scripts passaram. Cobrem fraude/replay/reordenação/interrupção, geração de personagem, gates, recarga/reinstalação, falha de registro/prazo/SetRace, confirmação de fase de Fera/Lorde, restauração com throw/no-op/reinício, persistência antes da conversão e callbacks obsoletos. Incluem pass-through dos poderes adiados. Mocks não comprovam visual, durabilidade real após crash nem transporte do motor.

Webpack e `Build.ps1 -SkipInstall` passaram sem reconstruir addon. `Stop` → `Build` → `Prepare` → `Start` executados após tasklist confirmar SkyrimSE/SkyrimVR fechados. Prepare sincronizou 435 plugins. Boot: `C:\Code\Aetherius - Servidor Local\logs\server-20261115-191225.stdout.log`, PID 21092: **16 ativos/2 desativados/0 falhas**, SkyDeath/SkySocial ativos, Sobrenatural 102/102 forms. Instalação após `moduleRegistry.bootAll()` evita a colisão de hooks encontrada no experimento adiado; investigação `INVESTIGACAO_15-11_formas-hook-boot.md`.

Reverse-check dos patches servidor `001-phase0.patch` e cliente `002-formas-raca.patch` passou. Os quatro arquivos server ativos foram conferidos com os espelhos. Build limpo isolado da base e39163c foi comparado ao oficial: após preservar somente os EOL de quatro vendor JS (conteúdo normalizado idêntico), SHA `07C397142FB943CFE35771CB6F4A0F6F071CECF78E2982FB96A11AB6A532C007` byte-idêntico ao oficial. Procedimento e ressalva em `overlays/cliente-local/verificacao-base.md`; nenhuma mudança de vendor na cópia ativa.

## Roteiro de teste humano

1. Abrir `scripts/Start-Client.ps1 -Mo2Profile 'AETHERIUS - LOCAL' -Profile 1`. F7 → Sobrenatural → selecionar o próprio alvo.
2. Setar Lobisomem. Equipar **Forma da Besta** e usar Z. Esperado: sequência kdu inicia, transforma após cerca de 10,5 s; o servidor só publica beast após confirmar aparência. F7 encerrar; repetir imediatamente (local 0, aguardar pelo menos 1 s). Registrar se efeito aparece sem sequência.
3. Remover e setar Vampiro na forma mortal. Conferir raça/olhos, sair e entrar, conferir novamente. Remover: raça/rosto/olhos anteriores voltam. Repetir setar/remover rapidamente para procurar callbacks antigos. Custom sem mapa deve ficar preservada e ser relatada.
4. Setar Vampiro, equipar **Lorde Vampiro**, usar Z. Esperado: início de transformação e raça de Lorde após cerca de 8 s. F7 encerrar e conferir retorno mortal vampírico; remover maldição para retorno original. Não usar ausência do menu/levitação da quest como regressão deste contrato reduzido.
5. Quando houver segundo jogador, observar começo/final e aparência de Fera/Lorde/raça. Com um jogador, registrar explicitamente que replicação visual externa não foi validada. Enviar horário/poder/sintoma para correlacionar `[supernatural-power]` e `[werewolf]/[vampire-lord]` nos logs.

## Continuação

Próximo lote: Morcego/Enxame/projeção da Névoa em JS; Visão e análise de Força. Decisão do dev preservada: **Névoa (regen) e Força (dano) PARCIAIS, sem nativo**; Enxame provisório LOCAL baseado nos records COTV (10/s por 15 s, raio a validar). O material experimental está guardado, não homologado.

Depois: corrigir origem de spells/perks para lobisomem e vampiro, persistindo apenas concessões efetivas e preservando preexistentes e concessões concorrentes de classe. O catálogo da Bruxa deverá plugar no mesmo mecanismo por provider server-side (`spellsFor`/`perksFor` com plugin+id local+EditorID), sem implementar Bruxaria neste worker. O contrato definitivo do ledger ficará no relatório da próxima entrega. O reconciliador antigo ainda está ativo: não declarar resolvida essa pendência neste candidato.
