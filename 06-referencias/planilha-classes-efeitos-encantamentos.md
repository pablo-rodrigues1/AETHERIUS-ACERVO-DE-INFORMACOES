---
titulo: Planilha "AETHERIUS - Classes, efeitos, encantamentos"
tipo: referencia
status: ativo
responsaveis: [mercurius17]
atualizado: 2026-11-14
tags: [classes, leveling, racas, pedras-guardias, maldicoes, alquimia, encantamento, design]
fonte: https://docs.google.com/spreadsheets/d/1O69M5KsGNmpX_s7zzav_5iMxZyacYLFRs9a4SuSVgxg
relacionados: [../03-decisoes/ADR-0003-codigo-prevalece-sobre-planilhas-de-design.md, ../02-sistemas/gameplay-core/README.md, ../07-guia-da-staff/classes/como-funcionam-as-classes.md]
---

# Planilha "AETHERIUS - Classes, efeitos, encantamentos"

## Resumo
Resumo da planilha de design de classes, leveling, raças, pedras guardiãs, poções, encantamentos e maldições, conferida aba por aba contra o código em 14/11/2026. Onde o código já define algo, **vale o código** ([ADR-0003](../03-decisoes/ADR-0003-codigo-prevalece-sobre-planilhas-de-design.md)); a planilha só é fonte para o que ainda não existe no código.

## Pontos principais
- Skills conferidas com o código; perks diferem em poucos pontos (tabela abaixo). **Retificação dos feitiços:** Classes contém 164 registros, e Classes - Progressão foi sincronizada com ela. O código mantém 128 entradas em authorizedSpells (127 associações únicas); allSpellsList contém os mesmos 164 nomes da origem, mas seis tiers diferem. Ver [catálogo e comparação completa](../02-sistemas/gameplay-core/feiticos-por-classe.md).
- Leveling: curva, XP base e grupo iguais; a tabela de diferença de nível ("Delta") e o grupo aplicado a chefes **não** valem.
- Raças e pedras: valores da planilha batem com os records conferidos.
- Poções, encantamentos e maldições ainda não têm código: a planilha é o design atual.

### Fonte
- Google Sheets: https://docs.google.com/spreadsheets/d/1O69M5KsGNmpX_s7zzav_5iMxZyacYLFRs9a4SuSVgxg
- Lida em 14/11/2026 (8 abas). Código comparado: `pablo-rodrigues1/AetheriusGameplayCore` `521a389` e a cópia `server/gameplay/core` do `aetherius-server` de 13/11 (catálogo v0.37.0).

## Aplicação no projeto

### Situação por aba

| Aba | Existe no código? | O que vale | Onde está documentado |
|---|---|---|---|
| Classes - Progressão (perks por marco) | Sim | Código | [Fichas das classes](../07-guia-da-staff/classes/como-funcionam-as-classes.md) |
| Classes - Progressão (skills por marco) | Sim, idêntico | Código | Fichas das classes |
| Classes - Progressão (feitiços autorizados) | Sim, idêntico | Código | Fichas das classes |
| Classes - Progressão (regras de funcionamento) | Parcial | Código onde existe | [Como funcionam as classes](../07-guia-da-staff/classes/como-funcionam-as-classes.md) |
| Classes (descrições de perks e feitiços) | Sim, em português | Código (`perks-descriptions.json`, `spells-descriptions.json`) | Fichas das classes |
| Sistema de Leveling | Sim | Código | [Nível, XP e pontos de atributo](../07-guia-da-staff/progressao/nivel-xp-e-atributos.md) |
| Raça & Pedra Guardiã | Sim (efeitos lidos dos records do jogo) | Records da load order | [Raças e pedras guardiãs](../07-guia-da-staff/racas-e-pedras/racas-e-pedras-guardias.md) |
| Poções | Não (Alquimista bloqueado por decisão) | Planilha, como design | [Poções e encantamentos](../07-guia-da-staff/itens/pocoes-e-encantamentos.md) |
| Encantamentos | Não (Encantador bloqueado por decisão) | Planilha, como design | [Poções e encantamentos](../07-guia-da-staff/itens/pocoes-e-encantamentos.md) |
| Maldições - Informações / Progressão | Não (só a transformação do lobisomem, em teste à parte) | Planilha, como design | [Maldições](../07-guia-da-staff/sobrenatural/maldicoes.md) |

### Divergências entre planilha e código

#### Classes
| Tema | Planilha | Código (vale) |
|---|---|---|
| Troca de classe | Livre até o nível 15; depois, ticket com a Staff | O jogador só escolhe a classe **uma vez** (se não tem nenhuma). Qualquer troca é feita pela Staff no painel F7 (`class.set`), em qualquer nível |
| Liberação das classes de magia | Keyword de facção `AlunoColegioWinterhold` dada pela Staff | Não há keyword: as 6 classes (`requiresWinterholdStudent`) são recusadas na tela do jogador e só a Staff define pelo painel |
| Estágios | "8 estágios" | 9 marcos: 1, 5, 10, 15, 20, 25, 30, 35, 40 (a planilha usa "0" para o primeiro) |
| Tecla da interface | K abre o menu | A aba CLASSE fica no menu radial do UI Core (TAB); a tecla K abre a roda de gestos |
| Arqueiro, nível 15 e 30 | Steady Aim (1) e Steady Aim (2) | Sem Steady Aim |
| Anti-Mago, nível 1 | Sem Conjuration Mastery | Recebe Conjuration Mastery (pré-requisito do Mystic Binding do nível 10), desde 14/11 |
| Anti-Mago, nível 30 | Magic Resistance (3), Void Brand | Só Magic Resistance (3) (Void Brand já vem no 25) |
| Grafias | "Rune Mestre", "Heavy Armor Mestre", "One-Hand Mastery" | Rune Master, Heavy Armor Mastery, One-Handed Mastery (mesmos perks) |

#### Feitiços — conciliação de 14/11/2026

A origem foi a aba **Classes**, inclusive as mesclagens da coluna E; a seção de feitiços em **Classes - Progressão B23:F40** agora reproduz seus nomes e tiers. A lista completa por classe e escola e todas as diferenças para `authorizedSpells`/`allSpellsList` estão no [catálogo documental](../02-sistemas/gameplay-core/feiticos-por-classe.md). As fichas da Staff foram atualizadas para esta referência de design, com indicação explícita de que os JSONs e o runtime não foram modificados. A igualdade de feitiços declarada na conferência anterior estava incorreta para o resumo de autorizações.

#### Leveling
| Tema | Planilha | Código (vale) |
|---|---|---|
| Diferença de nível | Tabela "Delta" (inimigo − jogador): 0% a −20, até +50% a +11 | Sem bônus. Se o personagem está **acima** da faixa recomendada do conteúdo, −5% por nível a mais, no máximo −90% (sempre 10% ou mais) |
| Chefes de XP fixo | 500/1000 × modificador de grupo | 500 (Dragon Priest) e 1000 (Dragão) para cada jogador elegível, **sem** modificador de grupo, relevância nem fadiga |
| Nível dos inimigos | 1 a 40 | 1 a 100; acima de 40, +2% do XP base por nível (fator 7× no nível 100) |
| Inimigos | Lista sem Thalmor | Igual à planilha nos valores base, mais Thalmor (18 XP) |
| Fadiga diária | Não consta | A partir do nível 15, limite de 20% do XP do nível atual por dia; reset às 06:00 (Brasília) |
| Mundo aberto | Não consta | Inimigo fora de masmorra rende 50% |
| Grupo | Elegibilidade não descrita | Online, mesma célula, até 5.000 unidades; não precisa causar dano |

Iguais nos dois: curva de XP dos níveis 1 a 40 (total 1.848.000), XP base dos 50 inimigos da planilha, fórmula até o nível 40 (+20% por nível até 20, +5% de 21 a 40), multiplicadores de grupo (1 a 8) e de raid (8 a 20), 15 pontos de atributo por nível (585 no total).

**Atenção ao reutilizar a planilha:** várias células numéricas da aba "Sistema de Leveling" foram convertidas em datas pelo Google Sheets (ex.: "2026-06-21" no lugar de 21,6). Os valores corretos estão no código (`modules/leveling-system/config/`).

#### Raças e pedras guardiãs
Os efeitos vêm dos records vencedores da load order (catálogos `races.json` e `standing-stones.json`, mods Aetherius.esp e Mundus). As magnitudes conferidas batem com a planilha (ex.: Orc +50% regeneração de vida, Bretão 25% resistência à magia e 10% de absorção, Pedra do Atronaca +100 de magicka e 20% de absorção, Pedra da Torre +100 de armadura e 100% de reflexo, Pedra do Corcel +100 de carga e +20% de velocidade). Se um mod mudar um valor, vale o record.

### Itens sem código (a planilha é a única fonte)
- **Poções e encantamentos:** valores do Alquimista e do Encantador no nível Mestre. As duas profissões estão "bloqueadas por decisão" em `server/gameplay/professions` (`PROFESSION_MATRIX.md`).
- **Maldições:** vampirismo (mod Curse of the Vampire), licantropia (Manbeast) e bruxaria (Triumvirate, Mysticism, Vokrii). Nenhum código de progressão de maldição no servidor ou no Core em 14/11/2026. A transformação sincronizada do lobisomem (sem progressão nem poderes) está em teste num pacote separado: [Lobisomem sincronizado](../02-sistemas/lobisomem/README.md).
- **Inconsistência interna da planilha (maldições):** diz que cada estágio exige o dobro do anterior e dá o exemplo de 46.500 de XP total até o estágio 6, mas também diz que o último ranking exige 2 milhões. Precisa de decisão antes de implementar.
