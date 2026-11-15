# Etapa 1.2b — Correções após revisão cruzada

Data: 2026-11-15. Autor: Terra High / Codex. Base: `C:\Code\Aetherius - New Systems\Sobrenatural\docs\REVISAO-1.2_sol.md`, incluindo arbitragem final de Opus 5.5, e `C:\Code\Aetherius - New Systems\Sobrenatural\docs\ETAPA-1.3_icones-fonte.md`. Cliente consultado na etapa anterior: `C:\Code\Aetherius - OFICIAL\aetherius-client-main\client\ui-core\`; cópia sem commit Git verificável. Este relatório não substitui revisão de confirmação por outro modelo.

## Implementação

Em `C:\Code\Aetherius - New Systems\Sobrenatural\ui-core-module\modules\supernatural\supernatural-module.js`, resposta e push usam `receive` e a `revision` numérica do servidor, monotônica por personagem, sem contador local de chegadas. Revisões antigas ou repetidas não substituem o estado aceito. Uma repetição validada e idêntica pode restaurar a interação após erro, sem substituir o estado. `grantId` opaco e `revision` acompanham toda intenção de escolha. O módulo consulta a cada 10 segundos apenas enquanto montado e sem pedido em trânsito, limpa o timer no unmount e oferece Atualizar em todas as telas.

A validação exige tipos completos, allowlists derivadas dos dados, IDs únicos e limites por maldição/estágio, além de coerência de `canChoose`. Estado inválido mantém o último estado válido, apresenta mensagem recuperável e bloqueia escolhas até recuperação; não remove entradas incorretas silenciosamente. Snapshot idêntico ao último estado aceito pode recuperar um erro, inclusive de transporte. A validação não exige escolha já feita em cada estágio, pois o jogador pode estar com escolha pendente.

`C:\Code\Aetherius - New Systems\Sobrenatural\ui-core-module\INTEGRACAO.md` registra o contrato com `revision`, `grantId`, recusa de concessão antiga, push completo pós-commit e polling. O manifesto e a ordem `supernatural-data.js`, `icons.js`, `supernatural-module.js` foram preservados. O preview contém estados com revisão/concessão e `previewOrdering` para publicação controlada; `preview\test-ordering.cjs` executa os cenários concorrentes com Promises, DOM mínimo e tempo controlado. Nenhum ícone, SVG, fonte ou CSS foi alterado.

## Evidências

- `node --check` passou para `supernatural-module.js`, `supernatural-data.js`, `icons.js` e `preview\test-ordering.cjs`.
- `node Sobrenatural\ui-core-module\preview\test-ordering.cjs`: **PASS** resposta nova após evento antigo, incluindo payload com concessão/revisão; **PASS** resposta e evento antigos após estado novo; **PASS** revisão repetida; **PASS** remoção durante escolha pendente; **PASS** resposta após unmount e limpeza do timer; **PASS** disciplina duplicada e desconhecida com bloqueio e recuperação por snapshot idêntico; **PASS** polling de 10 s sem sobreposição.
- Navegador Orca abriu `preview\index.html` por `file://`: os sete estados alternaram, todos apresentaram Atualizar, as seis telas com maldição mostraram seis cartões e a tela vazia nenhum; console vazio. Isso é teste de preview e não homologação Meridian/CEF no jogo.

## Pendências

Revisão cruzada de confirmação por Sol High, servidor real (emissão durável e monotônica de revisão por personagem, novo grantId por setagem, publicação pós-commit, autorização e transações), e validação em cliente real permanecem para o orquestrador. A segurança depende da validação autoritativa do servidor, inclusive rejeição de grantId antigo; o mock não implementa persistência ou efeitos. O cliente oficial, servidor, modpack, fonte e biblioteca de ícones não foram alterados. Consolidação no diário, RETOMADA e acervo é responsabilidade do orquestrador conforme o escopo deste despacho.
