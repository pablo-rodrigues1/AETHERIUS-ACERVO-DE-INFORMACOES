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
