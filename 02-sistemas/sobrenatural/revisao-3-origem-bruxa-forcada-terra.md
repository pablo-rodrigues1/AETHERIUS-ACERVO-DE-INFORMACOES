# Revisão cruzada 3 — origem, Bruxa e transformação forçada

Data: 16/11/2026. Revisora: Terra High; autor: Sol High; catálogo Bruxa: Gemini 4 Argon. Alvo: `C:\Code\Aetherius - Servidor Local`, raiz `856232aa0cc7474a50017e3382a86fe2d5c7f6aa`, incluindo `79098ad` e `4fc0034`; source `310cb598fd382cf4d6598cb34eada5db1a3fa9f8`, client-source `057f8553aa4c0ec6bc06dd37596aacb70e0d3163`. Revisão estática e testes em mocks; servidor não reiniciado, código e modpack não editados. Outro worker modifica Reserva de Sangue em paralelo: este parecer não é revisão de sua entrega posterior.

## Veredito: REJEITADO para integração/homologação

As garantias de preservação de origem e de custo sem cobrança em falha ainda não estão demonstradas e há casos concretos abaixo. O comportamento em jogo e a replicação visual seguem pendentes. Achados aguardam arbitragem e correção; nenhuma solução foi aceita nem rejeitada pelo autor nesta revisão.

## Achados

### 1. Alta — claims de perks não são reconstruídos antes de permitir remoção após reinício

`source/server/gamemode/supernatural/perk-origin.js:6-10,20-27`, `supernatural/client.js:38-50` e `client-source/client/src/services/services/gameplayGrantService.ts:190-201`: a união de claims fica apenas em `sp.storage` e nasce conforme chegam as publicações. Em cliente recém-iniciado, se uma perk compartilhada foi atribuída e atestada por ambos, mas a remoção de uma origem chega antes do ensure da outra, `attested` marca `owned`, `claims` está vazio e `removePerk` executa. A outra origem pode readicionar depois, mas existe remoção temporária e, se sua entrega estiver atrasada/falhar ou se a remoção tiver efeitos, a garantia de preservação não vale. Os testes das duas ordens no mesmo processo não substituem o cenário com `sp.storage` vazio e entregas atrasadas/reordenadas após relog.

**Correção:** coordenar um snapshot/barreira de claims de todas as origens duráveis por personagem antes de remover; não inferir ausência de claim de um mapa ainda não hidratado. Testar ambos os autores, ordem inversa, reconnect, reset/troca de maldição, perda de pacote e falha da outra entrega, incluindo efeitos da remoção.

### 2. Alta — custo de Sedução/Reanimação é debitado antes da decisão nativa final

`source/server/gamemode/supernatural/power-input.js:50-67,152-160` comita logo após o hook anterior aceitar; `source/server/cpp/server_guest_lib/ActionListener.cpp:1818-1826` ainda pode rejeitar a conjuração em `OpenBoundWeaponCast` depois desse commit. Nesse caminho o sangue é debitado sem envio/execução do cast. A ausência de reserva também libera o uso (`power-input.js:51-54`), comportamento explicitamente provisório, mas incompatível com uma garantia de que não há uso grátis quando a integração estiver habilitada e o módulo falhar ou não instalar. Além disso, o hook recebe uma proposta do cliente: sua aceitação não comprova efeito na vítima, como o próprio relatório reconhece.

**Correção:** desenhar confirmação posterior à validação nativa e definir política explícita para cast interrompido/falha e efeitos direcionados; não finalizar débito antes do último ponto capaz de recusar. Em modo que exija custo, falhar fechado se a reserva não existir. Exercitar recusa de `OpenBoundWeaponCast`, erro/false no commit, replay, duas reservas simultâneas, interrupção e reinício. A correção pode exigir fronteira nativa; não afirmar atomicidade enquanto não existir.

### 3. Alta — magias adquiridas por outra origem depois da concessão podem ser removidas

`source/server/gamemode/supernatural/grant-origin.js:13-21,24-30`: a preservação cobre presença **anterior** ao AddSpell e intenções ambíguas, mas, depois que a magia está `owned`, outra origem pode conceder a mesma magia sem registrar claim; a remoção da maldição executa `RemoveSpell` mesmo que essa outra origem ainda a exija. Isso afeta inclusive magias comuns de Bruxa e upgrades TENEBRAE. O relatório de Sol reconhece a limitação, mas ela contraria a garantia pedida de nunca remover concessões de outra origem. A mesma limitação existe para perks externas que não participem do protocolo compartilhado (`perk-origin.js:9-10,20-27`).

**Correção:** coordenar os provedores relevantes por ownership durável e atualização de claims, ou preservar conservadoramente itens cuja procedência posterior seja desconhecida e documentar a consequente possível retenção. Testar ambas as ordens de aquisição, relog, troca, remoção e progressão/redução; não anunciar proteção universal enquanto fontes externas forem inobserváveis.

### 4. Média — janela de transformação confirmada sem stamp durável

`source/server/gamemode/werewolf-service.js:186-200` grava e confirma `appearance` de fera antes de persistir `aetheriusBeastTransformStamp`. Falha/no-op da gravação do stamp devolve false depois da mudança de raça, mantém turning e tenta de novo no tick; uma queda nesse intervalo permite recuperar a aparência sem o stamp que `supernatural/forced-werewolf.js:80-86,107-108` usa para zerar as horas. O contador pode permanecer no limite apesar de transformação real já ter ocorrido. Além disso o readback de `mp.set` não prova durabilidade no disco de ator após crash. Os testes com mocks não simulam essa janela entre as duas escritas e um crash real.

**Correção:** estabelecer protocolo de recuperação transacional com intenção durável vinculada à concessão e fase, decidir recuperação conservadora quando a aparência chegou à fera sem stamp, e testar queda após cada escrita, falha/no-op de stamp, reinício e troca de personagem/concessão. Confirmar a durabilidade efetiva do armazenamento de propriedades.

### 5. Média — regressão da sincronização pontual da animação vanilla kdu

`source/server/gamemode/werewolf-service.js:246-262` desvia **todos** os eventos kdu para `power-input.js:132-138` enquanto o dispatcher existe; inclusive `reached`, que antes chamava `becomeBeast` no ponto SetRace da animação vanilla, passa a ser apenas evidência e é ignorado. `werewolf-service.js:298-300` só transforma no timeout (5,5 s vanilla por padrão). A proteção contra mensagens forjadas é válida, mas a sincronia da troca no marcador não foi preservada; o dono pode mudar de raça antes de vizinhos. A animação padrão local é `aeth`, por isso a regressão se manifesta na configuração vanilla e não é prova de defeito visual na configuração atual.

**Correção:** associar `reached` somente à sessão de transformação já iniciada pelo servidor, sua identidade/geração e limites temporais, sem permitir que o relato crie/antecipe uma sessão arbitrária; testar vanilla e aeth, mensagens antigas e duplicadas, ausência de marcador e dois clientes.

## Demais verificações e limites

- A ligação Bruxa seleciona `witch.spellsFor(stage, vertente)` e `witch.perksFor` em `supernatural-service.js:86-100`; catálogo é incluído na validação de boot. Os testes verificam upgrades/redução e pisos 30/40/50/60/60/60 via maior valor com a classe, inclusive remoção e troca de personagem. Isso não verifica records vencedores, quests ou aplicação no jogo. Parar três quests do Shadow Spell Package para todos possui efeito transversal documentado em `supernatural/client.js:7-29`; exige homologação desse impacto também para não bruxos.
- A recuperação da raça vampírica agora salva a referência antes da projeção e só limpa após readback (`supernatural-service.js:107-140`). A fase de fera só é publicada após readback da aparência e stamp, sujeito à janela do achado 4. O teste de callbacks de cliente passou; não demonstra sequenciamento no motor real.
- A compulsão usa relógio monotônico e acumula somente sessões observadas online; separa `characterId`, `grantId` e identidade de sessão; noite é [20, 5), aviso privado, cancelamento antes do start, e transformação usa o serviço kdu que publica a propriedade para dono e vizinhos. Persistência periódica a cada 30 s significa perda de até aproximadamente um intervalo em crash e não é precisão exata; tempo online entre último tick e desconexão não é contabilizado. Sem teste real de dois clientes, watchdog, áudio e visual não estão homologados.
- Atalho F7 usa a API no servidor e os testes de autorização, alvo e replay passaram. Kdu e F7 preservam regressões cobertas pelos mocks; isso não substitui validação com servidor e jogo.
- Cópias de source e overlay coincidem por SHA-256 para `supernatural-service.js`, `werewolf-service.js`, `vampire-lord-service.js`, `supernatural/{client,grant-origin,perk-origin,power-input,powers,witch-grants,forced-werewolf,forced-werewolf-client}.js` e `admin-panel/supernatural-admin.cjs`. `git apply --reverse --check` passou em client-source para patches 002 e 003 e em source para patches 005 e 006. Não comparei ESP ou runtime instalado, nem reavaliei vencedores de load order; a Visão e assets exigem validação houseCARL e jogo na integração.
- Os controles de nonce e gates do lote `4fc0034` reduzem replay e entradas antigas, mas o hook nativo continua baseado em mensagem de cliente e não prova uma conjuração genuína. A hipótese de segurança deve permanecer explicitamente limitada.

## Testes executados

Na raiz `C:\Code\Aetherius - Servidor Local`: `node --test overlays/sobrenatural/teste-transformacao-forcada.cjs overlays/sobrenatural/teste-origem.cjs overlays/sobrenatural/teste-poderes.cjs overlays/sobrenatural/teste-formas.cjs overlays/sobrenatural/teste-aparencia.cjs source/server/gamemode/werewolf/tests/werewolf.test.cjs source/server/gamemode/magic/tests/magic.test.cjs`: **122/122**. `node overlays/sobrenatural/teste-servico.cjs 'C:/Code/Aetherius - Servidor Local/source/server/gamemode'`, `node overlays/sobrenatural/teste-painel-f7.cjs`, `node overlays/sobrenatural/teste-revisao-f7.cjs` e `node --check source/server/gamemode/phase0-basic.js`: passaram. Fixture de serviço avisou records ausentes e ponte de reset ausente; isso não confirma a carga real dos plugins.

Em `C:\Code\Aetherius - Servidor Local\source\server\gameplay\core`: `node --test runtime/tests/perk-client.test.mjs runtime/tests/client-delivery.test.mjs runtime/tests/class-skills-delivery.test.mjs`: **29/29**. Os testes de 50 casos do lote menor estão incluídos no conjunto de 122. Não rodei build nem servidor, e não fiz teste humano.

## Tratamento e versão final

Achados propostos por Terra High, pendentes de decisão do coordenador e do autor. Versão corrigida e confirmação independente posterior ainda não existem; este documento não declara resolvidos os achados nem substitui a revisão do trabalho paralelo da Reserva de Sangue.

## Arbitragem do orquestrador (Opus 5.5, 2026-11-16)
Os 5 apontamentos foram **aceitos**. O Sol High corrige com PRIORIDADE sobre a Reserva de Sangue (que ele está fazendo), porque os itens 1–3 tocam o reconciliador e o custo dos poderes que a reserva usa. Depois o Terra High confirma.


## Decisões posteriores do dev e resposta do autor (16/11)

O coordenador confirmou: (2) Sedução/Reanimação **sem custo**, mantendo funcionamento normal; os sete poderes restantes falham fechados sem reserva válida; (3) remover todos os grants próprios e preservar o comprovadamente preexistente, aceitando que uma fonte externa posterior não observável possa ser removida junto; nenhum nativo. (1), (4) e (5) corrigidos com snapshots completos, diário antes da raça e marcador vinculado à sessão.

Sol High preparou o candidato e **180/180 testes Node**, scripts serviço/F7 e builds passaram. O tratamento detalhado, as exceções e os limites estão em `C:\Code\Aetherius - New Systems\Sobrenatural\docs\REVISAO-3_CORRECOES_sol.md`. Este acréscimo é a resposta do autor/coordenador, **não altera o veredito original da Terra nem presume a confirmação independente**, que ainda será solicitada.
