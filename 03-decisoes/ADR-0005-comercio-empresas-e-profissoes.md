---
titulo: COMÉRCIO como empresas ligadas às profissões
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [koy]
atualizado: 2026-11-15
tags: [planejamento, comercio, profissoes, emprego, impostos]
relacionados: [../02-sistemas/expansao-dos-paineis/comercio-empresas-e-profissoes.md, ../02-sistemas/expansao-dos-paineis/alocar-comercio.md, ../02-sistemas/residencias/README.md, ../02-sistemas/governos/README.md, ADR-0003-codigo-prevalece-sobre-planilhas-de-design.md]
---

# ADR-0005 — COMÉRCIO como empresas ligadas às profissões

## Resumo

Aprovado o planejamento do sistema COMÉRCIO, com estabelecimentos adquiríveis, contratação de profissionais e Imposto de Comércio mensal definido pelo Rei da jurisdição. A aprovação autoriza esta documentação; a implementação continua planejada.

## Contexto

O planejamento anterior define cadastro comercial, portas e hospedagem, mas deixa em aberto aquisição e tributação. O dono do projeto apresentou o modelo de empresa, pediu expansão das consequências econômicas e de RP, revisou o nome do sistema de referência para Residência e confirmou a publicação em 15/11/2026.

## Opções consideradas

| Opção | Características | Limite |
|---|---|---|
| Manter apenas cadastro e hospedagem | Preserva o recorte da revisão 3 de 13/11 | Não documenta a diretriz de empresas, emprego e imposto mensal pedida pelo dono |
| Complementar com o modelo COMÉRCIO aprovado | Liga estabelecimentos, profissões, remuneração e política fiscal | Exige definições adicionais e implementação futura |

## Decisão

- Adotar o [modelo aprovado](../02-sistemas/expansao-dos-paineis/comercio-empresas-e-profissoes.md) como complemento do planejamento comercial.
- Usar **Residência** como referência estrutural de cadastro, titularidade e acesso, com regras comerciais próprias.
- Prever aquisição por jogadores independentemente de sua profissão, contratação de profissionais e remuneração negociada. A propriedade não concede profissões, ranks ou receitas.
- Prever Imposto de Comércio mensal definido pelo Rei responsável pela jurisdição, integrado à arrecadação e ao tesouro oficiais.
- Manter explicitamente em aberto compra/arrendamento, preços, limites, significado de mês, base e limites do imposto, salários, vínculos, permissões e inadimplência.
- Preservar a diferença entre diretriz aprovada, consequências possíveis de RP e funcionalidade implementada, conforme ADR-0003.

## Consequências

- (+) Cadeias de abastecimento, emprego e negociações entre empresários, trabalhadores, governos e Casas ganham uma referência comum.
- (+) Aquisição, cadastro, profissão e acesso a bens têm responsabilidades distintas.
- (−) Concentração de propriedade, falta de profissionais, dívidas e disputas salariais exigirão políticas de operação.
- (−) Integração futura terá de preservar bens de terceiros e contratos de hospedagem, sem copiar automaticamente penalidades residenciais.
- Esta decisão não define números econômicos, não implementa sindicatos/greves automáticos e não modifica o código ou a cobrança residencial existente.
