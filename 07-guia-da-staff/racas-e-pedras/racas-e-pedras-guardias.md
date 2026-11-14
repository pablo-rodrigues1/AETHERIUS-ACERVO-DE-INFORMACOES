---
titulo: Raças e pedras guardiãs
tipo: guia-staff
status: ativo
responsaveis: [mercurius17]
atualizado: 2026-11-14
tags: [staff, racas, pedras-guardias]
fonte_tecnica: [../../02-sistemas/gameplay-core/README.md]
relacionados: [../../06-referencias/planilha-classes-efeitos-encantamentos.md, ../classes/como-funcionam-as-classes.md]
---

# Raças e pedras guardiãs

## Em uma frase
A raça e a pedra guardiã dão bônus permanentes ao personagem, como mais vida, resistência a fogo ou correr mais rápido.

## Como funciona
1. Cada **raça** tem uma habilidade própria, sempre ativa (tabela abaixo).
2. O jogador escolhe **uma pedra guardiã** por vez. Ela também dá um bônus permanente, às vezes com uma desvantagem.
3. Raça e pedra somam com a classe. Elas não dependem de nível.
4. Os números vêm dos arquivos do jogo do servidor (mods Aetherius e Mundus). Se um mod mudar, o valor do jogo é o que vale.

## Exemplo prático
> Um Orc com a Pedra do Guerreiro tem +50 de vida pela pedra e regenera vida 50% mais rápido pela raça, além do que a classe der.

## Tabela rápida

**Raças**

| Raça | Habilidade | O que faz |
|---|---|---|
| Argoniano | Seiva de Hist | +50% resistência a veneno e a doenças; poções duram 50% mais; respira embaixo d'água |
| Bretão | Proteção Contra Feitiços | +25% resistência à magia; 10% de chance de absorver magicka de magias recebidas |
| Elfo Sombrio | Sabedoria da Montanha Vermelha | +50% resistência ao fogo; magias e encantamentos custam 10% menos |
| Alto-Elfo | Essência Nobre | +50 de magicka; regeneração de magicka +50% |
| Imperial | Diamante Vermelho | +25 de vida, magicka e vigor; preços 10% melhores; encontra ouro extra |
| Khajiit | Bênção Lunar e Visão Noturna | Ataques poderosos e arco gastam 20% menos vigor; correr e se esgueirar gastam 50% menos; enxerga no escuro (ativa e desativa) |
| Nórdico | Filho do Céu | +50% resistência ao gelo; 25% menos dano ao fazer ataque poderoso, puxar arco ou conjurar |
| Orc | Herdeiro de Orsinium | +50 de vida; regeneração de vida +50% |
| Rubraguarda | Treinamento Marcial | +50 de vigor; regeneração de vigor +50% |
| Elfo Silvestre | Pacto Verde | Anda 10% mais rápido; 50% menos dano de queda; +25% de dano em ataque furtivo |

**Pedras guardiãs**

| Pedra | Habilidade | O que faz |
|---|---|---|
| Aprendiz | Nascido Elfo | Magicka regenera 2× mais rápido, mas −25% de resistência a fogo, gelo e choque |
| Atronaca | Juramento do Feiticeiro | +100 de magicka; 20% de chance de absorver magicka de feitiços recebidos; não regenera magicka em combate |
| Dama | A Graça da Dama | +25 de vida, magicka e vigor; abaixo da metade de um deles, +50% na regeneração dele |
| Lorde | Sangue do Norte | Regeneração de vida +100%; +25% resistência à magia |
| Amante | Toque da Amante | Vigor regenera 2× mais rápido, mas 25% de dano extra ao fazer ataque poderoso ou puxar arco |
| Mago | Caminho da Sabedoria | +50 de magicka; feitiços e encantamentos custam 10% menos |
| Ritual | Dor da Mãe | Uma invocação ou reanimação a mais ao mesmo tempo; elas duram metade do tempo |
| Serpente | Beijo da Serpente | +50% resistência a veneno; poções duram 50% mais; venenos duram 50% mais acertos |
| Sombra | Sombraluna | Anda 20% mais rápido se esgueirando; +25% de dano em ataque furtivo |
| Corcel | Cocheiro | +100 de carga; corre 20% mais rápido; correr gasta 50% menos vigor |
| Ladrão | Sorte de Trapaceiro | +50 de vigor; anda 10% mais rápido; 50% menos dano de queda |
| Torre | A Parede do Guardião | +100 de armadura; reflete 100% do dano corpo a corpo recebido |
| Guerreiro | Beligerante | +50 de vida; resiste a 10% do dano de armas e magias |

## Perguntas comuns de jogadores
- **Posso ter duas pedras?** Não, uma por vez.
- **A Pedra do Ritual deixa ter quantas invocações?** Uma a mais. Junto com o perk Twin Souls, chega a três (ver [Como funcionam as classes](../classes/como-funcionam-as-classes.md)).
- **O bônus da raça muda com o nível?** Não. É fixo.

## O que a Staff pode e não pode fazer
- **Pode:** explicar os bônus usando esta tabela.
- **Não pode:** dar bônus de raça ou de pedra fora do que o jogo define.

## Quer o detalhe técnico?
Veja [GameplayCore](../../02-sistemas/gameplay-core/README.md) (módulos `race-system` e `standing-stone-system`, catálogos `races.json` e `standing-stones.json`) e a [referência da planilha](../../06-referencias/planilha-classes-efeitos-encantamentos.md).
