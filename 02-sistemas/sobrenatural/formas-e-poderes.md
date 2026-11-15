---
titulo: Formas e poderes do Sobrenatural no laboratório
tipo: sistema
status: rascunho
fase: em-desenvolvimento
responsaveis: [pablo-rodrigues1, kdu]
repositorio: "projeto local: C:\Code\Aetherius - Servidor Local"
depende_de: [../lobisomem/README.md, ../gameplay-core/README.md, ../ui-core/README.md]
atualizado: 2026-11-15
tags: [sobrenatural, sincronizacao, poderes, investigacao]
relacionados: [README.md, diario.md, ../../03-decisoes/ADR-0014-forma-besta-recarga-real.md]
---

# Etapa 2.x — lote menor: Fera, Lorde, raça e recarga

Versão local: `4fc00349cbe8e75d62be962e687f98d544e9a182`. Data: 15/11/2026. Autor: Sol High; coordenação: Opus 5.5. Base do retorno: `3299cf1`, rejeitado por Terra High. Este candidato resolve os quatro apontamentos que afetam as formas/raça; **não recebeu confirmação da revisora nem teste humano**. Decisão de escopo do dev transmitida pelo orquestrador: testar primeiro somente estes itens, depois retomar poderes e origem de grants.

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
