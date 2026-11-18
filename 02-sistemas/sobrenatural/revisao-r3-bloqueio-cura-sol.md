# R3 — revisão do bloqueio de cura do vampiro

Data: 17/11/2026. Revisor: Codex, no papel Sol High (R3), independente do autor Terra High. **Veredito: REPROVADO**, por R3-1 e R3-2. Nenhuma correção aplicada nesta revisão.

## Escopo e versão examinada

- Servidor local: `C:\Code\Aetherius - Servidor Local`, commits solicitados `708f0184356d4527856ffdb6f82def10bd75bd5b`, `feae242d7f05852b2ffbd802388896a62703e0d5` e `b7566029e091a3b1e2668305fa1551930da1a35e`, considerando os incrementos posteriores.
- HEAD inicial: `a2e85832a3c93cdc5625e8409daedaad1b4c9638`; durante a revisão outro trabalho avançou para `a35b0e82a8e95ffaba7ce6be3f274961304ddb2e` e depois `15b8174bb2eaabe005ad835d9cd3c28fbefee830` (HEAD final observado). A bateria final foi executada depois da correção `a35b0e8`; `15b8174` altera documentação, sem diferença nos dois módulos revisados em relação a `a35b0e8`.
- Checkout aninhado `C:\Code\Aetherius - Servidor Local\source`: base `310cb598fd382cf4d6598cb34eada5db1a3fa9f8`, com overlays aplicados e alterações locais preexistentes. Não confundir esse commit-base com o estado efetivamente testado. O Git raiz estava limpo nas verificações inicial e final; o Git aninhado contém os deltas locais do laboratório.
- `feae242` altera apenas o relatório da etapa; o crédito +50 é introduzido por `b756602`. `708f018` introduz o filtro.
- Leitura: `C:\Code\Aetherius - New Systems\AGENTS.md`, `Sobrenatural\RETOMADA.md`, `Sobrenatural\docs\ETAPA-3_vampirismo-mecanicas.md` e regras de cura em `Sobrenatural\docs\DECISOES.md:90`. Consultado também o clone documental `C:\Code\Aetherius - New Systems\Sobrenatural\research\acervo-reserva-sangue`, commit `bcdbd9e2f22f5e46c385b06c06f3276e6c905f51`: INDICE, README do Sobrenatural/dependências e ADRs 0003/0015-Sobrenatural/0019/0020. Esse clone é histórico, não foi atualizado nem publicado pelo revisor.
- Único arquivo criado pelo revisor: este relatório. Sem alteração em source, overlays, runtime, cliente, oficiais, painel ou MO2; sem iniciar servidor/jogo, build nativo ou instalar mod. Os testes usam mocks e diretórios temporários próprios.

Conferência dos espelhos em `source\server\gamemode\supernatural\` e `overlays\sobrenatural\files\server\gamemode\supernatural\` (sob a raiz do servidor acima):

| Arquivo | SHA-256, idêntico nas duas cópias |
|---|---|
| `vampire-healing.js` | `3294627755C06A618E9651B44589B3BEADFDF3D9787836FA68B278E1496528BF` |
| `blood-reserve.js` | `C3A74191F7872692385801C65EB7E03ABA98411DCA1840BEC08D4BC878C7A0C3` |

## R3-1 — P1: Fortificar Vida é classificado como cura pura, bloqueando buff e item misto

**Local:** `C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\vampire-healing.js:24–30`, veto em `:45`. Mesmas linhas no overlay `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\supernatural\vampire-healing.js`.

O predicado considera curativo qualquer efeito positivo de Health com archetype 0/34 sem Hostile/Detrimental. Ele ignora **Recover (0x2)**, portanto também considera o modificador temporário de vida máxima como restauração de vida. `flags & 5` não detecta essa diferença.

Evidência houseCARL, apenas leitura, epoch `e2-ac8a903449c25337`:

| Record | Vencedor e dados relevantes |
|---|---|
| `03EAF2:Skyrim.esm`, `MAG_FortifyHealth01`, Potion of Extra Health | `Apothecary.esp`; único efeito `03EAF3:Skyrim.esm`, magnitude 20, duração 300 s |
| `03EAF3:Skyrim.esm`, `MAG_AlchFortifyHealth` | `Apothecary.esp`; PeakValueModifier, Health; flags **Recover**, NoArea, PowerAffectsMagnitude |
| `03EB15:Skyrim.esm`, `MAG_AlchRestoreHealth` | `Apothecary.esp`; PeakValueModifier, Health; NoArea, PowerAffectsMagnitude, **sem Recover** |
| `03EADD:Skyrim.esm`, `MAG_RestoreHealth01` | `Apothecary.esp`; `03EB15`, magnitude 5, duração 10 s |

Os dois efeitos de Health passam pelo mesmo predicado atual, embora um seja buff e o outro cura. A reprodução binária mínima abaixo retornou `pure-health`/`false` tanto para Fortificar Vida isolado quanto para **Restaurar Vida + Fortificar Vida**, que deve ser permitido por ser misto. Não é necessário supor o resultado do motor para demonstrar o veto indevido no wrapper JS. O motor nativo já tem limitações próprias ao interpretar buffs; esta revisão não propõe ampliá-lo, mas o filtro não deve acrescentar esse veto.

**Impacto:** perde-se a unidade no caminho nativo mesmo com veto, e o buff não passa; um item misto com esses dois efeitos também é integralmente vetado, contrariando a regra explícita. Afeta o formato de record efetivamente encontrado em Apothecary, não somente um record malformado.

**Correção recomendada:** distinguir restauração de Health de modificador recuperável/temporário antes de classificar o item inteiro; preservar o buff e tratar cura+buff como misto. Não basta excluir todo PeakValueModifier: a própria cura de Apothecary usa esse archetype. Acrescentar testes dos records representativos e confirmar em jogo os efeitos/consumo.

## R3-2 — P2: retirar efeitos antes do roteamento muda Magicka/Stamina em área

**Local:** `C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\vampire-healing.js:55–56`, em interação com `C:\Code\Aetherius - Servidor Local\source\server\gamemode\magic\healing.js:41–46`.

O wrapper remove Health de `cast.def.effects` antes de chamar o original. O original usa o conjunto completo para decidir `direct`: em uma magia Self com Health direto e Magicka em área, o Health torna `direct=true`, e Magicka em área não é restaurada no passe direto. Depois do filtro, só resta Magicka em área, `direct=false`, e o mesmo passe passa a restaurar Magicka. A política altera um recurso que deveria manter o comportamento anterior. O mesmo raciocínio vale para Stamina.

Reprodução executada: definição com `delivery=0`, efeito Health de área 0 e efeito Magicka de área 50, flags 0, magnitude 10. Antes do filtro/no alvo humano: nenhuma restauração de Magicka no passe direto; no vampiro: `RestoreActorValue('magicka',10)`. O teste de igualdade falha, sem alteração da definição original.

**Alcance da evidência:** regressão reproduzida no código com definição sintética válida para o parser. Não foi localizado nesta revisão um feitiço instalado com exatamente essa composição; não afirmar que uma magia nominal do modpack já manifesta o defeito. A incompatibilidade aparece ao receber essa composição de outro mod.

**Correção recomendada:** preservar as decisões de alvo/área feitas sobre a definição original e suprimir apenas a restauração Health elegível, sem recalcular a distribuição dos demais efeitos sobre uma lista truncada. Adicionar casos Self/direto/área com Health+Magicka e Health+Stamina, incluindo NoArea.

## Crédito da Poção de Sangue e limites já conhecidos

`C:\Code\Aetherius - Servidor Local\source\server\gamemode\phase0-basic.js:433–434` instala primeiro o filtro e depois a reserva. `C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\blood-reserve.js:168–178` encadeia o hook anterior, preserva `false` e só credita +50 para o FormDesc `18ef3:Dawnguard.esm`, ALCH/EditorID `DLC1BloodPotion`, em vampiro autoritativo. Não há outra chamada de cura nesse wrapper.

HouseCARL reconfirmou `018EF3:Dawnguard.esm` vencedor USSEP com três efeitos: script `018EF4:Dawnguard.esm`, cura `013812:Dawnguard.esm` de magnitude 100 e efeito survival `000831:ccQDRSSE001-SurvivalMode.esl`. Logo a classificação atual é mista e o veto de cura pura não intercepta esse item. A tradução local→global dos EFIDs é usada pelo filtro e pelo caminho nativo `C:\Code\Aetherius - Servidor Local\source\server\cpp\server_guest_lib\gamemode_events\EatItemEvent.cpp:44–55`.

**D7 continua aberta e não constitui achado novo desta revisão:** +50 é persistido antes de a remoção nativa terminar, sem recibo/idempotência durável de consumo, transação comum com inventário/PG ou flag própria que desligue a poção. Não aprovar atomicidade a partir dos testes Node. O relatório da etapa já explicita essa limitação e a decisão do dev; nenhuma alteração foi feita aqui.

**Consumo/ledger:** o comentário de `vampire-healing.js:41–42` não prova baixa de todas as poções no PG. O handler anterior chama survival, mas `C:\Code\Aetherius - Servidor Local\source\server\gamemode\survival-service.js:930–934` retorna antes de `_debitLedger` quando `amountsFor` não reconhece uma restauração de necessidades. Poções comuns não são comida nesse classificador. Sem execução de PG/config efetiva, não declarar garantida a equivalência ledger↔inventário para uma poção vetada; conferir esse ponto no roteiro de integração, junto de D7, sem atribuir ao filtro uma garantia inexistente.

**Veto não devolve unidade:** `C:\Code\Aetherius - Servidor Local\source\server\cpp\server_guest_lib\MpActor.cpp:533` chama EatItem, e `:557–558` remove a unidade depois, independentemente do veto aos efeitos. `:545–550` ainda propaga EquipItem aos outros listeners. Isso já estava documentado como limitação, não foi reinterpretado como autorização de estorno. Efeitos locais/visuais e eventual correção de HP precisam de jogo.

## O que passou e o que não foi homologado

- O estado consultado é a API privada autoritativa `mp.__aetheriusSupernatural.stateOf(actorId)`, sem depender de keyword/declaração do cliente e sem replicar keyword. O wrapper não altera maldição.
- Autocura e cura dirigida em concentração tiveram Health bloqueado no alvo vampiro em teste de integração com `magic-service`; Magicka/Stamina diretos permanecem; humano e remoção da maldição voltam ao fluxo original.
- Não há escrita de HealRate/HealRateMult nem interceptação genérica de RestoreActorValue no filtro. A regeneração usa outro caminho: `C:\Code\Aetherius - Servidor Local\source\server\cpp\server_guest_lib\ActionListener.cpp:1146–1177` e `CropRegeneration.cpp:64–76`. Isso é evidência de separação estática, não prova de regeneração no motor com todas as passivas COTV.
- Dreno real tem caminho de Absorb independente: `C:\Code\Aetherius - Servidor Local\source\server\cpp\server_guest_lib\ActionListener.cpp:1314–1345`, que não foi alterado. A exceção JS de FormDesc também passou, mas foi testada com efeito restaurativo sintético; não chamar esse mock de teste do Dreno COTV nativo. Enxame tem seu RestoreActorValue próprio em `source\server\gamemode\supernatural\powers.js:173`.
- Não identificado novo crash nativo ou vazamento de rede no lote. As falhas de leitura de records são capturadas e resultam em `unknown`, permitindo o item; falha na consulta da maldição resulta em não vampiro. Isso é política permissiva observada, não cobertura garantida quando a autoridade está indisponível.
- Performance do filtro: não acrescenta timer, busca global de atores, DB ou escrita por cura; faz leituras por efeito no consumo e alocação de lista por aplicação em vampiro. Resolve o FormDesc do Dreno a cada aplicação inclusive em humano, custo evitável, sem evidência medida de gargalo. Sem benchmark de host/população real, não prometer SLA.
- Não há prova nesta rodada de cobertura de cura por scripts/quests, pergaminhos, outros owners, perks que alteram efeitos no cliente ou toda composição de mods. A revisão não amplia o alcance do filtro para esses caminhos.

houseCARL estava no perfil **AETHERIUS - GRAFICO - QUALIDADE**, 436 plugins ativos, build `2.0.0+5e3c744862bc17c85ebe850830603a9a3e6f0779`. `housecarl_load_order_status(profile='AETHERIUS - LOCAL')` inspecionou LOCAL sem troca: 435 ativos, Apothecary ativo. Os records citados são winners da ordem ativa do houseCARL; não se alegou consulta de winners LOCAL nem identidade completa da ordem com os arquivos carregados pelo servidor. O defeito R3-1 é reproduzível pelo formato confirmado, independentemente do índice runtime. Skills housecarl/mutagen-reference usadas somente para leitura de contexto/schema/records.

## Testes executados e saídas

Node `v22.14.0`; comandos executados em `C:\Code\Aetherius - Servidor Local`. Sem servidor ou PostgreSQL real.

### Bateria existente, estado final

```powershell
node --test overlays/sobrenatural/teste-vampiro-cura.cjs overlays/sobrenatural/teste-reserva-sangue.cjs overlays/sobrenatural/teste-poderes.cjs source/server/gamemode/magic/tests/magic.test.cjs source/server/gamemode/magic/tests/records-ctda.test.cjs source/server/gamemode/core/survival-consumables.test.js source/server/gamemode/survival-service.test.js
```

```text
1..83
# tests 144
# suites 20
# pass 144
# fail 0
# cancelled 0
# skipped 0
# todo 0
# duration_ms 468.5377
exit 0
```

A rodada inicial (sem `teste-poderes`/`records-ctda`, antes do incremento concorrente offlineAt) também passou: 124 testes, 20 suites, 124 pass, 0 fail, duration 466.1216 ms. A rodada final acima prevalece.

### Integração adicional, somente memória/stdin

Três testes Node montaram `magic-service.install(...,{interval:false,registryFile:false})` com fixtures do parser (`magic/tests/fixtures.cjs`) e instalaram o filtro real. Cada teste desinstalou filtro/serviço ao terminar, sem novo arquivo de teste. Cobertura e resultado:

```text
ok 1 - integracao: autocura Health vetada, Magicka/Stamina mantidos, humano e remocao preservados
ok 2 - integracao: magia dirigida em concentracao nao cura vampiro mas cura humano
ok 3 - integracao: excecao explicita de Dreno preservada no filtro JS
1..3
# tests 3
# suites 0
# pass 3
# fail 0
# cancelled 0
# skipped 0
# todo 0
# duration_ms 19.7168
exit 0
```

### Reprodução dos defeitos, somente memória/stdin

Para repetir, alimentar o código abaixo a `node` pela entrada padrão a partir da raiz do servidor. Os asserts expressam o comportamento exigido e **falham no candidato atual**. O fixture de Fortificar usa os campos relevantes dos records confirmados; flags adicionais NoArea/PowerAffectsMagnitude não alteram o predicado `flags & 5`.

```javascript
'use strict';
const test = require('node:test'), assert = require('node:assert/strict');
const { Healing } = require('./source/server/gamemode/magic/healing');
const { classify, install } = require('./source/server/gamemode/supernatural/vampire-healing');
const { mgef, spell } = require('./source/server/gamemode/magic/tests/fixtures.cjs');
function potion(id, effects) { const s = spell(id, effects); s.type = 'ALCH'; return s; }
const rows = new Map([
  mgef(11, 34, { av: 24 }), mgef(12, 34, { av: 24, flags: 2 }),
  potion(21, [{ id: 11 }]),
  potion(22, [{ id: 12, mag: 20, duration: 300 }]),
  potion(23, [{ id: 11 }, { id: 12, mag: 20, duration: 300 }])
].map(r => [r.id, r]));
function fixture() { return {
  __aetheriusSupernatural: { stateOf: id => ({ curse: id === 1 ? 'vampire' : null }) },
  getIdFromDesc: () => 99,
  lookupEspmRecordById: id => ({ record: rows.get(id), toGlobalRecordId: x => x })
}; }
test('R3-A: Fortify Health e buff; nao deve ser vetado como cura pura', () => {
  const mp = fixture(), api = install(mp);
  try {
    console.log('Fortify Health:', classify(mp, 22), 'onEatItem:', mp.onEatItem(1, 22));
    assert.notEqual(classify(mp, 22), 'pure-health');
  } finally { api.uninstall(); }
});
test('R3-B: Restore Health + Fortify Health e item misto liberado', () => {
  const mp = fixture(), api = install(mp);
  try {
    console.log('Restore + Fortify:', classify(mp, 23), 'onEatItem:', mp.onEatItem(1, 23));
    assert.notEqual(mp.onEatItem(1, 23), false);
  } finally { api.uninstall(); }
});
test('R3-C: remover Health nao deve mudar o roteamento de Magicka em area', () => {
  const mp = fixture(), api = install(mp), restored = [];
  try {
    const h = Object.create(Healing.prototype);
    h.valid = () => true; h.s = {}; h.records = { passes: () => true };
    h.restore = (_id, av, n) => restored.push([av, n]);
    const e = (av, area) => ({ av, area, flags: 0, magnitude: 10,
      conditions: [], spellConditions: [] });
    const cast = { caster: 2, spell: 44,
      def: { delivery: 0, effects: [e(24, 0), e(25, 50)] } };
    h.apply(cast, 2, 1, false);
    const humanMagicka = restored.filter(x => x[0] === 'magicka');
    restored.length = 0; h.apply({ ...cast, caster: 1 }, 1, 1, false);
    console.log('human Magicka:', humanMagicka, 'vampire Magicka:', restored);
    assert.deepEqual(restored, humanMagicka);
  } finally { api.uninstall(); }
});
```

Saída efetiva (resumo, omitidas apenas as stacks e durações individuais):

```text
Fortify Health: pure-health onEatItem: false
Restore + Fortify: pure-health onEatItem: false
human Magicka: [] vampire Magicka: [ [ 'magicka', 10 ] ]
not ok 1 - R3-A: Fortify Health e buff; nao deve ser vetado como cura pura
  code: ERR_ASSERTION; actual: 'pure-health'; operator: notStrictEqual
not ok 2 - R3-B: Restore Health + Fortify Health e item misto liberado
  code: ERR_ASSERTION; actual: false; operator: notStrictEqual
not ok 3 - R3-C: remover Health nao deve mudar o roteamento de Magicka em area
  code: ERR_ASSERTION; actual: [['magicka', 10]]; expected: []; operator: deepStrictEqual
1..3
# tests 3
# suites 0
# pass 0
# fail 3
# cancelled 0
# skipped 0
# todo 0
# duration_ms 17.2016
exit 1
```

## Encaminhamento e condição de aprovação

Corrigir R3-1 e R3-2 em lote separado, com testes de regressão; devolver para confirmação independente. A aprovação eventual desse lote não resolve D7 nem homologa motor/multiplayer. Depois, testar no LOCAL: cura pura e baixa de uma unidade; Fortificar Vida; cura+buff; item misto e recursos; Restoration Self/dirigida/área/concentração; Dreno; regeneração; humano e remoção da maldição; comparar HP/inventário/ledger/UI entre owner e observador, inclusive relog.

Os achados e recomendações ficam pendentes de arbitragem/aceitação do coordenador; nada foi tratado silenciosamente como corrigido ou aprovado. O único resultado material desta revisão é este .md. Publicação no Acervo e consolidação de diário/RETOMADA ficam com o coordenador, conforme o escopo explícito de somente leitura deste despacho.

## Reconfirmacao (0e4dd84)

Data: 17/11/2026. Revisor: Codex, Sol High (R3), independente de Terra High. **Veredito: APROVADO no escopo da correção dos achados R3 e dos testes Node.** R3-1/P1 e R3-2/P2 estão corrigidos; não foi identificado novo bloqueador nesse diff. O veredito REPROVADO e as saídas com falha acima permanecem como histórico da versão anterior, não como estado da versão reconfirmada. Esta aprovação não homologa motor/multiplayer nem resolve D7.

### Base e integridade

Revisado o intervalo `15b8174..0e4dd84` na raiz `C:\Code\Aetherius - Servidor Local`: correção P1 `66d42d566a35bdb8f7ab5aa8d25fffd3d1817436`, correção P2 `190d4e715832fb8801012056f0161d52ad7df348`, documentação `0e4dd84ee56a03e65ce637891ee00c069002bfbc`. HEAD permaneceu em `0e4dd84ee56a03e65ce637891ee00c069002bfbc` durante os testes e a verificação final; Git raiz limpo. O diff altera somente o filtro no overlay, seus testes e a documentação da etapa. Não altera `blood-reserve.js`, o contrato nativo, o ledger ou o cliente.

As cópias efetivamente examinadas e testadas são idênticas byte a byte:

- `C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\vampire-healing.js`;
- `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\supernatural\vampire-healing.js`.

SHA-256 de ambas: `B1335E18C664C44FB16445ECDE4FA22E67E6C2345BA3B1AA81813AE9A691BA1E`.

### Confirmação dos achados

**R3-1/P1 corrigido — `vampire-healing.js:26` nos dois caminhos completos acima.** A máscara passou de `flags & 5` para `flags & 7`, incluindo Recover. Fortificar Vida recuperável deixa de ser confundido com restauração; Restaurar+Fortificar é classificado como misto e permitido. PeakValueModifier sem Recover continua elegível para cura pura, preservando o formato de Apothecary confirmado na revisão original. O teste dedicado mantém explicitamente o veto da cura pura em vampiro e sua liberação em humano. Não houve nova consulta ou modificação do modpack nesta reconfirmação; a correspondência dos fixtures com os records está documentada em R3-1 acima.

**R3-2/P2 corrigido — `vampire-healing.js:55–65`, em interação com `C:\Code\Aetherius - Servidor Local\source\server\gamemode\magic\healing.js:39–46`.** O método original recebe agora a definição integral, portanto decide direto/área sobre os mesmos efeitos de antes. Um wrapper temporário por instância omite somente `restore` de `health` para o alvo filtrado; Magicka/Stamina seguem o método anterior. O `finally` devolve o descritor original quando o método era próprio, ou remove a propriedade temporária quando era herdado. Isso impede que a política de uma aplicação permaneça nas seguintes. A implementação atual de `Healing.apply` é síncrona, sem await/callback de restauração diferida; a dependência dessa característica foi explicitamente documentada por Terra e é aceitável no código atual. Transformar esse método em assíncrono exigirá rever o filtro.

Os oito casos de roteamento adicionados por Terra cobrem Magicka/Stamina, Self/direcionada, passe direto/área e NoArea; há também teste de restauração do método após exceção. A reconfirmação independente acrescentou execução real do `magic-service` mockado e verificou o caso de método herdado, além de descritor próprio e exceção. Não foi necessário modificar implementação ou testes para obter os resultados.

### Reexecução independente das três reproduções originais

O bloco JavaScript da seção de reprodução deste próprio relatório foi extraído sem alterações e enviado pela entrada padrão de `node`, em `C:\Code\Aetherius - Servidor Local`. Não foi criado arquivo de teste. Saída:

```text
Fortify Health: mixed onEatItem: undefined
Restore + Fortify: mixed onEatItem: undefined
human Magicka: [] vampire Magicka: []
ok 1 - R3-A: Fortify Health e buff; nao deve ser vetado como cura pura
ok 2 - R3-B: Restore Health + Fortify Health e item misto liberado
ok 3 - R3-C: remover Health nao deve mudar o roteamento de Magicka em area
1..3
# tests 3
# suites 0
# pass 3
# fail 0
# cancelled 0
# skipped 0
# todo 0
# duration_ms 17.6146
exit 0
```

### Bateria solicitada: 324 + 14

Comandos reproduzidos na raiz do servidor local, Node `v22.14.0`:

```powershell
$testFiles=@(Get-ChildItem overlays/sobrenatural -File -Filter 'teste-*.cjs' |
  Where-Object {$_.Name -notin @('teste-servico.cjs','teste-painel-f7.cjs','teste-revisao-f7.cjs')} |
  Select-Object -ExpandProperty FullName)
$testFiles+=@('source/server/gamemode/werewolf/tests/werewolf.test.cjs',
  'source/server/gamemode/magic/tests/magic.test.cjs',
  'source/server/gamemode/survival-service.test.js',
  'source/server/gamemode/survival-effects.test.js',
  'source/server/gamemode/skysocial-service.test.js')
node --test --test-timeout=10000 @testFiles
node --test source/server/gamemode/magic/tests/records-ctda.test.cjs source/server/gamemode/core/survival-consumables.test.js
```

```text
Bateria principal:
1..258
# tests 324
# suites 18
# pass 324
# fail 0
# cancelled 0
# skipped 0
# todo 0
# duration_ms 960.5494
exit 0

Suplementares:
1..7
# tests 14
# suites 4
# pass 14
# fail 0
# cancelled 0
# skipped 0
# todo 0
# duration_ms 131.9093
exit 0
```

### Integrações adicionais independentes: 4/4

Executadas somente por stdin, sem persistir teste novo: `magic-service.install` com `interval:false`/`registryFile:false`, fixtures do parser, estado autoritativo mockado e filtro real. Os três casos de integração da primeira revisão foram repetidos; o quarto verifica a nova estratégia de wrapper, incluindo exceção na restauração de Magicka em vampiro, ausência de propriedade residual e cura normal no humano seguinte. Um descritor próprio não enumerável também foi recuperado integralmente após exceção.

```text
ok 1 - integracao: autocura Health vetada, Magicka/Stamina mantidos, humano e remocao preservados
ok 2 - integracao: magia dirigida em concentracao nao cura vampiro mas cura humano
ok 3 - integracao: excecao explicita de Dreno preservada no filtro JS
ok 4 - restore herdado e proprio sao restaurados apos excecao e aplicacao humana seguinte funciona
1..4
# tests 4
# suites 0
# pass 4
# fail 0
# cancelled 0
# skipped 0
# todo 0
# duration_ms 18.2149
exit 0
```

Portanto passaram **324 principais + 14 suplementares**, além das **3 reproduções originais e 4 integrações independentes**, todos sem falhas. O teste sintético da exceção de Dreno confirma o filtro JS; não substitui execução do Absorb nativo. Regeneração continua sem interceptação no diff, e os caminhos nativos não foram alterados; não foi simulado jogo como prova de regeneração, cura ou consumo real.

### Limites e encaminhamento após reconfirmação

Não há novo achado bloqueador de R3. Permanecem os limites já registrados: D7/crédito +50 ativo sem transação comum ou flag própria, veto sem estorno garantido, conciliação PG/inventário a verificar, cobertura de outros owners e matriz LOCAL/multiplayer. A aplicação temporária de `restore` é síncrona e por instância; não altera globalmente RestoreActorValue nem o cálculo nativo de regeneração. O pequeno custo adicional de closure/descritor por aplicação não foi medido como gargalo, e esta reconfirmação não promete desempenho de host real.

Somente esta seção foi acrescentada pelo revisor. Sem subagentes, novos arquivos de código/teste/evidência, alteração de source/overlays, instalação, servidor, runtime, PG real, modpack ou jogo. Consolidação do veredito e publicação no Acervo permanecem com o coordenador.
