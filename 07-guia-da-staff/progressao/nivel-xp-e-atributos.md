---
titulo: Nível, XP e pontos de atributo
tipo: guia-staff
status: ativo
responsaveis: [mercurius17]
atualizado: 2026-11-14
tags: [staff, progressao, xp, fadiga, atributos, grupo]
fonte_tecnica: [../../02-sistemas/gameplay-core/README.md]
relacionados: [../classes/como-funcionam-as-classes.md, ../dungeons/inimigos-e-masmorras.md, ../progresso.md]
---

# Nível, XP e pontos de atributo

## Em uma frase
O personagem ganha XP derrotando inimigos, sobe de nível de classe (do 1 ao 40) e a cada nível recebe pontos para aumentar vida, magia ou vigor.

## Como funciona
1. **XP vem de inimigos.** Cada tipo de inimigo vale uma quantidade base de XP. Inimigos de nível mais alto valem mais.
2. **Conteúdo fácil demais rende menos.** Se o personagem está acima da faixa de nível recomendada daquele lugar, o XP cai 5% por nível a mais, até no máximo 90% de corte (sempre sobra pelo menos 10%).
3. **Em grupo, todos ganham.** Quem está no grupo, online, na mesma área e perto (até 5.000 unidades do jogo) recebe XP, **mesmo sem ter batido no inimigo**. Quem deu o golpe final não ganha nada a mais. Quanto maior o grupo, menor o XP de cada um (tabela abaixo).
4. **Chefes especiais** (como Dragon Priest e dragões) dão um valor fixo de XP para cada jogador que se qualifica, sem cortes de nível, grupo ou fadiga.
5. **Inimigo fora de masmorra** (no mundo aberto) rende **metade** do XP do mesmo inimigo dentro de uma masmorra.
6. **Fadiga diária:** a partir do nível 15, existe um limite de XP por dia. O limite é 20% do XP necessário para passar do nível atual. O dia recomeça às **06:00 (horário de Brasília)**.
7. **Pontos de atributo:** cada nível depois do primeiro dá **15 pontos**. O jogador distribui em vida, magia ou vigor, sempre em blocos de 5. Cada ponto aumenta em 1 o máximo daquele atributo. No nível 40 são 585 pontos no total.

> **Situação atual:** essas regras estão prontas no código. A ligação do XP com os abates e o teste no servidor oficial ainda estão em andamento; confira o [painel de progresso](../progresso.md).

## Exemplo prático
> Um grupo de 4 jogadores derrota um inimigo numa masmorra. Os 4 estão online, na mesma masmorra e perto uns dos outros. Cada um recebe 80% do XP que receberia sozinho, inclusive o curandeiro que só curou e não bateu.

> Um jogador nível 15 precisa de 10.800 XP para chegar ao 16. O limite diário dele é 2.160 XP. Depois de atingir esse valor, ele para de ganhar XP até as 06:00 do dia seguinte.

## Tabela rápida

**XP por faixa de nível** (quanto falta para o próximo nível)

| Faixa | Ritmo | XP do primeiro ao último nível da faixa |
|---|---|---|
| 1 → 5 | Rápido | 200 a 700 |
| 5 → 10 | Médio | 1.000 a 3.200 |
| 10 → 20 | Lento | 4.100 a 19.800 |
| 20 → 30 | Muito lento | 23.000 a 69.500 |
| 30 → 40 | Extremamente lento | 77.000 a 198.500 |

XP total do nível 1 ao 40: **1.848.000**.

**XP em grupo** (porcentagem que cada membro recebe)

| Tamanho | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
|---|---|---|---|---|---|---|---|---|
| Grupo | 100% | 90% | 85% | 80% | 75% | 70% | 65% | 60% |

Raid (8 a 20 pessoas): de 60% com 8 até 40% com 20, caindo cerca de 1,7 ponto por pessoa a mais.

**Outros valores**

| Regra | Valor |
|---|---|
| Nível máximo | 40 |
| Pontos por nível | 15 (blocos de 5) |
| Fadiga começa no nível | 15 |
| Limite diário | 20% do XP do nível atual |
| Hora do reset diário | 06:00, horário de Brasília |
| Distância máxima para XP em grupo | 5.000 unidades do jogo |
| XP de inimigo no mundo aberto | 50% do valor em masmorra |

## Perguntas comuns de jogadores
- **Preciso bater no inimigo para ganhar XP no grupo?** Não. Basta estar no grupo, online, na mesma área e perto.
- **Parei de ganhar XP. Bugou?** Se o personagem é nível 15 ou mais, provavelmente bateu o limite diário. Volta às 06:00.
- **Matar inimigo muito mais fraco dá zero XP?** Não. O corte máximo é 90%: sempre sobra pelo menos 10%. Também não existe bônus por enfrentar inimigo mais forte que o personagem (a planilha antiga previa; o servidor não faz).
- **Chefe dá menos XP em grupo?** Não. Dragon Priest (500) e dragão (1.000) dão o valor cheio para cada jogador elegível.
- **Por que ganho tão pouco XP aqui?** O lugar está abaixo do nível do personagem; o XP é cortado para incentivar conteúdo do nível dele.
- **Errei a distribuição de pontos. Dá para refazer?** Os pontos só voltam numa troca de classe ou quando a Staff reduz o nível abaixo do que foi gasto.

## O que a Staff pode e não pode fazer
- **Pode:** definir o nível de classe pelo painel (F7), de 1 a 40. Ver [Como funcionam as classes](../classes/como-funcionam-as-classes.md).
- **Não pode:** dar XP avulso, aumentar o limite diário de um jogador ou dar pontos de atributo fora do nível. O servidor calcula tudo e não aceita valores mandados pelo jogo do jogador.

## Quer o detalhe técnico?
Veja [GameplayCore](../../02-sistemas/gameplay-core/README.md). Os valores ficam em `modules/leveling-system/config/` no repositório do GameplayCore.
