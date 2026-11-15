# Investigação — F8 do painel de teste do Sobrenatural

Data: 15/11/2026. Autor: Sol High, despachado por Opus 5.5.
**Resultado: causa não confirmada, abandonado em favor do F7**, por decisão explícita do dev transmitida pelo orquestrador. Este documento não declara uma correção da tecla F8.

## Ambiente e versões

- Projeto: `C:\Code\Aetherius - New Systems\Sobrenatural\`.
- Servidor local, Git raiz antes da investigação: `C:\Code\Aetherius - Servidor Local`, commit `2b77737b405564503573d8e0ea630270b83ba571`.
- Código ativo: `C:\Code\Aetherius - Servidor Local\source\`, HEAD `310cb598fd382cf4d6598cb34eada5db1a3fa9f8`, com overlays locais. Essa pasta é ignorada pelo Git raiz; os arquivos e diffs reproduzíveis ficam em `overlays\sobrenatural\`.
- Cliente: `C:\Code\Aetherius - Servidor Local\client-source\`, HEAD `a03d9b0`, com patch local do kdu ainda no working tree.
- MO2: `C:\modOrganizer`, perfil `AETHERIUS - LOCAL`; jogador sintético 1, staff local. Skyrim Platform `2.9.0` nos logs.
- Não houve alteração dos espelhos em `C:\Code\Aetherius - OFICIAL`, de plugins ESP, nem de mods compartilhados.
- Documentação oficial: https://github.com/pablo-rodrigues1/AETHERIUS-ACERVO-DE-INFORMACOES/tree/main/02-sistemas/sobrenatural . O orquestrador consolida o acervo após a revisão cruzada.

## Reprodução e observações em jogo

1. Iniciar o servidor por `C:\Code\Aetherius - Servidor Local\scripts\Start.ps1`.
2. Abrir o cliente por `scripts\Start-Client.ps1 -Mo2Profile 'AETHERIUS - LOCAL' -Profile 1 -SkseExecutableName 'SKSE'`.
3. Entrar com o jogador 1 e apertar/soltar F8 (`DirectInput 0x42`), com TAB e console fechados.
4. Resultado informado pelo dev: nada acontece. TAB → Sobrenatural e F7 funcionam normalmente.

Tentativas anteriores recebidas no despacho:

| Tentativa | Caminho | Resultado |
|---|---|---|
| 1 | `updateOwner` de `aetheriusSupernaturalPerks` lê F8 e envia `AetheriusUI.ToView` com `supernaturalHotkey` | Painel não abre |
| 2 | Event source `_aetheriusSupernaturalKey`, `sp.on('update')`, `sendModEvent` | Painel não abre |
| 3 | Mesmo event source, `ctx.sendEvent('f8')`, handler do servidor e envelope `supernatural-test/open` | Log de F8 não aparece |

O defeito da raiz canônica/404 do módulo Meridian já havia sido corrigido pelo orquestrador antes desta investigação. TAB funcionando confirma o módulo visual, mas não confirma a entrega dos event sources.

## Evidências coletadas

Os caminhos da tabela são relativos às raízes absolutas identificadas acima. As linhas referem-se aos arquivos analisados, salvo indicação histórica.

| Evidência | Arquivo:linha | Conclusão limitada |
|---|---|---|
| Event source enviado por nome/conteúdo e notificação da API | `source\server\cpp\addon\ScampServer.cpp:1223` | `makeEventSource` registra o corpo e notifica o estado; não exige handler existente nesse trecho |
| `_` é permitido no alfabeto | `source\server\cpp\addon\ScampServer.cpp:63` | O nome `_aetheriusSupernaturalKey` é válido |
| Eventos remotos sem `_` são recusados | `source\server\cpp\server_guest_lib\ActionListener.cpp:1103` | O prefixo `_` é necessário neste transporte |
| Handler é buscado quando o evento é recebido | `source\server\cpp\addon\ScampServerListener.cpp:41` | A ordem registro → atribuição do handler não explica uma falha após o boot |
| Lobisomem registra evento antes do handler | `source\server\gamemode\werewolf-service.js:205` | A ordem usada no F8 coincide com a base do kdu |
| kdu registra `sp.on('update')` e usa `ctx.sendEvent` | `source\server\gamemode\werewolf\client.js:22` | Mesma estrutura do leitor de F8; não houve novo teste da transformação nesta investigação |
| F7 consulta Input no update | `client-source\client\src\services\services\browserService.ts:47`, `:83` | O F8 usava a mesma thread/evento do padrão funcional |
| Event sources passam por verificação, criação de contexto e execução no próximo update | `client-source\client\src\services\services\gamemodeEventSourceService.ts:32`, `:70`, `:129` | Há etapas anteriores à primeira leitura de F8 que também podem falhar |
| `sendEvent` serializa um `CustomEvent` confiável | Mesmo arquivo, `:101` | Implementação do transporte existe no cliente analisado |
| Trace/error do cliente vai ao console Skyrim | `client-source\client\src\logging.ts:5`, `:19` | Ausência de erro no `skyrim-platform.log` não prova sucesso do event source |
| Hot reload desligado conserva o pacote inicial | `source\server\cpp\server_guest_lib\PartOne.cpp:505`, `:902` | O log de hot reload desligado, sozinho, não prova que os dados deixaram de chegar no login |
| Logger observado pelo dev registra apenas mouse 256–265 | `client-source\client\src\services\services\keyboardEventsService.ts:23` | Ausência de uma linha para F8 nesse logger é esperada; ele não registra teclas de função |

### Diagnóstico temporário em jogo

Foi adicionado um ack `ready` após a primeira leitura bem-sucedida de Input com ator remoto conhecido, e `keyError` para a primeira exceção. O servidor passou a logar o registro do event source, recebimento de `ready`, erro e F8.

- Build completo por `scripts\Build.ps1 -SkipInstall`: TypeScript/esbuild e GameplayCore concluídos, `failures=0`.
- `Prepare.ps1` e `Start.ps1`: servidor pronto, 435 plugins e forms do Sobrenatural 102/102.
- Log do teste: `C:\Code\Aetherius - Servidor Local\logs\server-20261115-165049.stdout.log:58`: `[supernatural] Painel de teste F8: ligado (event source registrado).`
- No mesmo log, `:63`: Sobrenatural ativo, 102/102 forms.
- O dev repetiu F8 duas vezes; não houve `ready`, `keyError` ou log de F8. F7 e TAB funcionaram. Nenhum erro de `eventSources` ou `_aetheriusSupernaturalKey` foi visto no console.

**Interpretação:** não foi demonstrado que o callback de polling executou com contexto utilizável e transporte ativo. A ausência do ack não distingue entrega do pacote, setup do contexto, callback, conhecimento do ator e envio de `CustomEvent`. Não há prova de conflito físico de F8 nem de que trocar a tecla resolveria.

### Modpack, somente leitura

- A skill houseCARL foi carregada e `housecarl_skse(findings='inventory', filter='Aetherius', peek=true)` confirmou o perfil `AETHERIUS - LOCAL`, com a bridge proveniente de `AETHERIUS - UI CORE (LOCAL)`.
- A consulta inicial com filtro contendo barras verticais não encontrou resultados; esse filtro não foi tratado como regex. A consulta simples substituiu-a.
- Busca estática em configs SKSE `.ini` pesquisadas não identificou uma declaração inequívoca de F8/0x42/tecla 66. Isso não cobre hotkeys compiladas, MCM/saves ou consumo em runtime; não demonstra ausência de conflito.
- Nenhuma escrita no modpack foi feita para tentar corrigir F8.

## Encerramento do F8

Decisão posterior do dev: usar o painel da staff F7, já funcional, para as mesmas operações sobre o jogador selecionado.

- Removidos `makeEventSource('_aetheriusSupernaturalKey', ...)` e seu handler de `source\server\gamemode\supernatural-service.js` (comentário de encerramento atual em `:213`).
- Removidos função/export `testKey` de `source\server\gamemode\supernatural\client.js`; permanece apenas a aplicação de perks.
- Cópias correspondentes atualizadas em `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\`.
- A flag `AETHERIUS_SUPERNATURAL_TEST_PANEL` foi removida do código e de `C:\Code\Aetherius - Servidor Local\config\local.env`; o arquivo privado não é commitado.
- Após a revisão Terra, foram removidos também função/rota `test` e projeção `hotkey`. `client-test-panel/supernatural-test.js` e a cópia no UI CORE LOCAL contêm apenas comentário inerte de compatibilidade com HTMLs antigos; não há listener ou comentário de hotkey. `test` agora retorna `UNKNOWN_ACTION`, sem depender de flag. A API interna usada pelo F7 permanece.
- Diagnósticos temporários de `ready/keyError` removidos com o leitor.

## Validação e pendências

- O teste Node do F7 confirma que `_aetheriusSupernaturalKey` não é registrado com a configuração final.
- Não afirmar que a tecla F8 foi consertada: investigação encerrada por mudança de escopo, sem causa raiz confirmada.
- Nova entrega e roteiro de teste: `C:\Code\Aetherius - New Systems\Sobrenatural\docs\ETAPA-2_painel-f7.md`.
- Revisão cruzada de Terra High: primeira versão F7 não aprovada; quatro achados aceitos por Opus e corrigidos pelo autor (sessão na fila/reset, restauração verificável, auditoria, remoção completa do caminho de teste). Terra confirmou posteriormente as quatro correções para teste local, com ressalvas, e o dev confirmou a interface F7 em jogo após a instalação em uma cópia completa COMPATIBILIDADE (LOCAL). O orquestrador autorizou o encerramento e consolida o acervo.
