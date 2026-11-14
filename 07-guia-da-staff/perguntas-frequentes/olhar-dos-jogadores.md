---
titulo: Para onde os jogadores olham
tipo: guia-staff
status: rascunho
responsaveis: [kdu]
atualizado: 2026-11-14
tags: [staff, multiplayer, olhar]
fonte_tecnica: [../../02-sistemas/olhar-sincronizado/README.md]
relacionados: [../progresso.md]
---

# Para onde os jogadores olham

> Em teste: ainda não está no servidor oficial.

## Em uma frase
Todo mundo vê para onde cada jogador está olhando: o personagem vira a cabeça para onde a câmera daquele jogador aponta, e não fica mais encarando quem chega perto.

## Como funciona
1. Você vira a câmera para algum lugar: uma pessoa, uma porta, o céu.
2. Na tela dos outros jogadores, a cabeça do seu personagem acompanha, de forma suave.
3. Se você olha para trás das costas do personagem, ele olha para a frente (o pescoço não gira tanto).

## Exemplo prático
> Três jogadores conversam numa taverna. Antes, quando um quarto chegava, todos os bonecos viravam a cabeça para ele ao mesmo tempo. Agora cada um olha para quem o próprio jogador está mirando.

## Perguntas comuns de jogadores
- **Os NPCs também mudaram?** Não. Os NPCs continuam olhando como no Skyrim normal.
- **Funciona com o head tracking do TDM?** Sim. O TDM continua cuidando do seu próprio personagem, na sua tela.

## O que a Staff pode e não pode fazer
- **Pode:** pedir aos devs para desligar o sistema, se der problema (é uma opção do servidor).
- **Não pode:** ajustar sozinha; não há comando de Staff para isso.

## Quer o detalhe técnico?
Veja [Olhar sincronizado](../../02-sistemas/olhar-sincronizado/README.md).
