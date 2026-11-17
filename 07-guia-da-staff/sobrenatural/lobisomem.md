---
titulo: Como funciona o lobisomem
tipo: guia-staff
status: rascunho
responsaveis: [kdu]
atualizado: 2026-11-16
tags: [staff, sobrenatural, lobisomem]
fonte_tecnica: [../../02-sistemas/lobisomem/README.md, ../../02-sistemas/sobrenatural/etapa-2-transformacao-forcada.md]
relacionados: [../progresso.md]
---

# Como funciona o lobisomem

> Em teste: ainda não está no servidor oficial.

## Em uma frase
O jogador lobisomem pode virar uma fera grande, e todo mundo em volta vê a transformação acontecer.

## Como funciona
1. O jogador ativa a transformação.
2. Ele faz uma animação de cerca de 10 segundos: grita de dor (voz de homem ou de mulher), o grito vira rosnado, o corpo sangra e a câmera treme. Quem se transforma ouve o coração disparando e vê a tela piscar vermelho; no fim a tela fica toda vermelha e ele termina com um uivo.
3. Ele vira a fera, uns 50% maior que uma pessoa.
4. Enquanto é fera, quem está perto vê o nome "Lobisomem" em vez do nome do personagem.
5. Quando ele volta ao normal, recupera a aparência de antes.

## Exemplo prático
> Um jogador vira lobisomem no meio da cidade. Quem está perto vê a transformação inteira e depois a fera com o nome "Lobisomem". Um guarda com espada de prata ataca: cada golpe dói 50% a mais na fera.

## Tabela rápida
| Item | Valor | Observação |
|---|---|---|
| Duração da transformação | ~10 segundos | o jogador fica parado durante a animação |
| Tamanho da fera | ~50% maior | |
| Dano de prata na fera | +50% | armas e flechas de prata |
| Nome mostrado | "Lobisomem" | só para quem está perto |
| Grito de dor | homem ou mulher | todo mundo perto ouve |
| Coração e tela vermelha | só de quem se transforma | os outros não veem |

## Perguntas comuns de jogadores
- **Os outros jogadores me veem transformado?** Sim, a transformação é igual para todos.
- **Se o servidor reiniciar, continuo fera?** A aparência humana é restaurada pela recuperação do servidor.
- **A prata me mata mais rápido?** Causa 50% a mais de dano, mas o extra sozinho nunca dá o golpe final.

## O que a Staff pode e não pode fazer
- **Pode:** pedir aos devs para mudar o dano de prata ou o tempo da transformação (são opções do servidor).
- **No laboratório:** o F7 permite encerrar a forma e testar a transformação forçada no jogador selecionado; exige noite, alvo vivo e sem outra forma. Revisão e teste em jogo pendentes; não está liberado no oficial.

## Quer o detalhe técnico?
Veja [Lobisomem sincronizado](../../02-sistemas/lobisomem/README.md).


## Transformação forçada (candidato no laboratório)
Depois de 72 horas reais conectado sem transformar, o lobisomem precisa se transformar à noite, entre 20h e 5h do jogo. Se completar as horas de dia, espera a próxima noite conectado. O alvo recebe dois avisos, coração, tremor e vermelho na própria tela; após 20 segundos começa a animação normal, vista por quem está perto. Sair, morrer ou perder a maldição interrompe o aviso. Uma transformação real zera as horas; tempo desconectado não conta. Ainda precisa de revisão e teste no jogo. [Detalhes técnicos e roteiro](../../02-sistemas/sobrenatural/etapa-2-transformacao-forcada.md).
