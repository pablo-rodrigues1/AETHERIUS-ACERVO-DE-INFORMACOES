# Investigação T3 — efeitos legados da transformação forçada (19/11/2026)

## Evidência e diagnóstico

O teste solo descrito em `C:\Code\Aetherius - New Systems\Sobrenatural\docs\TESTE-JOGO_19-11.md` relata no console `updateOwner aetheriusBeastCompulsion - TypeError: fx.cleanup is not a function` com personagem humano. A busca literal nos logs `C:\Code\Aetherius - Servidor Local\logs\server-20261119-003042.stdout.log` e `.stderr.log` não encontrou essa mensagem; o registro vem do console relatado pelo dev, não de uma linha desses logs.

No laboratório base `5effbd8`, `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\supernatural\forced-werewolf-client.js` reutilizava qualquer `sp.storage.aetheriusCompulsionFx` truthy sem verificar o formato e, ao receber valor nulo, chamava `fx.cleanup()` incondicionalmente. Um objeto sem essa função reproduz exatamente a exceção; sua origem específica não foi observada, portanto storage legado é hipótese consistente, não prova de como surgiu. O property update recorrente explica a repetição.

## Correção candidata

Commit de código `632869b`: estado atual com `version: 1` e validação de `cleanup`; invalidação de objeto antigo ou malformado, remoção dos modificadores sinalizados e captura do `heartId` antes de invalidar o listener antigo. Se parar áudio falhar, guarda o handle separadamente e repete no update, sem iniciar outro loop enquanto pendente, seguindo o padrão de `blood-effects-client.js` (commits `93d991a` e `2fffaf9`). Primitivos truthy também são substituídos. O source executável local foi sincronizado com o overlay; não houve instalação no MO2 ou reinício do servidor. O update serializado é enviado pelo gamemode, não exige alteração do código TypeScript do cliente.

## Verificação e limites

- `node --test overlays/sobrenatural/teste-transformacao-forcada.cjs`: 31/31, incluindo migração com cleanup ausente, falha e retry de stop, remoção dos filtros, retomada e valores malformados.
- Bateria `teste-*.cjs` (exceto scripts serviço/F7) mais `werewolf.test.cjs`, `magic.test.cjs`, `survival-service.test.js`, `skysocial-service.test.js`, `survival-effects.test.js`: 447/447 com `AETHERIUS_VAMPIRE_FEED_ENABLED=false` e 447/447 com `true`.
- Em `C:\Code\Aetherius - Servidor Local\client-source\client`: `npx tsc --noEmit --pretty false` passou; `npx webpack` com `DEPLOY_PLUGIN=false` e `ZIP_PLUGIN=false` passou. `git diff --check` passou.
- Testes simulados não demonstram o estado real do storage, a limpeza real dos efeitos nativos nem o desaparecimento do erro em jogo. Revisão independente por outro modelo e reteste em jogo, inclusive migração com áudio ativo, permanecem pendentes e pertencem ao coordenador.
