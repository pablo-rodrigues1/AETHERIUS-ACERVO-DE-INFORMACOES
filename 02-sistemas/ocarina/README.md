---
titulo: Ocarina (só música)
tipo: sistema
status: rascunho
fase: em-teste
responsaveis: [kdu]
repositorio: "pacote separado: mod AETHERIUS - OCARINA + patch sobre aetherius-server v0.38 (ainda não integrado ao oficial)"
depende_de: []
atualizado: 2026-11-14
tags: [item, musica, ocarina, cliente, servidor]
relacionados: [diario.md]
---

# Ocarina (só música)

## Resumo
Item novo "Ocarina", tirado do mod Playable Ocarina SE, só com a parte musical: notas pelas teclas, músicas inteiras e modo automático. Não substitui a flauta do jogo e não tem poderes (clima, hora, cura, cavalo), missão nem masmorra. Quem está perto vê a animação e ouve.

## Onde está o código
- Mod: `AETHERIUS - OCARINA`. Plugin ESL com item, objeto de animação, 5 notas e 5 músicas. A animação `IdleAethOcarina` é registrada pelo Pandora.
- Servidor: `gamemode/ocarina-service.js`.
- Cliente: `gamemode/ocarina/client.js`.
- Testes: `gamemode/ocarina/tests/ocarina.test.cjs`.

## Escopo
- Inclui:
  - equipar para tocar;
  - 5 notas;
  - reconhecer 4 músicas gravadas e tocá-las inteiras;
  - modo automático com 14 músicas;
  - som e animação para quem está perto.
- Não inclui: nenhum efeito de jogo (os poderes do mod original).

## Funcionamento
- Equipar a ocarina avisa o servidor. O servidor publica "tocando" e cada nota numa propriedade que quem está perto recebe. Cada PC toca o som no personagem e faz a animação.
- A própria nota toca na hora no PC de quem toca, sem esperar o servidor.
- O modo automático roda no servidor: publica as notas no ritmo e, quando existe, a gravação inteira no fim.

## Parâmetros e fórmulas
| Parâmetro | Valor | Descrição |
|---|---|---|
| `OCARINA` | ligado / `false` | desliga tudo |
| `OCARINA_AUTO` | `admin` (padrão), `staff`, `todos`, `ninguem` | quem usa o modo automático |
| `OCARINA_AUTO_PERFIS` | lista de perfis do site | libera também esses perfis |
| Intervalo entre notas (automático) | 420 ms (a última, 700 ms) | |
| Limite de notas | 1 a cada 80 ms por jogador | |

## Dependências
| Depende de | Por quê |
|---|---|
| Pandora (pelo MO2) | registra a animação nova |
| Arquivos do Playable Ocarina SE | modelo, animação e sons (conferir a permissão antes de distribuir) |

## Pendências conhecidas
- Teste em jogo: animação, alcance do som e teclas.
- Conferir a permissão dos arquivos do mod original para o modpack.
