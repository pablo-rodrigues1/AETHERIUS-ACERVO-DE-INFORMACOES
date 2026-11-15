---
titulo: Forma da Besta com recarga de tempo real
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [pablo-rodrigues1]
atualizado: 2026-11-15
tags: [sobrenatural, lobisomem, recarga]
relacionados: [../02-sistemas/sobrenatural/formas-e-poderes.md, ../02-sistemas/lobisomem/README.md]
---

# ADR-0014 — Forma da Besta com recarga de tempo real

## Resumo

Decisão explícita do dev no teste de 15/11: a Forma da Besta usa recarga configurável de 30 minutos reais por padrão, com zero no laboratório local. O limite vanilla de uma utilização por dia não atende ao sistema.

## Contexto

WerewolfChange era um SPEL Power do Skyrim, apesar da transformação ser controlada pelo serviço do kdu. Essa restrição do motor impedia testes sucessivos e usava tempo do jogo em vez de tempo real.

## Opções consideradas

| Opção | Consequência |
|---|---|
| Manter Power diário | Não atende ao pedido do dev |
| LesserPower com prazo no servidor | Configurável, independente do relógio do jogo e persistido no ator |

## Decisão

1. Override somente em AETHERIUS - SOBRENATURAL.esp: WerewolfChange.Type=LesserPower.
2. WEREWOLF_COOLDOWN_SECONDS tem padrão 1800; laboratório explicitamente usa 0.
3. Servidor valida estado/keyword/poder e grava aetheriusBeastCooldownUntil ao aceitar; prazo não é enviado ao cliente e não é apagado pelo encerramento da forma.
4. A duração de 150 s da forma não muda. Homologação em jogo e revisão da implementação ainda pendem.

## Consequências

- Recast local não exige esperar outro dia do jogo.
- A autoridade da recarga permanece no servidor, inclusive quando a entrada vier de relato de efeito/raça do cliente.
- Esse override é candidato instalado apenas no perfil local; não foi aplicado ao oficial.
