---
titulo: Quartos sem depósito de septins
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-17
tags: [guildas, magos, quartos, inventario]
relacionados: [ADR-0016-quartos-escolas-e-cronograma-dos-magos.md, ../02-sistemas/guildas/escopo.md]
---

# ADR-0024 — Quartos sem depósito de septins

## Resumo

Recusar novos depósitos de septins nos dez compartimentos dos quartos dos Magos e preservar todos os conteúdos existentes.

## Contexto

Ao responder à pergunta sobre depósito no quarto temporário, o dev esclareceu que não deve ser possível depositar septins e relatou novamente painel/porta sem abrir. Depois definiu que fará os testes no Skyrim por conta própria.

## Opções consideradas

| Opção | Avaliação |
|---|---|
| Apenas esconder o ouro no cliente | Não protege requisições diretas |
| Apagar moedas existentes | Perde pertences e não foi solicitado |
| Validar depósito no servidor e preservar estoque | Atende ao esclarecimento sem retirar itens |

## Decisão

- onPutItem recebe todos os argumentos e recusa o item Gold/Septim do Skyrim.esm nos compartimentos cadastrados de quartos, para qualquer cargo autorizado a usá-los.
- Depósitos de itens comuns mantêm as guardas atuais de dono, proximidade, instância, sessão e inventário. Moedas existentes podem ser retiradas por quem já tem acesso; nada é apagado ou migrado.
- Troca de dono continua permitida com conteúdo. Restrição não altera o baú coletivo, o Baú do Arquimago, bancos ou outras guildas.
- Isto substitui apenas a permissão de novos depósitos de septins do item 5 do ADR-0016; não altera acesso às portas ou gestão dos quartos.
- Os testes no Skyrim são realizados pelo dev, conforme instrução mais recente. Não operar mouse/teclado ou menus para validá-los automaticamente.

## Consequências

Comparações antigas que esperavam depósito de ouro precisam ser atualizadas. Preservar provas históricas e separar unitários de confirmação humana. Correções instaladas não significam relato de painel/porta resolvido sem o retorno do dev.
