---
titulo: Sincronização de NPCs (IA travada)
tipo: sistema
status: rascunho
fase: em-desenvolvimento
responsaveis: [kdu]
repositorio: "cliente e servidor SkyMP do Aetherius (aetherius-client / aetherius-server)"
depende_de: []
atualizado: 2026-11-14
tags: [npc, ia, sincronizacao, hospedagem, cliente, bug]
relacionados: [diario.md]
---

# Sincronização de NPCs (IA travada)

## Resumo
Investigação do bug em que a IA dos NPCs "quebra": inimigos e NPCs ficam parados ou em laço (só bloqueiam, não reagem, não acertam) e o jogador não consegue lutar. Acontece no servidor oficial e no servidor de teste. A suspeita principal é o cliente apagar e recriar o mesmo NPC a cada ~5,6 s, o que reinicia a IA dele.

## Onde está o código
- Cliente: `client/src/view/formView.ts` (criação/destruição do NPC na tela), `view/spawnProcess.ts`, `services/services/worldCleanerService.ts` (apagar com segurança), `sync/animation.ts` (bloqueio de animação de ataque), `services/services/remoteServer.ts` (mensagens de hospedagem).
- Servidor: `server/cpp/server_guest_lib/ActionListener.cpp` (`OnHostAttempt`: qual jogador "hospeda" a IA do NPC).

## Escopo
- Inclui: hospedagem da IA de NPCs (qual cliente roda a IA), ciclo de criação/destruição do NPC no cliente, bloqueio de animações de ataque de NPC não hospedado.
- Não inclui: dano e combate em si; spawns de inimigos/masmorras (ver [GameplayCore](../gameplay-core/README.md)).

## Funcionamento
- No SkyMP, a IA de cada NPC roda no computador de um jogador (o "hospedeiro"). Os outros só recebem a posição e a animação.
- O cliente recria o NPC na tela quando acha que ele ficou inválido. Há 7 motivos no código: célula mudou; NPC em célula diferente da do jogador; referência do plugin trocou; referência sem base ou com base diferente; nascimento não terminou em 5 s; erro de "respawn" no movimento; servidor removeu o NPC.
- Cada recriação desliga a IA do ator antigo e cria um novo. Se isso se repete a cada poucos segundos, o NPC nunca chega a lutar.

## Parâmetros e fórmulas
| Parâmetro | Valor | Descrição |
|---|---|---|
| `SPAWN_TIMEOUT_MS` | 5000 | prazo para o NPC terminar de nascer antes de ser recriado |
| Intervalo observado do laço | ~5,6 s | entre um "apagar" e o próximo, no mesmo NPC |
| Espera máxima do apagar seguro | 5 s | depois de desligar a IA, até apagar de vez |

## Dependências
| Depende de | Por quê |
|---|---|
| SkyMP (cliente e servidor) | o ciclo de vida do NPC e a hospedagem são do SkyMP |

## Decisões relacionadas
- Nenhuma ainda; a correção depende de saber qual dos 7 motivos dispara.

## Diário
Ver [`diario.md`](diario.md).

## Pendências conhecidas
- Reproduzir com o cliente instrumentado (kit de teste v19) e coletar `aetherius-cliente-logs.txt`.
- Identificar o motivo do laço e corrigir no cliente.
- Registrar no servidor as trocas de hospedeiro (exige recompilar o C++).
