---
titulo: Painel administrativo
tipo: sistema
status: ativo
responsaveis: []
atualizado: 2026-11-14
tags: [painel-adm]
relacionados: []
fase: em-teste
repositorio: https://github.com/eksmurf/painel-oficial
depende_de: [../loja/README.md, ../residencias/README.md]
---

# Painel administrativo

## Resumo

Operações de staff pelo F7, auditoria, inventário, PD e registro de compras.

## Onde está o código

A fonte de desenvolvimento é o repositório [painel-oficial](https://github.com/eksmurf/painel-oficial). A revisão documentada é **2026.11.13.1**. A instalação no servidor oficial depende da integração e homologação pela equipe responsável.

## Entregas por revisão

| Data | Entrega preparada |
|---|---|
| 26–31/10 | Clima, calendário, hora, Septims, janela e reset da aparência. |
| 03–06/11 | Paleta, foco, reabertura do painel por F7, auditoria de itens e Septims e histórico por personagem. |
| 08/11 | PD por etapas, nova tentativa de vínculo no login, nomes e remoção de itens, cadastro de residência pela porta. |
| 13/11 | Registro de compras em Diademas sobre a base 2026.11.08.6. |

Na perda definitiva de personagem (PD), a staff localiza a ficha, revisa a seleção e informa o motivo. O servidor revalida a autorização e se o jogador continua offline antes de confirmar.

## Registro de compras

ADM e Superadministrador consultam recibos confirmados da Loja por perfil histórico, produto e período, inclusive para jogadores offline. A tela informa produto, data e hora de Brasília e Diademas pagos, com 25 registros por página. A fonte é a operação de compra em Diademas no histórico da Loja. Recompensas creditadas e pacotes de Diademas adquiridos no site têm outros registros. A aba é somente leitura.

## Sessão e integração

A entrega de foco pausa o painel e revalida o acesso ao retornar. A continuidade após o vencimento de 15 minutos é um [projeto separado](continuidade-da-sessao.md) que depende do contrato de renovação e revogação do login. O espelho de clima de 26/10 usa transporte simulado. Tanto os deltas do painel quanto os assets distribuídos precisam ser conferidos com a base efetivamente instalada.

## Diário

[Histórico da entrega e pendências](diario.md)
