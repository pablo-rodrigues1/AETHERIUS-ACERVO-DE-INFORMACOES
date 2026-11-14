---
titulo: O código prevalece sobre planilhas de design
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [mercurius17]
atualizado: 2026-11-14
tags: [fonte-da-verdade, design, documentacao]
relacionados: [../06-referencias/planilha-classes-efeitos-encantamentos.md]
---

# ADR-0003 — O código prevalece sobre planilhas de design

## Resumo
Quando uma planilha ou documento de design diverge do que já está definido no código ou nos repositórios, vale o código. A planilha só é fonte para o que ainda não foi implementado.

## Contexto
As regras de classes, leveling, raças e maldições nasceram em planilhas (ex.: "AETHERIUS - Classes, efeitos, encantamentos"). O código evoluiu depois delas: valores aprovados mudaram, regras foram simplificadas e dados foram corrigidos. As planilhas não são atualizadas junto.

## Opções consideradas
| Opção | Prós | Contras |
|---|---|---|
| Planilha prevalece | Um lugar para o design | Desatualizada em vários pontos; documentaria regras que o servidor não aplica |
| Código prevalece | Documenta o que o jogador realmente encontra | Mudança de design precisa virar código (ou ADR) para valer |

## Decisão
Decisão do dono do projeto em 14/11/2026:
1. Se o código ou um repositório já define algo, o acervo documenta o código.
2. A divergência é registrada na referência da planilha em `06-referencias/`, com os dois valores.
3. O que só existe na planilha entra no acervo como **design/planejado**, marcado assim nos guias.

## Consequências
- (+) Os guias da Staff descrevem o comportamento real do servidor.
- (+) Divergências ficam visíveis para quem for revisar o design.
- (−) Quem quiser mudar uma regra pela planilha precisa pedir a mudança no código.
