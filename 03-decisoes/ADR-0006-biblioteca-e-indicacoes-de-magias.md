---
titulo: Biblioteca dos Magos e indicações de magias
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-15
tags: [guildas, biblioteca, classes, interface]
relacionados: [../02-sistemas/guildas/README.md, ADR-0003-codigo-prevalece-sobre-planilhas-de-design.md]
---

# ADR-0006 — Biblioteca dos Magos e indicações de magias

## Resumo

A aba de produção de tomos passa a se chamar Biblioteca. A antiga aba Biblioteca sai da navegação. Cada tomo indica suas classes, categorias e efeito base a partir das fontes já autorizadas e carregadas.

## Contexto

O dev solicitou uma única Biblioteca na bancada e indicações das magias para cada classe. Confirmou a continuidade do trabalho de Guildas por Melekyy. A política atual já relaciona as magias às classes e categorias; os plugins carregados definem o efeito.

## Opções consideradas

| Opção | Efeito |
|---|---|
| Manter duas abas com nomes semelhantes | Mantém navegação redundante |
| Renomear a produção e exibir metadados das fontes existentes | Atende ao pedido e preserva as regras autoritativas |
| Usar a planilha para ampliar ou redefinir as magias | Mistura apresentação com regras que não foram solicitadas |

## Decisão

1. Manter o funcionamento da produção na bancada e trocar apenas seu nome por Biblioteca; retirar a aba antiga.
2. Mostrar classe e categoria pela política autorizada existente, incluindo todas as associações de uma magia.
3. Consultar descrições do feitiço e dos efeitos nos registros dos plugins carregados. Indicar valores como base; bônus e modificadores de personagem não são calculados pela interface.
4. Quando a descrição estiver ausente ou for uma chave de localização não resolvida, mostrar o nome do efeito ou informar sua indisponibilidade. Não inventar descrições.
5. Preservar receita, autorização do bibliotecário, nomeação pela liderança, catálogo, dados e proximidade da bancada. Não alterar o Core, a conjuração ou as regras de aprendizado.

## Consequências

- A Biblioteca passa a identificar classes e categorias e aceita sua busca por nome.
- As descrições seguem o idioma disponível no plugin. Chaves de localização sem texto não são exibidas como conteúdo.
- A instalação privada e os testes automatizados não representam homologação no servidor oficial.
