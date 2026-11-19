---
titulo: Origem de grants e poderes sobrenaturais
tipo: sistema
status: ativo
responsaveis: [pablo-rodrigues1, kdu]
atualizado: 2026-11-16
tags: [sobrenatural, grants, poderes]
relacionados: [README.md, etapa-2-bruxaria.md]
---

# Etapa 2 — origem dos grants, Bruxaria e poderes vampíricos

## Resumo

Candidato local com origem de grants, integração Bruxaria e poderes JS; revisão Terra e teste humano pendentes.

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

Boot confirmado em `logs/server-20261116-000848.stdout.log`: 16 ativos, 2 desativados, 0 falhas; 162/162 forms; Enxame habilitado. Última atualização dos sources exige nova cópia de runtime antes do uso (o fechamento registra o boot final no diário). Nenhum mock comprova visual, scripts aceitos no jogo, dreno sobre jogadores reais ou replicação para observador. Revisão de Terra sobre este lote **pendente**; a revisão antiga rejeitada não foi convertida em aprovação pelo autor.

## Roteiro humano preparado para depois

1. Usar somente o perfil MO2 `AETHERIUS - LOCAL`. F7: consultar, remover a maldição antiga se necessário e setar a desejada. Não limpar magias antigas em massa: o histórico anterior sem origem é preservado por segurança.
2. Antes da concessão, registrar uma magia própria e uma perk própria. Conceder/remover cada maldição, reconectar e trocar entre elas: as próprias permanecem; só as concessões novas desaparecem. Testar uma perk compartilhada por classe e Bruxa nas duas ordens.
3. Bruxa → TENEBRAE: XP 0, 20k, 60k, 190k, 610k, 2M. Conferir escolas 30/40/50/60/60/60, Shadowbolt inicial→50→75, Dark Arts no estágio 3 e Atronach Expert no 5. Reduzir XP e remover: restaurar classe e remover somente grants próprios. Repetir MORTUI/DAEMONES para livros de magia e perks.
4. Vampiro: Visão própria deve revelar via Aura Whisper, mesmo com Predator Vision ativo. Morcego: dash/ocultação 1 s e novo uso após terminar; nenhuma espera de 3 s. Força/Névoa/Celeridade/Enxame: cronometrar 40 s de efeito e tentativa aos 40/59 s recusada, aos 60 s aceita.
5. Névoa: arte/ocultação vistas por outro cliente, bloqueio de ataque/interação e recuperação ao acabar/remover/morrer; não exigir a regeneração ainda parcial. Força: registrar efeitos observáveis sem declarar dano aumentado. Celeridade: medir deslocamento com/sem efeito. Enxame: vítima hostil aceita dentro de 10 pés perde ~10/s e só o dano efetivo cura; grupo, neutro, fora do raio e caído não recebem dano indevido.
6. Com o módulo Terra instalado: saldo 10→5→0 em dois usos aceitos; saldo 4 recusa sem negativo; recarga/replay/interrupção não cobram. Lorde só cobra ao confirmar a transformação; cancelar durante turning libera a reserva. Sedução/Reanimação mantêm suas regras e devem ter aceitação/custo correlacionados no log.
7. Repetir o roteiro anterior de Fera/Lorde/raça abaixo, agora com scripts assinados. Observador verifica apresentação de todos os efeitos públicos; proprietário verifica controles e limpeza. Registrar horário/poder/saldo/sintoma para correlacionar os logs.

## 18/11 — Reteste de poderes após a rodada em jogo

Candidato de Sol High no laboratório `415acde`, documentação `d8e5c94`. Os seis poderes próprios são SPEL `LesserPower` de alvo `Self`. O leitor usa o caminho de voz do servidor quando o poder está equipado no slot Z. O teste confirma aceitação e débito único do Enxame sem `SpellCast` nativo. Os relatórios do teste anterior trazem os efeitos observados pelo dono, mas o log só confirma o carregamento da definição do Enxame.

Após a confirmação do custo, o servidor distribui o som de entrada aos clientes que veem o ator. Cada um usa `Sound.play` no ator e os modelos sonoros radiais existentes. Consulta houseCARL em `AETHERIUS - LOCAL`, somente leitura. Os marcadores vêm da propriedade `IntroSoundFX` do mod.

| Poder | Marcador SOUN |
|---|---|
| Morcego | `019D82:Dawnguard.esm` |
| Névoa | `010E09:Dawnguard.esm` |
| Enxame | `013780:Dawnguard.esm` |
| Força | `005902:Curse of the Vampire.esp` |
| Celeridade | `010EC4:Dawnguard.esm` |

O Enxame mantém 10 HP/s, 40 s e raio de 10 pés. Os testes de NPC e jogador cobrem golpe físico recente, raio e veto social. A cura usa apenas perda de HP confirmada por duas leituras válidas. A investigação fica em `INVESTIGACAO_18-11_enxame-readback.md` no laboratório e na documentação de desenvolvimento.

V2 requer reteste com aceitação registrada e dois jogadores para medir posição e apresentação. O script COTV humano e o candidato disparam os mesmos eventos da animação. Se o movimento continuar ausente, revisar um caminho com colisão e sincronização. V3 exige confirmar a invisibilidade para o observador; V4, o áudio, a arte e o dreno contra NPC e jogador elegível; V5, a revelação de jogador e NPC no oficial autorizado. Visão e Lorde têm descritores SNDR sem SOUN correspondente localizado e o áudio para esses poderes segue pendente. Sedução e Reanimação seguem no caminho nativo com alvo, por validar em jogo.

Node 39/39 no recorte, incluindo os callbacks do dono e do vizinho. A bateria ampliada obteve 457/457 por modo da flag de alimentação sem a suíte concorrente de HUD do Véu. A execução completa obteve 469/472 com três falhas de mock nessa suíte. Revisão cruzada e testes em jogo estão pendentes.



## 18/11 — Sincronização sem rodada multiplayer agora

Por decisão do dev, o código e os testes Node simulam dono, vizinhos, entrada tardia, saída e retorno à relevância. A confirmação física fica para o servidor oficial. Candidato `827afe4` no laboratório e documentação `90ec13e` na Etapa 3.

- **V2:** teleporte autoritativo de 128 unidades no rumo do ator após o débito. Morcegos e ocultação são públicos. Validar no servidor oficial o único deslocamento visto por B e a posição atual para C, que entra depois. Conferir `bat dash ... applied=true` no servidor. Testar obstáculos, desnível e retorno de posição. A operação não varre colisão.
- **V3:** a réplica recebe arte e alpha 0 mesmo ao entrar durante a Névoa. Validar no servidor oficial ocultação, saída/retorno e reaparecimento no fim ou cancelamento. Conferir `accepted=true` e `[supernatural-visual]` nos consoles.
- **V4:** seleciona atores por relevância e alcance, com hostilidade física recente e permissões sociais. O dreno confirmado de cada alvo é registrado uma vez por ativação. Validar no servidor oficial visual e som imediato para B, apenas visual para C tardio e HP antes/depois contra NPC e jogador elegíveis. Conferir `enxame ... drained=` e o HP de ambos.
- **V5:** a Visão destaca jogadores e NPCs da mesma área em raio de 25 m, inclusive alvos invisíveis. Só o dono recebe os IDs para pulsos finitos de shader. Validar no servidor oficial o contorno privado, a entrada de jogador/NPC e a perda do destaque ao sair. Conferir `visão ... targets=` e `[supernatural-vision]`.
- **V7:** cinco marcadores existentes tocam no dono e em vizinhos próximos presentes, uma vez por sequência. Vizinhos tardios recebem o visual e a retirada da réplica encerra a instância ativa. Validar no servidor oficial perto/longe, saída durante o som e recusa por saldo ou recarga. Conferir `accepted=`, `som de ativação` e `[supernatural-visual]`. Visão e Lorde ainda dependem de reprodução de SNDR. Sedução e Reanimação conservam a conjuração com alvo.

Node **46/46** no recorte e **486/486** na bateria integral em cada modo da flag de alimentação. Revisão cruzada pendente. Nenhum servidor, cliente de jogo ou MO2 foi iniciado por este lote.
