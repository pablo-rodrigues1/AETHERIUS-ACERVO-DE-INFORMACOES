---
titulo: Poções e encantamentos (design)
tipo: guia-staff
status: rascunho
responsaveis: [mercurius17]
atualizado: 2026-11-14
tags: [staff, itens, alquimia, encantamento, profissoes, planejado]
fonte_tecnica: [../../06-referencias/planilha-classes-efeitos-encantamentos.md]
relacionados: [../profissoes/README.md, ../progresso.md]
---

# Poções e encantamentos (design)

## Em uma frase
Quanto as melhores poções e encantamentos devem render quando o Alquimista e o Encantador estiverem no nível máximo (Mestre).

> **Planejado, ainda não está no jogo.** As profissões Alquimista e Encantador estão bloqueadas por decisão no servidor (14/11/2026). Os valores abaixo vêm da planilha de design e podem mudar quando forem implementados.

## Como funciona
1. Os valores são o **teto**: o que um Alquimista ou Encantador Mestre consegue fazer.
2. Poções duram pouco (10 a 300 segundos). Encantamentos ficam no item.
3. Cada encantamento de armadura só pode ir em certas peças (entre parênteses na tabela).

## Exemplo prático
> Um Alquimista Mestre faz uma poção de Fortalecer Saúde: o jogador ganha +100 de vida por 5 minutos.

## Tabela rápida

**Poções (Alquimista Mestre)**

| Grupo | Efeito |
|---|---|
| Restauração | Restaura 30 de vida, magicka ou vigor por segundo, por 10 s |
| Fortificação | +100 de vida, magicka ou vigor por 300 s |
| Regeneração | +100% na regeneração de vida, magicka ou vigor por 300 s |
| Armas | +50% de dano com uma mão, duas mãos, arco, desarmado, ataque furtivo, ataque poderoso ou Gritos por 60 s; bloqueio 50% mais eficaz |
| Magia | Destruição, Ilusão e Restauração 50% mais fortes; Alteração e Conjuração duram 100% mais (60 s) |
| Ladino | Arrombar, furtar e se esgueirar 50% melhor por 60 s |
| Resistências | +50% a fogo, gelo, choque ou veneno por 60 s; +25% à magia por 300 s |
| Mundo | +100 de armadura ou de carga por 300 s; preços 20% melhores por 300 s; +20% de velocidade, respirar na água ou andar sobre a água por 60 s |
| Místicos | Abafamento, invisibilidade ou forma etérea por 60 s; curar doença |
| Refletir dano | Reflete 100% do dano corpo a corpo por 60 s |
| Venenos (dano rápido) | 10 de veneno, ou 20 de magicka ou de vigor, por segundo, por 10 s |
| Venenos (dano longo) | 6 de veneno, ou 12 de magicka ou de vigor, por segundo, por 30 s |
| Controle | Alvos até nível 40: acalmar, frenesi, silenciar, medo ou comandar por 30 s; paralisar por 10 s |
| Enfraquecer | −50% de velocidade, −50% de dano da arma ou −300 de armadura do inimigo por 60 s; −100% de resistência a fogo, gelo, choque ou veneno por 60 s |

**Encantamentos de arma (Encantador Mestre)**

| Grupo | Efeito |
|---|---|
| Elemental | 30 de dano de fogo, gelo (vida e vigor) ou choque (vida e magicka); caótico: 50% de chance de 30 de um dos três |
| Drenar | 10 de magicka ou de vigor por segundo, por 10 s |
| Absorver | 30 de vida, magicka ou vigor |
| Contra criaturas | 40 de dano solar a mortos-vivos; 5 de veneno por segundo por 10 s; mortos-vivos até nível 40 com 25% de chance de fugir; daedra até nível 40 com 25% de chance de serem banidos; 10/s por 10 s em autômatos Dwemer; 40 de dano a animais; à noite, absorve 30 de vida |
| Enfraquecer | −25% de velocidade ou de dano da arma, ou −150 de armadura, por 30 s; −50% de resistência a um elemento ou veneno por 30 s |
| Controle | Alvos até nível 40 com 25% de chance de silenciar, medo ou frenesi (30 s) ou paralisar (10 s) |
| Prisão de alma | Enche uma gema se o alvo morrer em até 30 s |

**Encantamentos de armadura (Encantador Mestre)**

| Grupo | Efeito (peças) |
|---|---|
| Custo de magia | Escola de magia custa 25% menos (cabeça, tronco) |
| Poder de magia | Destruição, Ilusão ou Restauração 25% mais fortes (colar, anel) |
| Duração de magia | Alteração, Conjuração, Ilusão ou Restauração duram 50% mais (colar, anel) |
| Armas | +25% de dano: uma mão, duas mãos, desarmado, ataque furtivo (luvas, anel); arco (cabeça, colar); ataque poderoso (cabeça, tronco) |
| Gritos | 25% mais fortes (cabeça, anel); duram 50% mais (colar, tronco) |
| Defesa | Bloqueio +25% (cabeça, colar, escudo); +100 de armadura (cabeça, tronco, escudo); reflete 50% do corpo a corpo (cabeça, tronco, escudo) |
| Escudo | Pancada +100%; ao bloquear, resiste 50% a flechas, ataques poderosos ou magia |
| Utilidade | Poções +50% de duração (cabeça, luvas); venenos +50% de acertos (cabeça, anel); preços 20% melhores (cabeça, colar); arrombar e furtar 25% melhor (luvas, anel); se esgueirar 25% melhor (anel, botas); +50 de carga (colar, botas) |
| Atributos | +50 de vida, magicka ou vigor; +50% na regeneração deles (peças variam por atributo) |
| Resistências | +50% a fogo, gelo, choque ou veneno; +25% à magia; +100% a doenças (colar, anel) |
| Movimento | Respirar na água e luz (cabeça, colar); queda suave, abafamento e andar sobre a água (botas) |

## Perguntas comuns de jogadores
- **Já posso ser Alquimista ou Encantador?** Ainda não. As duas profissões estão bloqueadas até o design ser decidido.
- **Esses valores são finais?** Não. São o design atual e podem mudar na implementação.

## O que a Staff pode e não pode fazer
- **Pode:** usar esta tabela para planejar eventos e responder curiosidade, deixando claro que é planejado.
- **Não pode:** prometer esses valores como definitivos nem criar poções ou encantamentos com eles fora do jogo.

## Quer o detalhe técnico?
Veja a [referência da planilha](../../06-referencias/planilha-classes-efeitos-encantamentos.md). O estado das profissões está no [painel de progresso](../progresso.md).
