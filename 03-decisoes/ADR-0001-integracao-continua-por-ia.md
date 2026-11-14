---
titulo: Integração contínua do desenvolvimento com o acervo via IA
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: []
atualizado: 2026-11-14
tags: [processo, ia, documentacao]
relacionados: [../REGRAS.md, ../05-processos/integracao-ia.md]
---

# ADR-0001: Integração contínua do desenvolvimento com o acervo via IA

## Resumo
As IAs dos devs instalam uma regra de documentação nos projetos e enviam atualizações contínuas ao acervo por commit direto na `main`.

## Contexto
A documentação tende a ficar defasada em relação ao código, principalmente com sistemas em repositórios isolados.

## Opções consideradas
| Opção | Prós | Contras |
|---|---|---|
| Commit direto na `main` | Acervo sempre atualizado, sem atrito | Sem revisão prévia; risco de conflito |
| Branch + Pull Request | Revisão humana | Atualizações atrasam até o merge |

## Decisão
- Commit direto na `main`, com `git pull --rebase` antes de cada envio.
- A IA cria `AGENTS.md` no projeto se não existir; se existir, pergunta ao dev antes de alterar.
- Falha de leitura/envio deve ser comunicada ao dev.
- Sistemas em repositórios isolados registram o link do repositório.
- Adotados também: quadro de status, diário por sistema, ADR automático, consulta prévia, mapa de dependências, changelog por entrega e alerta de documentos parados.

## Consequências
- (+) Documentação acompanha o desenvolvimento em tempo real.
- (−) Erros entram direto na `main`; correção via novo commit e histórico do git.
