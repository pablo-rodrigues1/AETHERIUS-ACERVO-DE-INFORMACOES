---
titulo: Transformação forçada do lobisomem
tipo: referencia
status: rascunho
responsaveis: [pablo-rodrigues1, kdu]
atualizado: 2026-11-16
tags: [sobrenatural, lobisomem, servidor, testes]
relacionados: [README.md, diario.md]
---

# Etapa 2 — transformação forçada do lobisomem

Atualizado em 16/11/2026 por Sol High, por despacho do Opus 5.5. Candidato local sobre o commit raiz `79098ad`; fonte do servidor `310cb598fd382cf4d6598cb34eada5db1a3fa9f8`, cliente `057f8553aa4c0ec6bc06dd37596aacb70e0d3163`. Implementação em `C:\Code\Aetherius - Servidor Local`; pacote original somente leitura em `C:\Code\Aetherius - New Systems\Sobrenatural\AETHERIUS - LOBISOMEM 14-11 - KDU`. Revisão independente e teste em jogo **pendentes**; nenhum teste humano solicitado ou executado, conforme decisão do dev.

Documentação oficial: https://github.com/pablo-rodrigues1/AETHERIUS-ACERVO-DE-INFORMACOES/tree/main/02-sistemas/sobrenatural . O orquestrador encaminha a revisão a outro modelo; o autor não converte testes Node em aprovação independente.

## Regra entregue

O personagem lobisomem acumula 72 horas **reais online** desde a última transformação efetivamente confirmada. O contador usa `process.hrtime.bigint()`, sem multiplicar pelo calendário/TimeScale e sem somar tempo offline ou entre processos. A identidade é `commands.getActiveCharacterData(actorId).characterId`, associada ao `grantId` autoritativo. A referência de sessão também é conferida para impedir que a reutilização do mesmo ator some um intervalo ao personagem novo. Não exige que esteja vivo para acumular horas online; exige vida/estado ALIVE para iniciar ou continuar o aviso.

A janela do calendário autoritativo é `[20h, 5h)`: 20:00 entra, 05:00 sai. Se as horas vencerem de dia, aguarda a próxima noite com o personagem online. Não consulta relógio informado pelo cliente. Sem EnvironmentAuthority v1 pronta, não dispara. Amanhecer durante o aviso limpa a sequência e mantém as horas para outra noite. Não inicia com personagem em criação, morto, caído, Fera/Lorde (incluindo turning/leaving), raça Fera/Lorde sem sessão, comércio ou locks de inventário.

| Tempo desde o início do aviso | Ação do servidor |
|---|---|
| 0 s | `A Besta clama pela caçada...` ao alvo e publicação privada da sequência |
| 5 s | inicia loop do coração do kdu no cliente do alvo |
| 5,25 s | primeiro tremor, depois do início do som; pulsos de 0,8 s |
| 10 s | filtro vermelho do kdu, mantido até a entrega à animação |
| 15 s | `A Besta está prestes a rasgar sua carne...` ao alvo |
| 20 s | `mp.__aetheriusWerewolf.start(actorId, { animate: true })`; limpa o pré-aviso e entrega os efeitos à sequência normal do kdu |
| ~30,5 s | troca de raça após a animação própria de 10,5 s, se nenhum bloqueio interromper |

**20 s inicia a transformação normal; não pula a animação nem antecipa a troca de raça.** O serviço normal conserva spell conhecido, recarga e recuperação de aparência. Uma recusa não zera horas; nova tentativa espera 30 s. Os avisos podem repetir após cancelamento elegível. O caminho forçado não oferece uma entrada de transformação ao cliente: o comando nasce somente no servidor/F7 autorizado.

## Arquivos e contratos

Todos os caminhos desta seção são relativos a `C:\Code\Aetherius - Servidor Local`:

- `source/server/gamemode/supernatural/forced-werewolf.js:13`: configurações; `:51`: vínculo por personagem/concessão/sessão; `:66`: elegibilidade; `:80`: reset; `:94`: tick de 250 ms; `:138`: API de teste interna. Espelho versionado em `overlays/sobrenatural/files/server/gamemode/supernatural/`.
- `source/server/gamemode/supernatural/forced-werewolf-client.js:3`: efeitos isolados do dono (também exige actor local `0x14`), um único listener de update, watchdog de 2,5 s sem amostra nova, limpeza de som/filtro/pulso. Tremores duram 0,25 s e não deixam um shake infinito.
- `source/server/gamemode/werewolf-service.js:189`: readback da aparência e stamp privado após a troca efetiva; `:236`: propriedade invisível a dono/vizinhos; `:277`: validação da concessão/personagem durante a animação; `:315`: `onTransformed(fn)` com unsubscribe. Fonte pronta em `overlays/sobrenatural/files/server/gamemode/werewolf-service.js`, aplicada por cima do pacote kdu, sem editá-lo.
- `source/server/gamemode/phase0-basic.js:435`: instala depois do Sobrenatural e EnvironmentAuthority. Registro/configuração inválidos desativam apenas este módulo com erro explícito, sem recomeçar um histórico ilegível.
- `source/server/gamemode/admin-panel/supernatural-admin.cjs:35`: ação `supernatural.forceTransform`; catálogo em `admin-panel/extension-catalog.cjs:89`, permissão `characters.class` e escopo ADM ou superior, alvo selecionado, reautorização após await, motivo/requestId/sessão/reserva/auditoria do F7 existentes. Auditoria conserva `formStatus: warning`. O cliente F7 renderiza o catálogo recebido, sem alteração de HTML/MeridianUI.
- `overlays/sobrenatural/009-transformacao-forcada.patch`: delta incremental do boot, catálogo e sanitização de auditoria, depois de `008-vampiro-sangue.patch`; reverse-check passou. Copiar os arquivos prontos de `files/server/` como nas demais entregas. Nenhuma alteração de addon, plugin ou modpack necessária.

### Persistência e falhas

`runtime/server/data/aetherius-werewolf-compulsion.json`, versão 1:

```json
{"version":1,"characters":{"personagem-sintetico":{"grantId":"concessao-sintetica","onlineMs":1000,"resetStamp":null}}}
```

Escrita via arquivo temporário e rename no mesmo diretório; checkpoint padrão a cada 30 s, e flush antes de iniciar aviso, na desconexão observada, reset e dispose. Até um intervalo de checkpoint pode ser perdido numa queda abrupta; desconexões são amostradas em até 250 ms. Reinício nunca usa timestamps absolutos para cobrar o intervalo offline. O `dispose` não inventa o intervalo depois do último tick. Os registros têm dados de personagem no runtime e **não** são enviados ao Git/acervo.

`aetheriusBeastTransformStamp` contém `{grantId, stamp}` e é invisível ao cliente. Só é gravado após readback da raça de fera. Qualquer transformação real (voluntária ou forçada, inclusive de dia) notifica o contador; entrar em `turning`, falhar `mp.set(appearance)`, morrer ou cancelar não zeram horas. Se o arquivo de horas ficou atrasado em uma queda, o stamp durável da concessão permite reparar o reset no próximo vínculo. Não adota stamp de outra concessão. Este é o readback autoritativo do host; não afirma confirmação visual do motor em todos os clientes.

Falha de disco impede iniciar um aviso novo; o contador em memória permanece para retry. JSON corrompido aborta instalação, sem substituir silenciosamente o histórico. Remoção da maldição descarta o contador quando observada; nova concessão recebe `grantId` novo e começa em zero, mesmo se restou uma linha antiga no arquivo. As keywords permanecem exclusivamente no servidor.

### Cancelamento e isolamento

Antes de t=20, morte, queda, saída, troca de personagem/concessão, remoção da maldição, forma já ativa, locks, relógio indisponível ou amanhecer cancelam e publicam null. O cliente interrompe a instância do som e remove somente os modificadores usados por este aviso; watchdog também limpa se a propriedade final não chegar. Não há callback `setTimeout` atrasado capaz de transformar após cancelamento.

Depois de t=20 a sessão pertence ao werewolf-service: morte/saída interrompem pelo fluxo normal, e os guards acrescentados impedem completar uma animação cuja concessão/personagem mudou. Na troca de personagem, os estados transitórios são limpos sem aplicar o rosto antigo sobre a aparência do substituto. Não se repassa a keyword nem o contador para a propriedade visual.

A propriedade `aetheriusBeastCompulsion` usa `isVisibleByOwner: true`, `isVisibleByNeighbors: false`, `updateNeighbor: ''`. As duas mensagens são enviadas somente ao alvo. A aparência e `aetheriusBeast` continuam pelo fluxo público de vizinhos do kdu: observadores veem a animação/fera e conservam os efeitos normais de corpo/som/tremor de proximidade do pacote. A restrição privada desta entrega se aplica ao pré-aviso de 20 s; o pacote original não foi refeito.

## Reuso conferido no houseCARL

Leitura somente, perfil `AETHERIUS - LOCAL`, epoch `e2-880b44abe6db79b6`, 435 plugins resolvidos. Vencedores conferidos em 16/11:

| Form definidor | EditorID | Uso |
|---|---|---|
| `000809:AETHERIUS - LOBISOMEM.esp` | AethLobisomemCoracao | reprodução local, renovada a cada 9,6 s |
| `000801:AETHERIUS - LOBISOMEM.esp` | AethLobisomemSangueSeguraImod | filtro vermelho; Duration=3 s; renovado a cada 1,5 s sem acumular instâncias |
| `000807:AETHERIUS - LOBISOMEM.esp` | AethLobisomemPulsoImod | pulso vermelho; Duration=1 s |

APIs Play/StopInstance, ImageSpaceModifier.Apply/Remove e Game.ShakeCamera conferidas no corpus papyrus-reference; schemas IMAD/SOUN conferidos em mutagen-reference. Não houve escrita via houseCARL nem em `C:\modOrganizer`. A duração/cadência percebida do áudio e o filtro são candidatos até teste no motor; os assets e condições de distribuição do pacote kdu permanecem os mesmos.

## Valores configuráveis

Configuração por variáveis de ambiente do servidor; exemplos não contêm segredos. Nomes `WEREWOLF_FORCE_` abaixo:

| Sufixo | Padrão | Unidade / limites |
|---|---|---|
| `ONLINE_HOURS` | 72 | horas; mínimo 1 s, máximo 87600 h |
| `NIGHT_START` / `NIGHT_END` | 20 / 5 | hora do jogo 0..23,99; diferentes |
| `HEART_SECONDS` | 5 | início do coração, 0..3600 s |
| `SHAKE_SECONDS` | 5,25 | início do tremor, 0..3600 s |
| `RED_SECONDS` | 10 | início do vermelho, 0..3600 s |
| `WARNING_SECONDS` | 15 | segunda mensagem, 0..3600 s |
| `TRANSFORM_SECONDS` | 20 | início da animação normal, 1..3600 s |
| `HEART_LOOP_SECONDS` | 9,6 | repetição do som, 0,25..60 s |
| `PULSE_SECONDS` | 0,8 | cadência de pulso/tremor, 0,1..10 s |
| `SAVE_SECONDS` | 30 | checkpoint, 0,25..300 s |
| `RETRY_SECONDS` | 30 | recusa do serviço normal, 1..300 s |

Usar ponto decimal no ambiente (`5.25`, `9.6`). Linha do tempo precisa ser crescente ou igual; configuração desordenada é recusada, valores individuais inválidos voltam aos padrões. `WEREWOLF_TURN_MS`, `WEREWOLF_ANIMATION`, `WEREWOLF_DURATION_SECONDS` e `WEREWOLF_COOLDOWN_SECONDS` continuam pertencendo ao kdu; o atalho não ignora sua recarga.

## Testes executados e instalação

Na raiz `C:\Code\Aetherius - Servidor Local`:

```powershell
node --test overlays/sobrenatural/teste-transformacao-forcada.cjs overlays/sobrenatural/teste-origem.cjs overlays/sobrenatural/teste-poderes.cjs overlays/sobrenatural/teste-formas.cjs overlays/sobrenatural/teste-aparencia.cjs source/server/gamemode/werewolf/tests/werewolf.test.cjs source/server/gamemode/magic/tests/magic.test.cjs
node overlays/sobrenatural/teste-servico.cjs 'C:/Code/Aetherius - Servidor Local/source/server/gamemode'
node overlays/sobrenatural/teste-painel-f7.cjs
node overlays/sobrenatural/teste-revisao-f7.cjs
node --check source/server/gamemode/phase0-basic.js
git -C source apply --reverse --check ../overlays/sobrenatural/009-transformacao-forcada.patch
```

**122/122 testes Node** (29 novos de compulsão + 93 de regressão), scripts serviço/F7/revisão F7 e parse/reverse-check passaram. Logs sem jogadores reais em `evidence/forced-base-tests.log`, `forced-service-tests.log`, `forced-f7-tests.log`, `forced-review-f7-tests.log`. Fixtures incompletos do teste antigo de serviço avisam records Bruxa/poderes ausentes; o script passa e não comprova a load order real.

Cobertura: limites noite/dia e autoridade real indisponível/pronta; tempo offline/reinício/personagens; toda a sequência com relógio simulado; readback/stamp e falha de disco; cancelamentos antes/depois de t=20; não sobrescrever rosto de personagem substituto; atalho no alvo, replay/requestId; limpeza/watchdog/privacidade cliente; renovação do IMAD finito. Sem testes que esperam 72 h reais nem sleeps de 20 s.

`tasklist` confirmou SkyrimSE/VR ausentes e o helper do laboratório confirmou servidor local parado antes de `scripts/Prepare.ps1 -SkipPluginSync`. Gamemode preparado no runtime preservando persistência. **Servidor continua parado; nenhum restart/boot foi necessário, nenhum bundle cliente alterado.** A rodada em jogo deve partir do runtime preparado depois da revisão.

## Roteiro para teste em jogo posterior

1. Usar somente perfil `AETHERIUS - LOCAL`. Confirmar assinatura dos scripts (pré-requisito da retomada) e carregamento do módulo no log após iniciar o servidor. Manter 2 jogadores: alvo lobisomem e observador próximo.
2. F7: setar maldição no alvo, definir calendário 22:00 e usar Sobrenatural → **Forçar transformação**. O atalho antecipa somente as horas; de dia deve recusar e pedir noite. Conferir alvo selecionado diferente do operador, motivo e entrada de auditoria.
3. Cronometrar aviso 0/5/5,25/10/15/20 s; áudio repete sem empilhar, vermelho permanece além dos 3 s do record. Só o alvo recebe os avisos, coração e filtro. Aos 20 s começa a animação kdu; ~10,5 s depois a fera é pública. Conferir rosto/roupa/escala e encerramento pelo F7.
4. Interromper em cada ponto com morte, estado caído, relog, remoção da maldição, transformação voluntária e amanhecer. Verificar limpeza em até um tick ou watchdog 2,5 s e nenhum cast tardio. Repetir morte/remoção durante os 10,5 s da animação normal.
5. Com relógio de horas reduzido **temporariamente** em configuração de teste, vencer o limite ao meio-dia: nada acontece; avançar para 20:00 online: começa o aviso. Desconectar por mais que o limite e retornar: intervalo offline não entra. Repetir com reinício e dois personagens da mesma conta. Restaurar padrão 72 h ao terminar.
6. Fazer transformação voluntária à luz do dia e confirmar reset por stamp após raça efetiva; falha/recusa/turning interrompido não devem zerar. Verificar que o observador continua vendo a forma/volta normal e que outro jogador não recebe a propriedade privada.
7. Conferir recarga normal e interações de comércio; alvo morto, caído, Lorde, já Fera ou em editor não deve iniciar. Falhas de visual/áudio/replicação devem virar investigação com log e versão, sem assumir aprovação pelos testes Node.

## Revisão

Autor: Sol High. Achados do autor tratados neste lote: fontes prévias ignoradas não versionadas; filtro kdu com duração finita; perda de maldição durante turning; troca de personagem durante turning. Esses achados não substituem revisão cruzada. Revisão independente por Terra/Luna e consolidação do Opus **pendentes**, conforme fluxo do projeto; registrar depois quem revisou, findings, tratamento e commit final. Testes em jogo foram explicitamente excluídos deste despacho.


Entrega no git raiz do laboratório: `856232aa0cc7474a50017e3382a86fe2d5c7f6aa`. Revisão independente ainda pendente.
