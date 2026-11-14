# Regras do Acervo

Estas regras existem para que o acervo seja **legível por humanos e consultável por IAs com o mínimo de tokens e ambiguidade**. São obrigatórias.

## 1. Formatos permitidos

- **Somente arquivos `.md`** (Markdown, UTF-8, quebras de linha LF).
- **Proibido:** `.pdf`, `.docx`, `.xlsx`, `.pptx`, `.odt`, `.txt`, `.html`, arquivos compactados e binários em geral.
- **Exceção única:** imagens `.png`/`.svg` em `<pasta>/assets/`, **apenas** quando um diagrama não puder ser expresso em [Mermaid](https://mermaid.js.org/) ou tabela. Toda imagem deve ter descrição textual completa no `.md` que a referencia (IAs não leem imagens de forma confiável).
- Material externo (PDF de terceiros, planilha, vídeo) deve ser **resumido em .md** em `06-referencias/`, com link para a fonte original.
- O CI bloqueia automaticamente formatos não permitidos (`.github/workflows/validar-acervo.yml`).

## 2. Nomes de arquivos e pastas

- `kebab-case`, minúsculas, sem acentos, sem espaços: `sistema-de-armadura.md`.
- Pastas de topo numeradas (`00-` a `06-`) — não criar novas sem ADR.
- Cada pasta de sistema tem um `README.md` como ponto de entrada.
- ADRs: `ADR-NNNN-titulo-curto.md` (4 dígitos, sequencial, nunca reutilizar número).

## 3. Cabeçalho obrigatório (frontmatter)

Todo `.md` (exceto `README.md` da raiz, `REGRAS.md`, `INDICE.md`) começa com:

```yaml
---
titulo: Sistema de Armadura
tipo: sistema            # sistema | decisao | processo | referencia | historico | guia
status: ativo            # rascunho | ativo | obsoleto | substituido
responsaveis: [nome1, nome2]
atualizado: 2026-11-14   # AAAA-MM-DD
tags: [combate, atributos]
relacionados: [../../03-decisoes/ADR-0001-formula-de-armadura.md]
---
```

O frontmatter permite que uma IA filtre arquivos sem ler o corpo.

## 4. Estrutura do conteúdo

- **Um assunto por arquivo.** Se passar de ~400 linhas, divida.
- Comece com um **resumo de 1 a 3 frases** logo após o título (seção `## Resumo`). Uma IA deve conseguir decidir se o arquivo é relevante lendo só isso.
- Use títulos hierárquicos (`#`, `##`, `###`) sem pular níveis.
- Prefira **tabelas e listas** a parágrafos longos para dados (valores, fórmulas, parâmetros).
- Fórmulas em bloco de código ou LaTeX inline, sempre com legenda das variáveis.
- Código: blocos com linguagem declarada (```ts, ```papyrus, ```json).
- Diagramas: **Mermaid** em bloco de código.
- Links internos **relativos** (`../02-sistemas/combate/README.md`), nunca URLs absolutas do GitHub.
- Não duplicar informação: referencie o documento-fonte com link.

## 5. Decisões (ADRs)

- Toda mudança relevante de design ou arquitetura gera um ADR em `03-decisoes/`.
- ADRs **aceitos são imutáveis**. Para mudar uma decisão, crie um novo ADR com `substitui: ADR-NNNN` e altere o status do antigo para `substituido` (única edição permitida).
- O documento do sistema afetado deve linkar o ADR em `relacionados`.

## 6. Índice

- `INDICE.md` lista **todos** os documentos com caminho + resumo de uma linha.
- Todo commit que cria, move ou remove um documento atualiza o `INDICE.md`.

## 7. Conteúdo proibido

- Senhas, tokens, chaves de API, IPs privados de produção, dados pessoais de jogadores.
- Prints de conversas como fonte de decisão — transcreva o essencial no ADR.

## 8. Fluxo de contribuição

1. Crie uma branch: `docs/<assunto>`.
2. Use o modelo correspondente de `_modelos/`.
3. Abra um Pull Request; o CI valida formato e frontmatter.
4. Ao menos 1 revisão do time antes do merge.

## 9. Instruções para IAs

IAs que consultam este acervo devem:

1. Ler `INDICE.md` primeiro e abrir apenas os arquivos relevantes.
2. Usar o frontmatter (`status`, `tags`, `atualizado`) para filtrar; ignorar documentos `obsoleto`/`substituido` salvo para histórico.
3. Considerar o ADR mais recente como fonte de verdade em caso de conflito.
4. Ao gerar documentação nova, seguir estritamente estas regras e os modelos de `_modelos/`.
