# Proposta de textos — Lobisomem, Bruxa e tela sem maldição

Status: **PROPOSTA revisada, aguardando aprovação final do dev** (autor: Opus 5.5, 2026-11-15). Sobretítulos definidos pelo dev: **Corrupção do Sangue / Corrupção da Lua / Corrupção do Véu**. Nomes de feitiços, perks e poderes sempre **traduzidos para português**.
Mesma estrutura da tela de Vampiro: sobretítulo, título, lema e cartões de estágio (descrição + frase). As descrições resumem o que a planilha concede em cada estágio.

## Tela sem maldição

- Elemento gráfico: o duplo hexágono vazio em branco/cinza, sem ícone dentro e com as pontas partidas (um selo que nunca foi rompido).
- Sobretítulo: `NENHUMA MARCA`
- Título: `SOBRENATURAL`
- Texto principal: **"Você não é portador de nenhuma praga."**
- Lema (itálico, opcional): *"Seu sangue ainda pertence apenas a você."*

## Vampiro — detalhes em Carmesim (definido na referência)

- Sobretítulo: `CORRUPÇÃO DO SANGUE`
- Título: `VAMPIRISMO`
- Lema: *"A eternidade cobra seu preço em sangue."* (mantido; já pertinente)

## Lobisomem — detalhes em Âmbar

- Sobretítulo: `CORRUPÇÃO DA LUA`
- Título: `LICANTROPIA`
- Lema: *"Sob a lua cheia, a carne esquece que um dia foi humana."*

| # | Estágio | Descrição | Frase |
|---|---|---|---|
| 01 | INICIADO | Vigor animal, garras que sangram e velocidade feral marcam o primeiro despertar. | *A primeira lua cheia nunca é esquecida.* |
| 02 | ÔMEGA | A fera fica mais forte, aprende o Uivo do Terror e os uivos voltam mais depressa. | *Na base da matilha, até o menor lobo aprende a morder.* |
| 03 | BETA | O vigor dobra, as garras ficam mais pesadas e as feridas se fecham quando o fim se aproxima. | *Hircine observa quem sobrevive à caçada.* |
| 04 | GAMA | O couro endurece contra aço e magia, e a força da fera alcança o mortal. | *Sob o pelo, a pele já não lembra o que era ser humana.* |
| 05 | DELTA | Golpes que derrubam presas feridas e garras que deixam rastros de sangue. | *Onde a fera passa, a trilha é vermelha.* |
| 06 | ALFA | A Fúria de Hircine, o Uivo da Fúria e o domínio absoluto da matilha. | *A matilha não segue a lua. Segue você.* |

Seção equivalente às Disciplinas: `DONS DA CAÇADA` / `UIVOS E INSTINTOS`, listando os uivos desbloqueados (Caçada, Terror, Fúria) e o bônus da forma de fera no estágio atual (vida, vigor, armadura, resistência mágica).

## Bruxa — detalhes em Violeta

- Sobretítulo: `CORRUPÇÃO DO VÉU`
- Título: `BRUXARIA`
- Lema: *"Quem rasga o véu jamais retorna inteiro do outro lado."*

| # | Estágio | Descrição | Frase |
|---|---|---|---|
| 01 | NEÓFITO | O Raio Sobrenatural e a maestria das quatro escolas marcam o primeiro pacto. | *O primeiro sussurro sempre parece um presente.* |
| 02 | ACÓLITO | Necromancia, invocações de fenda e o Fogo Funesto se abrem ao aprendiz. | *Quem atende ao chamado já não pode fingir que não ouviu.* |
| 03 | FEITICEIRO NEGRO | Pedras de Oblívio, conjuração dupla e o Raio da Bruxa. | *A escuridão aprende o seu nome e passa a responder.* |
| 04 | MAGUS DIABOLI | Mestre dos mortos e o Equilíbrio entre carne e magia. | *A carne é apenas um preço, e você aprendeu a pagá-lo.* |
| 05 | ALTO TEURGO | Almas gêmeas e poder bruto em sua forma mais pura. | *Os planos se curvam a quem ousa negociar com eles.* |
| 06 | HIEROFANTE | O ápice do pacto: a vertente escolhida revela seus segredos finais. | *No altar mais alto, só resta você e o abismo.* |

Seção equivalente às Disciplinas: `CAMINHOS PROIBIDOS` / `VERTENTES` (uma escolha):

| Vertente | Subtítulo | Frase |
|---|---|---|
| MORTUI | Necromancia | *Os mortos não descansam quando você os chama.* |
| DAEMONES | Demonologia | *Oblívio sempre envia quem ousa pedir.* |
| TENEBRAE | Umbramancia | *Onde a luz termina, seu domínio começa.* |

## Decisões do dev (2026-11-15)

1. Sobretítulos: Corrupção do Sangue (vampiro), Corrupção da Lua (lobisomem), Corrupção do Véu (bruxa). Os lemas foram refeitos para combinar com eles.
2. Nomes de feitiços, perks e poderes **traduzidos para português** (ex.: Raio Sobrenatural = *Eldritch Blast*, Fogo Funesto = *Balefire*, Raio da Bruxa = *Witch Bolt*). O glossário de tradução será mantido junto da implementação da UI.
3. A Bruxa escolhe a Vertente **no estágio 1 (Neófito)**.
4. Disciplina e Vertente **não podem ser trocadas** pelo jogador. Exceção prevista no design: no **Apoteótico** o vampiro escolhe a **segunda** Disciplina, que se soma à primeira e não a substitui.
