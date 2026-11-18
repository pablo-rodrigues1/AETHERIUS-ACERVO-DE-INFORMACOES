# Revisão R2 — Reserva de Sangue — Terra High

Data: 17/11/2026. Revisora independente: Codex, papel Terra High; autor do lote: Sol High. Base inspecionada: `C:\Code\Aetherius - Servidor Local` HEAD `d0a1258` (solar `4c631e1`, HUD `1f84be4`, efeitos `6fb08d1`, relógio `f5a4593`, alimentação `e4133ac`/`3246967`); `C:\Code\Aetherius - Servidor Local\client-source` HEAD `f57ec7d`. Fontes: `C:\Code\Aetherius - New Systems\AGENTS.md`, `Sobrenatural\RETOMADA.md`, `Sobrenatural\docs\ETAPA-3_vampirismo-mecanicas.md`, `Sobrenatural\docs\DECISOES.md` e `C:\Code\Aetherius - New Systems\referencia\Reserva_de_Sangue_Aetherius.md`. Este relatório é somente revisão estática e testes automatizados locais; não prova execução no jogo, rede, PostgreSQL ou modpack.

## Veredito

**Não aprovar como entrega completa ou habilitar alimentação para uso normal.** O candidato tem lacunas funcionais e riscos abaixo; os 298 testes passaram, mas usam mocks e não eliminam esses pontos. D7 é bloqueio conhecido e aceito para este despacho, não constitui motivo novo de reprovação. As correções e sua avaliação ficam com o autor e coordenador; nenhuma foi aceita ou rejeitada nesta revisão.

## Achados

1. **Médio — visão de Sede Crítica não demonstrou cobertura de criaturas, requisito definido.** `C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\blood-effects.js:18` só percorre `neighbors` e `:19` exige propriedade `type === 'MpActor'` e `isDead === false`. Não há demonstração de que criaturas existentes sejam vizinhas com essa representação e essas propriedades. Os testes mockam atores; o próprio documento de entrega admite cobertura de criaturas pendente. Consequência: não se pode aprovar o requisito de revelar criaturas e jogadores até prova no runtime com criaturas e observadores, ou integração corrigida.
2. **Médio — cauda da sessão descartada se a lista online falhar ou desaparecer antes do callback de saída.** `source\server\gamemode\supernatural\blood-reserve.js:99`–`:102` transforma exceção ao ler `onlinePlayers` em lista vazia e remove entradas de `active` sem `close`; no próximo callback `onCharacterInactive`, `:42`–`:44` já não encontra entrada. O tempo desde a última liquidação (normalmente até 10 s, potencialmente mais com falha) não é debitado, mesmo com disco saudável. Falha é condicional, não foi reproduzida no runtime; testar leitura excepcional e ordem real de desconexão, preservando a referência até fechamento seguro.
3. **Médio — risco de bloqueio do event loop por crescimento da reserva.** `source\server\gamemode\supernatural\blood-reserve.js:30`–`:31`, `:68`–`:74`, `:99`–`:106` e `:112` reescrevem sincronicamente o JSON inteiro, com cópia e serialização de todos os personagens, para cada vampiro com sangue a cada 10 s, além de operações de poderes/poção. Com V vampiros ativos e N registros acumulados, o trabalho do tick cresce aproximadamente V×N e ocorre no loop do servidor; não há teste de carga ou limite de histórico comprovado. Medir latência/event-loop e volume real antes de homologar; propor lote/armazenamento apropriado mantendo integridade.
4. **Baixo — efeitos e HUD têm latência de até um tick de reserva, não atualização imediata por drenagem.** `blood-reserve.js:82` é `peek`, sem liquidar tempo; `blood-effects.js:29` e `blood-presentation.js:17` usam esse snapshot, atualizado por `blood-reserve.js:106` a cada 10 s. Consequentemente cruzamentos de 30 e 0 podem aparecer com atraso de até cerca de 10 s mais agendamento. Confirmar se tolerância atende à decisão e testar bordas. Mudanças por poderes/adição são liquidadas antes do próximo tick de apresentação.

## D7 e alimentação desligada

`source\server\gamemode\supernatural\feeding.js:7` só habilita com string exata `true`; `:57`–`:58` recusa elegibilidade quando false, e `:159` retorna oferta somente após essa verificação. Início também passa pela mesma verificação (`:106`–`:109`); logo menu e solicitação não liquidam alimentação por padrão. Contudo desativar não significa ausência completa de efeitos: `:21`–`:44` carrega e recupera journal, e `:50` mantém personagens com reconciliação bloqueados; o lock social em `source\server\gamemode\skysocial\src\index.js:16` continua aplicável. Essa quarentena é conservadora, não prova de alimentação ativa.

D7 está documentada em `Sobrenatural\docs\ETAPA-3_vampirismo-mecanicas.md` seções 17/11 e `DECISOES.md` §17/11: alimentação protótipo desligado, journal, reconciliação manual e sem replay automático; poção segue sem transação atômica e sem autorização para garantia inferior. Ressalva operacional: o crédito antigo de poção ainda é instalado sem flag em `blood-reserve.js:118`–`:128`, portanto D7 não é um desligamento técnico da poção; é bloqueio de homologação. Não interpretar o registro como garantia de que não haja crédito em runtime.

## Demais verificações e limites

- Curva por faixas, 0–100, custo via reserve/commit/cancel e ações server-side conferidos estaticamente; persistência e comportamento sob crashes continuam dependentes dos limites acima e D7.
- Não encontrei envio literal das keywords `JogadorVampiro` etc. nos novos payloads inspecionados: sangue e efeitos privados para o dono, Feral público como apresentação. Isso não certifica todos os canais históricos fora do lote.
- Solar está desligado por padrão; evita o spell legado conhecido antes do dano (`vampire-sun.js:7`, `:35`–`:44`). Não garante ausência de outro caminho solar de mod; não foi feita leitura nova do MO2 nem teste em jogo.
- Aura, áudio, raio, política PvP, animação para três jogadores, HUD real e ownership de Health continuam por validar no jogo. As decisões D1–D3 permanecem abertas; a política de restauração é candidata.
- Não foram alterados código, runtime, cliente ou modpack, nem publicados documentos no Acervo por restrição expressa deste despacho; o coordenador deve consolidar e publicar a documentação.

## Testes executados

Na raiz `C:\Code\Aetherius - Servidor Local`, executei exatamente a seleção PowerShell documentada em `overlays\sobrenatural\docs\ETAPA-3_vampirismo-mecanicas.md` seção de validação final: todos os `teste-*.cjs` do overlay exceto os três scripts de integração, mais werewolf, magic, survival-service, survival-effects e skysocial-service. Saída final e exit 0:

```text
1..232
# tests 298
# suites 18
# pass 298
# fail 0
# cancelled 0
# skipped 0
# todo 0
# duration_ms 922.8458
```

Também executei `node overlays/sobrenatural/teste-servico.cjs 'C:\Code\Aetherius - Servidor Local\source\server\gamemode'`, `node overlays/sobrenatural/teste-painel-f7.cjs` e `node overlays/sobrenatural/teste-revisao-f7.cjs`: os três imprimiram suas linhas `OK`; foram exibidos avisos de records ausentes e de reset de classe no ambiente mockado. A sequência inteira retornou 0; como os comandos foram encadeados sem capturar individualmente cada código, a evidência de sucesso individual é a linha `OK`, não uma medição independente dos três exits.

## Reconfirmacao (a2e8583)

Revisão independente de `d0a1258..a2e8583` em `C:\Code\Aetherius - Servidor Local`, 17/11/2026, por Terra High. O HEAD raiz é `a2e8583`; o cliente permanece `f57ec7d`. O `blood-reserve.js` em `source\server\gamemode\supernatural\` e o do overlay têm SHA-256 idêntico `B3419C4CE101714BE23CF38C9D966D78AF8C7D11D6AA1837FB942CE7E8B895DB`.

**Veredito: confirmação parcial; #2 ainda tem uma condição de contagem offline a resolver ou arbitrar; #3 e #4 confirmados no escopo dos testes, com as ressalvas operacionais abaixo.** A conclusão original sobre entrega completa e alimentação permanece: não homologadas, D7 segue aberta e o teste em jogo é necessário. Não houve alteração de código nem teste no jogo.

1. **Criaturas (#1): evidência estática ampliada, cobertura em jogo pendente.** `source\server\cpp\server_guest_lib\WorldState.cpp:403` identifica NPC_, a construção descrita pelo autor em `:697`–`:699` materializa ator; `source\server\cpp\addon\property_bindings\TypeBinding.cpp:10` usa `AsActor()` e `NeighborsBinding.cpp:12`–`:16` agrega listeners/emitters. Isso sustenta a possibilidade de criaturas materializadas chegarem como MpActor, não prova que todo animal visível esteja instanciado/relevante ou que o shader funcione. Aceito a arbitragem do coordenador de não bloquear esta rodada por #1; lobo, criatura gerenciada/invocada e jogador, distâncias e relevância continuam testes obrigatórios antes de homologar cobertura.
2. **Cauda (#2): corrigida a perda original, mas incompleta para callback posterior à observação de ausência.** `blood-reserve.js:31`–`:37` e `:142`–`:144` preservam entradas quando a lista falha e registram `offlineAt` quando o ator não consta; falha de persistência e dispose mantêm fechamento fixo para retry (`:72`–`:81`, `:151`). Porém `onCharacterInactive` em `:117` chama `close(id,session)` sem usar `offlineAt`; `:72` usa `now()` e `:78` debita até esse horário, mesmo se `offlineAt` já foi registrado. Reproduzi em Node com relógio injetado: sessão começa t=0, lista vazia e tick t=2.000 ms, callback em t=3.602.000 ms; retorna sangue `79.99444444444444`, quando débito até a ausência observada seria `99.9888888888889`. Os testes list-first executam tick e callback no mesmo instante, portanto não cobrem esse intervalo. O polling normal de `source\server\gamemode\phase0-basic.js:688`–`:704` deve limitar o atraso em condições normais, mas não foi provada ausência de atraso anormal; pode ocorrer pequena drenagem após a primeira ausência observada. Recomendo teste com callback atrasado e decisão sobre qual instante é autoritativo, preservando o limite conhecido sem extrapolar o tempo offline.
3. **Desempenho (#3): confirmada eliminação do V×N de gravações do tick.** O draft preguiçoso e transação `blood-reserve.js:43`–`:57` reúnem alterações, e `:142`–`:149` faz no máximo um save por lote sujo; o arquivo é sincronizado antes do rename (`:18`–`:22`). Testes incluem falhas de gravação e retry de todos os relógios. Reexecutei `node overlays/sobrenatural/medir-reserva-lote.cjs`: N/V=1000/100: 1 gravação, tick 3,164 ms; 10000/1000: 1, 11,045 ms; 50000/2000: 1, 25,830 ms, callback agendado 28,788 ms, projeção 4,985 ms. Medição sintética de um ciclo na máquina local, não SLA nem carga real; operações fora do lote ainda gravam sincronicamente, e rename/fsync do arquivo não prova durabilidade do diretório ou atomicidade com PG/consumo.
4. **Apresentação (#4): confirmada em mocks.** `blood-reserve.js:119`–`:124` projeta drenagem monotônica e reservas sem gravar; os testes atravessam 30 e 0 antes do checkpoint e verificam HUD/Feral. Limiares passam a depender dos ticks de 1 s dos módulos e escalonamento, não do checkpoint de 10 s; jogo e crash abrupto entre checkpoints seguem limites conhecidos.
5. **Retry após dispose (`3a4b70e`): confirmado para a falha exercitada.** `blood-reserve.js:151` itera snapshot, conserva entrada pendente e o teste injeta falha de save e retry com relógio avançado. A bateria com timeout terminou; não garante recuperação depois de término do processo com disco indisponível.

Executei a bateria documentada com `node --test --test-timeout=10000 @testFiles`: exit 0, saída:

```text
1..245
# tests 311
# suites 18
# pass 311
# fail 0
# cancelled 0
# skipped 0
# todo 0
# duration_ms 914.1187
```

Executei separadamente `teste-servico.cjs` com diretório de gamemode e `teste-painel-f7.cjs` e `teste-revisao-f7.cjs`; cada comando imprimiu `OK` e retornou exit 0. Esses testes não reproduzem rede, PostgreSQL, motor ou crash real. A D7 permanece bloqueio de homologação; poção antiga segue ativa sem flag, alimentação default false. Publicação no Acervo fica com o coordenador conforme escopo deste despacho.

## Reconfirmacao residual #2 (a35b0e8)

Revisão independente Terra High, 17/11/2026, do intervalo `a2e8583..15b8174` em `C:\Code\Aetherius - Servidor Local`; HEAD `15b8174`, código `a35b0e8`, cliente inalterado `f57ec7d`. O arquivo ativo `source\server\gamemode\supernatural\blood-reserve.js` e o overlay têm SHA-256 `C3A74191F7872692385801C65EB7E03ABA98411DCA1840BEC08D4BC878C7A0C3`.

**Veredito: correção residual #2 confirmada nos cenários examinados.** `blood-reserve.js:72`–`:81` agora fixa o fim no menor de `endAt` e `offlineAt`, apenas na primeira tentativa, mantendo a cauda determinada em retries. Em `:95`–`:105`, reaparecimento observado fecha o segmento antigo e abre nova referência sem o marcador de ausência. A contagem é limitada à primeira ausência *observada*, que não estabelece o instante físico exato de desconexão; falhas de processo, estado online não observável e comportamento real do ciclo de vida permanecem limites de validação, não cobertos pelo mock.

Reproduzi independentemente com relógio injetado e `assert` sem criar arquivos: sessão iniciada em t=0, ausência observada em t=2.000 ms e callback em t=3.602.000 ms resultou em **99,9888888888889**, ou dois segundos de drenagem; no reaparecimento após a hora ausente, seguido de um segundo online e fechamento, resultou em **99,98333333333333**, três segundos online no total. O novo teste do autor exercita reaparecimento por `tick` e `get`, além de callback repetido. Não encontrei regressão adicional nesse escopo.

Bateria documentada, na raiz do servidor local, `node --test --test-timeout=10000 @testFiles` com a seleção de testes descrita na seção anterior: exit 0, saída:

```text
1..248
# tests 314
# suites 18
# pass 314
# fail 0
# cancelled 0
# skipped 0
# todo 0
# duration_ms 932.9734
```

`git diff --check a2e8583 15b8174` apontou apenas espaços ao fim de duas linhas do log histórico `evidence/reserva-r2-residual-before.log:116,119`, sem impacto funcional; não alterei esse arquivo. D7, alimentação desativada e validação de criaturas e dos demais efeitos em jogo permanecem conforme as seções anteriores. Publicação ao Acervo e decisão de homologação cabem ao coordenador; não modifiquei runtime, código ou modpack.
