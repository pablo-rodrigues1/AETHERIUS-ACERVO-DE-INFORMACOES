---
titulo: Sobrenatural em lotes locais e poderes parciais em JS
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [pablo-rodrigues1]
atualizado: 2026-11-15
tags: [sobrenatural, sincronizacao, poderes, laboratorio]
relacionados: [../02-sistemas/sobrenatural/formas-e-poderes.md, ADR-0014-forma-besta-recarga-real.md]
---

# ADR-0015 — Sobrenatural em lotes locais e poderes parciais em JS

## Resumo
Decisões explícitas do dev transmitidas pelo orquestrador em 15/11: testar primeiro Fera/Lorde/raça/recarga e deixar os demais poderes para outro lote. Não alterar nem recompilar o addon nativo.

## Contexto
O teste revelou que efeitos Papyrus ativos não garantem execução mecânica no SkyMP. A revisão Terra rejeitou o primeiro candidato com cinco apontamentos aceitos; quatro afetam diretamente as formas/raça. Regeneração da Névoa e dano da Força não têm solução integral certificada em JS neste host.

## Opções consideradas
| Opção | Prós | Contras |
|---|---|---|
| Expandir o nativo agora | Poderia fornecer APIs ausentes | Escopo/risco não autorizado pelo dev |
| Entregar poderes parciais JS em lotes | Teste menor e limites explícitos | Sem equivalência mecânica integral neste momento |

## Decisão
1. Primeiro lote: Fera, Lorde (projeção da forma, sem quest completa), raça/olhos/login/reversão e recarga real da ADR-0014. Quatro achados pertinentes corrigidos; Visão adiada e caminho anterior preservado.
2. Próximo lote JS: Morcego, Enxame e apresentação/invisibilidade pública da Névoa. Névoa (regen) e Força (dano) ficam PARCIAIS; não mascarar limites como implementação concluída.
3. Enxame pode usar provisoriamente os records COTV só no laboratório: 10 vida/s, 15 s; raio deve ser extraído/validado antes do teste. Não é fórmula final nem autorização para produção.
4. O dev autorizou no diário do orquestrador o teste do lote menor antes da confirmação Terra; isso não aprova a revisão nem substitui a homologação. Resultados humanos continuam pendentes.

## Consequências
- Nenhum addon/DLL recompilado e nenhum espelho oficial modificado.
- A documentação distingue candidato instalado, experimento adiado e comportamento confirmado no motor.
- Origem persistida de grants permanece tarefa seguinte; Bruxaria é implementada por outro worker.
