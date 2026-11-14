---
titulo: Sincronização dos Cores com os repositórios oficiais
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [mercurius17]
atualizado: 2026-11-14
tags: [gameplay-core, ui-core, sincronizacao]
relacionados: [../02-sistemas/gameplay-core/README.md, ../02-sistemas/ui-core/README.md, ../05-processos/sincronizacao-dos-cores.md]
---

# ADR-0002 — Sincronização dos Cores com os repositórios oficiais

## Resumo
As cópias dentro de `aetherius-server` e `aetherius-client` são a versão mais atual do GameplayCore e do UI Core e voltam para os repositórios de origem arquivo a arquivo, com quatro exceções fixas: arquivos em que o Core está à frente, acoplamentos a código de fora do Core, catálogos assinados e módulos de outros sistemas.

## Contexto
O GameplayCore roda como cópia em `server/gameplay/core` e o UI Core em `client/ui-core`. Os mantenedores do servidor e do cliente alteram essas cópias. Os repositórios `pablo-rodrigues1/AetheriusGameplayCore` e `pablo-rodrigues1/AetheriusUI_Core` precisam receber essas mudanças sem perder o que só existe neles e sem passar a depender do resto do servidor ou do cliente.

## Opções consideradas
| Opção | Prós | Contras |
|---|---|---|
| Espelhar a cópia inteira | simples | desfaz mudanças em que o Core está à frente (DS-7, correção `41e8334`); quebra o build isolado; traz módulos de terceiros e GPL |
| Comparar arquivo a arquivo, com exceções fixas | preserva o Core e o build isolado | exige revisar a direção de cada diferença |

## Decisão
Comparar arquivo a arquivo e trazer a versão da cópia oficial, exceto:

1. **Core à frente:** quando a cópia oficial é mais antiga que o Core (ex.: `ds5a/Host.h` sem `hitSeq`), fica a versão do Core.
2. **Acoplamento externo:** linhas que importam código de fora do Core são omitidas (ex.: `export * as professionApi from '../../../professions/src/index.ts'`). Testes que localizam o cliente/servidor seguem a convenção de caminho do repositório de destino.
3. **Catálogos (`catalog/*.json`):** não são reassinados para os masters do servidor oficial (decisão do dono em 14/11/2026, mantendo a de 02/11).
4. **UI Core, só o framework:** shell, chargen, bridge nativa, inventário/feitiços/mapa, tela de carregamento e fontes. Ficam no cliente: profissões, loja, casas, roda de gestos, refino, baú e propriedades. Ganchos inertes dessas telas no `shell.js` e na bridge podem vir junto.
5. **Migrations** de outros sistemas que vivem na pasta do Core (ex.: `0013`/`0014` de profissões) entram, para a numeração não colidir; segue-se o fim de linha do `.gitattributes` do Core (LF).

## Consequências
- (+) Os repositórios de origem ficam atualizados sem regredir e continuam compilando isolados.
- (+) Cada sincronização deixa uma nota no próprio repositório e uma entrada no diário do sistema no acervo.
- (−) A migration `0013` tem bytes diferentes no servidor (CRLF) e no Core (LF); o hash em `gc_schema_migrations` diverge entre as duas origens.
- (−) Enquanto os catálogos não forem reassinados, testes que dependem do catálogo v0.37.0 (ex.: Anti-Mago com Maestria em Conjuração) não são exercitados no Core.
