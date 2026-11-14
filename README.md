# AETHERIUS — ACERVO DE INFORMAÇÕES

Repositório central de documentação técnica do servidor **Aetherius** (SkyMP): descrição dos sistemas, decisões de design e histórico de mudanças.

> **Usa IA no desenvolvimento?** Vincule este acervo a ela: a IA seguirá [`AGENTS.md`](AGENTS.md) e manterá a documentação do seu sistema atualizada automaticamente.
>
> **Antes de contribuir, leia [`REGRAS.md`](REGRAS.md).** Toda IA ou pessoa que consulta este acervo deve começar por [`INDICE.md`](INDICE.md).

## Estrutura

| Pasta | Conteúdo |
|---|---|
| `00-guia/` | Como usar o acervo, glossário, modelos de documento |
| `01-visao-geral/` | Visão do projeto, arquitetura geral, repositórios |
| `02-sistemas/` | Um subdiretório por sistema (ex.: `combate/`, `armadura/`, `classes/`) |
| `03-decisoes/` | ADRs — registros de decisão, numerados e imutáveis |
| `04-historico/` | Changelog consolidado e notas de versão |
| `05-processos/` | Fluxos de trabalho do time (deploy, revisão, testes) |
| `06-referencias/` | Material de apoio externo resumido em .md (SkyMP, Papyrus, APIs) |
| `_modelos/` | Templates obrigatórios para novos documentos |

## Início rápido

1. Abra [`INDICE.md`](INDICE.md) para localizar o que procura.
2. Para documentar um sistema novo: copie `_modelos/SISTEMA.md` para `02-sistemas/<nome-do-sistema>/README.md` e `_modelos/DIARIO.md` para `diario.md` na mesma pasta.
3. Para registrar uma decisão: copie `_modelos/DECISAO.md` para `03-decisoes/ADR-NNNN-<titulo>.md`.
4. Atualize o `INDICE.md` no mesmo commit.
