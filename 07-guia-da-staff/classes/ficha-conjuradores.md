---
titulo: Ficha das classes — Conjuradores
tipo: guia-staff
status: ativo
responsaveis: [mercurius17]
atualizado: 2026-11-14
tags: [staff, classes, perks, skills, feiticos]
fonte_tecnica: [../../02-sistemas/gameplay-core/README.md]
relacionados: [como-funcionam-as-classes.md, ../../06-referencias/planilha-classes-efeitos-encantamentos.md]
---

# Ficha das classes — Conjuradores

## Em uma frase
O que cada classe do grupo Conjuradores (Elementalista, Criomante, Eletromante, Piromante, Invocador, Curandeiro) recebe em cada marco: skills, perks e feitiços que pode aprender.

## Como funciona
- **Skills e perks:** os valores abaixo vêm do código do servidor (GameplayCore, `modules/class-system/config/classes-config.json`), com as descrições em português do próprio código.
- **Feitiços:** referência documental da aba Classes, sincronizada com Classes - Progressão em 14/11/2026. A lista foi completada, mas o código ainda tem diferenças; consultar o [catálogo técnico](../../02-sistemas/gameplay-core/feiticos-por-classe.md). Os nomes originais aparecem entre parênteses quando há tradução. Os tiers são independentes dos marcos de classe; esta revisão não altera o que o servidor autoriza ou entrega.
- **Skills:** "Todas 20" vale para as skills principais da classe. Quando um marco não cita uma skill, ela fica no valor do marco anterior.
- **Perks:** chegam sozinhos ao atingir o marco. Um número entre parênteses é o grau do perk (ex.: Armadura Mágica 2).
- **Feitiços:** a classe só diz quais são permitidos. O jogador aprende por roleplay no Colégio de Winterhold.
- Todo nível dá 15 pontos de atributo, inclusive os marcos (ver [Nível, XP e pontos de atributo](../progressao/nivel-xp-e-atributos.md)).

## Exemplo prático
> Um jogador pergunta o que ganha ao chegar no nível 15. Procure a classe dele abaixo, linha "Nível 15": são esses perks, e as skills ficam no valor da linha 15 da tabela.

## Tabela rápida

### Elementalista

O Elementalista é um tecelão das forças primordiais, capaz de canalizar o Fogo, o Gelo e o Trovão em perfeito equilíbrio. Embora não alcance a maestria absoluta de um especialista dedicado em um único elemento, sua força reside na versatilidade adaptativa: ele molda o campo de batalha adaptando seus feitiços à fraqueza de qualquer adversário, combinando a brutalidade do combate de proximidade com um arsenal elemental devastador.

**Classe do Colégio de Winterhold:** só a Staff define.

| Marco | Skills da classe |
|---|---|
| 1 | Todas 0 |
| 5 | Todas 20 |
| 10 | Destruição 30, Alteração 30, Restauração 30 |
| 15 | Destruição 40, Alteração 40, Restauração 40 |
| 20 | Destruição 50 |
| 25 | Destruição 60 |
| 30 | — (mantém o marco anterior) |
| 35 | — (mantém o marco anterior) |
| 40 | — (mantém o marco anterior) |

**Perks recebidos em cada marco:**

- **Nível 1:** Maestria em Destruição (Feitiços de Destruição custam 0,5% a menos de Mágicka por nível em Destruição.); Maestria em Alteração (Feitiços de Alteração custam 0,5% a menos de Mágicka por nível em Alteração.); Maestria em Uma Mão (Armas de uma mão causam 1% a mais de dano e 5% a mais de dano crítico por nível em Uma Mão.); Maestria em Restauração (Feitiços de Restauração custam 0,5% a menos de Mágicka por nível em Restauração.)
- **Nível 5:** Conjuração Dupla de Destruição (Lançar um feitiço de Destruição com ambas as mãos potencializa seus efeitos, aumentando o poder e o custo de conjuração.); Armadura Mágica I (Feitiços de proteção como Carne de Carvalho são 100% mais fortes se você não estiver usando armadura.)
- **Nível 10:** Chamas Aumentadas I (Feitiços e efeitos de fogo são 20% mais poderosos.); Resistência Mágica I (Aumenta a Resistência Mágica em 10%.)
- **Nível 15:** Gelo Aumentado I (Feitiços e efeitos de gelo são 20% mais poderosos.); Armadura Mágica II (Feitiços de proteção como Carne de Ferro são 150% mais fortes se você não estiver usando armadura.); Barreira Vigilante I (Barreiras custam 50% a menos de Mágicka e reduzem o dano recebido em 30%.)
- **Nível 20:** Raios Aumentados I (Feitiços e efeitos elétricos são 20% mais poderosos.); Resistência Mágica II (Aumenta a Resistência Mágica em 20%.)
- **Nível 25:** Armadura Mágica III (Feitiços de proteção como Carne de Pedra são 200% mais fortes se você não estiver usando armadura.)
- **Nível 30:** Conjuração Dupla de Restauração (Lançar um feitiço de Restauração com ambas as mãos potencializa seus efeitos, aumentando o poder e o custo de conjuração.); Estabilidade (Feitiços e efeitos de Alteração duram 25% a mais.)
- **Nível 35:** Preparação de Ocato (Ao entrar em combate, ativa automaticamente o feitiço de armadura mágica mais eficaz que você conhece.); Absorção de Barreira (Quando sua barreira bloqueia um feitiço, você recupera Mágicka equivalente a 30% do custo desse feitiço.)
- **Nível 40:** Impacto (Feitiços de Destruição baseados em projéteis têm 30% de chance de desestabilizar os alvos quando lançados com ambas as mãos.)

**Feitiços da planilha por tier (aprendizado por roleplay no Colégio):**

- **Novato:** Chamas (Flames); Mordedura de Gelo (Frostbite); Faíscas (Sparks); Pele de Carvalho (Oakflesh)
- **Aprendiz:** Estaca de Gelo (Ice Spike); Raio Relampejante (Lightning Bolt); Toque Flamejante (Burning Touch); Pele de Pedra (Stoneflesh); Passo do Vento (Windwalker); Luz Mágica (Magelight); Barreira Firme (Steadfast Ward)
- **Adepto:** Bola de Fogo (Fireball); Manto de Gelo (Frost Cloak); Relâmpago em Cadeia (Chain Lightning); Pele de Ferro (Ironflesh); Carapaça Elétrica (Shock Shell); Barreira Maior (Greater Ward)

### Criomante

O Criomante abdica de qualquer outra manifestação mágica para se devotar inteiramente ao abraço gélido da morte. Senhor do zero absoluto, ele molda a geada e a tempestade de neve para desacelerar, congelar e estilhaçar seus inimigos. Envolto em barreiras de gelo e dominando feitiços de congelamento devastadores, ele transforma o campo de batalha em um túmulo congelado.

**Classe do Colégio de Winterhold:** só a Staff define.

| Marco | Skills da classe |
|---|---|
| 1 | Todas 0 |
| 5 | Todas 20 |
| 10 | Destruição 30, Alteração 30, Restauração 30 |
| 15 | Destruição 40, Alteração 40, Restauração 40 |
| 20 | Destruição 50 |
| 25 | Destruição 60 |
| 30 | — (mantém o marco anterior) |
| 35 | — (mantém o marco anterior) |
| 40 | — (mantém o marco anterior) |

**Perks recebidos em cada marco:**

- **Nível 1:** Maestria em Destruição (Feitiços de Destruição custam 0,5% a menos de Mágicka por nível em Destruição.); Maestria em Alteração (Feitiços de Alteração custam 0,5% a menos de Mágicka por nível em Alteração.); Maestria em Uma Mão (Armas de uma mão causam 1% a mais de dano e 5% a mais de dano crítico por nível em Uma Mão.); Maestria em Restauração (Feitiços de Restauração custam 0,5% a menos de Mágicka por nível em Restauração.)
- **Nível 5:** Conjuração Dupla de Destruição (Lançar um feitiço de Destruição com ambas as mãos potencializa seus efeitos, aumentando o poder e o custo de conjuração.); Armadura Mágica I (Feitiços de proteção como Carne de Carvalho são 100% mais fortes se você não estiver usando armadura.)
- **Nível 10:** Gelo Aumentado I (Feitiços e efeitos de gelo são 20% mais poderosos.); Resistência Mágica I (Aumenta a Resistência Mágica em 10%.)
- **Nível 15:** Mestre das Runas I (Permite posicionar runas três vezes mais longe, e as runas são 10% mais poderosas.); Armadura Mágica II (Feitiços de proteção como Carne de Ferro são 150% mais fortes se você não estiver usando armadura.); Barreira Vigilante I (Barreiras custam 50% a menos de Mágicka e reduzem o dano recebido em 30%.)
- **Nível 20:** Geada Enregelante (Feitiços de gelo reduzem a velocidade de ataque em 20% e infligem 15% de fraqueza a gelo por 10 segundos.); Resistência Mágica II (Aumenta a Resistência Mágica em 20%.)
- **Nível 25:** Gelo Aumentado II (Feitiços e efeitos de gelo são 40% mais poderosos.); Armadura Mágica III (Feitiços de proteção como Carne de Pedra são 200% mais fortes se você não estiver usando armadura.)
- **Nível 30:** Abraço do Inverno (Feitiços de gelo congelam alvos com menos de 20% de Vida em um bloco de gelo por 5 segundos.); Estabilidade (Feitiços e efeitos de Alteração duram 25% a mais.)
- **Nível 35:** Preparação de Ocato (Ao entrar em combate, ativa automaticamente o feitiço de armadura mágica mais eficaz que você conhece.); Absorção de Barreira (Quando sua barreira bloqueia um feitiço, você recupera Mágicka equivalente a 30% do custo desse feitiço.)
- **Nível 40:** Tempestade Infernal (Causa 50% a mais de dano elemental a alvos que estejam em chamas, congelados, silenciados ou levitados por outras vantagens de Destruição.)

**Feitiços da planilha por tier (aprendizado por roleplay no Colégio):**

- **Novato:** Mordedura de Gelo (Frostbite); Pele de Carvalho (Oakflesh)
- **Aprendiz:** Estaca de Gelo (Ice Spike); Toque Congelante (Chilling Touch); Runa de Gelo (Frost Rune); Barreira Firme (Steadfast Ward)
- **Adepto:** Geada Superior (Greater Frostbite); Manto de Gelo (Frost Cloak); Tempestade de Gelo (Ice Storm); Pele de Ferro (Ironflesh); Passo do Vento (Windwalker); Luz Mágica (Magelight); Carapaça Glacial (Frost Shell); Barreira Maior (Greater Ward)
- **Especialista:** Muralha de Gelo (Wall of Frost); Lança Glacial (Icy Spear)
- **Mestre:** Nevasca (Blizzard)

### Eletromante

O Eletromante abdica de qualquer outra manifestação mágica para se devotar inteiramente ao poder da tempestade. Senhor dos raios, ele molda a eletricidade para chocar, paralisar e desintegrar seus inimigos. Envolto em barreiras elétricas e dominando feitiços de choque devastadores, ele transforma o campo de batalha em uma tempestade implacável.

**Classe do Colégio de Winterhold:** só a Staff define.

| Marco | Skills da classe |
|---|---|
| 1 | Todas 0 |
| 5 | Todas 20 |
| 10 | Destruição 30, Alteração 30, Restauração 30 |
| 15 | Destruição 40, Alteração 40, Restauração 40 |
| 20 | Destruição 50 |
| 25 | Destruição 60 |
| 30 | — (mantém o marco anterior) |
| 35 | — (mantém o marco anterior) |
| 40 | — (mantém o marco anterior) |

**Perks recebidos em cada marco:**

- **Nível 1:** Maestria em Destruição (Feitiços de Destruição custam 0,5% a menos de Mágicka por nível em Destruição.); Maestria em Alteração (Feitiços de Alteração custam 0,5% a menos de Mágicka por nível em Alteração.); Maestria em Uma Mão (Armas de uma mão causam 1% a mais de dano e 5% a mais de dano crítico por nível em Uma Mão.); Maestria em Restauração (Feitiços de Restauração custam 0,5% a menos de Mágicka por nível em Restauração.)
- **Nível 5:** Conjuração Dupla de Destruição (Lançar um feitiço de Destruição com ambas as mãos potencializa seus efeitos, aumentando o poder e o custo de conjuração.); Armadura Mágica I (Feitiços de proteção como Carne de Carvalho são 100% mais fortes se você não estiver usando armadura.)
- **Nível 10:** Raios Aumentados I (Feitiços e efeitos elétricos são 20% mais poderosos.); Resistência Mágica I (Aumenta a Resistência Mágica em 10%.)
- **Nível 15:** Mestre das Runas I (Permite posicionar runas três vezes mais longe, e as runas são 10% mais poderosas.); Armadura Mágica II (Feitiços de proteção como Carne de Ferro são 150% mais fortes se você não estiver usando armadura.); Barreira Vigilante I (Barreiras custam 50% a menos de Mágicka e reduzem o dano recebido em 30%.)
- **Nível 20:** Choque Ensurdecedor (Feitiços elétricos têm 20% de chance de silenciar os alvos por 2 segundos.); Resistência Mágica II (Aumenta a Resistência Mágica em 20%.)
- **Nível 25:** Raios Aumentados II (Feitiços e efeitos elétricos são 40% mais poderosos.); Armadura Mágica III (Feitiços de proteção como Carne de Pedra são 200% mais fortes se você não estiver usando armadura.)
- **Nível 30:** Esfera Crepitante (Feitiços elétricos têm 20% de chance de levitar os alvos por 2 segundos.); Estabilidade (Feitiços e efeitos de Alteração duram 25% a mais.)
- **Nível 35:** Preparação de Ocato (Ao entrar em combate, ativa automaticamente o feitiço de armadura mágica mais eficaz que você conhece.); Absorção de Barreira (Quando sua barreira bloqueia um feitiço, você recupera Mágicka equivalente a 30% do custo desse feitiço.)
- **Nível 40:** Tempestade Infernal (Causa 50% a mais de dano elemental a alvos que estejam em chamas, congelados, silenciados ou levitados por outras vantagens de Destruição.)

**Feitiços da planilha por tier (aprendizado por roleplay no Colégio):**

- **Novato:** Faíscas (Sparks); Pele de Carvalho (Oakflesh)
- **Aprendiz:** Raio Relampejante (Lightning Bolt); Toque Elétrico (Shocking Touch); Runa de Eletricidade (Lightning Rune); Barreira Firme (Steadfast Ward)
- **Adepto:** Faíscas Superiores (Greater Sparks); Manto da Tempestade (Lightning Cloak); Relâmpago em Cadeia (Chain Lightning); Pele de Pedra (Stoneflesh); Passo do Vento (Windwalker); Luz Mágica (Magelight); Carapaça Elétrica (Shock Shell); Pele de Ferro (Ironflesh); Barreira Maior (Greater Ward)
- **Especialista:** Muralha da Tempestade (Wall of Storms); Trovão Fulminante (Thunderbolt)
- **Mestre:** Tempestade de Raios (Lightning Storm)

### Piromante

O Piromante é o avatar da destruição incandescente, um mago que entregou sua alma às chamas eternas. Ignorando todas as outras vertentes da magia elemental, ele domina a arte de reduzir tudo a cinzas. Capaz de conjurar desde ondas de calor sufocantes até tempestades de fogo apocalípticas, o Piromante prospera no caos da combustão e no pavor daqueles que queimam sob seu poder.

**Classe do Colégio de Winterhold:** só a Staff define.

| Marco | Skills da classe |
|---|---|
| 1 | Todas 0 |
| 5 | Todas 20 |
| 10 | Destruição 30, Alteração 30, Restauração 30 |
| 15 | Destruição 40, Alteração 40, Restauração 40 |
| 20 | Destruição 50 |
| 25 | Destruição 60 |
| 30 | — (mantém o marco anterior) |
| 35 | — (mantém o marco anterior) |
| 40 | — (mantém o marco anterior) |

**Perks recebidos em cada marco:**

- **Nível 1:** Maestria em Destruição (Feitiços de Destruição custam 0,5% a menos de Mágicka por nível em Destruição.); Maestria em Alteração (Feitiços de Alteração custam 0,5% a menos de Mágicka por nível em Alteração.); Maestria em Uma Mão (Armas de uma mão causam 1% a mais de dano e 5% a mais de dano crítico por nível em Uma Mão.); Maestria em Restauração (Feitiços de Restauração custam 0,5% a menos de Mágicka por nível em Restauração.)
- **Nível 5:** Conjuração Dupla de Destruição (Lançar um feitiço de Destruição com ambas as mãos potencializa seus efeitos, aumentando o poder e o custo de conjuração.); Armadura Mágica I (Feitiços de proteção como Carne de Carvalho são 100% mais fortes se você não estiver usando armadura.)
- **Nível 10:** Chamas Aumentadas I (Feitiços e efeitos de fogo são 20% mais poderosos.); Resistência Mágica I (Aumenta a Resistência Mágica em 10%.)
- **Nível 15:** Mestre das Runas I (Permite posicionar runas três vezes mais longe, e as runas são 10% mais poderosas.); Armadura Mágica II (Feitiços de proteção como Carne de Ferro são 150% mais fortes se você não estiver usando armadura.); Barreira Vigilante I (Barreiras custam 50% a menos de Mágicka e reduzem o dano recebido em 30%.)
- **Nível 20:** Chamas Vorazes (Feitiços de fogo incendeiam alvos com menos de 20% de Vida, causando dano adicional por 5 segundos.); Resistência Mágica II (Aumenta a Resistência Mágica em 20%.)
- **Nível 25:** Chamas Aumentadas II (Feitiços e efeitos de fogo são 40% mais poderosos.); Armadura Mágica III (Feitiços de proteção como Carne de Pedra são 200% mais fortes se você não estiver usando armadura.)
- **Nível 30:** Terra Arrasada (Feitiços de fogo reduzem suas vítimas a cinzas, deixando uma pira que incendeia oponentes, causando dano adicional por 10 segundos.); Estabilidade (Feitiços e efeitos de Alteração duram 25% a mais.)
- **Nível 35:** Preparação de Ocato (Ao entrar em combate, ativa automaticamente o feitiço de armadura mágica mais eficaz que você conhece.); Absorção de Barreira (Quando sua barreira bloqueia um feitiço, você recupera Mágicka equivalente a 30% do custo desse feitiço.)
- **Nível 40:** Tempestade Infernal (Causa 50% a mais de dano elemental a alvos que estejam em chamas, congelados, silenciados ou levitados por outras vantagens de Destruição.)

**Feitiços da planilha por tier (aprendizado por roleplay no Colégio):**

- **Novato:** Chamas (Flames); Pele de Carvalho (Oakflesh)
- **Aprendiz:** Dardo Ígneo (Firebolt); Toque Flamejante (Burning Touch); Runa de Fogo (Fire Rune); Barreira Firme (Steadfast Ward)
- **Adepto:** Chamas Superiores (Greater Flames); Manto de Chamas (Flame Cloak); Bola de Fogo (Fireball); Pele de Pedra (Stoneflesh); Passo do Vento (Windwalker); Luz Mágica (Magelight); Carapaça Ígnea (Flame Shell); Pele de Ferro (Ironflesh); Barreira Maior (Greater Ward)
- **Especialista:** Muralha de Chamas (Wall of Flames); Incinerar (Incinerate)
- **Mestre:** Tempestade de Fogo (Firestorm)

### Invocador

O Invocador é um arquiteto do plano cósmico, capaz de rasgar o tecido da realidade e subjugar os horrores de Oblivion à sua vontade. Comandando Atronachs como peças em um tabuleiro, ele nunca luta sozinho. Enquanto suas monárquicas criações elementais esmagam a linha de frente inimiga, o Invocador controla o combate à distância, seguro atrás de suas maquinações interdimensionais.

**Classe do Colégio de Winterhold:** só a Staff define.

| Marco | Skills da classe |
|---|---|
| 1 | Todas 0 |
| 5 | Todas 20 |
| 10 | Conjuração 30, Alteração 30, Restauração 30 |
| 15 | Conjuração 40, Alteração 40, Restauração 40 |
| 20 | Conjuração 50 |
| 25 | Conjuração 60 |
| 30 | — (mantém o marco anterior) |
| 35 | — (mantém o marco anterior) |
| 40 | — (mantém o marco anterior) |

**Perks recebidos em cada marco:**

- **Nível 1:** Maestria em Conjuração (Feitiços de Conjuração custam 0,5% a menos de Mágicka por nível em Conjuração.); Maestria em Alteração (Feitiços de Alteração custam 0,5% a menos de Mágicka por nível em Alteração.); Maestria em Restauração (Feitiços de Restauração custam 0,5% a menos de Mágicka por nível em Restauração.); Maestria em Uma Mão (Armas de uma mão causam 1% a mais de dano e 5% a mais de dano crítico por nível em Uma Mão.)
- **Nível 5:** Conjuração Dupla de Conjuração (Lançar um feitiço de Conjuração com ambas as mãos potencializa seus efeitos, aumentando a duração e o custo de conjuração.); Armadura Mágica I (Feitiços de proteção como Carne de Carvalho são 100% mais fortes se você não estiver usando armadura.)
- **Nível 10:** Atromancia (Daedra e criaturas invocadas duram o dobro do tempo.); Resistência Mágica I (Aumenta a Resistência Mágica em 10%.)
- **Nível 15:** Pedra do Oblivion I (Daedra e criaturas invocadas recebem 100 pontos de armadura e 25% de resistência mágica.); Armadura Mágica II (Feitiços de proteção como Carne de Ferro são 150% mais fortes se você não estiver usando armadura.); Invocador da Fenda I (Permite invocar três vezes mais longe.)
- **Nível 20:** Invocador da Fenda II (Permite invocar a qualquer distância.); Resistência Mágica II (Aumenta a Resistência Mágica em 20%.)
- **Nível 25:** Potência Elemental (Feitiços de invocação de Atronachs agora convocam Atronachs Potentes de nível mais alto com ataques elementais aprimorados.); Armadura Mágica III (Feitiços de proteção como Carne de Pedra são 200% mais fortes se você não estiver usando armadura.)
- **Nível 30:** Barreira Vigilante I (Barreiras custam 50% a menos de Mágicka e reduzem o dano recebido em 30%.); Pedra do Oblivion II (Daedra e criaturas invocadas recebem 200 pontos de armadura e 50% de resistência mágica.)
- **Nível 35:** Confluxo Elemental (Enquanto estiver perto de um atronach aliado, recebe um bônus de 20% em feitiços e efeitos do elemento correspondente e 50 de dano elemental correspondente em armas vinculadas.); Absorção de Barreira (Quando sua barreira bloqueia um feitiço, você recupera Mágicka equivalente a 30% do custo desse feitiço.)
- **Nível 40:** Almas Gêmeas (Permite invocar ou reanimar um lacaio adicional.)

**Feitiços da planilha por tier (aprendizado por roleplay no Colégio):**

- **Novato:** Invocar Lobo Espectral (Conjure Spectral Wolf); Adaga Vinculada (Bound Dagger); Chamas (Flames); Mordedura de Gelo (Frostbite); Faíscas (Sparks); Pele de Carvalho (Oakflesh)
- **Aprendiz:** Invocar Atronach da Chama (Conjure Flame Atronach); Pele de Pedra (Stoneflesh); Barreira Firme (Steadfast Ward)
- **Adepto:** Invocar Atronach do Gelo (Conjure Frost Atronach); Cura Daédrica (Daedric Cure); Passo do Vento (Windwalker); Luz Mágica (Magelight); Carapaça Ígnea (Flame Shell); Pele de Ferro (Ironflesh); Barreira Maior (Greater Ward)
- **Especialista:** Invocar Atronach da Tempestade (Conjure Storm Atronach)
- **Mestre:** Invocar Monarca da Chama (Conjure Flame Monarch); Invocar Monarca do Gelo (Conjure Frost Monarch); Invocar Monarca da Tempestade (Conjure Storm Monarch); Expulsar Daedra Maior (Expel Daedra)

### Curandeiro

O Curandeiro é o pilar de sustentação e a salvação de qualquer grupo no campo de batalha. Mestre incontestável da escola de Restauração, ele canaliza auras purificadoras, sana ferimentos mortais instantaneamente e concede bônus vitais aos seus aliados. Sua presença transforma um esquadrão prestes a rcar em uma força invencível e incansável.

**Classe do Colégio de Winterhold:** só a Staff define.

| Marco | Skills da classe |
|---|---|
| 1 | Todas 0 |
| 5 | Todas 20 |
| 10 | Alteração 30, Restauração 30, Uma-Mão 30 |
| 15 | Alteração 40, Restauração 40, Uma-Mão 40 |
| 20 | Alteração 50, Restauração 50 |
| 25 | Alteração 60, Restauração 60 |
| 30 | Restauração 70 |
| 35 | Restauração 80 |
| 40 | — (mantém o marco anterior) |

**Perks recebidos em cada marco:**

- **Nível 1:** Maestria em Restauração (Feitiços de Restauração custam 0,5% a menos de Mágicka por nível em Restauração.); Maestria em Destruição (Feitiços de Destruição custam 0,5% a menos de Mágicka por nível em Destruição.); Maestria em Uma Mão (Armas de uma mão causam 1% a mais de dano e 5% a mais de dano crítico por nível em Uma Mão.)
- **Nível 5:** Conjuração Dupla de Restauração (Lançar um feitiço de Restauração com ambas as mãos potencializa seus efeitos, aumentando o poder e o custo de conjuração.); Armadura Mágica I (Feitiços de proteção como Carne de Carvalho são 100% mais fortes se você não estiver usando armadura.)
- **Nível 10:** Misericórdia (Feitiços de Restauração são até 50% mais eficazes se a Vida restante do alvo estiver baixa.); Combatente Disciplinado (Ataques de poder de uma mão custam 25% a menos de Vigor.)
- **Nível 15:** Luz Interior I (Regenera 1% adicional da sua Mágicka total por segundo.); Barreira Vigilante I (Barreiras custam 50% a menos de Mágicka e reduzem o dano recebido em 30%.); Armadura Mágica II (Feitiços de proteção como Carne de Ferro são 150% mais fortes se você não estiver usando armadura.)
- **Nível 20:** Luz Interior II (Regenera 2% adicionais da sua Mágicka total por segundo.); Inspirar I (Aliados vivos a até 15 pés recebem 5 pontos de cura por segundo.)
- **Nível 25:** Resistência Mágica I (Aumenta a Resistência Mágica em 10%.); Armadura Mágica III (Feitiços de proteção como Carne de Pedra são 200% mais fortes se você não estiver usando armadura.)
- **Nível 30:** Resistência Mágica II (Aumenta a Resistência Mágica em 20%.); Barreira Vigilante II (Barreiras custam 80% a menos de Mágicka e reduzem o dano recebido em 60%.)
- **Nível 35:** Absorção de Barreira (Quando sua barreira bloqueia um feitiço, você recupera Mágicka equivalente a 30% do custo desse feitiço.); Inspirar II (Aliados vivos a até 15 pés recebem 10 pontos de cura por segundo.)
- **Nível 40:** Chama Perene (Feitiços de cura concedem armadura equivalente a 4 vezes a sua magnitude enquanto você se concentra ou por 5 segundos após a conjuração.)

**Feitiços da planilha por tier (aprendizado por roleplay no Colégio):**

- **Novato:** Cura Básica (Healing); Raio Solar (Sunbeam); Chamas (Flames)
- **Aprendiz:** Cura Rápida (Fast Healing); Curar Aliado (Heal Other); Barreira Firme (Steadfast Ward); Pele de Pedra (Stoneflesh); Luz Mágica (Magelight)
- **Adepto:** Cura Superior (Greater Healing); Detectar Vida (Detect Life); Dissipar Magia (Dispel); Pele de Ferro (Ironflesh); Carapaça Elétrica (Shock Shell)
- **Especialista:** Aura de Espinhos (Aura of Thorns)
- **Mestre:** Barreira Suprema (Grand Ward); Círculo de Proteção (Circle of Protection); Mutagênico Restaurador (Mutagen); Fonte de Vitalidade (Font of Vitality)


## Perguntas comuns de jogadores
- **Por que não recebi um feitiço da lista?** Feitiços são aprendidos por roleplay; a lista só mostra o que é permitido.
- **Recebi um perk e ele não aparece na árvore do jogo.** O perk é entregue pelo servidor; confira com a Staff se a classe e o nível estão certos.

## O que a Staff pode e não pode fazer
- **Pode:** consultar esta ficha para responder dúvidas e definir classe/nível pelo painel (ver [Como funcionam as classes](como-funcionam-as-classes.md)).
- **Não pode:** prometer perks, skills ou feitiços diferentes desta ficha. Mudanças passam pelos devs.

## Quer o detalhe técnico?
Veja [GameplayCore](../../02-sistemas/gameplay-core/README.md).
