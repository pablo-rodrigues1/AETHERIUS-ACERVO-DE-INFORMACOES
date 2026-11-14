---
titulo: A tecla ; abre a constelação
tipo: guia-staff
status: rascunho
responsaveis: [kdu]
atualizado: 2026-11-14
tags: [staff, teclas, interface]
fonte_tecnica: [../../02-sistemas/ui-core/diario.md]
relacionados: [../progresso.md]
---

# A tecla ; abre a constelação

> A correção já está pronta, mas ainda não chegou ao modpack oficial.

## O que acontece
No teclado brasileiro (ABNT2), apertar `;` abre o painel de habilidades do Skyrim (a constelação) junto com o que o jogador queria fazer.

## Por quê
O jogo enxerga o `;` do teclado brasileiro como a tecla `/` do teclado americano, e essa tecla abria o painel de habilidades. A correção tira o painel dessa tecla.

## Se continuar acontecendo depois da correção
O jogador provavelmente mudou alguma tecla pelo menu do próprio Skyrim. Quando alguém faz isso, o jogo guarda as teclas dele num arquivo próprio, que vale mais que o do modpack.

1. Peça para o jogador fechar o jogo.
2. Ele deve abrir a pasta `Documentos\My Games\Skyrim Special Edition\`.
3. Se houver um arquivo `ControlMap_Custom.txt`, ele deve renomear para `ControlMap_Custom.txt.antigo`.
4. Abrir o jogo de novo. As teclas voltam às do modpack, então o que ele tinha mudado precisa ser refeito.
