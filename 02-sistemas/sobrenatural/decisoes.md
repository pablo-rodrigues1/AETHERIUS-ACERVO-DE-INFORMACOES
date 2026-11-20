# DECISÕES — Funcionamento das mecânicas e sistemas do Sobrenatural

Registro consolidado (Opus 5.5, 16/11/2026) de **tudo o que foi decidido** sobre como o Sobrenatural funciona. Ordem de prevalência: decisão do dev nesta pasta > planilha (`ESPECIFICACAO.md`) > `referencia/Aetherius_Sistema_Vampirismo_Mecanicas.md`. Itens marcados **(provisório)** são valores de teste aprovados pelo dev, não balanceamento final. Itens **(orquestrador)** foram decididos tecnicamente pelo Opus e aguardam confirmação do dev.

Detalhes de implementação e evidências ficam nos documentos de etapa (`ETAPA-*.md`); o que falta fazer está em `PENDENCIAS.md`.

---

## 1. Regras gerais (todas as maldições)

| Tema | Decisão |
|---|---|
| Quem concede | Só a staff, pelo **Painel Administrativo**. No ambiente local, pela aba **Sobrenatural do F7** (consultar, setar, remover, XP, Disciplina, Vertente, encerrar forma, forçar transformação). Nunca pelo cliente. |
| Quantidade | No máximo **uma** maldição por personagem. |
| Keyword | `JogadorVampiro`, `JogadorLobisomem` ou `JogadorBruxa`, permanente, **só no servidor**. Nunca vai ao cliente nem aparece para o jogador; o cliente recebe só o estado de apresentação (maldição, XP, escolhas, `revision`, `grantId`). |
| Ao setar | Começa no estágio 1 com 0 XP; grava a data ("Portador desde"); **o servidor reseta a classe** para o nível 1 (ponte ainda pendente). |
| Ao remover | **Apaga todo o progresso** (XP, estágio, Disciplinas, Vertente, data) e **remove tudo o que o Sobrenatural concedeu** (magias, perks, poderes, raça vampírica, forma ativa). Preserva só o que o personagem comprovadamente tinha **antes** da concessão. Limitação aceita: uma magia/perk ganha por outra fonte **depois** da concessão pode sair junto. |
| Permadeath | Decisão de RP da staff, não é mecânica. |
| Origem | O servidor registra (persistido) o que concedeu e nunca remove o que já existia antes da concessão (magias no servidor; perks no cliente com registro de autoria). |
| Versão do estado | `revision` monotônica por personagem (nunca reinicia); `grantId` novo a cada setagem; escolhas levam `grantId` + `revision` e são recusadas se desatualizadas. |

## 2. Progressão (6 estágios, igual nas três maldições)

| Estágio | Custo | XP acumulada |
|---|---:|---:|
| 1 | — | 0 |
| 2 | 20.000 | 20.000 |
| 3 | 40.000 | 60.000 |
| 4 | 130.000 | 190.000 |
| 5 | 420.000 | 610.000 |
| 6 | 1.390.000 | 2.000.000 |

- 20k e 40k definidos pelo dev; o restante foi calculado até o total de **2.000.000** (razão ≈3,26).
- XP de maldição pelas mesmas ações da XP de classe (gancho no GameplayCore pendente).

## 3. Interface (TAB → Sobrenatural)

- 4 telas: sem maldição ("Você não é portador de nenhuma praga."), Vampiro, Lobisomem, Bruxa.
- Visual do módulo de classes: branco sobre preto transparente (mesmas regras de fundo do class-module), fonte **Sovngarde Bold**, ícones estilizados à mão. Cor só nos detalhes: **Carmesim** (vampiro), **Âmbar** (lobisomem), **Violeta** (bruxa).
- Sobretítulos: **Corrupção do Sangue**, **Corrupção da Lua**, **Corrupção do Véu**. Trilha mostra só "Sobrenatural".
- Nomes de feitiços, perks e poderes **em português**. Necromaster aparece como **"Necromestre"**.
- Layout em duas colunas (hero + trilha de 6 marcos à esquerda; marcos e dons à direita); atualização por push do servidor + consulta a cada 10 s + botão Atualizar.
- Regra técnica: no Meridian, uma pasta `MeridianUI` **nunca** pode vir de dois mods (dá 404). Alterações de interface só em cópias completas `(LOCAL)` dos mods.

## 4. Vampirismo

### 4.1 Estágios e bônus
- Estágios: Neófito, Vampiro Pleno, Ancilla, Ancião, Suserano, Apoteótico.
- Vida/Magicka/Vigor extra (só o valor do estágio atual, não soma): 50/75/100/125/150/200. Dano desarmado: —/5/10/15/20/**20** (Apoteótico assumido igual ao Suserano; a confirmar).
- Bônus aplicados por habilidades do plugin **AETHERIUS - SOBRENATURAL.esp**.

### 4.2 Disciplinas
- Celeridade (velocidade +10–50%), Fortitude (vida +25–200), Potência (dano físico, armas incluídas, +5–25%), Feitiçaria do Sangue (magicka +25–200), Tenacidade (vigor +25–200).
- 1ª escolha no **Vampiro Pleno**; 2ª (diferente, soma-se à 1ª) no **Apoteótico**. **Não pode trocar.**
- **Grau = estágio − 1** (Vampiro Pleno = I … Apoteótico = V); a 2ª já entra no grau V.

### 4.3 Inatos e poderes
- Inatos desde o Neófito (Curse of the Vampire): resistências base, veneno 100%, gelo 25%, fraqueza a fogo 25%, Supernatural Awareness, vigor acelerado, fraqueza ao sol, Visão Vampírica, Sedução, Forma de Morcego, Dreno, Reanimação.
- Por estágio: Vampiro Pleno → Força Imensurável; Ancilla → Forma de Névoa; Ancião → Celeridade Não Natural; Suserano → Enxame Devorador.
- **Duração 40 s** e **recarga de 60 s a partir do uso** (20 s de espera após o efeito) para **Força, Névoa, Celeridade e Enxame**.
- **Forma de Morcego:** avanço pelo motor do dono e invisibilidade por 1 s. A correção B1 de 19/11 substituiu o prazo sem recarga por 3 s provisórios. A configuração VAMPIRE_BAT_COOLDOWN_SECONDS aceita 2–60 s e o host verifica a posição dos pacotes no avanço.
- **Sedução e Reanimação:** mantêm as regras próprias de duração **(orquestrador)** e **não custam sangue**.
- **Névoa** (zerar regeneração) e **Força** (bônus no dano real): **parciais** — exigem código nativo, fora do escopo.
- **Enxame:** números do COTV como base **(provisório)**: dreno de 10/s por 15 s.
- **Visão Vampírica:** deve funcionar como o **Aura Whisper** (revelar criaturas e jogadores próximos), com prioridade sobre o Predator Vision.
- Efeitos e invisibilidade dos poderes devem ser **vistos por todos** os jogadores.

### 4.4 Raça e Lorde Vampiro
- Ao setar, a raça atual é trocada pela **equivalente vampírica** (10 raças vanilla; inclui olhos vampíricos). Ao remover, volta a raça original. *(Aprovado em jogo.)*
- **Forma de Lorde Vampiro:** sincronizada para todos, no mesmo modelo da fera do kdu (servidor dono da forma).
- Na versão final, o poder do Lorde **só será dado por uma função específica do painel**. Por enquanto, para testes, é concedido desde o Neófito.

### 4.5 Reserva de Sangue (mecânica principal; spec em `referencia/Reserva_de_Sangue_Aetherius.md`)
| Tema | Decisão |
|---|---|
| Faixa | 0–100; vampiro recém-setado começa com **100**. |
| Tempo | **Tempo real online** (1 h = 3.600 s com o vampiro conectado); não drena offline. |
| Drenagem | 100→80 em 1 h; 80→60 em 2 h; 60→40 em 3 h; 40→30 em 1h30 (mesma taxa da faixa anterior); 30→0 em 6 h. Total **13h30**. |
| Necessidades | Fome, sede e frio **desligados por personagem** (lógica, efeitos e HUD); no lugar, barra vermelha de sangue com ícone no canto inferior direito. |
| Poderes | Cada uso **bem-sucedido** custa **5**; recusado com menos de 5. Contrato no servidor: `reserve` (separa) → `commit` (desconta no sucesso) / `cancel` (devolve na falha); token expira sozinho. Poderes cobrados: Força, Névoa, Celeridade, Enxame, Morcego, Visão, Lorde. **Sedução e Reanimação isentas.** Se o módulo da reserva não estiver instalado, os poderes cobrados ficam bloqueados. |
| Sede Crítica | Em **≤30**: batimentos em loop e Visão Vampírica automática (raio 25 m), sem custo; sai quando **>30** (histerese ainda a decidir). |
| Feral | Em **0**: aura vermelha visível a todos e nome visível **"VAMPIRO FERAL"** (sem alterar o nome salvo). Não tira o controle nem mata. |
| Poção de Sangue | `DLC1BloodPotion`: **+50 de sangue** e **cura 100** (a cura de 100 já existe no item; o servidor só credita o sangue, uma vez por poção). |
| Alimentação | **Só em jogadores**, pela opção **"Beber Sangue" do menu X**. Vampiro: **+100 de sangue e cura 200**. Vítima: perde **20% da vida máxima**, sem morrer; alcance **2 m**; recarga de **60 min por vítima**; respeita as regras de PvP/zonas existentes. Sequência: tela preta de 10 s com as mensagens na vítima; som e tela vermelha no vampiro; animação de feeding nos dois. Nunca transforma a vítima. |
| NPC | Alimentação em NPC **cancelada**. |
| Morte | **Mantém** o valor da reserva. |

### 4.6 Cura, comida e sol
- Regeneração natural de vida **mantida**.
- Vampiro **não pode usar itens de cura pura** (efeito principal de restaurar vida); itens mistos ficam liberados (limitação: separar só o efeito de cura exigiria nativo). Magias de cura convencionais bloqueadas. Canais vampíricos (Dreno, alimentação, Poção de Sangue) curam.
- **Dano solar: 5 por segundo**, das **7h às 19h** do jogo; chuva, neve, nevasca e interior protegem; nublado não; **nado não protege** até haver forma confiável de detectar. Sem recuperação retroativa offline. Controlado pelo servidor atrás de `AETHERIUS_VAMPIRE_SERVER_SUN`; quando ligado, o efeito solar do Curse of the Vampire deixa de ser concedido (sem dano duplicado).

## 5. Licantropia

### 5.1 Base
- A **forma da fera é do sistema do kdu** (`werewolf-service`, animação própria via Pandora). O Sobrenatural cuida de setagem, keyword, progressão, perks, bônus e UI.
- Estágios: Iniciado, Ômega, Beta, Gama, Delta, Alfa. Perks do **Manbeast** por estágio (acumulam) e a habilidade de nível da fera (`MAG_WWLevelAbility15..40`) do estágio atual.

### 5.2 Bônus (plugin AETHERIUS - SOBRENATURAL.esp)
| | Iniciado | Ômega | Beta | Gama | Delta | Alfa |
|---|---:|---:|---:|---:|---:|---:|
| Vida e vigor (humano e fera) | 125 | 150 | 175 | 200 | 225 | 250 |
| Vida e vigor extra (só fera, **somam**) | 150 | 200 | 250 | 300 | 350 | 400 |
| Total na fera | 275 | 350 | 425 | 500 | 575 | 650 |
| Dano desarmado (humano e fera) | 5 | 10 | 15 | 20 | 25 | 30 |
| Armadura (só fera) | 150 | 200 | 250 | 300 | 350 | 400 |
| Resistência mágica (só fera) | 10% | 15% | 20% | 25% | 30% | 35% |

### 5.3 Forma da Besta
- Recarga de **30 min de tempo real** por padrão (o poder deixou de ser "uma vez por dia"; virou LesserPower); **sem recarga** nos testes locais.
- Encerrar forma pelo F7 (testes).

#### Forma da Besta no TAB (decisão do dev, 19/11)

- O botão fica no topo da tela do Lobisomem. A intenção usa a mesma sequência do F7, com pré-aviso privado e animação sincronizada do kdu. A forma dura **10 min reais a partir da troca de raça** e encerra automaticamente.
- A recarga do botão dura **2 h reais a partir do aceite**, por personagem e concessão. O relógio e o registro do servidor mantêm o prazo durante relog ou reinício. Morte, saída ou encerramento posterior ao aceite preservam a recarga. Nova concessão inicia seu próprio prazo.
- O uso pelo botão mantém o contador como estava no momento do aceite. Depois disso, as horas online seguem sua contagem normal, inclusive durante a forma.
- **Confirmado pelo dev (19/11):** o botão pode ser usado a qualquer hora. Uma sequência automática/F7 já iniciada bloqueia o botão. Se as 72 h vencerem durante o aviso, transformação ou retorno do botão, a compulsão aguarda o personagem voltar a humano e estar elegível à noite. A compulsão pode ocorrer durante as 2 h de recarga do botão.
- **Confirmado pelo dev (19/11) — Z mantido junto com o botão:** mantém poder, duração, recarga e reset legados quando a transformação pelo Z é confirmada. Durante o aviso do botão, o serviço recusa o Z. As duas entradas compartilham uma única sessão de fera e têm recargas independentes. T1/T2 e os uivos seguem seu roteiro de diagnóstico.

### 5.4 Transformação forçada
- O servidor força a transformação após **72 h de tempo real online**, **à noite no jogo (20h–5h)**. O contador por personagem zera na transformação automática, pelo F7 ou pelo Z legado quando a raça é confirmada. Se vencer de dia, espera a próxima noite online.
- Sequência: t=0 mensagem **"A Besta clama pela caçada..."**; t=5 s batimento cardíaco em loop e tremor de tela; t=10 s filtro vermelho; t=15 s **"A Besta está prestes a rasgar sua carne..."**; t=20 s transformação pelo fluxo normal do kdu.
- Efeitos só na tela do jogador; transformação vista por todos. Cancela com limpeza em morte, desconexão, perda da maldição ou se já estiver em forma. Atalho de teste no F7.

## 6. Bruxaria

- Mods: Triumvirate, Mysticism, Vokrii e **Shadow Spell Package**.
- Estágios: Neófito, Acólito, Feiticeiro Negro, Magus Diaboli, Alto Teurgo, Hierofante. Tabelas completas por estágio em `ESPECIFICACAO.md` §5 (revisada pelas coordenadas da planilha).
- **Vertente:** MORTUI, DAEMONES ou TENEBRAE, escolhida no **estágio 1**, **sem troca**.
- **Pisos de habilidade** (Alteração, Conjuração, Destruição, Ilusão): 30/40/50/60/60/60. Aplicados pelo GameplayCore com o **maior valor entre a classe e a maldição**, entregue pelo servidor, por personagem; volta ao valor da classe ao remover.
- **Upgrades da TENEBRAE feitos pelo servidor** (o upgrade automático do mod não é usado): Acólito → Shadowbolt nível 50; Alto Teurgo → Conjure Shadow Atronach já na versão **Expert**; Hierofante → Shadowbolt nível 75. As três quests de upgrade automático do Shadow Spell Package são paradas no cliente, para todos os jogadores (efeito colateral documentado).
- **Necromancy:** `VKR_Con_030_Necromancer_Perk_WasNecromancy` (PERK 000581DD).
- **Conjure Keeper:** só **Arqueiro** e **Guerreiro** (o Cavaleiro não entra).

## 7. Ambiente local de testes (decisões técnicas)
- Servidor local em `C:\Code\Aetherius - Servidor Local`, homologação parcial aceita (Alma e market_stalls sem migrations; núcleo nativo do kit v19).
- Perfil MO2 **`AETHERIUS - LOCAL`**: oficial + mods do Sobrenatural, do kdu (animação + plugin), saída do Pandora isolada (`AETHERIUS - LOCAL - PANDORA`) e cópias `(LOCAL)` de UI CORE e MERIDIAN - COMPATIBILIDADE. O perfil oficial não é alterado.
- Cliente local recompilado com os patches (base sem patches é byte-idêntica ao oficial).
- **Scripts do servidor assinados** com chave Ed25519 de teste (o cliente recusava scripts sem assinatura). O defeito do cliente oficial (`publicKeys = {}`) deve ser levado ao responsável.
- Nada no repositório oficial, no painel oficial ou no modpack oficial é alterado; integração documentada como patch.

## 8. Processo
- Fluxo do `AGENTS.md`: implementação por um modelo, **revisão cruzada** por outro, validação do Opus, documentação local + acervo.
- Exceções autorizadas pelo dev: Bruxaria implementada pelo Gemini 4 Argon (records levantados pelo Luna MAX); lote Fera/Lorde/raça foi ao teste sem a confirmação do Terra (revisão feita depois).

## 17/11 — parâmetros de candidato e bloqueio D7 (não substituem aprovação final)

- **D1 aberta:** `AETHERIUS_VAMPIRE_NEEDS_RESTORE=frozen` por padrão; alternativa `default`. Marcador PG conserva o retorno também offline. Não é decisão final do dev.
- **D2 aberta:** `AETHERIUS_VAMPIRE_CRITICAL_EXIT=30` por padrão (entra ≤30, sai >30); alternativa de teste 35. Raio de visão 25 m; conversão configurável `AETHERIUS_SKYRIM_UNITS_PER_METER`, padrão aproximado 70/m, a validar em jogo.
- **D3 parcialmente fechada pelo dev, 17/11 (via Opus):** alimentação **sem consentimento** da vítima. Vampiro seleciona Beber Sangue no menu X autenticado e inicia direto se **ambos estiverem sem elmo**; capuz permitido. Servidor valida equipamento/records no início, recusa elmo com aviso claro ao vampiro e falha fechada quando não consegue classificar. `AETHERIUS_VAMPIRE_FEED_REQUIRE_CONSENT` foi retirada do leitor; convites legados são cancelados no boot e não há feed-reply/UI de consentimento. `AETHERIUS_VAMPIRE_FEED_ENABLED=false` permanece, ativação depende do dev. +100 sangue/+200 cura, −20% máxima não letal, 2 m, 60 min/vítima, PvP/zonas, exclusividade e journal/quarentena preservados. Critério de capuz/elmo e ambiguidades documentados na seção D3 de ETAPA-3; não é regra por nome enviado pelo cliente.
- **D7 — bloqueio aceito pelo Opus em nome do dev:** a Poção de Sangue não tem transação atômica entre JSON/reserva, baixa nativa e ledger PostgreSQL; não ampliar contrato nativo neste despacho e não aceitar garantia menor para ela. Alimentação compartilha a mesma fronteira Health/JSON; autorizado só o candidato desativado, com intenção durável, bloqueio conservador e reconciliação manual em falha/crash, SEM alegação de atomicidade. Ativar a flag depende do dev. Evidências, opções e limites em `C:\Code\Aetherius - New Systems\Sobrenatural\docs\ETAPA-3_vampirismo-mecanicas.md`, seções de 17/11.


## 17/11 — D7, ressalva operacional confirmada na R2

O crédito antigo **+50 da Poção de Sangue continua ativo sem flag**, instalado por `C:\Code\Aetherius - Servidor Local\source\server\gamemode\phase0-basic.js:434`, wrapper `supernatural\blood-reserve.js:166–176` (`api.add` em `:172`). As flags solar e `AETHERIUS_VAMPIRE_FEED_ENABLED=false` não desligam esse crédito. D7 bloqueia homologação/atomicidade, não representa desligamento do caminho antigo: JSON, remoção nativa e ledger PG continuam sem transação comum. O fsync/rename do lote R2 não altera esse limite; não houve autorização para remover/flaggear a poção nesta arbitragem. Alimentação permanece default false, com quarentena manual, e sua ativação depende da decisão do dev. Duas opções e evidências nativas permanecem em ETAPA-3; nenhuma garantia menor foi aceita como atomicidade.


## 17/11 — arbitragem Opus do instante de saída (residual R2 #2)

Ao fechar a reserva, usar `min(now, offlineAt)` quando existe ausência observada; sem ela, now. Reaparecimento observado liquida o segmento anterior até esse limite e abre um novo relógio sem offlineAt; não cobrar o gap desconhecido. Correção `a35b0e8` no servidor local, blood-reserve.js:78, regressões com callback atrasado e reentrada via tick/get. Decisão não altera D7 nem presume precisão física do socket. Evidência e limites em ETAPA-3, seção residual R2.

## 17/11 — D7 decidida pelo dev: descartar o crédito em caso de dúvida

O dev autorizou perda do item ou do efeito quando houver falha, crash ou resultado ambíguo. A Poção de Sangue só pode render +50 com consumo confirmado e crédito idempotente. Uma sessão de Beber Sangue interrompida deve terminar automaticamente sem recompensa em sangue, com registro da vida observada e sem replay. O dev autorizou habilitar a flag apenas no laboratório depois de implementar a D7. O padrão do código continua false.

Triagem Sol High sobre o servidor local `1088d0b`: a política está decidida. A aplicação ao caso de vida já alterada em Beber Sangue foi encaminhada ao coordenador antes de implementar. O ledger da poção não confirma por si só a baixa nativa daquele evento. Ver a análise de fronteiras no relatório da Etapa 3.

## 17/11 — D7 implementada no candidato local

A arbitragem do Opus confirmou o contrato da poção: o `EatItem` observado após a verificação de posse é evidência suficiente em processo vivo. Por evento, a poção rastreada exige a confirmação do débito com chave idempotente. Uma poção fora do estoque rastreado continua valendo após consulta PG bem-sucedida. Falha ou resultado incerto do PG ou da reserva descarta o crédito, sem replay. A cura continua sob o efeito do item.

Beber Sangue preserva HP já alterado, saldo legado e cooldown gravado. A recuperação registra resultados confirmados ou desconhecidos e libera os participantes. O fluxo novo fecha o registro de HP e encerra a sessão antes do commit final do sangue. O dev autorizou a flag verdadeira em `config/local.env`; o código continua falso por padrão. Candidatos `0ec39d2` e `2088225`.

## 18/11/2026 — Bruxaria: Vínculo do Véu (dev)
- Vínculo inicial 100; 100→0 em 12 h online; sem consumo offline.
- Estágio V (0): Magicka máxima −100% e evento de terror a cada 5 min (sempre com evento).
- Ritual iniciado por botão na tela da Bruxa (TAB → Sobrenatural), que abre nova interface estilizada com entidade, explicação mínima e "Iniciar Ritual".
- Pedra da Alma Negra cheia e vazia; carne = todas as carnes cruas; pele = todas as peles; livro = qualquer tomo.
- Selo no centro inferior (maldições exclusivas entre si); barra central do gauge em degradê como a referência.
- Demais parâmetros sugeridos da especificação aceitos como iniciais configuráveis.
- Escrita no `AETHERIUS - SOBRENATURAL.esp` autorizada; runa = `MasterSpellsRunes.esp` (Conjuração).
- Pendente: quem escolhe os 3 ingredientes (suposição: servidor automático).
- Detalhe: `docs/ETAPA-4_bruxaria-vinculo-do-veu.md`. Implementação **não iniciada** por ordem do dev.

## 18/11/2026 — Sobrecarga do Vínculo adicionada ao plano (dev)
- Especificação `referencia/Aetherius_Implementacao_Sobrecarga_Bruxas.md` incorporada à ETAPA-4 §8 (teto 300, ritual de sobrecarga com receita dobrada, blocos de 50, +50% Magicka máx. fixo, olhos Dark/Light/Glow, BRUXA violeta, aura roxa, sussurros, selo com raios e capacete demoníaco). Não iniciada; pendências S1–S7 na ETAPA-4 §8.3.
- 18/11 (dev): raça sem a variante de olho pedida usa a variante Magic disponível mais próxima.

## 18/11/2026 — Isolamento da entrega em relação ao Aetherius UI (dev) — REGRA DE COMPILAÇÃO FINAL
- O Sobrenatural é um sistema **isolado**. A compilação final **não pode conter nenhuma alteração** no Aetherius UI que não seja do próprio módulo Sobrenatural.
- **Proibido na entrega:** mudanças visuais na HUD do Aetherius UI fora dos elementos do Sobrenatural (barra de sangue, selo do Vínculo, vinheta/efeitos próprios), no **menu radial** e em **qualquer outro módulo** do UI Core/Meridian (CSS/JS/HTML globais, temas, layout do shell, `modules.json` além do registro do próprio módulo, `index.html` além do ponto de montagem estritamente necessário).
- **Permitido:** arquivos do módulo `supernatural` e componentes exclusivos do Sobrenatural; o mínimo de integração (registro do módulo e ponto de montagem da HUD própria), documentado como diff isolado.
- As "4 exceções de CSS do shell" aceitas na Etapa 1.4 e qualquer alteração já feita fora do escopo (ex.: arquivos do UI Core tocados em `8bcc121`/`8d9fc09`) devem ser **auditadas e removidas ou reescritas** para ficar dentro do módulo antes da compilação final.
- Critério de aceite da compilação final: diff do cliente/UI Core contra o oficial contendo **apenas** arquivos do Sobrenatural + integração mínima listada.

## 18/11/2026 — Premissa (dev): não existe save game
- Personagens são carregados do banco de dados e persistidos nele; não há save/load de jogo no servidor oficial. Cenários de 'carregar save' só valem para carregamentos internos do cliente SkyMP (conectar, reconectar, respawn, troca de personagem).

## 18/11/2026 — Modelo de confiança P1 dos poderes: Opção A (orquestrador)
- **Antes (P1 do porte, 17/11):** um poder gerenciado só era executado com o comando `activate` (nonce/geração/sequência) **e** a proposta nativa `SpellCast` (`mp.onSkySocialSpellCast`) do mesmo feitiço em até 750 ms.
- **Problema medido em jogo (L1/V6):** o Z não emite `spellCast`; a proposta nativa nunca chega e Beast Form/Lorde nunca transformam. Ver `investigacoes/INVESTIGACAO_18-11_transformacoes.md`.
- **Agora:** um `activate` vindo de `actionVoiceFire` (`via: 'voice'`) é executado **sem** proposta nativa quando o estado do servidor tem `equipment.voiceSpell === spell`. `via` é só uma dica; o servidor reconfere o slot. Mãos/instant continuam exigindo a proposta nativa.
- **Continua autoritativo no servidor:** maldição certa, poder em `knownSpells` (concessão por estágio), `minXp` dos poderes de vampiro, slot de voz, vivo/SkyDeath, travas SkySocial/profissão, sem forma ativa (fera, Lorde, névoa), canal do personagem/ator, nonce de uso único + sequência (anti-replay), cadência de 1 s e a recarga/sangue de cada domínio. O cliente não decide resultado.
- Ritual do Véu: exclusivo da Bruxa; a checagem de maldição (lobisomem/vampiro) já o exclui.
- Implementação: `power-input.js` (`voiceOnly`, `complete`, `request`). Revisão cruzada: Gemini Neon.

## 18/11/2026 — Sem teste com 2 jogadores (dev)
- Efeitos visíveis/audíveis a outros jogadores (poderes do vampiro, runa/som do ritual, BRUXA/aura/olhos) são implementados pela melhor solução de sincronização conhecida, cobertos por testes Node que simulam 2+ jogadores, e validados no servidor oficial após a integração, com roteiro documentado em cada item.

## 19/11/2026 — Recarga do Morcego (dev)
- Morcego (Bat Form) passa a ter recarga de **3 s** (configurável 2–60 s), substituindo a decisão anterior "Morcego sem recarga", para impedir encadear dashes. Limite nativo de 256 u por dash continua.

## 19/11/2026 — Som do ritual (dev)
- O som do ritual (Fire Storm Charge, 3,35 s, sem loop) toca no início e na conclusão bem-sucedida, audível aos próximos; não toca em cancelamento.
- Usar o SOUN 000843. Cada disparo tem janela de 3,351 s e pode chegar a quem entra nas proximidades durante a reprodução. A confirmação pertence ao servidor. A runa tem ciclo de vida separado. Implementação local em 65edf8c e roteiro da Etapa 4.

## 19/11/2026 — Mãos, velocidade e Z da fera

- **Mãos:** o servidor retira das mãos armas, escudo, tocha e feitiços ao confirmar a raça de fera e impede que um pacote antigo recoloque esse equipamento. O cliente dono limpa somente as mãos a partir da fase de fera e da raça efetiva. A entrada preserva peças fora das mãos e inventário. Na volta, o jogador volta a poder equipar normalmente.
- **Velocidade (T4):** aplicar diretamente +50% em relação ao nominal do Manbeast. Com base 100 e Feral Speed de +20, a habilidade recebe +60 pontos de SpeedMult, resultando em 180 no cenário nominal. O modificador está no ESP do Sobrenatural, nas seis habilidades de estágio mutuamente exclusivas, condicionado à WerewolfBeastRace. A distância real e o valor efetivo após transformação ficam para o reteste em jogo.
- **Uivos (T6):** Iniciado sem uivo. A partir do Ômega é liberado Uivo de Terror (`MAG_HowlofTerror`), e no Alfa é liberado Uivo de Fúria (`MAG_HowlofRage`), conforme a especificação. O Z obedece à progressão do Sobrenatural, sem consultar a escolha vanilla nem executar a quest de Companions. A ligação ao Z e a validação de cast no host ficam pendentes com T1/T2.
