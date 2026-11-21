# Sistema Sobrenatural — como funciona

Documento técnico e conceitual da **Entrega_Sobrenatural_21_11**. Descreve a base do sistema e as funcionalidades **validadas em jogo** (teste de 19/11/2026). As demais funcionalidades presentes no código ainda não foram validadas e estão documentadas fora desta entrega, no laboratório e no Acervo.

Caminhos de código são relativos à árvore do servidor (`server/gamemode/...`) e do cliente (`client/...`) depois de aplicar os patches de `01-servidor` e `02-cliente`.

---

## 1. Conceito

O Sobrenatural acrescenta ao Aetherius três **maldições** (pragas), independentes de classe e profissão:

| Maldição | Nome no sistema | Corrupção | Cor |
|---|---|---|---|
| Vampirismo | `vampire` | do Sangue | Carmesim |
| Licantropia | `werewolf` | da Lua | Âmbar |
| Bruxaria | `witch` | do Véu | Violeta |

Três princípios guiam todo o desenho:

1. **O servidor é a autoridade.** Quem carrega qual maldição, em que estágio, com quanto sangue ou vínculo, é decidido e guardado só no servidor. O cliente recebe apenas o necessário para desenhar a interface e aplicar efeitos visuais.
2. **A maldição é secreta.** A marca de identidade (keyword) nunca sai do servidor. O cliente recebe um estado de apresentação, nunca a keyword.
3. **Só a staff concede ou remove.** Não existe item, magia, mordida ou ritual dentro do jogo que transmita uma maldição. A transmissão acontece em RP; a maldição passa a existir quando a staff a concede pelo Painel Administrativo (F7).

---

## 2. Núcleo: `supernatural-service.js`

### 2.1 Registro e identidade
- Registro em disco: `data/aetherius-supernatural.json`. Gravação atômica (arquivo `.tmp` + `rename`). Se o arquivo estiver ilegível, o serviço **recusa iniciar** para não sobrescrever dados.
- A identidade é o **personagem** (`characterId`, obtido de `commands.getActiveCharacterData(actorId)`), não o ator. Trocar de personagem na mesma conta nunca mistura progressos.
- Por personagem o registro guarda: `keyword`, `curse`, `grantId`, `since` (data da concessão), `xpTotal`, `disciplines` (vampiro), `vertente` (bruxa) e, quando há, a concessão do Lorde.
- **`grantId`** é um UUID novo a cada concessão. Todos os subsistemas (Reserva de Sangue, Vínculo, compulsão) guardam o próprio estado vinculado a ele: uma maldição removida e concedida de novo nunca herda o estado anterior.
- **`revision`** é um contador por personagem que só cresce. O cliente envia a revisão que viu; se ela estiver velha, a operação é recusada com `STALE_REVISION`.
- Operações sobre o mesmo personagem são **serializadas em fila**: duas ações simultâneas (por exemplo, dois membros da staff) nunca se intercalam.

### 2.2 Keywords
| Maldição | Keyword (só servidor) |
|---|---|
| Vampiro | `JogadorVampiro` |
| Lobisomem | `JogadorLobisomem` |
| Bruxa | `JogadorBruxa` |
| Concessão de Lorde (vampiro) | `JogadorLordeVampiro` |

### 2.3 Conceder (`grant`)
1. Valida personagem ativo, maldição válida e que o personagem **não** tenha maldição (`ALREADY_CURSED`). Um personagem carrega no máximo uma.
2. Cria o registro com `grantId` novo, `since` = data de hoje, XP 0.
3. Pede o **reset de classe para o nível 1** pela ponte `globalThis.aetheriusClassReset`. Se a ponte falhar, a concessão segue e o resultado informa `classReset: false` para a staff.
4. Revalida a sessão do alvo e do operador depois dos passos assíncronos; se o alvo trocou de personagem no meio, grava só o registro e adia os efeitos para o próximo login.
5. Grava, aplica os efeitos (raça, magias, perks) e publica o estado ao cliente.

### 2.4 Remover (`remove`)
Apaga **todo** o progresso: XP, estágio, disciplinas ou vertente, data e os estados dos subsistemas ligados ao `grantId`. Os efeitos da maldição são retirados e, no vampiro, a raça original é restaurada (seção 4.3).

### 2.5 Estágios e XP
Seis estágios por maldição. O estágio é derivado do `xpTotal`:

| Estágio | XP acumulada | Vampiro | Lobisomem | Bruxa |
|---|---|---|---|---|
| 1 | 0 | Neófito | Iniciado | Neófito |
| 2 | 20.000 | Vampiro Pleno | Ômega | Acólito |
| 3 | 60.000 | Ancilla | Beta | Feiticeiro Negro |
| 4 | 190.000 | Ancião | Gama | Magus Diaboli |
| 5 | 610.000 | Suserano | Delta | Alto Teurgo |
| 6 | 2.000.000 | Apoteótico | Alfa | Hierofante |

A staff ajusta XP pelo Painel (`addXp`, limitado entre 0 e 2.000.000). Ao mudar de estágio, o servidor reconcilia magias e perks do novo estágio.

### 2.6 Escolhas permanentes
- **Vampiro:** uma Disciplina ao atingir o estágio 2 e uma segunda, diferente, no estágio 6.
- **Bruxa:** uma Vertente (MORTUI, DAEMONES ou TENEBRAE) no estágio 1.

O jogador escolhe na tela Sobrenatural (`request(..., 'choose')`). O servidor confere `grantId`, `revision` e se há escolha disponível. A escolha não pode ser trocada pelo jogador.

### 2.7 Estado de apresentação
O cliente recebe apenas: `curse`, estágio, `xpTotal`, escolhas, `revision`, `grantId`, se pode escolher e, no vampiro, se tem a concessão de Lorde. Nunca a keyword.

---

## 3. Interface: tela Sobrenatural (TAB)

- Módulo `supernatural` do Aetherius UI Core, rota `/supernatural`, slot 8 do radial (`client/ui-core/modules/supernatural/`).
- Quatro telas: **sem maldição** (*"Você não é portador de nenhuma praga."*), **Vampiro**, **Lobisomem** e **Bruxa**.
- Cada tela mostra: sobretítulo da corrupção, nome da maldição, *"Portador desde"* (data `since`), estágio atual, XP no estágio e barra, e as escolhas permanentes.
- Toda ação da tela passa por `request(context, action, payload)` no servidor: `snapshot`, `choose` e as ações específicas de cada maldição (como `ritual.*`).
- Direção de arte: branco sobre fundo preto translúcido, tipografia serifada, losangos e a cor da maldição só nos detalhes.

---

## 4. Vampirismo

### 4.1 Reserva de Sangue (`supernatural/blood-reserve.js`) — validada em jogo
**Conceito:** a sede é o centro da praga. O vampiro tem uma reserva de 0 a 100 que se esvazia enquanto ele joga e que ele precisa repor.

**Funcionamento:**
- Inicia em **100** a cada concessão (`grantId` novo).
- Esvazia por **tempo online real**, de forma escalonada, mais rápido quando cheia:

| Faixa | Taxa | Tempo na faixa |
|---|---|---|
| 100 → 80 | 20 por hora | 1 h |
| 80 → 60 | 10 por hora | 2 h |
| 60 → 30 | 6,67 por hora | 4 h 30 |
| 30 → 0 | 5 por hora | 6 h |

  De cheia a vazia: **13 h 30 online**. Tempo offline não conta, e o servidor não compensa o tempo entre reinícios.
- O servidor liquida o saldo a cada **10 s** e grava em lote em `data/aetherius-blood-reserve.json`, por `characterId` e `grantId`, com `fsync` e `rename` atômico.
- Gastos usam **reserva e confirmação** (`reserve` → `commit` ou `cancel`): um poder que falha depois de reservar não cobra sangue.
- A morte não devolve nem tira sangue.
- **Poção de Sangue** (`DLC1BloodPotion`): o gancho `onEatItem` reconhece a poção em vampiros e credita sangue, limitado a 100, uma vez por unidade consumida.

**Estados** (`status`): `NORMAL` (acima de 30), `CRITICAL` (de 1 a 30) e `FERAL` (0).

### 4.2 HUD da Reserva de Sangue — validada em jogo
- O servidor (`supernatural/blood-presentation.js`) publica a propriedade privada **`aetheriusBloodPresentation`** = `{ v: 1, blood, state }`, só para o dono, só quando o valor muda, verificando a cada 1 s.
- No cliente, o valor vai para `ctx.sp.storage.aetheriusBloodPresentationV1` e para a HUD nativa do UI Core (`client/ui-core/modules/supernatural/blood-reserve-hud.js`, estilos em `supernatural-hud.css`).
- A HUD fica no canto inferior direito, com o preenchimento proporcional ao sangue, sem número nem tooltip. Recebe o primeiro estado imediatamente e atualizações repetidas não reiniciam a animação.
- Nunca aparece ao mesmo tempo que o selo da Bruxa: as maldições são exclusivas.

### 4.3 Raça vampírica — validada em jogo
**Conceito:** o vampiro pertence à variante vampírica da própria raça, e o mundo o vê assim.

**Funcionamento:**
- Tabela `RACE_TO_VAMPIRE` (`supernatural/vampire-grants.js`) liga cada raça jogável à sua variante vampírica de `Skyrim.esm` (por exemplo, `BretonRace` → `BretonRaceVampire`).
- Ao conceder Vampirismo, o servidor guarda a raça original em `restoreRace[characterId]` e troca a raça na aparência sincronizada (`appearance.raceId`). Todos os jogadores veem a variante vampírica.
- Ao remover a maldição, a raça original é restaurada pela mesma via.
- Uma raça sem variante vampírica é registrada em log e mantida.

### 4.4 Visão Vampírica (`supernatural/powers.js`) — validada em jogo
**Conceito:** o vampiro enxerga a vida ao redor.

**Funcionamento:**
- Poder `SN_VisaoVampirica` (`AETHERIUS - SOBRENATURAL.esp`), disponível desde o estágio 1.
- O uso passa pelo despachante de poderes do servidor, que confere maldição, estágio, recarga e estado de vida antes de aceitar.
- O cliente mantém o efeito de imagem do próprio feitiço e o servidor publica a propriedade privada **`aetheriusSupernaturalVision`** com os alvos dentro de um raio de **25 m** (`25 × 70` unidades).
- O cliente do dono aplica um shader de detecção sobre esses alvos (efeito *Aura Whisper*), renovado a intervalos curtos enquanto o poder dura.
- Recarga: **30 s**.
- O efeito é privado: só o próprio vampiro vê os alvos destacados.

---

## 5. Licantropia

### 5.1 Base
A forma de fera pertence ao sistema do lobisomem do kdu (`werewolf-service.js` e `werewolf/`, plugin `AETHERIUS - LOBISOMEM.esp`), adaptado. O Sobrenatural concede a maldição, a progressão, as perks do Manbeast por estágio e controla quando a transformação é obrigatória.

### 5.2 Transformação forçada pelo Painel (`supernatural/forced-werewolf.js`, ação `supernatural.forceTransform`) — validada em jogo
**Conceito:** a staff pode fazer a Besta despertar, por exemplo num evento.

**Funcionamento:**
1. A staff escolhe o alvo no F7 e executa **Forçar transformação**.
2. O servidor confere que o alvo é lobisomem, está online, vivo, fora da forma de fera e sem interação bloqueada (troca, profissão). Exige também que seja **noite no calendário do servidor (20h às 5h)**; de dia a ação é recusada com `NIGHT_REQUIRED`.
3. Inicia a **sequência de 20 segundos**, privada do jogador:

| Tempo | Efeito |
|---|---|
| 0 s | mensagem *"A Besta clama pela caçada..."* |
| 5 s | batimento cardíaco |
| 5,25 s | tremor da visão |
| 10 s | filtro vermelho crescente |
| 15 s | mensagem *"A Besta está prestes a rasgar sua carne..."* |
| 20 s | transformação, com a animação do kdu |

4. O estado da sequência vai ao cliente pela propriedade privada `aetheriusBeastCompulsion`, renovada a cada 250 ms; o cliente limpa sons e filtros sozinho se as amostras pararem por 2,5 s.
5. Morte, queda, desconexão ou troca de personagem cancelam a sequência sem efeito residual.
6. A transformação confirmada zera o contador da compulsão do personagem (persistido em `data/aetherius-werewolf-compulsion.json`).

---

## 6. Bruxaria

### 6.1 Vínculo do Véu (`supernatural/veil-bond.js`) — validado em jogo
**Conceito:** o poder da bruxa vem de uma ligação com algo do outro lado do Véu. A ligação se desgasta e precisa ser alimentada.

**Funcionamento:**
- Faixa normal de **0 a 100**; inicia em **100** a cada concessão.
- Esvazia **1 ponto a cada 432 s online**: de 100 a 0 em **12 h online**. Nada é perdido offline.
- Persistência em `data/aetherius-veil-bond.json`, por personagem e `grantId`, com a mesma infraestrutura de sessão e gravação em lote da Reserva de Sangue.
- Penalidades:

| Vínculo | Efeito |
|---|---|
| 100 a 51 | nenhum |
| 50 a 31 | regeneração de Magicka −50% |
| 30 a 1 | regeneração −50%, Magicka máxima −50% e Terror |
| 0 | Magicka máxima −100% e Terror a cada 5 min |

- As penalidades são modificadores identificáveis: subir acima de 30 remove a de Magicka máxima e o Terror; acima de 50, a de regeneração. Relogar ou trocar de célula não as duplica.

### 6.2 Terror (`supernatural/veil-terror.js`)
Manifestações **só para a própria bruxa**, uma de cada vez, sem repetição imediata:

| Manifestação | Chance com Vínculo de 30 a 1 |
|---|---|
| nada | 35% |
| sussurros ou grito | 30% |
| pensamento intrusivo (mensagem na tela) | 18% |
| batimento cardíaco por 10 s | 12% |
| tela preta por 1 a 1,5 s com respiração | 5% |

Com Vínculo de 30 a 1 há uma tentativa a cada 12 a 20 min; em 0, uma manifestação garantida a cada 5 min. A tela preta tem intervalo mínimo de 90 min e nunca ocorre em combate, salto, queda, diálogo, menu ou cena. Estado em `data/aetherius-veil-terror.json`.

### 6.3 Selo do Vínculo (HUD) — validado em jogo
- O servidor publica a propriedade privada **`aetheriusVeilPresentation`** só para a bruxa.
- O cliente desenha o selo (`client/ui-core/modules/supernatural/veil-seal.js`, `veil-seal.css`, arte `witch-gauge-reference.png`) no **centro inferior** da tela: cerca de 90 px em 1920×1080, proporcional em resoluções menores.
- A barra central violeta enche de baixo para cima. Abaixo de 70, 50, 30 e em 0 o selo perde brilho, racha e pulsa suavemente. Sem número nem tooltip. `prefers-reduced-motion` desliga o movimento.

### 6.4 Ritual (`supernatural/veil-ritual.js`, tela `ritual-screen.js`) — HUD e timer validados em jogo
**Conceito:** o Vínculo se renova pelo sacrifício.

**Funcionamento:**
- Na tela da Bruxa, o botão **Ritual** abre uma tela com a entidade do outro lado do Véu e os 7 ingredientes, acesos quando a bruxa os tem no inventário.
- **Categorias:** Coração Daedra; Pedra da Alma Negra (cheia ou vazia); carne crua de animal; pele de animal; tomo de feitiço; Beladona (Nightshade); Nirnroot.
- A bruxa escolhe **3 categorias diferentes**. O servidor confere o inventário e **reserva** os itens sem consumi-los.
- O Ritual dura **300 s**, com a animação de conjuração e a runa no chão sincronizada a quem está perto. Durante ele, a HUD mostra o tempo restante e o botão Cancelar.
- **Interrupção** (cancelar, mover, combate, desconexão, morte, perda da maldição): sem recompensa e sem consumo.
- **Conclusão:** nova validação, consumo de 1 item de cada categoria e Vínculo **+50**, limitado a 100.
- Os sussurros do Ritual tocam só para a bruxa.

---

## 7. Painel Administrativo (F7)

Ações registradas em `admin-panel/supernatural-admin.cjs`. Todas passam pelo `service.cjs` do painel: permissão (`characters.class`), hierarquia de staff, sessão do alvo, revalidação depois de cada passo assíncrono e auditoria.

| Ação | O que faz |
|---|---|
| `supernatural.grant` | concede uma maldição |
| `supernatural.remove` | remove a maldição e todo o progresso |
| `supernatural.xp` | ajusta a XP da maldição |
| `supernatural.discipline` | define a Disciplina do vampiro |
| `supernatural.vertente` | define a Vertente da bruxa |
| `supernatural.forceTransform` | força a transformação do lobisomem (seção 5.2) |
| `supernatural.endForm` | encerra uma forma ativa |
| `supernatural.lordGrant` / `lordRevoke` | concede ou revoga a Forma de Lorde |
| `supernatural.status` | consulta o estado do alvo |

O navegador do F7 nunca envia keyword nem código: só o nome da ação e parâmetros validados no servidor. O frontend do F7 (`client/admin-panel/app.js`) e o contrato das ações estão em `06-painel-adm`.

---

## 8. Persistência

| Arquivo (`data/`) | Conteúdo |
|---|---|
| `aetherius-supernatural.json` | maldição, keyword, `grantId`, XP, escolhas, raça original e concessão de Lorde por personagem |
| `aetherius-blood-reserve.json` | Reserva de Sangue por personagem e `grantId` |
| `aetherius-veil-bond.json` | Vínculo do Véu por personagem e `grantId` |
| `aetherius-veil-terror.json` | agenda e intervalos do Terror |
| `aetherius-werewolf-compulsion.json` | contador da compulsão por personagem e `grantId` |
| `aetherius-werewolf.json` (+ `.transforms.json`) | estado da forma de fera (sistema do kdu) |

Na atualização do servidor, preserve esses arquivos: apagá-los zera as maldições.

---

## 9. Validação em jogo

Itens validados no teste solo de 19/11/2026 e como repeti-los:

| Funcionalidade | Como testar |
|---|---|
| Concessão pelo F7 e tela Sobrenatural | conceder cada maldição; conferir a tela, a data e o reset de classe; remover e conferir a tela "sem maldição" |
| Raça vampírica | conceder Vampirismo e conferir a raça vampírica; remover e conferir a raça original |
| HUD da Reserva de Sangue | conceder Vampirismo e conferir a HUD cheia no canto inferior direito; ajustar o valor e conferir o preenchimento |
| Visão Vampírica | usar o poder perto de NPCs e jogadores; conferir o destaque e a recarga de 30 s |
| Selo da Bruxa | conceder Bruxaria e conferir o selo no centro inferior |
| Ritual | com 3 categorias no inventário, iniciar o Ritual; conferir runa, timer e +50; cancelar e conferir que nada foi consumido |
| Transformação forçada | à noite no servidor, usar `supernatural.forceTransform` num lobisomem; conferir a sequência de 20 s e a transformação |

**Problemas conhecidos, observados em jogo e não resolvidos:** o Z (comando de voz) não transforma; o avanço do Morcego não funciona; a XP de maldição ainda não é ganha jogando (só pelo Painel).
