# Comparação — Lobisomem oficial v0.42.0, base kdu e Sobrenatural local

Data: 17/11/2026. Autor: Codex, no papel **Sol High**, despachado pelo coordenador Opus. Tarefa `task_7d51aed72da0`, dispatch `ctx_ab2ced0331fa`. Análise de código e testes sem iniciar servidor ou jogo; houseCARL exclusivamente em leitura. **Este parecer ainda precisa de revisão por outro modelo pelo coordenador**; não equivale a homologação multiplayer.

## Resposta e recomendação única

**A implementação oficial não substitui a nossa com vantagem técnica geral.** O `werewolf-service.js` oficial é **idêntico ao serviço do pacote kdu de 14/11**, normalizando finais de linha: os mesmos 275 trechos de linha, regras de prata, nome, duração e recuperação. Nossa versão de 403 linhas é uma extensão desse mesmo serviço, com correções adicionais de autoridade, readback, recuperação e integração com Sobrenatural. Não são duas transformações independentes competindo por qualidade visual.

Recomendo **(c) híbrido**: tomar a infraestrutura, perfis e correções gerais da **v0.42.0** como destino da integração, mantendo **uma única implementação do serviço de fera: a nossa versão endurecida**, e portar seletivamente novidades oficiais após revisar o contrato. Preservar os efeitos/animação kdu; manter fora deles concessão de maldição, progressão, autoria de grants e compulsão. Não carregar duas cópias nem substituir o serviço local pelo oficial de 275 linhas.

Há uma novidade funcional real no script cliente entregue pelo servidor oficial: `actionVoiceFire` para o poder usado com Z. Porém o **fonte do cliente oficial inspecionado não contém `switchPlayerCreatureRace` nem o caminho `Actor.setRace` do patch kdu**. Além disso, nossa rotina de aparência antecede mudanças oficiais de habilidades raciais: copiar o cliente local inteiro sobre o novo perderia correções oficiais. Isso exige mescla seletiva dos dois lados; não uma troca de arquivos baseada na nota de release.

## 1. Escopo, fontes e identificação reproduzível

As referências abaixo usam estas raízes **completas**; `O-S/server/...:N`, por exemplo, significa o arquivo absoluto obtido concatenando a raiz O-S com `/server/...`, linha N. Os números são das cópias lidas nesta data.

| Sigla | Raiz absoluta / identificação |
|---|---|
| O-S | `C:\Code\Aetherius - OFICIAL\aetherius-server-main` — release declarada em `docs/releases/v0.42.0.md:1–8` |
| O-C | `C:\Code\Aetherius - OFICIAL\aetherius-client-main` — release declarada em `docs/releases/v0.42.0.md:1–10` |
| K | `C:\Code\Aetherius - New Systems\Sobrenatural\AETHERIUS - LOBISOMEM 14-11 - KDU` — pacote imutável de 14/11, `LEIA-ME.md` |
| L | `C:\Code\Aetherius - Servidor Local` — Git raiz HEAD `0e4dd84ee56a03e65ce637891ee00c069002bfbc`, 17/11/2026 21:52:21 −03 |
| L-S | `C:\Code\Aetherius - Servidor Local\source` — base Git interna `310cb598fd382cf4d6598cb34eada5db1a3fa9f8`, com alterações locais aplicadas por overlays |
| L-C | `C:\Code\Aetherius - Servidor Local\client-source` — HEAD `f57ec7de9cbebd09c61acef78690efe0eaabd5b3`, 17/11/2026 20:37:53 −03 |
| D | `C:\Code\Aetherius - New Systems\Sobrenatural\docs` |

**O-S e O-C não têm `.git` nestas cópias.** Não é possível atribuir um commit upstream verificável: a versão é a declarada nas notas de release, e os arquivos críticos são identificados por hashes abaixo. Também não havia bundle `skymp5-client.js` nem arquivo `AETHERIUS - LOBISOMEM.esp` dentro dessas árvores de fonte; a conclusão sobre o cliente refere-se ao **fonte disponível**, não a um binário do launcher que não foi fornecido nessas árvores. Os perfis referenciam o plugin e o houseCARL confirmou sua presença no modpack ativo.

O último commit do Git raiz que alterou o espelho do serviço local foi `e86c0cb743c4d5502719573cc39006815c134741` (B1, 17/11 20:28:12 −03). Seu arquivo ativo e o espelho `L/overlays/sobrenatural/files/server/gamemode/werewolf-service.js` têm o mesmo hash bruto. A base Git interna de `source` sozinha não representa o candidato; é necessário considerar os overlays e hashes.

### Hashes SHA-256

| Arquivo | Hash |
|---|---|
| O-S `server/gamemode/werewolf-service.js` | `2c4b01ff5d00674f19bcb71ba0c0127fb9330dcb4dc85f536bcf713bd9f93539` |
| O-S `server/gamemode/werewolf/client.js` | `bd7b93d83aac7cbce79b61e8709247425255150fb1052a08cd57e61ec404453e` |
| O-C `client/src/sync/appearance.ts` | `6597e38ba72d579a0c0dc8bd3ec29a3ed2953b602e7fbed416d990d84dd68afd` |
| O-C `client/src/services/services/remoteServer.ts` | `0308982e4b16f9bff2129f23829afd6a2857a4f4960d8229bc3830ca7edbdd95` |
| L-S `server/gamemode/werewolf-service.js` (bruto, CRLF) | `c785ccec45335ba797aee6392d0101dbbaf054a7861417117dbd7a1afd2df493` |
| L-S `server/gamemode/supernatural/werewolf-transform-journal.js` | `01371d1f36fe9f17fd5e237cfa17a76c1e62a70f7857588f56cb7e2c81a17941` |
| L-S `server/gamemode/supernatural/forced-werewolf.js` | `9514889991a047b7fd27ce379bd7fb9493e3c01a1f07c77cebc9214cec60180d` |
| L-S `server/gamemode/supernatural-service.js` | `93cd91758389a610cfd1ff33c03acc9038711c4faf69ac66c449b7ba1767b157` |
| K `patches/servidor-lobisomem.patch` | `b7e2a8ef3b75347bc2ae0cdeaf7cf16f4988f6be3207961afc9712e7d8a93662` |
| K `patches/cliente-lobisomem.patch` | `68a6c70e513b8bbe9ac19fd4be235b01021113307258605ca889ef435df12d6d` |
| K `plugin/AETHERIUS - LOBISOMEM.esp` | `e036683077b23a2d288e930d43b7a50dfef7071f652b80e85a9ec9614fc6d7ce` |

Foram lidos `Sobrenatural/RETOMADA.md`, os quatro documentos solicitados e a documentação oficial no [Acervo Sobrenatural](https://github.com/pablo-rodrigues1/AETHERIUS-ACERVO-DE-INFORMACOES/tree/main/02-sistemas/sobrenatural), incluindo INDICE, READMEs de Sobrenatural/Lobisomem/UI Core/Painel ADM/GameplayCore e ADRs 0003, 0014 (recarga), 0019 e 0020. O Acervo ainda descreve o lobisomem como pacote não integrado; a nota v0.42.0 e o código atual superam essa descrição histórica, conforme ADR-0003. **Nada foi publicado nem atualizado no Acervo**, pois este despacho autoriza criar somente este relatório; a consolidação documental cabe ao coordenador.

## 2. Relação real entre as três versões

Extraí **em memória** os arquivos adicionados em K `patches/servidor-lobisomem.patch` e comparei com O-S e L-S, sem aplicar patches:

| Componente | K → oficial v0.42.0 | K → nossa versão |
|---|---|---|
| `server/gamemode/werewolf-service.js` | **Idêntico**, 275 linhas, SHA normalizado LF `2c4b01ff…f93539` | 403 linhas, SHA normalizado LF `7304e5153f2853182bdb77c04d5faf16eae38ae1b9650425934dca43a455b5c9` |
| `server/gamemode/werewolf/client.js` | 236 → 247 linhas; único delta: listener `actionVoiceFire`, O-S `:33–43` | 236 → 240 linhas; marcador vanilla `{seq, marker}` e rejeição de callback antigo, L-S `:33–41,157–165` |
| `server/gamemode/werewolf/tests/werewolf.test.cjs` | **Idêntico**, 423 linhas, SHA LF `9b6db1ca3346e79b925233a6f3d301cea92af9d56b2665073a2b64225334de72` | suíte preservada com adaptação ao contrato novo; regressões adicionais em overlays |
| Patch TS de raça do dono | K o entrega explicitamente em `patches/cliente-lobisomem.patch` | Integrado e ampliado no nosso `002-formas-raca.patch` |
| Fonte TS oficial disponível | **Não contém a rotina do patch K**, apesar da release conjunta | Nossa cópia contém fila de aparência, SetRace e geração de callbacks |

“Kit v23” no comentário de O-S `server/gamemode/phase0-basic.js:53–55` não demonstra uma reescrita do serviço: o diff efetivo tem o resultado acima. O benefício oficial é a integração no produto mais recente e a correção de entrada Z; não uma nova implementação visual ou uma nova estratégia de persistência da fera.

## 3. Comparação por critério

| Critério | Base K e oficial O-S | Nossa versão | Avaliação |
|---|---|---|---|
| Autoridade da forma | Servidor valida online, vivo/SkyDeath e poder conhecido; publica raça e fase (`O-S/server/gamemode/werewolf-service.js:66–74,149–172`). Aceita relatos `cast/race/reached` do cliente (`:213–233`). | Entrada de jogador passa pelo dispatcher com geração, nonce, sequência, proposta nativa e gates (`L-S/server/gamemode/supernatural/power-input.js:99–160`). Serviço confere keyword, recarga, Lorde e vínculo (`L-S/server/gamemode/werewolf-service.js:166–195,341–352`). | Nossa é mais adequada às restrições do Sobrenatural. O oficial não pretende implementar a concessão da maldição. |
| Dono ↔ servidor | K inclui `Actor.setRace` no patch TS; O-S adiciona voz Z, mas O-C inspecionado aplica só a aparência da base (`O-C/client/src/sync/appearance.ts:126–151,222–239`; `remoteServer.ts:923–927`). | `L-C/client/src/sync/appearance.ts:197–231,242–261` troca raça real e cancela callbacks obsoletos. | Nossa tem o caminho necessário no fonte. Oficial exige reconfirmação/correção do cliente entregue. |
| Terceiros observando | `aetheriusBeast` visível a dono/vizinhos; aparência recriada no fluxo de `FormView` (`O-S/.../werewolf-service.js:203`; `O-C/client/src/view/formView.ts:83–116,699`). Efeitos usam ator remoto. | Mantém a mesma replicação pública (`L-S/.../werewolf-service.js:296`); aviso da compulsão é privado (`forced-werewolf.js:137`). | Mesma arquitetura visual de vizinhança. Nenhuma prova atual de sincronismo visual com dois jogadores foi obtida. |
| Crash / falha de escrita | Lista de atores em JSON tmp+rename; original fica em propriedade do ator; recuperação no boot/login (`O-S/.../werewolf-service.js:121–135,187–202,241`). Erros de registro só avisam; fase/limpeza não dependem do sucesso da aparência. | Readback antes de fase/limpeza; diário com intenção anterior à raça, fsync e identidade; fila B1 conserva prova/ambiguidade (`L-S/.../werewolf-service.js:152–165,200–291`; `supernatural/werewolf-transform-journal.js:18–63`). | Nossa é mais robusta a falhas parciais testadas, com custo e limites explicitados abaixo. |
| Desconexão / morte | Tick a cada 200 ms termina turning/beast se offline/morto (`O-S/.../werewolf-service.js:238–260`). | Preserva regra; retry mantém recuperação; invalida concessão/personagem; resolve intenção cancelada em fila (`L-S/.../werewolf-service.js:235–291,341–383`). | Nossa protege melhor falha de restauração e troca de personagem. Ambos têm latência de tick e dependem do host. |
| Animação / aparência | K: 10,5 s `aeth` ou 5,5 s de limite vanilla; sangue, gore, som, coração, escala 85→100%, raça ampliada. O-S mantém efeitos iguais, salvo entrada Z (`O-S/.../werewolf/client.js:79–241`). | Reusa os mesmos efeitos. UUID+seq protege o ponto vanilla; aparência precede fase confirmada (`L-S/.../werewolf-service.js:200–232,308–314`). | Não há arte nova superior em O-S. A nossa melhora ordenação e controle, não qualidade da animação. |
| Cooldown / duração | Duração fixa 150 s a partir da troca, sem bônus servidor por feeding (`O-S/.../werewolf-service.js:56,166–170`). **Não tem cooldown real server-side**; a restrição diária do Power no jogo não é equivalente. | Mesmo padrão de duração; `WEREWOLF_COOLDOWN_SECONDS=1800`, laboratório 0; propriedade privada e readback (`L-S/.../werewolf-service.js:57–63,171–195,229,299`). | Nossa atende ADR-0014. Não confundir duração, recarga e horas online para compulsão. |
| Prata / combate | Keywords de prata ou arco/besta com munição vestida, +50%, exclui bash, piso não letal (`O-S/.../werewolf-service.js:76–118,206–210`). | Mesmo algoritmo (`L-S/.../werewolf-service.js:82–124,301–305`). | Empate no algoritmo. Não somar uma segunda instância do bônus. Combate geral novo não pertence à transformação. |
| Plaquinha SkySocial | Nome Lobisomem, sem classe/identidade reconhecida para terceiros próximos até 4096; dono preserva seu nome (`O-S/server/gamemode/skysocial/src/identity.js:40–41`). Nome também projetado na aparência (`werewolf-service.js:39–42`). | API `isBeast/name` preservada; a mesma integração K pode ser mantida. | Empate no contrato. Entrada/saída e privacidade só se alteram corretamente se fase corresponder à aparência. |
| Configuração `.env` | Seis chaves lidas no serviço; não encontrei `WEREWOLF_*` em O-S `server/gamemode/.env.example`. Não havia `.env` real no espelho. | As seis permanecem; recarga e doze chaves da compulsão são adicionais. | Compatibilidade nominal das chaves comuns; oficial puro ignora chaves novas. A nota de release não prova que os valores estão instalados. |
| Testes | 29 testes do pacote K, iguais no oficial; passaram nesta rodada. | Mesma base mais testes de formas, geração, disco, diário, B1, origem, compulsão e F7; resultados abaixo. | Nossa cobre mais falhas do escopo Sobrenatural. Quantidade não prova motor/produção. |
| Performance | Tick de 200 ms, mapa de eventos, cache de material, gravações síncronas nas transições; VFX por quadro no cliente. | Mantém esses custos; acrescenta fsync, journal completo e retries, tick de 250 ms da compulsão, checkpoints de 30 s e amostras privadas durante aviso. | Oficial é mais simples e tende a menor I/O. Não foi medido ganho real de latência/FPS de nenhuma versão. |
| Compatibilidade do modpack | Plugin e perfis integrados no oficial; K já dependia de Manbeast/Pandora/OAR/assets. | Mesmo núcleo de raça/efeitos; plugin Sobrenatural e integração Core/cliente acrescentam requisitos. | Oficial tem vantagem de distribuição consolidada. Não torna nossos overlays antigos diretamente aplicáveis. |

### Limites de autoridade e sincronização

No oficial, a raça resultante é decidida pelo servidor, mas um cliente pode solicitar transformação com um poder conhecido sem maldição registrada no Sobrenatural. Isso era o escopo original do K. `race` também pode iniciar uma forma sem animação e encerrar uma forma confirmada; `reached` só exige sequência e 1,5 s transcorridos, inclusive com animação `aeth`. Não há prova de cast genuíno nesses relatos.

Na nossa instalação completa, `L-S/server/gamemode/werewolf-service.js:316–318` entrega os relatos ao dispatcher: `cast/race/effect` viram evidência, não comando de troca; `reached` só atua em sessão vanilla atual, marcador UUID+seq e janela permitida. O comando aceito pelo dispatcher ainda é **entrada não confiável validada**, não atestado criptográfico de uma tecla/cast real. O nonce é visível ao dono e a proposta nativa também é uma entrada do cliente.

Nossa proteção de keyword em `start` é **condicional à presença de `mp.__aetheriusSupernatural`** (`:169`). Em uso isolado/falha de instalação desse domínio existe fallback compatível com K. Portanto a garantia depende de instalar e verificar a composição inteira; não se deve anunciar que o arquivo sozinho impede toda transformação sem maldição.

Os VFX das três versões calculam tempo desde o recebimento da publicação (`O-S/server/gamemode/werewolf/client.js:85–89`), sem timestamp global de início. Há ordenação compartilhada de fases, não garantia de quadro exato simultâneo: latência e entrada tardia podem deslocar a sequência local. O burst de 3 s evita repetir o surgimento para quem chega muito depois; não soluciona sozinho espectadores chegando durante `turning`.

## 4. Falhas concretas do oficial e o que nossa extensão corrige

### 4.1 Fase de fera publicada antes de confirmar a raça

O-S `server/gamemode/werewolf-service.js:166–172` faz:

```js
session.phase = 'beast';
session.until = now() + durationMs;
publish(id, session);
setAppearance(id, beastAppearance(session.original, name), 'aplicar a forma de fera');
```

Se `mp.set(appearance)` falhar ou for no-op, `isBeast`/plaquinha/prata e fase podem dizer fera enquanto a aparência continua mortal. O retorno de `setAppearance` não altera essa decisão. No nosso `:200–232`, a intenção precisa ser gravada, a aparência lida de volta e o diário confirmado antes de fase/prazo/notificação; falhas mantêm tentativa recuperável. Isso confirma estado do host, não renderização do motor nos clientes.

### 4.2 Falha de restauração apaga a recuperação no oficial

O-S `server/gamemode/werewolf-service.js:174–184` ignora o retorno de `setAppearance`, limpa `aetheriusBeastOriginal` e retira a sessão. A fera pode permanecer sem o original nesse caminho. L-S `:235–250` retorna `{ok:false,status:'restore_failed'}` e conserva a sessão/original; a limpeza só vem após readback. O F7 usa esse resultado para não afirmar sucesso falso.

### 4.3 Registro de recuperação e prova durável são coisas diferentes

O JSON oficial contém IDs dos atores; o rosto original depende da propriedade do ator. A escrita usa rename, sem fsync, e sua falha apenas gera log (`O-S/.../werewolf-service.js:128–134,155–162`). Não constitui transação atômica entre JSON e persistência nativa do host.

Nosso diário grava `{actorId, characterId, grantId, stamp, original}` antes de mutar raça; fsync no arquivo temporário precede rename (`L-S/server/gamemode/supernatural/werewolf-transform-journal.js:18–24,38–48`). O boot transforma uma intenção interrompida em confirmação **conservadora**, para não cobrar imediatamente uma segunda transformação forçada (`:27–35`). Isso pode conceder um reset mesmo quando a raça não chegou visualmente ao cliente. Não é exatamente-uma-vez do motor, nem transação com o save do host; diretório/armazenamento físico e perda abrupta de energia não foram testados.

O B1 corrigido conserva `raceApplied`/`raceUncertain` entre retries; cancelamento transfere trabalho para `pendingIntents`, que confirma a mesma intenção ou aborta apenas no-op comprovado (`L-S/.../werewolf-service.js:205–220,252–267,356`). **Terra confirmou B1 em `e86c0cb`**, no adendo final de `D/REVISAO-3AB_CONFIRMACAO_terra.md`; o título histórico “REPROVADO” no começo do arquivo é do parecer anterior e não deve ser lido isoladamente.

Limites restantes: fila é volátil quando o disco está indisponível, boot usa política conservadora, corrupção do diário aborta sua inicialização em vez de resetar silenciosamente, e retries síncronos a cada 200 ms podem afetar latência. Não há homologação de crash físico ou performance sob volume com falha.

### 4.4 Reproduções executadas sem arquivos nem servidor

Executei Node em stdin, importando o serviço O-S com `interval:false`, `registryFile:null`, relógio simulado, `mp` em memória, ator vivo/online e Beast Form conhecido. Quatro cenários produziram:

```text
aeth early reached true race cdd84
apply no-op isBeast true actualRace 13746
restore throw original null sessionCount 0 actualRace cdd84
no curse start true
```

Procedimento: (1) iniciar `aeth`, avançar 2 s e enviar `reached` com a sequência pública; (2) fazer `mp.set(appearance)` no-op, avançar além do timeout e chamar tick; (3) entrar em fera, injetar throw ao restaurar aparência e chamar finish; (4) expor `mp.__aetheriusSupernatural.keywordOf=()=>null` e iniciar com poder conhecido. São reproduções de fluxo do serviço, não testes do host nativo. A quarta evidencia diferença de escopo do oficial, não defeito da política original K.

## 5. Cliente oficial: lacuna e novidades a preservar

**Lacuna comprovada no fonte:** O-C `client/src/services/services/remoteServer.ts:923–927` encaminha aparência do dono diretamente para `applyAppearanceToPlayer`. Essa função (`client/src/sync/appearance.ts:222–239`) usa `applyAppearanceCommon`, que chama `TESModPlatform.setNpcRace` na base (`:126–135`), não `Actor.setRace`. Não encontrei `switchPlayerCreatureRace` no fonte O-C. K `patches/cliente-lobisomem.patch` entrega justamente a correção para evitar grafo humano em corpo de criatura. Nossa `L-C/client/src/sync/appearance.ts:242–261` contém essa rotina; `:197–231` acrescenta geração, readback e retorno mortal sem callback antigo.

Consequência provável, **inferida do código e da causa descrita em K/LEIA-ME.md**, caso o bundle distribuído corresponda ao fonte O-C: a troca autoritativa de aparência não assegura a troca real do grafo do dono, especialmente em `aeth`/transformação forçada sem Papyrus vanilla aplicando SetRace. Pode haver corpo de fera com animação humana, falhas de controles/retorno e divergência dono versus observador. Não reproduzi esse defeito no Skyrim nem inspecionei o binário instalado pelo launcher; não atribuo CTD observado a essa lacuna.

**Novidade de entrada:** O-S `server/gamemode/werewolf/client.js:33–43` trata `actionVoiceFire` porque `spellCast` não chegava para o Power Z no teste descrito no comentário. K e nosso script kdu não têm esse listener. Contudo, simplesmente copiar essas 11 linhas para nossa versão só enviaria `cast` ao dispatcher, onde é evidência sem transição. Para aproveitar o fallback, é preciso adaptar o caminho de comando `activate` de `L-S/server/gamemode/supernatural/power-input.js:25–30`, preservar nonce/geração e correlacionar com a proposta nativa (`:116–160`). Nosso WerewolfChange é LesserPower no laboratório segundo ADR-0014; a necessidade do fallback para ele e para o Anel deve ser medida no motor, não presumida.

**Correção oficial que nossa cópia não deve sobrescrever:** O-C `client/src/sync/appearance.ts:179–220,234` implementa `syncPlayerRacialSpells`, retirando habilidades das outras raças jogáveis e aplicando as da raça correta. Nosso `applyAppearanceToPlayer` em L-C `:204–231` não chama essa rotina. A mescla precisa preservar essa correção oficial, testar sua interação com raça vampírica/criatura e manter a proteção de headparts do editor/reconexão. O-C `remoteServer.ts:1089` chama `releasePlayerHeadpartsForRaceMenu`; a nota de release também descreve correção de reconexão/allocator. A revisão desta entrega não auditou o nativo da correção de reconexão.

## 6. Escopo que não deve ser cobrado do kdu

| Responsabilidade | Quem implementa hoje | Por que precisa sobreviver à integração |
|---|---|---|
| Raça/forma/animação pública, duração, nome, prata | Núcleo K, incorporado no oficial e estendido localmente | É o serviço único compartilhado; não duplicar |
| Conceder/remover maldição, no máximo uma, keyword privada e estado por personagem | `L-S/server/gamemode/supernatural-service.js:21–25,35–76,156–187,277–292` | Oficial não substitui nem migra esse domínio |
| Seis estágios, grants/perks/atributos, autoria e convivência com classe | `supernatural-service.js:79–102`, `supernatural/werewolf-grants.js`, reconciliadores de origem e Core | Remover uma maldição não pode apagar grant de outra origem; base K não trata progressão |
| 72 h reais online/noite, pré-aviso privado e reset por transformação | `L-S/server/gamemode/supernatural/forced-werewolf.js:13–29,51–60,80–139` | É regra de maldição Sobrenatural, não requisito omitido do K |
| F7 autorizado, auditoria e confirmação do retorno | `L-S/server/gamemode/admin-panel/supernatural-admin.cjs:13–64` | Operações administrativas dependem dos retornos do nosso serviço |
| UI Sobrenatural e XP da maldição | Domínio Sobrenatural/UI/Core | Não se tornam entregues por instalar a release do lobisomem |

A compulsão usa relógio monotônico online e calendário autoritativo pronto, janela `[20,5)`, contador por personagem/grant, aviso de 20 s, tick de 250 ms e checkpoints (`forced-werewolf.js:31–60,98–139`). Reset depende de `lastTransform`/`onTransformed`, não de começar animação. Não cobra tempo offline/reinício; tem janela de perda entre checkpoints, e os testes Node não substituem medição do motor. Mantém a regra normal de cooldown do serviço: a tentativa forçada pode ser recusada e retentada, sem fabricar confirmação.

## 7. O que quebra se o oficial entrar como está

| Entrada/substituição | Consequência concreta | Evidência |
|---|---|---|
| Reaplicar `overlays/lobisomem-kdu/001-servidor-lobisomem.patch` sobre v0.42 | **Não aplica**: arquivos já existem e identidade SkySocial já foi alterada. Não reaplicar o pacote antigo. | `git apply --check` executado, exit 1; mensagens em §10 |
| Sobrescrever nosso `werewolf-service.js` pelo de 275 linhas | Some proteção de keyword/recarga/Lorde/vínculo, readback, diário, B1 e delegação ao dispatcher. Eventos antigos voltam a abrir/encerrar formas fora dos gates do Sobrenatural. | O-S `:149–233` versus L-S `:166–352` |
| Compulsão com serviço oficial | `start` existe, portanto a sequência pode até transformar, mas faltam `onTransformed`, `lastTransform` e gravação de stamp. Contador não recebe reset confiável; pode permanecer vencido e repetir ciclos após retorno. O diário antigo deixa de ser consultado. | `forced-werewolf.js:80–90,111–112,128,138`; API oficial `werewolf-service.js:263–269` |
| F7 “Encerrar forma” com serviço oficial | `finish` retorna `undefined`; adaptador considera restauração não confirmada e devolve `FORM_RESTORE_FAILED/unknown`, mesmo quando visualmente restaurou. No caso de falha real o oficial pode já ter apagado original. | `supernatural-admin.cjs:49–54`; O-S `werewolf-service.js:174–185` |
| Script `werewolf/client.js` oficial com nosso servidor | `reached` envia **inteiro**, nosso `reached` espera `{seq,marker}`; perde o ponto exato vanilla e fica no timeout. Seus `cast/race` continuam evidência quando nosso dispatcher está ativo. | O-S client `:44–49,167–169`; L-S service `:308–318` |
| Nosso script de efeitos com servidor oficial | `reached` em objeto é recusado por `Number.isSafeInteger` do oficial; publicação oficial não tem UUID. O timeout continua existindo, mas os protocolos não são intercambiáveis. | O-S service `:140–143,214,221–224`; L-S client `:157–165` |
| Cliente O-C puro no lugar de L-C | Perde SetRace real, gerações/cancelamento, suporte de raça vampírica e protocolo de autoria do nosso cliente. Resultado depende também do Core/overlays entregues; não instalar metade do protocolo. | O-C appearance `:222–239`; L-C appearance `:197–261`; `L/overlays/cliente-local/003–005*.patch` |
| Aplicar nosso `002-formas-raca.patch` sem adaptar | **Não aplica** contra O-C atual no hunk de `appearance.ts:180`. Copiar o arquivo inteiro perderia `syncPlayerRacialSpells`. | `git apply --check` executado, exit 1 |
| Trocar o `.env` inteiro | Pode apagar valores locais; manter chaves novas com serviço oficial não ajuda: ele simplesmente não as lê. Recarga de teste 0 jamais deve migrar implicitamente como configuração de produção. | §8 e `L/config/local.env:40,46` |
| Duas instalações do serviço | `install` retorna a primeira API em `mp.__aetheriusWerewolf`; a segunda não “adiciona” extensões. Sem esse singleton, uma duplicação de hook ainda arriscaria prata repetida. | O-S service `:63`; L-S `:66`; hooks `:301–305` |
| Sobrepor `phase0-basic.js` local completo | Pode apagar mudanças v0.42 de outros sistemas; instalar Sobrenatural antes de SkySocial/Core muda propriedade de hooks. | O-S boot do lobisomem `:53–55`; L-S `:427–440`; `L/overlays/sobrenatural/001-phase0.patch` |

**Não houve renomeação geral** de `install/start/finish/isBeast`, `mp.__aetheriusWerewolf`, `aetheriusBeast`, `aetheriusBeastOriginal` ou `_aetheriusBeast`: os nomes centrais são os mesmos. As incompatibilidades são **APIs adicionais ausentes**, retorno de `finish`, delegação do evento e tipo/política de `reached`. Não há mapeamento de nomes novo a inventar.

O `mp.onAetheriusHit` do combate v0.42 encadeia o hook anterior (`O-S/server/gamemode/combat-sync-service.js:241–248`), assim como durabilidade (`durability-service.js:295–299`). O callback de prata também preserva retorno anterior. Não encontrei nessas atribuições uma necessidade de renomear eventos. Ainda é preciso validar a composição final; `dispose` do serviço de fera restaura o hook antigo diretamente, e descarregar fora de ordem pode retirar wrappers instalados depois — limitação compartilhada, não melhoria oficial.

## 8. Configuração: nomes compatíveis e extensões

| Chave | Oficial e K | Nossa versão |
|---|---|---|
| `WEREWOLF_ANIMATION` | `aeth` padrão, `vanilla` alternativa | Igual |
| `WEREWOLF_TURN_MS` | 10500 / 5500; faixa 0–20000 | Igual |
| `WEREWOLF_DURATION_SECONDS` | 150; faixa 10–3600 | Igual |
| `WEREWOLF_REQUIRE_KNOWN_SPELL` | Só literal `false` desativa | Igual; keyword é outra verificação |
| `WEREWOLF_NAME` | Lobisomem, normalizado/limitado a 48 caracteres | Igual |
| `WEREWOLF_SILVER_MULT` | 1.5; faixa 1–5 | Igual |
| `WEREWOLF_COOLDOWN_SECONDS` | Não lida | 1800; faixa 0–86400; local 0 |
| `WEREWOLF_FORCE_ONLINE_HOURS` | Não lida | 72 |
| `WEREWOLF_FORCE_NIGHT_START/END` | Não lidas | 20 / 5 |
| `WEREWOLF_FORCE_HEART_SECONDS` | Não lida | 5 |
| `WEREWOLF_FORCE_SHAKE_SECONDS` | Não lida | 5.25 |
| `WEREWOLF_FORCE_RED_SECONDS` | Não lida | 10 |
| `WEREWOLF_FORCE_WARNING_SECONDS` | Não lida | 15 |
| `WEREWOLF_FORCE_TRANSFORM_SECONDS` | Não lida | 20 |
| `WEREWOLF_FORCE_HEART_LOOP_SECONDS` | Não lida | 9.6 |
| `WEREWOLF_FORCE_PULSE_SECONDS` | Não lida | 0.8 |
| `WEREWOLF_FORCE_SAVE_SECONDS` | Não lida | 30 |
| `WEREWOLF_FORCE_RETRY_SECONDS` | Não lida | 30 |

Leitores: O-S `server/gamemode/werewolf-service.js:27–61`; L-S `server/gamemode/werewolf-service.js:28–64`; L-S `server/gamemode/supernatural/forced-werewolf.js:9–25`. Os nomes comuns não foram renomeados. Os caminhos persistentes também precisam ser mantidos: `data/aetherius-werewolf.json`, `data/aetherius-werewolf.json.transforms.json`, `data/aetherius-werewolf-compulsion.json` e `data/aetherius-supernatural.json`. Não apagar diário/contador ao atualizar.

## 9. Modpack e desempenho: evidência e limites

Consultas de leitura houseCARL nesta rodada:

- Instância `C:\modOrganizer`, perfil ativo **AETHERIUS - GRAFICO - QUALIDADE**, 436 plugins ativos/resolvidos; epoch `e2-ac8a903449c25337`; houseCARL `2.0.0+5e3c744862bc17c85ebe850830603a9a3e6f0779`.
- `housecarl_records` em `0CDD84:Skyrim.esm` e `092C48:Skyrim.esm`, árvore de provedores: WerewolfBeastRace ganha de Manbeast em **AETHERIUS - LOBISOMEM.esp**. Frente ao Manbeast, os deltas retornados foram nome e altura (1.13/1.00 → 1.69/1.50). WerewolfChange nesse perfil tem apenas Skyrim.esm como provedor, portanto **o override LesserPower local não está demonstrado no perfil oficial ativo**.
- `housecarl_check` restrito a AETHERIUS - LOBISOMEM.esp: 0 referências pendentes, 0 masters faltantes, 0 records ilegíveis. Não varreu Papyrus, animações, DLLs ou VFS de assets; não é certificado geral de compatibilidade.
- Inspeção nominal do perfil LOCAL, sem troca de perfil: 435 ativos e VISOES inativo; a consulta pelo nome `AETHERIUS - SOBRENATURAL.esp` não o encontrou na ordem informada. Não substituí isso por memória dos relatórios antigos e não presumi o plugin local instalado atualmente. Reconfirmar esse inventário antes do próximo teste humano; o resolver de records continuou no perfil oficial ativo.

Os schemas de Race/Spell foram lidos na skill mutagen-reference (`references/records.jsonl`, linhas 105 e 119), antes da leitura dos records. Não foram lidos/editados scripts Papyrus nem interpretadas condições OAR neste despacho.

Os perfis O-C `client/profiles/AETHERIUS - GRAFICO - QUALIDADE/{modlist.txt:18,plugins.txt:254,loadorder.txt:265}` já ligam o mod/plugin; o perfil PERFORMANCE tem as mesmas entradas. K/LEIA-ME.md documenta dependência de Pandora para registrar IdleAethLobisomem, assets de som, decisão sobre uivo OAR abrangente e alimentação/retorno vanilla ainda dependentes de scripts. Esses limites não desaparecem porque o mod foi renomeado/distribuído na release. O próprio LEIA-ME registra que sangue no corpo, áudio tardio e dois jogadores ainda precisavam de confirmação naquele pacote; não transformei essa documentação histórica em validação atual.

Não alterei MO2, perfis, ESPs, assets, runtime ou outputs gerados. Não usei Pandora/Synthesis/DynDOLOD como base de patch.

Quanto à performance: `onlinePlayers.includes` repetido em loops/sessões já existe no serviço original; a análise assintótica pode chegar a trabalho quadrático conforme população/sessões. A nossa leitura profunda de aparência é por transição/retry, não um novo VFX por quadro. O diário regrava o mapa de confirmações (uma entrada por chave de personagem/ator), com fsync, e não tem poda nesta implementação; crescimento histórico e disco com falha aumentam o custo. `pendingIntents` retenta a cada 200 ms. O pré-aviso publica somente para o alvo até quatro amostras/s, não para o servidor todo. Não medi carga, latência de storage, tráfego real nem FPS; **não há base para anunciar nossa versão como mais rápida**.

## 10. Validação executada e reproduzível

Node `v22.14.0`. Todos os comandos abaixo foram executados sem iniciar servidor/jogo. Testes que usam disco trabalham com fixtures temporários; não foram gravadas evidências em arquivos de repositório.

### Oficial — 29/29, exit 0

Na raiz O-S:

```powershell
node --test server/gamemode/werewolf/tests/werewolf.test.cjs
```

Cobrem ciclo normal, morte/saída, recuperação JSON, propriedades, efeitos simulados, prata e SkySocial. O arquivo de testes é o mesmo K; a extração K foi comparada em memória, não instalada para executar outra cópia. A adição `actionVoiceFire` não trouxe teste específico novo nessa suíte.

### Local — 130/130, exit 0

Na raiz L:

```powershell
node --test source/server/gamemode/werewolf/tests/werewolf.test.cjs overlays/sobrenatural/teste-formas.cjs overlays/sobrenatural/teste-aparencia.cjs overlays/sobrenatural/teste-transformacao-forcada.cjs overlays/sobrenatural/teste-revisao-3.cjs overlays/sobrenatural/teste-origem.cjs
```

Inclui base K, readback com throw/no-op, replay/geração/gates, callback de aparência antigo, compulsão/noite/online, recuperação por diário, marcadores vanilla, autoria compartilhada, cancelamento e regressões B1.

Também passaram, cada um com exit 0:

```powershell
node overlays/sobrenatural/teste-painel-f7.cjs
node overlays/sobrenatural/teste-revisao-f7.cjs
```

Saídas: `OK F7: alvo, oito ações, autorização A+...` e `OK revisão F7: ... restore com throw/no-op, recuperação preservada e painel sem sucesso falso...`.

### Core — 39/39, exit 0

Em `C:\Code\Aetherius - Servidor Local\source\server\gameplay\core`:

```powershell
node --test runtime/tests/perk-client.test.mjs runtime/tests/perk-origin-barrier.test.mjs runtime/tests/perk-ownership-retention.test.mjs runtime/tests/client-delivery.test.mjs runtime/tests/class-skills-delivery.test.mjs
```

MockTimers emitiu aviso experimental; não houve falha. Fixtures simulam entrega/SQL: não validam PostgreSQL nem persistência nativa do Skyrim.

### Aplicabilidade dos overlays — falhas esperadas capturadas, sem aplicar

Na raiz O-S:

```powershell
git apply --check 'C:\Code\Aetherius - Servidor Local\overlays\lobisomem-kdu\001-servidor-lobisomem.patch'
```

Exit 1: `identity.js: patch does not apply`; `werewolf-service.js`, `werewolf/client.js` e `werewolf/tests/werewolf.test.cjs`: `already exists in working directory`.

Na raiz O-C:

```powershell
git apply --check 'C:\Code\Aetherius - Servidor Local\overlays\cliente-local\002-formas-raca.patch'
```

Exit 1: `client/src/sync/appearance.ts:180: patch does not apply`. Esse resultado exige rebase do overlay, não diz que a funcionalidade local é desnecessária. Não foram testados todos os overlays de outros sistemas contra v0.42; nenhuma alegação de compatibilidade total decorre destes dois checks.

## 11. Caminho híbrido proposto e custo

Nenhuma etapa abaixo foi aplicada neste despacho. Estimativas de engenharia são aproximadas para um desenvolvedor familiarizado com o projeto; revisão e iterações no Skyrim podem ampliá-las.

| Etapa | Trabalho concreto | Estimativa |
|---|---|---|
| Base e overlays | Preparar candidato isolado sobre v0.42, retirar reaplicação de K já incorporado, preservar demais mudanças oficiais e confirmar ordem de boot/hooks. Não editar os espelhos oficiais. | 2–4 h |
| Serviço único da fera | Portar nossa versão completa sobre o mesmo arquivo oficial: readback, recarga, UUID, dispatcher, vínculo, diário e B1; manter prata/nome/duração existentes. Preservar dados e retornos públicos. | 3–6 h |
| Cliente | Rebase seletivo de `002–005`, conservar SetRace/gerações/claims e integrar `syncPlayerRacialSpells`/correções oficiais de reconexão. Tratar entrada Z como comando validado se necessária; não criar atalho por `cast`. Recompilar sem instalar. | 4–8 h |
| Domínios e painel | Reaplicar composição Sobrenatural/Core/compulsão/F7 sem sobrescrever arquivos novos inteiros; manter `onTransformed/lastTransform/finish` e limites de origem. Reconfirmar forms e configuração. | 2–4 h |
| Regressão e revisão | Rodar suítes, revisar por outro modelo, documentar divergências e preparar pacote pareado cliente/servidor e rollback de dados. | 3–5 h |

Total orientativo: **14–27 h de trabalho técnico**, mais rodada humana e eventuais correções. O maior risco é rebase do cliente/Core, não o serviço de 275 linhas, cujo núcleo já coincide com nossa base. Adotar o oficial puro e reconstruir todas as extensões não traz uma melhoria de núcleo que justifique descartar trabalho revisado.

### Extensões que devem obrigatoriamente permanecer

1. Keyword e estado por personagem no servidor, concessão/remoção pelo painel, progressão e grants por origem.
2. Dispatcher/gates, geração de identidade, nonce e sequência; forma válida não pode nascer de relato antigo de raça/efeito.
3. Recarga real de 30 min configurável, local 0 explícito e independente de duração/relógio de jogo.
4. Aparência antes de fase, recuperação só limpa após readback e `finish` com resultado confirmado para F7.
5. Diário durável antes da raça, identificação por personagem/grant, política conservadora documentada e fila B1 de cancelamentos.
6. `onTransformed`, `lastTransform` e stamp privado para reset da compulsão.
7. Compulsão 72 h online/noite, aviso privado, calendário autoritativo, F7 e cancelamentos.
8. Cliente SetRace real, cancelamento de callbacks, preservação de rosto e protocolos de autoria pareados com Core/servidor.

### Critérios antes de homologar a integração futura

Revisor independente deve confirmar o parecer e o diff final, em especial a lacuna do fonte oficial e a composição com habilidades raciais. Testar no motor Z/Anel, `aeth` e vanilla, morte/caído/desconexão, relog, remoção/troca de maldição/personagem, retorno F7, cooldown real, compulsão com atalho e recuperação após interrupção controlada. Com **dois jogadores**, observar início/final/retorno, nomes e roupas; com terceiro observador, reconfirmar privacidade e entrada tardia. Medir latência de fsync e fila sob falha antes de afirmar custo aceitável em produção.

O relatório entrega a comparação e um caminho definido, não aplica integração. Permanecem revisão independente deste parecer, decisão do coordenador/dev sobre o lote de integração, atualização do Acervo e validação humana; nenhum desses itens foi marcado como concluído por testes Node.

## Revisao Terra High

Revisão independente por Codex, papel Terra High, em 17/11/2026; parecer original de Sol High acima. Examinei as árvores oficiais em `C:\Code\Aetherius - OFICIAL\aetherius-server-main` e `C:\Code\Aetherius - OFICIAL\aetherius-client-main` (versão declarada v0.42.0, sem atribuição adicional de commit), o pacote `C:\Code\Aetherius - New Systems\Sobrenatural\AETHERIUS - LOBISOMEM 14-11 - KDU`, e o laboratório `C:\Code\Aetherius - Servidor Local` HEAD `0e4dd84ee56a03e65ce637891ee00c069002bfbc` e `client-source` HEAD `f57ec7de9cbebd09c61acef78690efe0eaabd5b3`. Não iniciei jogo ou servidor; conclusões de código não demonstram renderização ou sincronização no motor.

**Veredito: concordo com a recomendação (c), híbrido, como direção técnica condicionada a rebase, revisão do diff resultante e testes pareados.** A versão local preserva contratos que o Sobrenatural utiliza; o oficial contém mudanças de cliente que precisam ser trazidas seletivamente. A estimativa de 14–27 horas é planejamento aproximado do autor, não foi validada por medição independente nem é garantia de prazo.

1. **Serviço kdu e oficial: confirmado.** Extraí em memória as linhas adicionadas do patch `K\patches\servidor-lobisomem.patch` e comparei com `O-S\server\gamemode\werewolf-service.js`: 275 linhas em cada, idênticas após normalizar finais de linha. O teste `werewolf/tests/werewolf.test.cjs` também é igual, 423 linhas. Isso identifica o código disponível, sem extrapolar para binários externos ao espelho.
2. **Cliente de efeitos do servidor: confirmado.** A mesma extração/diff de `werewolf/client.js` mostra 236 linhas kdu e 247 oficiais; o único acréscimo é o listener `actionVoiceFire` em `O-S\server\gamemode\werewolf\client.js:33`–`:43`. Não confundir esse arquivo JS entregue pelo servidor com o cliente TS completo.
3. **Fonte TS oficial: confirmado com limite.** Busca textual em `O-C\client\src` por `switchPlayerCreatureRace`, `.setRace(` e `setRace(` não retornou ocorrências; `O-C\client\src\sync\appearance.ts:222`–`:239` aplica aparência à base e chama `syncPlayerRacialSpells`, enquanto `L-C\client\src\sync\appearance.ts:212,242` contém a rotina de troca de raça. Isso não prova o conteúdo ou comportamento de um bundle de launcher não fornecido nem, isoladamente, o resultado no jogo.
4. **Falhas 4.1–4.3: confirmadas no fluxo e condições descritas.** Em `O-S\server\gamemode\werewolf-service.js:166`–`:172`, fase/publicação precedem a escrita de aparência, que pode falhar ou ser no-op; em `:174`–`:184`, retorno da restauração é ignorado e o original é limpo. Em `:121`–`:134`, registro é lista de IDs via tmp+rename, com falha apenas logada, e `:155`–`:161` depende da propriedade para o original. O local em `L-S\server\gamemode\werewolf-service.js:200`–`:232,235`–`:250` verifica readback e mantém a recuperação, com diário; isso não prova persistência nativa atômica nem chegada visual ao cliente. A classificação de Sol como cenários condicionais, e não demonstração de falha habitual em jogo, está correta.
5. **Seção 7: concordo com as incompatibilidades principais.** Reexecutei `git apply --check` sem aplicar: o patch kdu de servidor falhou porque os três arquivos já existem e o hunk de identidade não aplica; `002-formas-raca.patch` falhou no hunk `appearance.ts:180` oficial. Verifiquei o contrato F7 em `L-S\server\gamemode\admin-panel\supernatural-admin.cjs:49`–`:53` versus `O-S\server\gamemode\werewolf-service.js:174`–`:185`: retorno `undefined` produz falha de confirmação. `L-S\server\gamemode\supernatural\forced-werewolf.js:80`–`:85,111`–`:112,138` depende de `lastTransform`/stamp e assinatura opcional `onTransformed`; o serviço oficial não fornece tais garantias. Os formatos de `reached` diferem entre `O-S\server\gamemode\werewolf\client.js:44`–`:49` e `L-S\server\gamemode\werewolf\client.js:33`–`:39`, com validação local em `werewolf-service.js:308`–`:310`; a perda do ponto de antecipação não elimina o timeout. Confirmei os efeitos indicados para substituições isoladas; não executei a integração completa sobre v0.42.
6. **Risco da entrada Z:** `L-S\server\gamemode\supernatural\power-input.js:25`–`:30` envia `activate` a partir de `spellCast`; o script de fera local também usa `spellCast` e não contém `actionVoiceFire`. A observação oficial de que Z pode não produzir `spellCast` é documentada no código oficial, mas não foi reproduzida nesta revisão. No híbrido, adaptar a entrada Z ao dispatcher validado e testar no motor; simplesmente copiar o listener oficial que envia `cast` não demonstra que o dispatcher local aceitará um comando.
7. **Custo e riscos da seção 11: qualitativamente plausíveis.** Rebase seletivo de cliente/Core, serviço único, ordem de hooks, migração de dados, revisão e testes são necessários; em especial não copiar integralmente o `appearance.ts` local, que substituiria a sincronização racial oficial. As horas propostas não foram cronometradas nem todos os overlays foram verificados; tempo real pode variar. O novo diário e retries também acrescentam I/O síncrono e não há medição de carga real aqui.

**Testes repetidos:** em O-S `node --test server/gamemode/werewolf/tests/werewolf.test.cjs`: **29/29**, exit 0. Em `C:\Code\Aetherius - Servidor Local`, a seleção local de §10 (werewolf, formas, aparência, transformação forçada, revisão 3 e origem): **130/130**, exit 0. Ambos usam mocks, não validam nativo, visual, rede ou produção. Confirmei também SHA-256 bruto igual de `L-S\server\gamemode\werewolf-service.js` e seu overlay: `C785CCEC45335BA797AEE6392D0101DBBAF054A7861417117DBD7A1AFD2DF493`.

A aprovação deste parecer não aprova a integração futura; faltam decisão do coordenador/dev, diff híbrido revisado e testes em jogo com dois ou mais jogadores. Nenhuma edição foi feita nos espelhos oficiais, servidor, cliente ou modpack; consolidação no Acervo fica com o coordenador.
