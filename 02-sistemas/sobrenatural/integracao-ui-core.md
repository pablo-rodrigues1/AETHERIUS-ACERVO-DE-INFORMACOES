# Integração proposta — Sobrenatural no UI Core

A implementação está em `C:\Code\Aetherius - New Systems\Sobrenatural\ui-core-module\modules\supernatural\`. A fonte do cliente consultada é `C:\Code\Aetherius - OFICIAL\aetherius-client-main\client\ui-core\`; a cópia analisada não contém metadados Git, portanto seu commit é indisponível. Nada foi alterado no cliente oficial.

Quando a integração for autorizada, copiar a pasta `supernatural` inteira para `C:\Code\Aetherius - OFICIAL\aetherius-client-main\client\ui-core\modules\supernatural\`. Aplicar este diff a `C:\Code\Aetherius - OFICIAL\aetherius-client-main\client\ui-core\modules.json`:

```diff
@@
   "modules": [
+    {
+      "id": "supernatural",
+      "slot": 8,
+      "route": "/supernatural",
+      "sdkMajor": 1,
+      "styles": [
+        "modules/supernatural/supernatural-module.css"
+      ],
+      "scripts": [
+        "modules/supernatural/supernatural-data.js",
+        "modules/supernatural/icons.js",
+        "modules/supernatural/supernatural-module.js"
+      ]
+    },
```

O `package.cjs` copia as pastas de `modules/` ao frontend empacotado, valida existência e prefixo dos assets do manifesto e gera `modules-config.js`; o loader também exige as extensões `.css`/`.js` e a ordem acima. A biblioteca `icons.js` deve carregar entre `supernatural-data.js` e `supernatural-module.js`; ela exporta `AETHERIUS_SUPERNATURAL_ICONS` com SVG inline, sem arquivos externos no manifesto. O módulo herda a família global Sovngarde do shell; o TTF e sua licença em `preview/fonts/` são exclusivos da prévia e não entram no manifesto. O catálogo do shell já contém a rota e o slot, mas a disponibilidade também depende da navegação autorizada pelo servidor.

## Apresentação — revisão 1.4

A apresentação usa duas colunas: identidade/progresso à esquerda, marcos/dons à direita, mantendo todos os textos. A trilha de seis ícones é informativa e não emite ações; a barra continua mostrando a XP dentro do estágio. A composição inteira foi conferida em 1920×1080; em 1280×720 os dons são alcançados por rolagem vertical normal da área do módulo. O shell deve conservar `overflow:auto` na área de conteúdo, como no cliente de referência. `html[data-motion=reduced]` e `prefers-reduced-motion:reduce` desativam animações e transições.

`preview/comparison.html`, `preview/icons.html`, `preview/references/` e `preview/screens/` são evidências locais e não entram no manifesto. As referências copiadas são usadas somente pela comparação. Não há asset novo de runtime em relação à lista acima; o contrato, a revisão monotônica e o intervalo de polling de 10 segundos permanecem os mesmos.

### Exceções de shell e ciclo de vida — Correções 1.4

O conteúdo e seu padding aprovado pertencem à raiz `.aetherius-supernatural.module-mount`. Os overrides redundantes de fundo de topline, conteúdo e rodapé foram removidos. Para conservar o visual aprovado nas partes externas à raiz, ficam **somente estas exceções de integração**, prefixadas por `#aetherius-shell.is-supernatural-workspace`, sem `!important`:

| Elemento externo | Propriedades | Motivo |
|---|---|---|
| `.world-shade` | `background:rgba(0,0,0,.12)` | A camada de mundo cobre o workspace inteiro e é ancestral externa à raiz. |
| `.workspace-frame` | Fundo preto com alfa `.78`, borda zero e sombra aprovada | A moldura do shell inclui regiões externas ao módulo; um fundo dentro da raiz não substitui sua pintura. |
| `.workspace-rule` | `display:none` | O divisor é irmão externo do conteúdo; não pode ser ocultado pelo estilo de um descendente. |
| `.module-content` | `padding:0` e `scrollbar-color` | Neutraliza o padding padrão do host para que o padding seja aplicado pela raiz; a barra pertence ao host rolável do shell. O `overflow:auto` vem do shell, sem override. |

Isso é uma exceção local documentada, não uma nova API genérica do UI Core. A classe é adicionada dentro do `try` de montagem; o `finally` desfaz a montagem se ela não chegar a retornar. O cleanup é idempotente e usa `finally` encadeados para limpar timer, subscription, listener, atributos/conteúdo da raiz e a classe própria do shell, mesmo se uma etapa de cleanup lançar erro. Se a classe já existia antes do mount, seu estado anterior é preservado. As exceções deixam de alcançar outro módulo após o unmount normal ou a falha de montagem.

O polling com revisão igual não troca `innerHTML`, não mostra carregamento e não recria controles. Render ocorre quando há novo estado, recuperação da validação ou mudança de mensagem/erro. Durante um pedido, `aria-busy`/`aria-disabled` e a guarda `busy` impedem pedidos sobrepostos sem desabilitar nativamente e desfocar o botão. Quando há reconstrução necessária, o foco é restaurado pelo identificador lógico de Atualizar ou da escolha, somente se o controle ainda estiver disponível; foco externo não é capturado. A rolagem da raiz e de seus ancestrais é restabelecida depois da troca e do foco.

Regressões reproduzíveis: `node preview/test-ordering.cjs` a partir de `ui-core-module/`; abrir `preview/test-interaction.html` para os ensaios com DOM real, inclusive navegação e falhas de montagem. `preview/compare-captures.cjs`, a página de teste e seu JS são exclusivos de verificação e não integram o manifesto.

## Contrato de apresentação — servidor ainda não implementado

O módulo chama `context.request('supernatural', action, payload)` e assina `context.subscribe`. São necessárias as ações `snapshot` com `{}` e `choose` com `{kind, id, grantId, revision}`. O nome lógico da intenção é **`supernatural:choose`**, representado pelo par módulo `supernatural` + ação `choose` no transporte existente; não é um ModEvent novo. `kind` é `discipline` ou `vertente`; IDs de disciplinas: `celeridade`, `fortitude`, `potencia`, `feiticaria_do_sangue`, `tenacidade`; de vertentes: `mortui`, `daemones`, `tenebrae`.

O servidor registra o módulo externo com `AetheriusUiSystem.registerExternalModule('supernatural', register)`, provê disponibilidade no catálogo da sessão e retorna estado completo após cada ação; registrar apenas o router não disponibiliza a navegação. Além disso publica para a sessão/personagem atual o estado completo após commit de XP/estágio, escolha, setagem e remoção/reset. Push pós-commit é obrigatório; polling de `snapshot` a cada 10 segundos enquanto montado e o botão Atualizar são recuperação complementar, não garantia de entrega ou persistência.

```json
{"state":{"revision":42,"grantId":"id-opaco-da-concessao","curse":"vampire","since":"2026-11-02","xpTotal":190000,"disciplines":["celeridade"],"vertente":null,"canChoose":false}}
```

Todos os estados completos, inclusive respostas e pushes de remoção, levam `revision`: inteiro seguro não negativo, monotônico por personagem, persistido e nunca reiniciado por remoção, reset ou nova setagem. Uma revisão identifica um conteúdo imutável. Cada setagem gera `grantId` opaco novo, string não vazia; sem maldição usa `grantId: null`, `curse: null`, `xpTotal: 0`, lista vazia, `vertente: null` e `canChoose: false` (data pode ser `""`). Para maldição ativa `since` é data `AAAA-MM-DD`; `curse` é `vampire`, `werewolf` ou `witch`; `xpTotal` é inteiro seguro não negativo; `disciplines` é lista de IDs únicos conhecidos; `vertente` é ID conhecido ou `null`; `canChoose` é booleano. Vampiro não possui vertente e pode ter no máximo zero disciplinas no estágio 1, uma nos estágios 2–5 e duas no estágio 6; bruxa não tem disciplinas e tem no máximo uma vertente; lobisomem não possui escolhas. `canChoose` não pode ser verdadeiro sem vaga aplicável. O estado pode vir diretamente ou sob `state`.

Resposta e push passam pela mesma validação e comparação; versões menores ou iguais nunca substituem estado aceito. Um snapshot idêntico à versão aceita pode restabelecer a interação após erro sem substituir os dados. Snapshot incoerente mostra erro recuperável e bloqueia escolhas até estado válido; não são descartados IDs individuais silenciosamente. O cliente calcula estágio somente para apresentação e não aplica efeitos nem registra escolhas localmente.

O servidor precisa conferir o vínculo personagem/sessão, validar o `grantId` contra a concessão atual e recusar uma intenção de concessão antiga, além de validar `revision`, maldição, estágio, disponibilidade, limites, unicidade e irreversibilidade com proteção transacional. O servidor é autoritativo; não pode confiar em `canChoose` nem no estágio derivado na UI. A política de conflitos de `revision`, serialização, persistência e transações será definida na Etapa 2; `grantId` é obrigatório para impedir que pedido atrasado afete concessão posterior. Nenhuma keyword interna integra a apresentação.

Até existir handler, a tela real informa erro de consulta; a prévia funciona com estados simulados. Build integral e testes de servidor/CEF continuam pendentes.
