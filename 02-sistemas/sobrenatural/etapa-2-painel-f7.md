# Etapa 2 — Sobrenatural no painel administrativo F7

Data: 15/11/2026. Implementação: Sol High, a pedido do dev via Opus 5.5. Revisão cruzada inicial: Terra High, não aprovada; quatro achados aceitos por Opus e corrigidos nesta versão. **Confirmação final:** Terra High confirmou as quatro correções para teste local, com ressalvas; o dev confirmou que a interface F7 funcionou em jogo na instalação final. Opus autorizou o encerramento desta entrega.

## Resultado

As operações do painel de teste foram integradas ao F7 existente, em uma aba **Sobrenatural**, sobre o jogador online selecionado. **Interface confirmada pelo dev em jogo**, após instalar uma cópia completa do mod de compatibilidade com um único provedor ativo da raiz. As tentativas anteriores (aba ausente e 404) estão registradas na seção de implantação. O cliente envia identificadores/valores fechados; o servidor concede/remove maldição e valida progressão pela API `mp.__aetheriusSupernatural`. Nenhuma keyword é enviada ao cliente.

O F8 foi abandonado por decisão do dev, com causa não confirmada. Histórico completo: [investigação](investigacoes/INVESTIGACAO_15-11_f8-painel-teste.md).

## Ambiente e base

- Repositório local: `C:\Code\Aetherius - Servidor Local`, base `2b77737b405564503573d8e0ea630270b83ba571`.
- Servidor ativo: `C:\Code\Aetherius - Servidor Local\source\`, HEAD base `310cb598fd382cf4d6598cb34eada5db1a3fa9f8`, com overlays locais. O Git raiz ignora essa pasta; a entrega versiona arquivos novos e patches em `overlays\sobrenatural\`.
- Cliente fonte de referência: `C:\Code\Aetherius - Servidor Local\client-source\`, HEAD `a03d9b0`, com patch de criatura do kdu no working tree; bundle não foi recompilado nesta entrega.
- MO2: `C:\modOrganizer`, perfil `AETHERIUS - LOCAL`; a instalação final do frontend F7 usa a cópia COMPLETA `AETHERIUS - MERIDIAN - COMPATIBILIDADE (LOCAL)`, habilitada com o original desabilitado nesse perfil.
- Espelhos oficiais em `C:\Code\Aetherius - OFICIAL` e demais mods não foram editados.
- Acervo: https://github.com/pablo-rodrigues1/AETHERIUS-ACERVO-DE-INFORMACOES/tree/main/02-sistemas/sobrenatural . Revisão e publicação serão consolidadas pelo orquestrador.

## Contrato de ações

Todas usam categoria `supernatural`, permissão `characters.class`, alvo `{actorId, session}` e motivo de 5–240 caracteres. Os IDs de maldição são os mesmos do contrato de apresentação; os rótulos aparecem em português.

| ID do painel | Parâmetros fechados | Chamada interna |
|---|---|---|
| `supernatural.grant` | `curse`: `werewolf`, `vampire`, `witch` | `grant(target.actorId, curse)` |
| `supernatural.remove` | nenhum | `remove(target.actorId)` |
| `supernatural.xp` | `amount`: inteiro de −2.000.000 a +2.000.000 | `addXp(target.actorId, amount)`; domínio limita total a 0–2.000.000 |
| `supernatural.discipline` | `id`: `celeridade`, `fortitude`, `potencia`, `feiticaria_do_sangue`, `tenacidade` | `request({actorId}, 'choose', {kind:'discipline', id, grantId, revision})` |
| `supernatural.vertente` | `id`: `mortui`, `daemones`, `tenebrae` | Mesmo `choose`, `kind:'vertente'` |
| `supernatural.endForm` | nenhum | `mp.__aetheriusVampireLord.finish(actorId)`; havendo sessão de fera, `mp.__aetheriusWerewolf.finish(actorId, {animate:true})` |
| `supernatural.status` | nenhum | `request({actorId}, 'snapshot', {})` |

Exemplo do envelope **já existente** do painel:

```json
{
  "version": 1,
  "requestId": "exemplo_f7_0001",
  "type": "action",
  "data": {
    "action": "supernatural.xp",
    "target": { "actorId": 21, "session": "sessaoAtualDoAlvo123" },
    "reason": "Teste local de progressão",
    "params": { "amount": -20000 }
  }
}
```

`actorId` e `session` são obtidos da lista atual do painel; não são usados como identidade persistente da maldição. O serviço resolve o personagem ativo do ator. Esta entrega antecipa o contrato do painel oficial de eksmurf, sem editar seu repositório.

## Autorização, escolhas e resposta

- **Permissão:** `characters.class`, já usada para definir classe e nível; consta no cargo `admin` e é herdada por `superadmin`, em `source\server\gamemode\admin-panel\aetherius-store.cjs:21`. Não foi criada uma segunda matriz de cargos.
- **Hierarquia adicional:** `staff-policy.cjs:17` classifica `supernatural.*` como `characters` (A+); o frontend usa o mesmo escopo. Moderador é recusado mesmo se receber uma permissão indevida em um mock.
- A hierarquia de alvo do painel permanece: Administrador não atua sobre outro membro de mesmo cargo ou superior; Superadministrador conserva as regras existentes. O operador pode selecionar a si mesmo.
- As escolhas consultam o snapshot atual **do alvo**, extraem `grantId/revision` e chamam `choose`; não escrevem disciplinas/vertente diretamente. `NO_SLOT`, `STALE_GRANT`, `STALE_REVISION` e demais recusas do domínio continuam válidas.
- A operação interna recebe um guard de identidade persistente e sessões (`characterId`, sessão do alvo, operador/sessão e callbacks confiáveis do servidor). `supernatural-service.js:52` revalida dentro da fila e imediatamente antes de mutar; `service.cjs:120` reconsulta permissão, hierarquia e identidade. Depois do await do reset de classe, revalida novamente. Se o contexto mudou após iniciar o reset, salva apenas o personagem original, não publica/aplica no ator atual e sinaliza `effectsDeferred`; `onCharacterActive` reaplica no próximo login. Não desfaz às cegas um reset que pode ter feito efeitos externos.
- Conceder uma segunda maldição retorna `ALREADY_CURSED`; a staff deve remover primeiro. A remoção apaga a progressão.
- Cada sucesso devolve uma projeção explícita: `curse`, `stage` (1–6, ou `null` sem maldição), `xpTotal`, `revision`, `disciplines`, `vertente`, `canChoose`. Não faz spread do registro persistente; `keyword` e `grantId` não são exibidos. A resposta também pode trazer `parameters`, `xpBefore`, `xpAfter`, `effectsDeferred` e `formStatus` para auditoria.
- A mensagem resume maldição, estágio, XP e revisão; a tela já existente do F7 mostra os dados abaixo do resultado. Falta da ponte de reset de classe é exibida como **Reset de classe PENDENTE**, conservando a limitação do domínio.

## Fluxo e auditoria

`admin:request` → `admin-panel/service.cjs` → catálogo/protocolo → autorização e hierarquia → validação de sessão do operador/alvo → `store.reserve(requestId, hash)` → `aetherius-tools.execute` → `supernatural-admin.execute` → API do domínio → `store.finish`.

O `service.cjs` existente, especialmente `:65`, `:103`, `:149`, `:160`, conserva motivo, operador, personagem-alvo, ação, hash e resultado em `aetherius_admin_operations`. Repetir o mesmo `requestId`/conteúdo retorna o resultado reservado, sem chamar o domínio outra vez; mudar o conteúdo com o mesmo ID é `CONFLICT`. A reserva inicial já guarda os parâmetros validados em `result_json.data.parameters`; a finalização conserva-os inclusive em recusas/resultados incertos. Ajustes guardam também XP antes/depois, calculados dentro da fila do domínio, incluindo clamp. `audit-details.cjs` filtra parâmetros pelo catálogo; os detalhes no frontend mostram delta/maldição/escolha e antes/depois, sem keyword ou grantId. Recusas conhecidas do domínio voltam como `rejected`; exceções após iniciar efeitos seguem a política existente de resultado `unknown` para conferência.

Não há transação distribuída entre o JSON da maldição e o PostgreSQL da auditoria. Uma queda após gravar o domínio e antes de finalizar a operação pode deixar `accepted/unknown`: conferir a auditoria e consultar estado antes de gerar outro requestId. Esta entrega não promete atomicidade entre esses dois armazenamentos.

## Arquivos e instalação

Os caminhos abaixo são relativos a `C:\Code\Aetherius - Servidor Local\`:

| Arquivo | Responsabilidade |
|---|---|
| `source\server\gamemode\admin-panel\extension-catalog.cjs:73` | Sete ações, enums e limite assinado de XP |
| `source\server\gamemode\admin-panel\supernatural-admin.cjs:13` | Executor, consulta das revisões e projeção sem keyword |
| `source\server\gamemode\admin-panel\aetherius-tools.cjs:29`, `:249` | Anuncia ações no F7 e encaminha ao executor |
| `source\server\gamemode\admin-panel\staff-policy.cjs:17` | Escopo A+ |
| `overlays\sobrenatural\003-admin-panel.patch` | Diff de catálogo, roteamento, escopo, reautorização, reserva e auditoria |
| `overlays\sobrenatural\files\server\gamemode\admin-panel\supernatural-admin.cjs` | Cópia versionada do executor novo |
| `client-test-panel\admin-panel\app.js:32`, `:58`, `:1360`, `:1441`, `:1850` | Aba, escopo, entrada de inteiro com sinal, validação de sinal e ícone |
| `overlays\sobrenatural\004-admin-panel-client.patch` | Diff reproduzível do frontend sobre `client-source\client\admin-panel\app.js` |
| `overlays\sobrenatural\files\server\gamemode\supernatural-service.js` e `supernatural\client.js` | Cópias finais sem leitor F8 |
| `overlays\sobrenatural\teste-painel-f7.cjs` | Teste do serviço F7 com domínio real e host/store simulados |

### Raiz efetiva do F7 e implantação corrigida

O primeiro teste em jogo, operador S+ LocalA, mostrou as abas antigas e nenhuma aba Sobrenatural. O servidor já anunciava as sete ações: `C:\Code\Aetherius - Servidor Local\logs\server-20261115-170130.stdout.log:49`. A cópia inicial em `Platform/UI` era um override de um caminho legado que este backend não utiliza. A presença no VFS nesse caminho não demonstrava que o CEF o carregava.

A implementação de referência de [BrowserApiMeridian.cpp](https://github.com/pablo-rodrigues1/CompatibilidadeMeridianPlatform/blob/main/source/runtime/skyrim-platform/src/platform_se/skyrim_platform/BrowserApiMeridian.cpp) registra o consumidor `aetherius-client` (`:57`). A raiz local correspondente é `C:\modOrganizer\mods\AETHERIUS - MERIDIAN - COMPATIBILIDADE\MeridianUI\aetherius-client\`, que contém `index.html`, `bootstrap.js`, `widgets.js`, fontes e `admin-panel/app.js`. O conteúdo textual do app do mod coincide com a base do cliente fonte.

A segunda tentativa copiou essa árvore para `AETHERIUS - SERVIDOR LOCAL`, sobrepondo o mod de compatibilidade. O dev recebeu tela cheia `mod://aetherius-client/index.html HTTP ERROR 404` ao abrir o F7 e ficou preso até fechar o jogo. O orquestrador identificou novamente a restrição de raiz canônica do Meridian: duas origens físicas para a mesma raiz não formam um frontend válido, mesmo com vencedor correto no VFS.

**Correção final aplicada pelo orquestrador:** cópia COMPLETA do mod original em `C:\modOrganizer\mods\AETHERIUS - MERIDIAN - COMPATIBILIDADE (LOCAL)`, com o app novo em `MeridianUI\aetherius-client\admin-panel\app.js`. No perfil `AETHERIUS - LOCAL`, habilitou `+AETHERIUS - MERIDIAN - COMPATIBILIDADE (LOCAL)` e desabilitou `-AETHERIUS - MERIDIAN - COMPATIBILIDADE`. Removeu as árvores sobrepostas `MeridianUI/` e `Platform/UI` do mod SERVIDOR LOCAL. A alteração de perfil foi versionada no cliente fonte, commit `e39163c` (substitui a revisão anterior `a03d9b0` para a implantação).

houseCARL confirmou depois: **um único provedor ativo**, COMPATIBILIDADE (LOCAL), para `MeridianUI/aetherius-client/index.html` e `admin-panel/app.js`; `SkyrimPlatform.dll` também vem da cópia LOCAL. O original e o Data do jogo não foram editados. O dev confirmou posteriormente que a interface F7 funcionou em jogo nessa instalação final; resultado transmitido por Opus em 15/11/2026.

Atualização reproduzível, com Skyrim fechado e a cópia COMPLETA (LOCAL) já instalada/habilitada exclusivamente no perfil:

```powershell
& 'C:\Code\Aetherius - Servidor Local\client-test-panel\Install-Painel-F7.ps1'
```

Hash SHA-256 do app preparado/instalado nesta revisão: `18D36FAC004231E463E467D09814DB56B71E41EC8DC7BC910B4079CE1B6104E1`. O console CEF identifica a versão `20261115-r2`. O instalador recusa ausência da cópia completa, original simultaneamente ativo ou outra raiz habilitada sobreposta; só atualiza o app dentro do mod COMPATIBILIDADE (LOCAL).

O rodapé antigo “Sessão oficial validada · alterações bloqueadas” refletia `state.access.readOnly` da aba de acesso oficial. As ações de jogo passam por `aetherius-game.cjs`/`service.cjs` e aparecem habilitadas independentemente desse bloqueio de escrita da API de acesso. O rodapé foi esclarecido nas abas de jogo: “Sessão do jogo validada · ações de staff no servidor”. A aba Acesso conserva seu estado próprio.

Reaplicar backend: copiar `overlays\sobrenatural\files\server\` para `source\server\` depois do overlay kdu; aplicar `002-ui-e-login.patch` e `003-admin-panel.patch`. O overlay inclui a cópia revisada de `werewolf-service.js`, mantendo o pacote original de kdu intocado. `004-admin-panel-client.patch` pode ser aplicado ao cliente fonte para compilação final; o frontend atual roda como asset e não exige webpack.

## Verificação concluída

- `node overlays/sobrenatural/teste-painel-f7.cjs`, na raiz local: passou com domínio Sobrenatural real, protocolo e `service.cjs` do painel reais, host/store/mundo simulados.
- Cobertura: alvo diferente do operador; sete ações; resposta sem keyword; reset pendente visível; replay e conflito de requestId; auditoria do alvo; permissão ausente, Moderador e hierarquia; sessão antiga e alvo desconectado durante prepare; enums inválidos; inteiros fracionários/textuais e limites; XP positivo/negativo e clamp; escolhas recusadas antes do estágio/sem vaga; encerramento das duas formas; F8 não registrado; ação antiga `test` retorna `UNKNOWN_ACTION`.
- Teste anterior `node overlays/sobrenatural/teste-servico.cjs "C:\Code\Aetherius - Servidor Local\source\server\gamemode"`: passou, incluindo spells/perks, raça, Lorde e persistência.
- `node --check` nos arquivos alterados do painel e frontend: passou.
- `git apply --check` do patch de frontend na base cliente e `git apply --check --reverse` do patch servidor aplicado: passaram.
- Após remover o caminho legado também do TypeScript, novo `Build.ps1 -SkipInstall`: TypeScript/esbuild e GameplayCore passaram, `failures=0`. `Stop.ps1` → build → `Prepare.ps1` → `Start.ps1` com Skyrim fechado confirmado por `tasklist`. Servidor PID 22800, log `C:\Code\Aetherius - Servidor Local\logs\server-20261115-172009.stdout.log`.
- `node overlays/sobrenatural/teste-revisao-f7.cjs`: fila bloqueada, ator reutilizado, reset suspenso, autorização revogada, reaplicação no próximo login; falha/ausência silenciosa de restauração preservam original e registro; F7 não retorna sucesso falso; auditoria da reserva e finalização sanitizada, clamp e regressão da projeção de outras ações.

## Validação em jogo e roteiro complementar

**Concluído:** o dev confirmou que a interface F7 funcionou na cópia completa COMPATIBILIDADE (LOCAL), e o orquestrador autorizou o commit/encerramento. Não foi recebido um relatório individual de cada operação ou uma confirmação de renderização das formas; as regras das ações foram exercitadas pelos testes Node. Formas e poderes terão outro despacho, conforme o orquestrador. Roteiro para validação funcional ampliada:

1. Abrir o cliente no perfil `AETHERIUS - LOCAL`, jogador 1, pelo script de lançamento local.
2. F7 → selecionar o jogador 1 → aba Sobrenatural → Consultar estado, informando motivo.
3. Setar Lobisomem; consultar estágio 1/XP 0/revisão; ajustar +20.000 e −20.000. Confirmar que a digitação do sinal negativo funciona no Meridian.
4. Remover → setar Vampiro → tentar disciplina no estágio 1 (recusar) → +20.000 → Celeridade (aceitar) → outra escolha (recusar sem vaga).
5. Remover → setar Bruxa → escolher Mortui → tentar trocar (recusar).
6. Consultar Auditoria: ação, alvo, motivo e resultado devem aparecer. Nenhuma keyword deve ser exibida. F8 deve ficar inativo; o caminho `test` e a flag foram removidos.
7. Com dois jogadores, repetir sobre o segundo alvo e verificar que o operador não muda. Transformação/bonificações e sincronização da fera/Lorde continuam tendo seus testes próprios pendentes.

## Revisão cruzada e pendências

- Revisora independente: **Terra High**. Orquestrador: **Opus 5.5**, responsável pela arbitragem e pela publicação consolidada no acervo. Não foram despachados helpers por este worker.
- Primeira revisão Terra High: **não aprovada**. Os quatro apontamentos foram aceitos, corrigidos e depois **confirmados por Terra para teste local, com ressalvas**. O dev confirmou a interface F7 em jogo na instalação final; Opus autorizou encerrar a entrega. Essa confirmação não equivale a homologação de formas/poderes, de todas as ações em PostgreSQL real ou de multiplayer com dois jogadores.
- Reset de classe na concessão ainda depende da ponte `globalThis.aetheriusClassReset`, pendência anterior à integração.
- A identidade e as sessões agora são revalidadas dentro da fila, antes da mutação e após reset/autorização aguardados. Continua inexistente uma transação distribuída entre JSON, reset externo e auditoria PostgreSQL; recuperação após queda permanece explícita.

## Correções após revisão Terra High

| Achado | Arbitragem Opus | Implementação final do autor | Evidência |
|---|---|---|---|
| 1 — sessão/identidade na fila e reset | Aceito | Guard interno com personagem esperado e tokens; reautorização A+/hierarquia dentro da fila; após reset, persistência só no personagem e efeitos adiados se o contexto mudou | `supernatural-service.js:52`, `:136`, `:150`; `service.cjs:120`; teste de revisão |
| 2 — encerramento sem confirmação | Aceito | `finish` devolve resultado; verifica aparência lida depois do set; falha preserva original/sessão/registro. F7 diferencia indisponível, inativo, restaurado e `unknown/FORM_RESTORE_FAILED` | `werewolf-service.js:174`, `vampire-lord-service.js:86`; teste com throw e no-op |
| 3 — parâmetros ausentes na auditoria | Aceito | Parâmetros validados na reserva e finalização; XP antes/depois da fila; projeção pública do catálogo e apresentação nos detalhes F7 | `aetherius-store.cjs:42`, `:54`; `audit-details.cjs`; teste de reserva/clamp/regressão |
| 4 — resíduos do F8 | Aceito | Removidos função `test`, rota UI Core `test`, projeção hotkey, flag privada e listener. Arquivo antigo contém somente comentário inerte para HTMLs antigos | `002-ui-e-login.patch`, cópias finais do serviço e `supernatural-test.js`; teste `UNKNOWN_ACTION` |

Nenhum achado foi rejeitado. O veredito inicial da revisora foi preservado no documento [REVISAO-2_painel-f7_terra.md](REVISAO-2_painel-f7_terra.md); a confirmação posterior de Terra também consta nesse documento. O status final é: quatro correções confirmadas para teste local com ressalvas e interface aceita pelo dev em jogo; demais limites permanecem explícitos.

## Entrega versionada

O Git raiz de `C:\Code\Aetherius - Servidor Local` registra os patches/cópias, frontend, instalador, testes e espelhos finais destes documentos em `overlays\sobrenatural\docs\`. Os originais deste relatório, da investigação e da revisão permanecem em `C:\Code\Aetherius - New Systems\Sobrenatural\docs\`. Commit final: `ec7d45529f40bcac5e2845262eaea9fda644af85` (`feat(sobrenatural): integrar painel F7 com validacao e auditoria`), árvore do repositório raiz limpa após o commit; a publicação no acervo fica com ele, conforme combinado.
