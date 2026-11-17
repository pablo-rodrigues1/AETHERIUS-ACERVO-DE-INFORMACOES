---
titulo: Transformação forçada após tempo real online
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [pablo-rodrigues1]
atualizado: 2026-11-16
tags: [sobrenatural, licantropia, tempo, servidor]
relacionados: [../02-sistemas/sobrenatural/etapa-2-transformacao-forcada.md, ADR-0014-forma-besta-recarga-real.md]
---

# ADR-0020 — Transformação forçada após tempo real online

## Resumo
Decisão do dev transmitida pelo Opus: após 72 h reais online sem transformação, o lobisomem deve transformar à noite (20h–5h do jogo), por personagem, com contador persistido e reset em transformação real.

## Contexto
O pacote de kdu controla a forma sincronizada e a animação; a obrigação por maldição pertence ao Sobrenatural. Calendário acelerado não mede permanência online e não pode substituir o tempo real.

## Opções consideradas
| Opção | Prós | Contras |
|---|---|---|
| Contar dias do calendário | Simples | Muda com TimeScale; contraria tempo real online |
| Somar relógio monotônico por sessão e persistir por personagem | Não cobra offline/reinício; separa personagens | Checkpoints têm pequena janela de perda em queda abrupta |

## Decisão
1. Somar 72 h reais de sessão; não somar offline ou intervalo entre processos. Depois de vencer, aguardar noite online com personagem vivo e humano. O controlador usa identidade/concessão do servidor e calendário autoritativo.
2. Sequência: 0 s mensagem inicial; 5 s coração em loop e tremor logo depois; 10 s vermelho; 15 s segunda mensagem; 20 s início do fluxo normal do werewolf-service, com animação kdu. Som/filtro do pré-aviso só no alvo; a forma/animação normal continua pública para vizinhos.
3. Reset apenas após raça de fera confirmada por readback no host, incluindo transformação voluntária. Cancelar/limpar em morte, desconexão, remoção da maldição ou forma já ativa; não disparar em morto/caído/Lorde/Fera.
4. Valores configuráveis; F7 tem Forçar transformação sobre o alvo, antecipando horas e conservando noite/eligibilidade/autorização. Sem teste em jogo neste despacho; roteiro preparado e revisão independente posterior pelo coordenador.

## Consequências
- O pacote kdu original permanece somente leitura; código candidato e patch ficam nos overlays do laboratório.
- Nenhuma keyword/contador privado é enviado ao cliente, e nenhum nativo/modpack/OFICIAL é alterado.
- 122 testes Node passaram; revisão independente, visual/áudio e dois jogadores permanecem pendentes. Não há homologação de produção.
