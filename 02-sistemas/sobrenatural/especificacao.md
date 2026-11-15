# Especificação — Sistema Sobrenatural (Maldições e Pragas)

Fonte: planilha [AETHERIUS - Classes, efeitos, encantamentos](https://docs.google.com/spreadsheets/d/1O69M5KsGNmpX_s7zzav_5iMxZyacYLFRs9a4SuSVgxg/edit?gid=1790029713), abas **Maldições - Informações** e **Maldições - Progressão**, lidas em 2026-11-15. Pelo ADR-0003 do acervo, o código prevalece sobre a planilha; divergências viram pergunta/ADR.

## 1. Regras gerais

1. As três maldições são **externas** a Classe e Profissão (qualquer classe pode ter qualquer maldição).
2. Obtidas **somente via roleplay + setagem no Painel Administrativo**. Não há seleção pelo jogador. O jogador deve manter RP compatível.
3. Ao ser setado, o progresso de **CLASSE é resetado ao nível 1**.
4. Portadores estão sujeitos a **permadeath** (decisão de staff via RP, não é mecânica).
5. Progressão de maldição:
   - Estágio inicial só via painel.
   - O jogador ganha **XP de maldição** pelas mesmas ações que dão XP de classe.
   - 6 estágios; cada transição custa o **dobro** da anterior.
   - **Decisão do dev (2026-11-15, revisada):** 1→2 = 20.000 XP; 2→3 = 40.000 XP (total 60.000); total final **2.000.000 XP**. As demais transições foram calculadas pelo orquestrador: restam 1.940.000 XP para 3 transições, distribuídos em progressão geométrica de razão ≈3,26 (40.000·(r+r²+r³) = 1.940.000), arredondados para dezenas de milhar.

| Estágio | Custo da transição | XP acumulada |
|---|---|---|
| 1 | — | 0 |
| 2 | 20.000 | 20.000 |
| 3 | 40.000 | 60.000 |
| 4 | 130.000 | 190.000 |
| 5 | 420.000 | 610.000 |
| 6 | 1.390.000 | 2.000.000 |

   A mesma curva vale para as três maldições. **Substitui** a curva de dobro (64.516…) usada na imagem de referência, cujos limiares devem ser ignorados.

## 2. Regras definidas pelo dev (fora da planilha)

- Keyword permanente, **somente servidor**: `JogadorVampiro`, `JogadorLobisomem`, `JogadorBruxa`. Nunca visível ao cliente/jogador.
- UI: Aetherius UI Core → TAB → módulo **Sobrenatural** (`id: supernatural`, rota `/supernatural`, slot radial 8 — já presente em `client/vendor/aetherius-ui-core/shared/catalog.ts:19` e `client/ui-core/frontend/shell.js:13`).
- 4 telas: (1) sem maldição — elemento gráfico estilizado + "Você não é portador de nenhuma praga."; (2) Vampiro; (3) Lobisomem; (4) Bruxa.
- Direção de arte, cores (Carmesim / Âmbar / Violeta) e análise da referência de UI: ver `DIRECAO-DE-ARTE.md`.
- O servidor precisa guardar a **data da setagem** ("Portador desde DD/MM/AAAA").
- Sobretítulos: **Corrupção do Sangue** (vampiro), **Corrupção da Lua** (lobisomem), **Corrupção do Véu** (bruxa). Nomes de feitiços, perks e poderes exibidos **em português**.
- Trilha/cabeçalho exibe apenas **"Sobrenatural"** (nada de nome de personagem ou classe).
- **Setagem pelo painel:** o servidor atribui a keyword, grava a data e **reseta a classe para o nível 1** automaticamente.
- **Remoção pelo painel:** o servidor remove a keyword e **apaga todo o progresso da maldição** (XP, estágio, Disciplinas/Vertente, data).

## 3. Vampirismo

Mod: **Curse of the Vampire**. MGEFs originais das poções que concediam poderes:
`COTV_PotentVampirePotionUnnaturalCelerity` [MGEF:50436C3F], `…ImmeasurableStrength` [50436C3C], `…MistForm` [50436C42], `…DevouringSwarm` [50436C43].

| | Neófito | Vampiro Pleno | Ancilla | Ancião | Suserano | Apoteótico |
|---|---|---|---|---|---|---|
| Vida/Magicka/Vigor extra | 50 | 75 | 100 | 125 | 150 | 200 |
| Dano desarmado extra | — | 5 | 10 | 15 | 20 | — |
| Poder | — | Immeasurable Strength | Vampire Mist Form | Unnatural Celerity | Devouring Swarm | 2ª Disciplina |

**Disciplinas** — escolhe UMA ao atingir Vampiro Pleno; a segunda no Apoteótico. Evoluem com a progressão (5 níveis). **Nível da disciplina = estágio − 1** (Vampiro Pleno = nível 1 … Apoteótico = nível 5), confirmado pela referência `VAMPIRISMO - DISCIPLINAS.png` (no Ancião: Celeridade +30%, Fortitude +100, Potência +15%). A 2ª disciplina, escolhida no Apoteótico, segue a mesma regra (já entra no nível 5). A escolha é registrada pelo servidor e **não pode ser trocada** pelo jogador. A 2ª Disciplina, escolhida no Apoteótico, soma-se à primeira e precisa ser diferente dela.
- Celeridade: +10/20/30/40/50% velocidade de movimento
- Fortitude: +25/50/100/150/200 vida máxima
- Potência: +5/10/15/20/25% dano físico (inclui armas)
- Feitiçaria do Sangue: +25/50/100/150/200 magicka máxima
- Tenacidade: +25/50/100/150/200 vigor máximo

Inatos (vêm do mod, não precisam ser concedidos):
- Resistências: 100% doença, 100% veneno, 25% gelo, 25% dano físico, 25% dano de queda, Supernatural Awareness, regen de vigor 20–100%, respiração aquática.
- Debuffs: 5 dano/s sob sol direto; santuários aédricos cegam e não abençoam; regen de vida 100% mais lenta; comida e cura mágica sem efeito; 25% fraqueza a fogo.
- Poderes: Vampiric Vision, Vampiric Seduction, Vampire Bat Form, Vampiric Drain (15 vida/s), Vampiric Reanimation (60 s).

## 4. Licantropia (Lobisomens)

Mod: **Manbeast**. Atributos/resistências via SPELLs `MAG_WWLevelAbility15/20/25/30/35/40`.

| | Iniciado | Ômega | Beta | Gama | Delta | Alfa |
|---|---|---|---|---|---|---|
| Perks | Animal Vitality 1; Beastly Might; Bloody Claws 1; Feral Speed | Manbeast 1; Animal Strength 1; Primal Instinct (Howl of Terror); Call of the Blood (−25%) | Animal Vitality 2; Animal Vigor 1 (+10 desarmado); Hircine's Bounty | Manbeast 2; Animal Strength 2; Thick Hide | Animal Vigor 2; Savage Strength; Bloody Claws 2 | Hircine's Rage; Call of the Blood (−50%); Primal Instinct (Howl of Rage) |
| Dano desarmado extra (humano e besta) | 5 | 10 | 15 | 20 | 25 | 30 |
| Vida/Vigor extra (humano e besta) | 125 | 150 | 175 | 200 | 225 | 250 |
| Vida/Vigor extra (só besta) | 150 | 200 | 250 | 300 | 350 | 400 |
| Armadura / Resist. mágica (besta) | 150 / 10% | 200 / 15% | 250 / 20% | 300 / 25% | 350 / 30% | 400 / 35% |
| **Total de vida/vigor na forma de fera** (humano + besta, somados) | 275 | 350 | 425 | 500 | 575 | 650 |

> **Regra (dev, 2026-11-15):** os bônus "humano e besta" e "só besta" se **somam** na forma de fera. Ex.: Ômega na forma humana = +150 de vida e vigor; ao assumir a forma de fera recebe mais +200, total +350.

> Distribuição das perks por coluna transcrita pela ordem da planilha; confirmar visualmente (células mescladas).

Habilidades (via perks): Revert Form, Howl of the Hunt, Howl of Terror, Howl of Rage, Animal Senses, Lycanthrope (+100% resist. doença).
Debuffs inatos: −50% resist. veneno; transformação forçada após 7 dias sem transformar.

Integração: o Sobrenatural é construído **a partir do sistema de kdu**, em `Sobrenatural/AETHERIUS - LOBISOMEM 14-11 - KDU/` (patches de servidor/cliente/perfis sobre a v0.38, plugin `AETHERIUS - LOBISOMEM.esp` e o mod de animação). Ele é dono da forma da fera (`gamemode/werewolf-service.js`, registro `data/aetherius-werewolf.json`). O Sobrenatural acrescenta setagem, keyword, progressão, perks e UI. Tudo será compilado depois em uma pasta final.

## 5. Bruxaria

(Revisado em 2026-11-15 com as coordenadas das células da aba "Maldições - Progressão", linhas 26–44. Substitui a versão anterior, que deduzia o estágio pela ordem das colunas.)

Mods: **Triumvirate, Mysticism, Vokrii e Shadow Spell Package** (novo). Escolhe **uma vertente** no **estágio 1 (Neófito)**: MORTUI, DAEMONES ou TENEBRAE; não pode trocar. Perks e alguns feitiços são globais e independem da vertente.

| | Neófito (B) | Acólito (C) | Feiticeiro Negro (D) | Magus Diaboli (E) | Alto Teurgo (F) | Hierofante (G) |
|---|---|---|---|---|---|---|
| Alteração/Conjuração/Destruição/Ilusão | 30 | 40 | 50 | 60 | 60 | 60 |
| Perks globais | Alteration/Conjuration/Destruction/Illusion Mastery | Necromancy, Rift Summoner 1–2, Atromancy; Raw Power 1 | Ghoul Frenzy, Oblivion Stone 1–2, Conjuration Dual Casting | Necromaster, Blood Zombie; Raw Power 2 | — | Twin Souls; Raw Power 3 |
| Feitiços globais | Raio Sobrenatural (Eldritch Blast) | Fogo Funesto (Balefire) | Raio da Bruxa (Witch Bolt) | Equilíbrio* | — | — |

### Vertentes — feitiços liberados por estágio (acumulam)

| Vertente | Neófito | Acólito | Feiticeiro Negro | Magus Diaboli | Alto Teurgo | Hierofante |
|---|---|---|---|---|---|---|
| **MORTUI** (Necromancia) | Conjure Skeleton; Reanimate Corpse | Conjure Skeleton Archer; Conjure Skeleton Warrior | Conjure Boneman*; Revenant | Daedric Cure; Dread Zombie | Conjure Mistman*; Conjure Skeleton Champion; Oblivion's Embrace | Conjure Wrathman*; Dead Thrall; Conjure Keeper* |
| **DAEMONES** (Demonologia) | Conjure Dremora Warrior | Conjure Ravagor; Weaken | Conjure Dremora Warlock; Conjure Dremora Hunter | Daedric Cure; Cloudkill | Conjure Magister; Frailty | Conjure Leviathan; Oblivion's Embrace; Decrepify |
| **TENEBRAE** (Umbramancia, **Shadow Spell Package**) | Shadowbolt | Crushing Darkness; Shadow Stride | Curse of Exhaustion | Curse of Fragility; Shadowform | Conjure Shadow Atronach | Shadow Rend |

**TENEBRAE — perks/efeitos por estágio** (linha 44): Acólito → upgrade do Shadowbolt (nível 50); Feiticeiro Negro → perk **Dark Arts: Shadow Mastery** (+25% no dano das magias de sombra de Destruição); Alto Teurgo → upgrade do Conjure Shadow Atronach (nível 75); Hierofante → upgrade do Shadowbolt (nível 75).

**Regra do Shadow Spell Package** (Maldições - Informações, item 3): no mod, quase todos os feitiços de sombra sobem de nível sozinhos ao atingir 25/50/75 de Destruição (Sear, Drain Soul, Shadowbolt 50/75, Crushing Darkness 75, Conjure Shadow Atronach 50/75) ou por perk (Curse of Exhaustion, Curse of Fragility). A versão antiga do feitiço é removida e a nova é adicionada. No Aetherius, os upgrades da TENEBRAE seguem a **progressão da maldição** (linha 44), não os checkpoints de habilidade do mod. **Decisão do dev (2026-11-15): o upgrade automático do mod não vai funcionar no Aetherius; o upgrade é feito pelo SERVIDOR.** No estágio indicado, o servidor remove a versão anterior do feitiço e entrega a versão melhorada do próprio Shadow Spell Package (RemoveSpell/AddSpell), do mesmo jeito que os demais grants. Os records de cada nível ainda precisam ser localizados com o houseCARL.

Itens marcados com `*` estão assim na planilha; significado a confirmar. Feitiços que a nova TENEBRAE removeu da versão anterior: Draining Touch, Azra's Wrath, Darkness, Possession, Draining Shroud, Draining Mist, Gather Shadows, Step/Pull Through Shadows, Nightfall.

## Decisão do dev (2026-11-15): poder do Lorde Vampiro

Na versão final, o poder **Forma de Lorde Vampiro** (`DLC1VampireChange`) **não** vem com a maldição nem com o estágio: só é atribuído por uma **função específica do Painel Administrativo**. **Por enquanto, para os testes, fica como está** (liberado desde o Neófito, `VAMPIRE_LORD_MIN_STAGE = 0` em `vampire-grants.js`).

O Shadow Spell Package já está no modpack oficial (`ShadowSpellPackage.esp` + `Shadow Spell Package - Tweaks and Bug Fixes.esp`, ativos no perfil oficial).
