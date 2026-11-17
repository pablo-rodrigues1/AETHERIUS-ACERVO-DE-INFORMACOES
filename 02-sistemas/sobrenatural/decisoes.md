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
- **Forma de Morcego:** mantém o avanço/invisibilidade de 1 s, **sem recarga**.
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

### 5.4 Transformação forçada
- Se passar **72 h de tempo real online** sem se transformar (contagem por personagem, zera a cada transformação), o servidor força a transformação, **só à noite no jogo (20h–5h)**; se vencer de dia, espera a próxima noite online.
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
