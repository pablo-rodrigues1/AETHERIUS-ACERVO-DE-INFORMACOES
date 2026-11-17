# Revisão 3 — correções de origem, custo e transformação

Data: 16/11/2026. Autor: Sol High, sob despacho do Opus 5.5. Parecer original: `C:\Code\Aetherius - New Systems\Sobrenatural\docs\REVISAO-3_origem-bruxa-forcada_terra.md`, por Terra High, sobre os commits raiz `79098ad`, `856232a` e `4fc0034` de `C:\Code\Aetherius - Servidor Local`. Os cinco achados foram aceitos pelo coordenador. Este relatório registra o tratamento do autor; **confirmação independente de Terra e testes em jogo ainda pendentes**. A Reserva de Sangue foi interrompida antes de qualquer alteração de seu núcleo para priorizar estas correções.

Código entregue no commit raiz **`9b4230a0b9e22d7631aae312da9977c53a062f83`**, cliente corrigido **`79303d57cc0a75246f45ca2ff2c83ba95dceb824`**. A confirmação Terra deve revisar esses artefatos, não apenas a base rejeitada.

## Decisões posteriores do dev

Transmitidas pelo coordenador em 16/11, após pergunta sobre as fronteiras ausentes do host:

1. **Sedução e Reanimação não consomem sangue** e funcionam normalmente pelo caminho nativo. Não bloquear esses poderes por falta da reserva. A exceção substitui a lista provisória anterior de nove poderes cobrados.
2. Força, Névoa, Celeridade, Enxame, Morcego, Visão e Lorde conservam custo de **5**, via `reserve/commit/cancel`. Módulo ausente/incompleto, reserva negada ou commit sem `true` explícito recusam o poder cobrado; não permitir uso gratuito.
3. Remoção/redução retira os grants comprovadamente feitos pelo Sobrenatural. Preservar o que já existia antes da concessão, sem retenção generalizada por dúvida. **Aquisição da mesma magia/perk por fonte externa depois da concessão é inobservável neste host JS e pode ser removida junto**, limitação conscientemente aceita pelo dev. Claims explícitos ativos de GameplayCore/Sobrenatural continuam compondo a união de perks.
4. Não alterar nem recompilar addon nativo, conforme ADR-0015. O protocolo de confirmação da conjuração dirigida continua ausente; a decisão de isenção elimina o débito antecipado nesse caminho.

## Tratamento dos cinco achados

Todos os caminhos nesta seção partem de `C:\Code\Aetherius - Servidor Local`, salvo indicação contrária.

| Achado Terra | Tratamento | Evidência / limites |
|---|---|---|
| 1 — remoção antes de reconstruir claims | Barreira com snapshots **completos** de `gameplay` e `supernatural`, por scope de personagem. Ausência de publicação não significa conjunto vazio. | `source/server/gamemode/supernatural/perk-origin.js:5`; `supernatural/client.js`; `client-source/client/src/services/services/gameplayGrantService.ts:203`; `source/server/gameplay/core/runtime/src/projection/perks.ts:161`. |
| 2 — Sedução/Reanimação debitadas antes da última validação nativa | Removida a interceptação de cobrança desses dois poderes, por decisão do dev. Demais caminhos cobrados falham fechados com API ausente/incompleta ou commit inválido. | `source/server/gamemode/supernatural/power-input.js:52`. Hook nativo anterior recebe alvo, interrupção e argumentos intactos; sua recusa e a recusa nativa posterior não geram reserva ou débito. |
| 3 — fonte externa posterior | Mantida a retirada dos grants próprios e a proteção de preexistentes. Registrada a limitação aceita pelo dev; não foi alegada proteção universal. | `source/server/gamemode/supernatural/grant-origin.js`; testes de aquisição anterior e posterior em `overlays/sobrenatural/teste-revisao-3.cjs`. Não há observador nativo de todas as aquisições externas. |
| 4 — raça confirmada sem stamp durável | Diário independente com intenção gravada e `fsync` **antes** da raça, confirmação após readback, e recuperação por personagem/concessão. O contador consulta o diário, não exige a propriedade dinâmica. | `source/server/gamemode/supernatural/werewolf-transform-journal.js`; `werewolf-service.js:188,230,346`; `supernatural/forced-werewolf.js:80`. Política de crash ambíguo abaixo. |
| 5 — marcador vanilla ignorado | `reached` volta a completar uma sessão vanilla autorizada, com `seq` e marker UUID da sessão, identidade/concessão, vivo/online e janela temporal. | `source/server/gamemode/werewolf-service.js:268`; `supernatural/power-input.js:133`; `werewolf/client.js`. Aeth continua pelo timeout, sem aceitar esse marcador. |

### 1. Barreira de perks

O Core envia `claims: {wanted: [...], owned: [...]}` além dos itens aplicáveis. `wanted` inclui intenção de grants não resolvidos e de fontes nativas, mesmo quando esses records não entram no lote `items`; um lote parcial não pode autorizar a exclusão de um claim omitido. `owned` vem do registro durável do Core. O Sobrenatural publica sua lista completa desejada e as atestações de seu próprio ledger. Ambos usam o mesmo scope SHA-256 do personagem.

O helper compartilhado reconstrói os claims **antes** de percorrer os itens. Um claim publicado já protege a perk mesmo se o `ensure` correspondente estiver atrasado ou falhar. Antes de receber os dois snapshots completos, um `remove` que destruiria a última presença retorna `WAITING_CLAIMS`; nenhuma chamada `removePerk` é feita. O cliente traduz isso em pendência/`FAILED`, verifica novamente nas atualizações seguintes e conserva o registro de autoria para nova entrega. O limite de verificação do Core continua 3 atualizações/500 ms; a property do Sobrenatural tenta novamente a cada 250 ms. Se o outro provider nunca entregar, a remoção permanece pendente, sem falso ACK de remoção.

Disconnect, conexão aceita, spawn, carga e HELLO novo invalidam a hidratação. HELLO antigo/repetido é rejeitado antes de alterar o scope. Scope de outro personagem nunca completa a barreira atual. Presença inicialmente ambígua não é marcada como externa antes da reconstrução: a atestação durável tardia da outra origem pode provar autoria. Presença comprovadamente externa permanece protegida.

**Compatibilidade:** servidor/Core, property do Sobrenatural e bundle cliente precisam desta versão em conjunto. Um lote legado sem `claims` ainda pode assegurar perks, mas não certifica o snapshot completo do Core; remoções esperam. O bundle cliente anterior rejeita o campo novo do Core. O bundle novo foi compilado, **não instalado no MO2 nesta correção**; instalar o candidato somente no mod LOCAL antes de iniciar teste após revisão.

### 4. Protocolo de transformação e crash

Novo arquivo privado do runtime: `C:\Code\Aetherius - Servidor Local\runtime\server\data\aetherius-werewolf.json.transforms.json`. Estrutura versão 1, com mapas `pending` e `confirmed`; cada linha vincula `characterId`, `grantId`, `actorId`, UUID e aparência original. Nenhum dado real desse arquivo é versionado nem enviado ao cliente.

Sequência:

1. Ao chegar ao ponto de trocar raça, gravar intenção em `.tmp`, executar `fsync` no descritor, fechar e renomear no mesmo diretório. Falha impede mudar a raça.
2. Aplicar aparência de fera e conferir readback completo. No-op comprovado aborta a intenção, sem resetar o contador; falha de aborto conserva a intenção para recuperação.
3. Gravar a confirmação no mesmo diário, movendo a intenção para `confirmed`, **antes** de publicar `beast` ou notificar o contador.
4. A propriedade `aetheriusBeastTransformStamp` é espelho privado auxiliar. Falha/no-op dela é registrada, mas não invalida a prova durável já gravada. O contador usa `werewolf.lastTransform(actorId)`, que só retorna a confirmação da identidade/concessão atual.
5. Na reinicialização, uma intenção interrompida é promovida uma vez a confirmação com `recovered: true`. Sua aparência original também permite recuperar uma fera cujo save tenha perdido os campos dinâmicos ou a lista de atores do registro antigo. Sem identidade/concessão compatíveis, a restauração espera e tenta novamente no tick; não aplica o rosto antigo sobre outro personagem.

**Escolha conservadora no crash ambíguo:** depois da intenção durável e antes da confirmação, não é possível distinguir em JS “raça chegou ao jogador, mas seu save não gravou” de “crash imediatamente antes da troca”. A recuperação credita essa tentativa autorizada uma vez para evitar outra transformação forçada imediata. Portanto, nesse intervalo específico, o contador pode zerar mesmo que a raça ainda não tenha chegado ao cliente. Fora dessa recuperação de crash, aviso/turning, recusa, morte e no-op comprovado não zeram horas. Este limite substitui a garantia anterior excessiva de stamp dinâmico durável; não há transação nativa entre aparência e arquivo. `fsync` do arquivo e rename não prometem resistência a qualquer falha de hardware/volume.

### 5. Marcador da animação

Property `turning` agora inclui `marker`, UUID novo por sessão. O callback vanilla publica `{seq, marker}` somente se a publicação capturada ainda for atual. O input conserva o objeto; o dispatcher o encaminha ao método `werewolf.reached`, em vez de descartar o marcador como simples evidência.

O método não cria sessão: exige uma já iniciada pelo servidor, animação `vanilla`, fase `turning`, a mesma concessão/personagem, online/vivo, mesmo `seq` e `marker`, mínimo de 1,5 s desde o início e no máximo `changeAt + 250 ms`. Inteiros antigos, marker de outra geração, duplicados, dono inválido e Aeth são recusados. Ausência do marcador conserva o timeout vanilla de 5,5 s; Aeth conserva 10,5 s por padrão. O relato continua sendo uma dica do dono, não prova nativa da animação; a janela não torna o cliente autoridade para abrir ou prolongar uma forma.

## Entrega e validação

- Fontes prontas em `overlays/sobrenatural/files/server/gamemode/`, incluindo cópia do cliente kdu e de seus testes atualizados; o pacote original em `C:\Code\Aetherius - New Systems\Sobrenatural\AETHERIUS - LOBISOMEM 14-11 - KDU` continua somente leitura.
- `overlays/sobrenatural/010-revisao-3-perk-claims.patch`: mudanças incrementais do Core e testes, depois dos patches 005/006/009 existentes.
- `overlays/cliente-local/004-perk-claims-barrier.patch`: complemento de 003 sobre o checkpoint `057f855` do client-source. Checkpoint corrigido do client-source: `79303d57cc0a75246f45ca2ff2c83ba95dceb824`. Helper cliente/property byte-idêntico, SHA-256 `32AE0640F469C660A9C9AF999B104F81CE1A2731922A69863C57C760E8938592`.
- Bundle pronto em `C:\Code\Aetherius - Servidor Local\client-source\build\dist\client\Data\Platform\Plugins\skymp5-client.js`, SHA-256 `1F0B6C7676A2D404D05C7F157B067922A8A9A0005C4CC53CFE7715289BFBC3BE`. Webpack passou; nenhuma raiz `MeridianUI` ou mod foi alterada.
- Build JS/TS do servidor e staging do Core passaram, `failures=0`. `Prepare.ps1 -SkipPluginSync` preparou o runtime sem reiniciar o servidor. `tasklist` confirmou SkyrimSE/VR ausentes e o helper local confirmou zero processos do servidor.

Testes na raiz do laboratório:

```powershell
node --test overlays/sobrenatural/teste-revisao-3.cjs overlays/sobrenatural/teste-transformacao-forcada.cjs overlays/sobrenatural/teste-origem.cjs overlays/sobrenatural/teste-poderes.cjs overlays/sobrenatural/teste-formas.cjs overlays/sobrenatural/teste-aparencia.cjs source/server/gamemode/werewolf/tests/werewolf.test.cjs source/server/gamemode/magic/tests/magic.test.cjs
node overlays/sobrenatural/teste-servico.cjs 'C:/Code/Aetherius - Servidor Local/source/server/gamemode'
node overlays/sobrenatural/teste-painel-f7.cjs
node overlays/sobrenatural/teste-revisao-f7.cjs
```

**144/144** no conjunto Node, além dos scripts serviço/F7/revisão F7. Em `C:\Code\Aetherius - Servidor Local\source\server\gameplay\core`:

```powershell
node --test runtime/tests/perk-client.test.mjs runtime/tests/perk-origin-barrier.test.mjs runtime/tests/client-delivery.test.mjs runtime/tests/class-skills-delivery.test.mjs
```

**36/36**, com o receptor TS realmente compilado e a property assinável real do Sobrenatural nas duas ordens de chegada. Total dos dois conjuntos: **180/180**. Cobertura nova: storage vazio/reconnect, origem atrasada/ausente, conjunto vazio explícito, intenção não resolvida omitida de `items`, envelope legado/inválido, falha de API/commit, exceções sem cobrança, propriedade de stamp perdida, cortes de persistência antes/depois da raça, crash ambíguo, troca de personagem/concessão e marcador vanilla/Aeth.

Os testes PG `runtime/tests/perks.test.mjs` e `perk-concurrency.test.mjs` foram tentados sem `AETHERIUS_TEST_DATABASE_URL` e recusados por seu guard `Only local aetherius_test`; **não foram executados no banco**. Não se desabilitou o guard nem se apontou para dados reais. Os mocks não demonstram consistência de transações PG ou comportamento do motor.

Logs locais em `C:\Code\Aetherius - Servidor Local\evidence\review3-*.log`: `node-tests`, `core-tests`, `service-tests`, `f7-tests`, `review-f7-tests`, `client-build`, `server-build`, `prepare`. `.gitattributes` conserva os bytes dos patches 010/004, incluindo contexto CRLF do source Windows; os blobs estagiados também passaram reverse-check. Parse do boot, `git diff --check`, reverse-check de 010 no source e de 004 no client-source passaram. Fonte/overlay/runtime comparados sem divergência. Servidor continua parado, nenhum teste humano, addon, OFICIAL ou modpack foi alterado.

## Roteiro humano posterior, após confirmação Terra

1. Instalar o bundle novo somente em `AETHERIUS - SERVIDOR LOCAL\Platform\Plugins`, preservando as cópias completas `(LOCAL)` do Meridian e sem sobreposição de raízes. Conferir assinatura dos scripts e ambas as versões antes de iniciar o servidor.
2. Com personagem de teste, relogar e remover/reduzir uma maldição com perks compartilhadas, atrasando a entrega de uma origem em ambiente descartável. Nenhuma perda temporária de perk ainda desejada; ausência de provider deve aparecer como pendência. Trocar personagem e repetir.
3. Saldo 4 recusa os sete poderes cobrados; 10→5→0 em usos aceitos. Remover/desligar a API em ambiente descartável recusa os cobrados. Sedução/Reanimação mantêm alvo/interrupção/regras próprias e **não alteram sangue**, com reserva presente ou ausente.
4. Confirmar a política de origem em humanos/Bruxa/lobisomem: preexistente permanece, próprio sai na remoção e na redução/upgrade; registrar a limitação de aquisição externa posterior sem anunciar preservação universal.
5. No laboratório descartável, exercitar falha após intenção, após raça e antes de stamp; conferir a recuperação uma vez por concessão. Não apagar/gravar arquivos reais de personagens para simular falhas. Troca de personagem nunca deve receber aparência antiga.
6. Testar **vanilla** com dono e observador: troca no SetRace dentro da janela, sem esperar sempre 5,5 s; sem marcador, timeout normal. Testar depois **Aeth**, que mantém 10,5 s e ignora reached. Conferir retorno, morte, relog e mensagens antigas.

Confirmação Terra não foi presumida. O orquestrador deve registrar o novo parecer e consolidar este relatório, decisões e status no Acervo antes de homologação. A tarefa da Reserva de Sangue permanece aberta e será retomada após a checagem desta prioridade.
