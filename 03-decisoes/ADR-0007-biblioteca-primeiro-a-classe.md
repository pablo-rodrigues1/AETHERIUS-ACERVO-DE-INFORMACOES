---
titulo: Biblioteca primeiro por classe e depois por tomo
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-15
tags: [guildas, biblioteca, classes, navegacao]
relacionados: [ADR-0006-biblioteca-e-indicacoes-de-magias.md, ../02-sistemas/guildas/README.md]
---

# ADR-0007 — Biblioteca: primeiro a classe

## Resumo

A Biblioteca apresenta primeiro as classes. O jogador escolhe uma classe para consultar seus tomos e pode retornar à lista de classes.

## Contexto

Na continuação do ajuste da Biblioteca, o dev esclareceu que deseja classe antes de tomo, em vez de uma lista de tomos com etiquetas de classe. A origem dos dados e as regras do ADR-0006 continuam vigentes.

## Opções consideradas

| Opção | Efeito |
|---|---|
| Tomos em uma lista geral com etiquetas | Não atende à ordem solicitada |
| Seleção de classe seguida dos seus tomos | Organiza a navegação conforme o pedido |

## Decisão

1. Exibir cartões de classes com quantidade de tomos e abrir a lista apenas após a escolha.
2. Construir os grupos pela política autorizada e contar cada tomo uma vez por classe, mesmo que tenha várias categorias nessa classe.
3. Filtrar a classe antes da paginação. Busca, plugin, categoria e efeito permanecem disponíveis dentro da classe.
4. Manter o botão Todas as classes e preservar receita, permissões, proximidade e catálogo.
5. Preservar a seleção na sessão de cada jogador; a atualização privada não deve reiniciar o jogo ou alterar os dados persistidos.

## Consequências

- A entrada da Biblioteca deixa de exibir uma lista geral de tomos.
- Um tomo compartilhado pode aparecer nas classes autorizadas correspondentes.
- A alteração é de navegação e não muda quem pode aprender ou fabricar a magia.
