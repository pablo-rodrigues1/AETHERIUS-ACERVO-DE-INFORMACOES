# Revisão cruzada — Etapa 1.2 / Sobrenatural UI Core

Data: 2026-11-15. Autor da implementação: **Terra High**. Revisor: **Sol High / Codex**, em tarefa Orca `task_c7f8b1e9c0ea`, dispatch `ctx_c41064c89380`. Nenhum arquivo do autor, cliente oficial, servidor oficial, painel ou modpack foi alterado. Este parecer é a única entrega gravada por esta revisão.

**Veredito: REPROVADO na versão analisada**, por falha reproduzível de ordenação de estado autoritativo (apontamento 1). A base de telas e o adapter são aproveitáveis; corrigir a reconciliação e fechar o contrato de atualização antes de considerar a entrega aprovada. A ausência dos handlers de jogo, explicitamente fora da Etapa 1.2, não é por si só motivo da reprovação.

## Escopo, fontes e versão

Todos os caminhos abreviados abaixo se resolvem por estas raízes absolutas:

| Sigla | Raiz |
|---|---|
| `S` | `C:\Code\Aetherius - New Systems\Sobrenatural` |
| `C` | `C:\Code\Aetherius - OFICIAL\aetherius-client-main` |
| `V` | `C:\Code\Aetherius - OFICIAL\aetherius-server-main\server` |
| `R` | `C:\Code\Aetherius - New Systems\referencia` |

Lidos: `C:\Code\Aetherius - New Systems\AGENTS.md`, `S\RETOMADA.md`, `S\docs\ESPECIFICACAO.md`, `DIRECAO-DE-ARTE.md`, `PROPOSTA-TEXTOS.md`, `ETAPA-1.2_telas.md`, as duas coletas em `S\docs\coleta\`, `S\ui-core-module\INTEGRACAO.md`, os três arquivos em `S\ui-core-module\modules\supernatural\` e `S\ui-core-module\preview\index.html`. As cinco imagens em `R` foram abertas, e a prévia Ancião foi inspecionada no navegador Orca em 1290 × 939, sem transbordamento horizontal nesse tamanho.

Também foram consultados o shell, loader, SDK/registry, manifesto, empacotador e módulo `class` do cliente; o sistema UI, router e protocolo do servidor; e o acervo GitHub, `main` observado em `8e29e4b3aa81ccc6597b505ee221c670e6c47a00`: `INDICE.md`, `REGRAS.md`, README de Sobrenatural, UI Core, Lobisomem, Painel ADM e GameplayCore, índice de ADRs e ADRs 0000–0003. A tarefa é exclusivamente de revisão local; a consolidação de diário/RETOMADA e publicação no acervo ficam com o orquestrador.

As cópias locais não fornecem um commit atual verificável. `C\client\ui-core\PROVENANCE.md:11–16` registra bases importadas `665831014f8c10e6b599ae1b29a1f6612522b0f7` (framework) e `f6225187cd7eee5fcd5eea658726d646f8833dc5` (class), sem garantir identidade com os arquivos atuais. Para identificar exatamente a revisão, SHA-256 dos arquivos centrais lidos:

| Arquivo | SHA-256 |
|---|---|
| `S\ui-core-module\modules\supernatural\supernatural-module.js` | `CD487F8D2209817EADB0A2A28000F712FBF05021296BA06DECD71DD4CAE3094C` |
| `S\ui-core-module\modules\supernatural\supernatural-data.js` | `AD36AD1B465AAF951144E1E01B121A51B393C076277052B5643F83C514FFA354` |
| `S\ui-core-module\modules\supernatural\supernatural-module.css` | `FD34D7FF206FB20654D2CA6F87923F0956FB729477EEDD5EFE1A182F5006EBD2` |
| `C\client\ui-core\frontend\shell.js` | `8C59BF6DF818504F3126CD955F5DFE8CA0935BD16932D6BC5BA2D06356A84BB2` |
| `V\ts\systems\aetheriusUiSystem.ts` | `51EFE71B27A3DE0B975253D366E22A3004597A7759D80D891EEE7D48E49B96B9` |

**Atualização de escopo recebida do orquestrador:** ícones SVG e fonte estão sendo refeitos em paralelo; a estética de ambos foi excluída dos apontamentos. Não houve acesso ao modpack nem análise de records/Papyrus/DLLs.

## Apontamentos

### 1. IMPORTANTE — O contador de chegadas descarta uma resposta mais nova e aceita evento mais velho

**Evidência:** `S\ui-core-module\modules\supernatural\supernatural-module.js:43,46,68,71`; contrato em `S\ui-core-module\INTEGRACAO.md:32–35`.

```js
let /* ... */ revision = 0;
function receive(payload) {
  state = safeState(payload && (payload.state || payload));
  revision++;
}
// request:
const startedAt = revision;
const result = await send(action, payload);
if (revision === startedAt) receive(result);
```

`revision` conta mensagens aceitas pelo cliente; não representa a versão do estado no servidor. Portanto, uma atualização recebida durante um pedido pode ser anterior à operação que responde a esse pedido. A chegada de qualquer atualização faz o resultado completo ser descartado, inclusive quando contém a escolha que acabou de ser confirmada no servidor.

**Reprodução executada com o JS original em Node/vm, contexto de transporte controlado e recipiente DOM mínimo:**

1. Montar com vampiro Apoteótico, `disciplines: ['celeridade']`, `canChoose: true`.
2. Clicar em Fortitude; manter a Promise de `choose` pendente.
3. Entregar um evento válido de XP, ainda com apenas Celeridade.
4. Resolver `choose` com `disciplines: ['celeridade','fortitude']`, `canChoose: false`.
5. Resultado observado: apenas Celeridade aparece escolhida. A UI volta a permitir intenção de escolha porque conservou o estado do evento anterior ao commit.

Também foi reproduzido o inverso: entregar primeiro um evento com as duas disciplinas e depois um evento antigo com uma; a tela regride para uma disciplina. O callback de `subscribe` ignora qualquer versão do estado.

O shell não elimina o problema: `C\client\ui-core\frontend\shell.js:825–843` compara revisão de envelopes `snapshot`/`patch`, mas não ordena `event`; `:845–855` resolve a Promise com **apenas `envelope.payload`**, sem filtrar respostas por revisão. O router produz respostas sem revisão de domínio em `V\vendor\aetherius-ui-core\sdk\server\router.ts:152–161`. A projeção de profissões já exemplifica eventos completos com revisão dentro do payload em `V\ts\systems\aetheriusUiSystem.ts:123–136`.

**Impacto:** disciplina/vertente confirmada não aparece, escolha reaparece disponível, XP/estágio ou remoção podem regredir visualmente. Não foi observado efeito concedido localmente nem escalada de autoridade; um servidor correto recusará a intenção extra, mas a UI ficará divergente.

**Correção sugerida:** definir uma revisão monotônica do estado de apresentação por personagem, válida também através de setagem/remoção/reset, e incluí-la em **todos** os payloads completos, tanto respostas como pushes. Normalizar ambos pelo mesmo caminho e comparar essa revisão, sem usar a contagem de chegadas para escolher o vencedor. Exemplo de alteração conceitual, dependente de fechar o contrato com o servidor:

```diff
- let state = null, busy = false, disposed = false, message = '', revision = 0;
+ let state = null, busy = false, disposed = false, message = '', stateRevision = -1;
- function receive(payload) { state = safeState(payload && (payload.state || payload)); revision++; }
+ function receive(payload) {
+   const raw = payload && (payload.state || payload);
+   if (!Number.isSafeInteger(raw?.revision) || raw.revision < 0)
+     throw new Error('Estado sobrenatural indisponível.');
+   if (raw.revision <= stateRevision) return;
+   const next = safeState(raw);
+   state = next;
+   stateRevision = raw.revision;
+ }
```

Em `request`, chamar `receive(result)` sem o teste `revision === startedAt`; em `subscribe`, continuar passando o estado completo ao mesmo `receive`. A revisão precisa vir do servidor, não ser gerada pelo cliente. Estado de mesma revisão deve ser imutável. Isso é uma proposta para revisão do orquestrador, não uma decisão de arquitetura aplicada.

**Validação exigida após correção:** resposta nova depois de evento anterior; resposta antiga depois de evento novo; eventos fora de ordem; mesma revisão repetida; remoção/reset durante pedido; resposta após unmount. Conferir estado final e disponibilidade das escolhas, não somente texto de sucesso.

### 2. IMPORTANTE — Falta tornar obrigatória a atualização contínua e prever recuperação após a primeira consulta

**Evidência:** `S\ui-core-module\modules\supernatural\supernatural-module.js:52–53,66,71–72`; `S\ui-core-module\INTEGRACAO.md:29–37`; referência existente `C\client\ui-core\modules\class\class-module.js:133–148`.

O módulo consulta uma vez no mount. Depois do primeiro estado válido, não existe botão de atualização e não há polling. A única atualização espontânea possível vem de `subscribe`; o UI Core não a fabrica a partir de mudanças do banco. O módulo `class` possui consulta periódica de cinco segundos, enquanto profissões possui projeção explícita no servidor. O documento atual descreve como receber pushes, mas não estabelece sua emissão obrigatória a cada alteração relevante, nem recuperação caso uma projeção falhe.

**Cenário:** consulta inicial bem-sucedida; o jogador ganha XP suficiente para avançar, ou a staff remove a maldição; nenhum push é emitido/entregue pela integração. A tela aberta mantém indefinidamente o estágio/maldição anterior e o jogador não consegue solicitar nova consulta pela própria tela. No ensaio controlado, o HTML após snapshot não continha `data-refresh`; a fonte não contém timer de consulta. Um push válido atualiza normalmente.

**Correção sugerida:** declarar no contrato quais mutações publicam estado completo (XP/estágio, escolha, setagem, remoção/reset), como a projeção é entregue à sessão/personagem atual e qual mecanismo recupera falha de entrega. Incluir uma ação de atualização acessível também nas telas com estado e na tela sem maldição. Se for adotado polling, respeitar `busy`/`disposed`, limpar o timer no unmount e limitar a frequência; isso depende da decisão de transporte abaixo. A revisão do apontamento 1 deve ser compartilhada entre respostas e pushes.

Não se exige implementar agora a mecânica de XP: exige-se que o contrato da UI não deixe implícita a condição necessária para ela permanecer sincronizada quando integrada.

### 3. MENOR — Validação de apresentação aceita disciplinas desconhecidas/duplicadas e conta registros inexistentes

**Evidência:** `S\ui-core-module\modules\supernatural\supernatural-module.js:31–33,59–61`; IDs válidos em `S\ui-core-module\modules\supernatural\supernatural-data.js:30–40`.

`safeState` filtra apenas pelo tipo string; não valida domínio, unicidade, quantidade ou coerência por maldição. O cálculo de vagas usa o comprimento dessa lista, enquanto o selo ESCOLHIDA depende da correspondência com um dos cinco IDs conhecidos.

**Reprodução executada:** Apoteótico com `disciplines: ['celeridade','celeridade']` mostra **“2 de 2 disciplina(s)”**, somente um cartão escolhido e nenhuma opção disponível. Um ID desconhecido também consome vaga sem aparecer como escolhido. Uma vertente desconhecida produz “Vertente registrada pelo servidor” sem cartão correspondente.

Não há XSS nesse caminho, pois os IDs recebidos não são interpolados como HTML; é uma falha de validação/coerência visual diante de bug ou evolução incompatível do servidor.

**Correção sugerida:** validar o snapshot contra allowlists derivadas de `data.disciplines`/`data.branches`, exigir unicidade e cardinalidade compatível com maldição/estágio e rejeitar o estado inconsistente com uma mensagem recuperável. Não “corrigir” silenciosamente o payload removendo entradas e depois liberar uma escolha potencialmente incompatível com o servidor. Limpar disciplinas/vertente somente a partir de um estado autoritativo válido.

## Contrato com o servidor: viabilidade e perguntas ao orquestrador

O transporte proposto **é viável**: o frontend usa a assinatura real `context.request(moduleId, action, payload)` (`C\client\ui-core\frontend\shell.js:118–129,410–431`), recebe o retorno do handler diretamente no payload (`:852`) e recebe pushes do seu próprio módulo (`:855`). Retornar `{state: ...}` ou o estado diretamente funciona com `receive`. Não é necessário criar ModEvent adicional para `supernatural:choose`.

Para disponibilizar o módulo, o caminho concreto é `AetheriusUiSystem.registerExternalModule('supernatural', register)` em `V\ts\systems\aetheriusUiSystem.ts:51–61`, registrando `snapshot` e `choose`; registrar apenas no router deixa `navigationCatalog()` com `available: false` (`:401–402`). A sessão inicial recebe o catálogo em `:373–383`; registrar o módulo depois dessa emissão exige reenviar catálogo ou renovar a sessão. O manifesto local e as capabilities declaradas no adapter não concedem permissão no servidor.

A infraestrutura já vincula `sessionId`, `userId` e `actorId`: `V\ts\systems\aetheriusUiSystem.ts:296–305,321–332` e `V\vendor\aetherius-ui-core\sdk\server\router.ts:66–79`. Ela também deduplica o mesmo `messageId` (`router.ts:81–88`) e permite política de capabilities (`:39–44,101–105`). Isso não substitui autorização de domínio, vínculo do actor ao personagem persistente, serialização de comandos ou validação de estágio. Um segundo clique gera outro ID de transporte; o limite definitivo precisa ser protegido em transação no servidor.

**Perguntas de arquitetura (nenhuma decisão foi aplicada pelo revisor):**

1. Aprovar revisão monotônica por personagem em todos os estados completos, inclusive remoção/reset, conforme apontamento 1? Se o contador reiniciar ao conceder outra maldição, será preciso também identificar a geração da concessão para comparar corretamente.
2. A integração fornecerá projeção com entrega recuperável/outbox, consultas periódicas, ou ambos? Quais eventos e qual garantia de recuperação mantêm uma tela aberta sincronizada, conforme apontamento 2?
3. A intenção de escolha deverá carregar um identificador opaco da concessão/versão observada, validado no servidor? Hoje `{kind,id}` não distingue uma escolha atrasada da concessão anterior de uma nova concessão da mesma maldição após remoção/reaplicação pela staff. `since` tem precisão de dia e não resolve essa identidade. A geração pode ser parte do contrato de apresentação sem revelar keyword.
4. O serviço Sobrenatural será integrado ao caminho durável de comandos do GameplayCore, com lock por personagem e alteração atômica/serializada contra setagem/remoção do painel? O router da UI aceita pedidos concorrentes e não fornece essa transação de domínio. Definir também onde o vínculo ao UUID do personagem e a projeção pós-commit serão feitos, mantendo a forma da fera sob responsabilidade do serviço de kdu.

Essas perguntas qualificam a futura integração de alta complexidade; a revisão não criou um serviço novo nem alterou a arquitetura existente.

## Itens conferidos sem apontamento

| Área | Resultado e evidência |
|---|---|
| Registro/loader | ID, rota `/supernatural`, slot 8, versões e capabilities válidos para `C\client\ui-core\frontend\shell.js:161–175`. O diff do manifesto satisfaz `frontend\modules-loader.js:22–33`: dados antes do adapter, extensões e prefixos locais válidos. A lista `assets` do adapter segue o padrão de `class`; não precisa repetir o próprio script principal. |
| Empacotamento | `C\client\ui-core\scripts\package.cjs:59–69` copia módulos e gera o manifesto. Caminhos dos três assets propostos existem e estão dentro de `modules/supernatural/`. Build completo não executado: requer bridge, distribuição Meridian e bundle externos. |
| Ciclo de vida | `supernatural-module.js:70–73` remove listener de clique, inscrição e classe do shell, limpa DOM e marca `disposed`. Não cria timers. Ensaio confirmou remoção de inscrição/listener/DOM. Retorno de pedido após unmount tem guard de descarte (`:68`); o request pendente continua limitado pelo timeout de oito segundos do shell (`shell.js:121–128`). |
| Autoridade | O código do módulo não concede/remove maldição, não aplica efeitos e não muta escolha local ao clicar. O envio contém apenas `{kind,id}` (`:45,69`). As keywords internas não constam dos arquivos JS/CSS/HTML de apresentação nem do payload proposto. A garantia de nunca transmiti-las deverá ser validada novamente quando existir o serializer do servidor. |
| XSS | Textos variáveis/erros passam por `esc` (`:5,52,56,61,66`); `curse` é allowlist; XP é finita/não negativa; data só produz dígitos/barras a partir da regex (`:32,35`). Ensaio de `since` com `<img ... onerror=...>` não produziu tag HTML. Não foi encontrada interpolação de string arbitrária recebida do servidor sem escape. |
| Curva | `supernatural-data.js:3` usa 0/20.000/60.000/190.000/610.000/2.000.000. Onze ensaios de fronteira (limiares e valores imediatamente anteriores) passaram. O cálculo é compartilhado pelas três maldições (`supernatural-module.js:30,54`). |
| Disciplinas | Para estado válido, primeira vaga no estágio 2, segunda no estágio 6, escolhido indisponível para nova escolha (`:59–61`). Índice `i-1` equivale a nível = estágio−1 porque `i` é índice iniciado em zero. No Ancião, a prévia real mostra +30%/+100/+15%/+100/+100; no Apoteótico a nova disciplina usa nível 5. |
| Vertente | No Neófito, três opções disponíveis quando `canChoose: true` e `vertente: null`; ensaio passou. Com vertente válida registrada, não há vaga para trocar (`:59–61`). |
| Textos/trilha | Lemas, sobretítulos, nomes e descrições correspondem aos documentos lidos. Trilha contém apenas SOBRENATURAL (`:51`). A tela vazia usa a frase exigida (`:53`). O seletor de mocks está só em `preview/index.html`; não faz parte da pasta de módulo a copiar. Textos propostos continuam sujeitos à aprovação do dev. |
| CEF | Alvo documentado em `C\MERIDIAN-COMPATIBILIDADE.md:23`: Meridian 1.5.0 / CEF 152.0.6. Não foi encontrado recurso JS/CSS utilizado pelo módulo incompatível com esse alvo; optional chaining, nullish coalescing, `replaceChildren`, grid, flex gap, `clamp` e variáveis CSS são coerentes com ele. `accent-color` tem fallback WebKit para a barra. Isto não comprova homologação dentro do jogo nem confirma o binário instalado. |
| Performance | Sem polling ou animação contínua no módulo atual; render é limitado a seis marcos e até cinco escolhas. Cada push reconstrói `innerHTML`; manter frequência de projeção controlada/coalescida ao integrar XP, para evitar perda de foco e reconstruções desnecessárias. Não foi identificado crash nativo a partir desta entrega de frontend. |

## Verificações executadas e limites

- `node --check` passou em `supernatural-data.js` e `supernatural-module.js`.
- Node/vm executou os arquivos originais, interceptou o adapter e controlou Promises/eventos para reproduzir os dois problemas de ordenação do apontamento 1 e o contador inconsistente do apontamento 3. Foram usados mocks mínimos de DOM, não um servidor real.
- Os mesmos ensaios conferiram as onze fronteiras da curva, opções de Bruxa no estágio 1, data maliciosa e cleanup. Testes foram transitórios via stdin, sem alterar arquivos do autor.
- Navegador Orca abriu a prévia, alternou para Ancião e verificou estrutura/texto, magnitudes, XP `110.000 / 420.000` para total 300.000 e ausência de overflow horizontal nesse viewport. Não foi aberto o jogo nem compilado o pacote.
- Não há teste de persistência, autorização real de `choose`, reset de classe ou conflito de mods nesta etapa: esses componentes não foram implementados nesta entrega. As conclusões sobre eles são requisitos/perguntas para a integração, não defeitos comprovados de código ausente.

## Encaminhamento para a versão revisada

Terra deve corrigir os apontamentos de UI e contrato aceitos pelo orquestrador, com revalidação dos cenários concorrentes. O orquestrador deve registrar aceites/rejeições com justificativa, responder às perguntas de arquitetura e consolidar diário, RETOMADA e acervo; este documento não presume aceite das sugestões. A investigação de dessincronização está reproduzida neste parecer e pode ser referenciada/extraída para `S\docs\investigacoes\INVESTIGACAO_15-11_ordenacao-estado-ui.md` na consolidação, sem alterar o escopo desta tarefa de arquivo único.

**Veredito final desta revisão: REPROVADO.** Nenhuma correção foi aplicada pelo revisor; a próxima aprovação deve identificar a nova versão e demonstrar que resposta nova não é descartada e estado antigo não regride a tela.

---

## Validação e arbitragem do orquestrador (Opus 5.5, 2026-11-15)

| # | Apontamento | Decisão |
|---|---|---|
| 1 | Ordenação de estado | **Aceito.** O servidor envia `revision` monotônica por personagem em todo estado completo (resposta e push), inclusive após remoção, reset e nova setagem. O cliente descarta revisão ≤ atual e remove o contador de chegadas. |
| 2 | Atualização contínua | **Aceito.** O contrato passa a exigir push de estado completo após XP/estágio, escolha, setagem e remoção. Como reserva, polling de `snapshot` a cada 10 s enquanto o módulo está aberto (respeitando `busy`/`disposed`, com limpeza no unmount), mais um botão "Atualizar" em todas as telas. |
| 3 | Validação de disciplinas/vertente | **Aceito.** Usar allowlist, unicidade e cardinalidade por estágio. Um estado incoerente é rejeitado com mensagem recuperável, sem corrigir em silêncio. |

Perguntas de arquitetura:
1. **Sim.** A revisão é monotônica por personagem e nunca reinicia.
2. **Ambos:** push pós-commit mais polling de reserva (10 s).
3. **Sim.** O estado de apresentação inclui `grantId` opaco, gerado a cada setagem. A intenção vira `{kind, id, grantId, revision}` e o servidor recusa se o `grantId` não for o atual. Isso não revela a keyword.
4. **Fica para a Etapa 2 (Sol High):** integração ao caminho durável de comandos do GameplayCore, com lock por personagem, transação contra setagem/remoção, vínculo ao UUID do personagem e projeção pós-commit. A forma da fera continua com o serviço do kdu.

Próxima ação: o autor (Terra High) aplica 1–3 e o novo contrato **depois** que a Etapa 1.3 (ícones/fonte, Sol High) terminar, para evitar edição concorrente nos mesmos arquivos. O Sol High faz a revisão de confirmação.

---

## Revisão de confirmação

**Data:** 2026-11-15. **Revisor:** Sol High / Codex. Tarefa Orca `task_43c150e3cc0a`, dispatch `ctx_35a8969878c2`. Foram relidos `S\RETOMADA.md`, `S\docs\ETAPA-1.2b_correcoes.md`, a arbitragem acima, `S\ui-core-module\INTEGRACAO.md`, todo o módulo atual e `S\docs\ETAPA-1.3_icones-fonte.md`. As siglas e raízes absolutas são as definidas no início deste parecer. Nenhum arquivo do autor foi alterado; somente esta seção foi acrescentada.

**Veredito de confirmação: APROVADO COM RESSALVAS para a entrega de frontend das Etapas 1.2b/1.3.** Os três apontamentos originais estão resolvidos no escopo da UI e de seu contrato documentado. Restam dois achados menores de feedback de pedidos, descritos abaixo; não foi encontrada regressão de autoridade ou de ordenação do estado válido. Este veredito substitui a reprovação da versão anterior, cujo histórico permanece preservado neste arquivo, e não homologa a integração de servidor ou a execução dentro do jogo.

### Identificação da versão confirmada

Não há commit local verificável. SHA-256 calculados durante esta confirmação:

| Arquivo | SHA-256 |
|---|---|
| `S\ui-core-module\modules\supernatural\supernatural-module.js` | `4BF3037CA676FDA00C8FEB488F5E269D2D4013B6B59C8BA9EF5C120F9CD4AA2C` |
| `S\ui-core-module\modules\supernatural\supernatural-module.css` | `133ABA0794812F7459EE4122F6AECC0F5F7D0AE43D860201706B7C8A9A6196CA` |
| `S\ui-core-module\modules\supernatural\icons.js` | `05B01BDC3D73AFFF5550E89C2C238BCC878A6AB6EEF397C8E75A1DCCE11907B8` |
| `S\ui-core-module\preview\test-ordering.cjs` | `0DDF06DF17A6E6C9523B13B003A0F72419A2944F5AC2A4AC1BB8C716D0B9A5D8` |

Fonte da prévia `S\ui-core-module\preview\fonts\Sovngarde-Bold.ttf` e fonte oficial `C\client\ui-core\frontend\fonts\Sovngarde-Bold.ttf`: SHA-256 iguais, `A4ABB4F2CF49F7E52D87F45D59B1FE2592D464C94F049094F66520072317C2EF`.

### Situação dos apontamentos e da arbitragem

| Item | Status | Confirmação |
|---|---|---|
| 1 — Ordenação de estado | **Resolvido** | `supernatural-module.js:52–67,95–97,109–112` centraliza a recepção, usa a revisão segura enviada pelo servidor e não troca o estado por revisão menor/igual. Reexecutada a corrida original: evento 11 seguido de resposta 12 confirma duas disciplinas; resposta/evento 11 após estado 12 não regridem a tela. |
| 2 — Atualização contínua e recuperação | **Resolvido** no frontend/contrato | `:74–76` oferece Atualizar também no carregamento e na tela vazia; `:115–116` consulta a cada 10 s, respeita `busy`/`disposed` e limpa o timer. `INTEGRACAO.md:32,38–42` exige push completo pós-commit e revisão durável. A emissão real desses pushes permanece para a Etapa 2, como arbitrado. |
| 3 — IDs duplicados/desconhecidos | **Resolvido** | `:13–39` valida allowlists, unicidade, cardinalidade e coerência por maldição; `:69,83,105` bloqueia escolhas após erro. Payload com duplicata/ID desconhecido preserva a última seleção válida e não libera vaga; snapshot idêntico ao último estado válido recupera a interação (`:56–61`). |
| `grantId` na apresentação/intenção | **Resolvido** no frontend/contrato | `:21,30–32,39,106` exige concessão para maldição ativa e transmite `{kind,id,grantId,revision}`. Após remoção e nova concessão, uma resposta antiga não restaura a concessão anterior; a próxima intenção leva o novo ID/revisão. A rejeição autoritativa de concessão antiga ainda precisa ser implementada/testada no servidor, conforme `INTEGRACAO.md:42`; o mock não prova essa autorização. |
| Lock, transação, UUID e persistência | **Fora desta entrega** | Permanecem na Etapa 2, conforme a quarta resposta da arbitragem. Nenhuma implementação desse backend foi pressuposta nesta confirmação. |

### Ensaios reexecutados e complementares

O comando `node S\ui-core-module\preview\test-ordering.cjs` passou nos sete grupos existentes. Além dele, um harness independente e transitório via stdin/Node `vm` executou os três scripts originais (`supernatural-data.js`, `icons.js`, `supernatural-module.js`) com Promises, eventos e timer controlados; não foi gravado teste nem modificado código do autor.

| Cenário | Resultado observado |
|---|---|
| Resposta mais nova após push anterior | **PASS:** snapshot inicial 10, push 11, resposta de escolha 12 com Celeridade/Fortitude → dois cartões escolhidos. |
| Resposta/evento mais velhos e revisão repetida | **PASS:** não substituem o estado mais novo; o teste existente também confirma repetição de revisão com conteúdo conflitante sem regressão. |
| Disciplinas duplicadas/desconhecidas | **PASS:** erro recuperável, zero escolhas habilitadas, última seleção válida preservada; snapshot idêntico ao último aceito restaura as quatro vagas visuais no Apoteótico com uma disciplina. |
| Polling durante consulta inicial e consulta periódica pendente | **PASS:** cinco disparos manuais do timer em cada fase geram somente o pedido já em trânsito; o intervalo registrado é 10.000 ms. |
| Unmount durante pedido | **PASS:** timer, inscrição e clique removidos; disparo residual do callback não envia novo pedido; resposta posterior não repopula o DOM. |
| Remoção durante escolha pendente | **PASS:** remoção de revisão 11 permanece após resposta antiga 10; nenhuma opção de escolha permanece na tela vazia. |
| Concessão antiga e reaplicação | **PASS no cliente:** escolha enviada com `grant-A`/10; remoção 11 e novo `grant-B`/12; resposta antiga de A ignorada; próxima intenção usa `grant-B`/12. **Não executado no servidor:** persistência e recusa real de A. |
| Erro textual malicioso | **PASS:** mensagem `<img src=x onerror=alert(1)>` recebida na rejeição vira texto escapado, sem tag executável. |
| Retorno sem mudança de revisão com mensagem de operação | **Achado menor 4:** mensagem não aparece, conforme reprodução abaixo. |
| Rejeição de pedido antigo após nova concessão | **Achado menor 5:** bloqueio temporário conservador da interação nova; recupera por snapshot idêntico, sem regressão de estado. |

`node --check` passou nos três scripts do módulo. Não foi executado build integral nem backend real. Os ensaios de concorrência usam DOM mínimo; a conferência seguinte no navegador usa o DOM real do Chromium do Orca.

### Confirmação técnica de SVG, fonte, loader e CEF

O manifesto em `S\ui-core-module\INTEGRACAO.md:10–23` tem quatro assets locais, com ordem **dados → icons → adapter**, dentro do limite de 16 do loader real (`C\client\ui-core\frontend\modules-loader.js:23–33`). O adapter declara a dependência em `supernatural-module.js:7–11,44`. Os caminhos existem e têm prefixo/extensão válidos. Não é necessário registrar fontes no manifesto do módulo: o CSS herda a família do shell (`supernatural-module.css:6`), cuja declaração real está em `C\client\ui-core\frontend\styles.css:1–2`; os arquivos de `preview/fonts/` pertencem à prévia.

Na biblioteca `icons.js:143–151`, a classe interpolada é limitada por regex, os desenhos vêm de constantes locais e não de payload de servidor. A seleção dos desenhos da progressão deriva da maldição validada e de um índice fixo (`supernatural-module.js:78–79`); disciplinas/vertentes renderizadas vêm do catálogo estático (`:84`). Foram renderizados todos os 38 IDs e recusada uma classe contendo tentativa de injeção de atributo. Nenhuma saída continha `script`, `foreignObject`, filtro, animação, URL externa, atributo de evento, referência `href` ou IDs SVG compartilhados. As mensagens do transporte continuam escapadas.

Cada SVG tem **seis grupos e seis paths** (incluindo camadas vazias); o maior HTML de ícone produzido possui **1.719 bytes UTF-8**. Não há filtros de blur, animação contínua, carregamento de recurso externo ou listener por ícone. O brilho usa repetição de paths com opacidade e largura de traço. Esse custo é limitado e não indicou problema estrutural de performance; a medição de frame time no jogo continua pendente. Polling reconstrói a tela no início/fim da consulta mesmo quando a revisão é igual (`:93,99`), mas a frequência de 10 s não caracteriza, por si só, defeito bloqueante de performance.

No navegador Orca, a prévia foi aberta novamente e os sete cenários foram alternados por eventos DOM, com console vazio. `document.fonts.check('16px Sovngarde')` retornou `true`; família computada: `Sovngarde, Georgia, serif`. Contagens reais do módulo:

| Cenário | Marcos | SVGs | Paths / grupos | Elementos SVG perigosos pesquisados |
|---|---:|---:|---:|---:|
| Sem praga | 0 | 2 | 12 / 12 | 0 |
| Vampiro Neófito / Ancião / Apoteótico | 6 cada | 14 cada | 84 / 84 cada | 0 |
| Lobisomem Gama | 6 | 15 | 90 / 90 | 0 |
| Bruxa Neófito / Alto Teurgo | 6 cada | 12 cada | 72 / 72 cada | 0 |

Todos apresentaram botão Atualizar. O alvo documentado continua Meridian 1.5.0 / CEF 152.0.6 (`C\MERIDIAN-COMPATIBILIDADE.md:23`); as adições usam SVG básico, `currentColor`, opacidade, CSS já compatível com esse alvo e TTF existente no próprio cliente. Não foi encontrada incompatibilidade técnica nova. A prova de carregamento no Chromium do Orca não substitui teste dentro do Meridian. A estética dos desenhos e da fonte não foi avaliada.

### Novos achados

#### 4. MENOR — Mensagem de operação some quando o estado permanece na mesma revisão

**Evidência:** `S\ui-core-module\modules\supernatural\supernatural-module.js:56–62,97`.

O recebimento retorna `false` para revisão repetida, corretamente evitando substituir o estado. Entretanto, a atribuição de `result.result.message` fica condicionada a esse mesmo retorno. No ensaio, a escolha retornou:

```js
{
  state: estadoAtualNaRevisao10,
  result: { message: 'Escolha recusada: revisão antiga.' }
}
```

O HTML final não continha a mensagem. Uma recusa sem alteração de domínio pode legitimamente conservar a revisão; o mesmo vale para uma confirmação idempotente. O contrato ainda não fixa a forma das recusas: se o servidor usar exclusivamente envelope `error`, o `catch` mostrará a mensagem, mas o ramo `result.message` implementado hoje não cobre esse caso de resposta com estado inalterado.

**Correção sugerida:** separar a decisão de aplicar estado da decisão de mostrar resultado do pedido. Exibir mensagem da operação quando a resposta corresponde à concessão/contexto atual e sua revisão é a atual; continuar descartando mensagens de respostas obsoletas para não sobrescrever feedback mais recente. Alternativamente, fechar explicitamente no contrato que recusas são sempre envelopes `error` e testar essa convenção na Etapa 2. Não incrementar artificialmente a revisão para fazer uma mensagem aparecer.

#### 5. MENOR — Erro tardio de escolha da concessão anterior bloqueia temporariamente uma concessão nova

**Evidência:** `S\ui-core-module\modules\supernatural\supernatural-module.js:69,83,91–99,106`.

**Reprodução:** iniciar escolha em `grant-A`/10; aceitar push válido de concessão nova `grant-B`/12; rejeitar a Promise antiga com `Error('Concessão antiga.')`. O `catch` chama `fail` sem verificar qual concessão iniciou o pedido, marca o estado atual como inválido e deixa zero escolhas habilitadas em B. Snapshot de B/12 idêntico recupera as quatro opções, comprovado no ensaio.

O comportamento é conservador e recuperável pelo polling/Atualizar; não troca a concessão, não aplica efeito e não permite escolher sem servidor. Ainda assim, a falha de uma intenção de A não invalida o estado válido de B que acabou de chegar, e apresenta um bloqueio/erro pertencente a outro contexto.

**Correção sugerida:** capturar concessão e revisão observadas no início do pedido; se uma concessão nova foi aceita enquanto ele estava pendente, descartar o feedback/invalidade derivados da resposta ou erro de A, preservando B. Manter `fail` para falha que realmente deixa incerto o contexto atual. Confirmar o caso também com remoção para estado sem maldição.

### Conclusão da confirmação

**APROVADO COM RESSALVAS.** O defeito que reprovou a primeira versão foi corrigido, e os três apontamentos originais estão encerrados no escopo do frontend. Os achados 4–5 são menores e devem ter aceite/rejeição documentado pelo orquestrador; a Etapa 2 ainda precisa provar revisão persistida, nova concessão por setagem, recusa autoritativa de `grantId` antigo, serialização/autorizações e projeção pós-commit. Não há autorização implícita de aplicar o pacote nos repositórios oficiais ou no modpack.

## Arbitragem final do orquestrador (Opus 5.5, 2026-11-15)
- Veredito aceito: **aprovado com ressalvas**. A Etapa 1 (frontend) está encerrada.
- Achados menores 4 (mensagem de operação some na mesma revisão) e 5 (bloqueio temporário após rejeição de concessão antiga): **aceitos e adiados para a Etapa 2**. Eles dependem do formato real das respostas/recusas do servidor; serão corrigidos e testados junto com os handlers `snapshot`/`choose`.
