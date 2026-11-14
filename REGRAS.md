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

## 10. Integração contínua com o desenvolvimento (obrigatório para IAs)

Detalhes operacionais e o bloco de instruções a instalar estão em [`05-processos/integracao-ia.md`](05-processos/integracao-ia.md).

### 10.1 Instalação da regra no projeto do dev
Quando um dev vincular este acervo à sua IA, a IA **deve**, no projeto em que estiver trabalhando:
- **Se o projeto NÃO tiver `AGENTS.md`:** criar o arquivo com o bloco padrão de integração.
- **Se o projeto JÁ tiver `AGENTS.md`:** **perguntar ao dev** antes de alterar: *"Seu projeto já tem um AGENTS.md. Posso inserir nele as instruções de envio ao Acervo Aetherius?"* Só inserir após um "sim". Se o dev recusar, seguir as regras apenas na sessão atual e registrar isso no diário do sistema.
- Se existir `CLAUDE.md`, `.cursorrules` ou equivalente, aplicar a mesma lógica (perguntar antes de alterar arquivo existente).

### 10.2 Envio contínuo
- Envio por **commit direto na branch `main`**, com mensagens no formato `docs(<sistema>): <resumo>`.
- Antes de cada envio: `git pull --rebase` para evitar conflitos com outros devs/IAs.
- Enviar sempre que houver: início de um sistema, decisão relevante, mudança de fórmula/parâmetro, entrega concluída, bloqueio e, no mínimo, ao fim de cada sessão de trabalho.
- Cada envio atualiza: `diario.md` do sistema, o quadro de status em `02-sistemas/README.md` e, se aplicável, `README.md` do sistema, ADRs, `CHANGELOG.md` e `INDICE.md`.

### 10.3 Falha de acesso
Se a IA **não conseguir ler ou enviar** ao acervo (sem acesso de escrita, sem rede, erro de autenticação, conflito não resolvido), ela **deve avisar o dev imediatamente**, explicando o erro, e entregar os arquivos `.md` prontos para que ele envie manualmente. Nunca falhar em silêncio.

### 10.4 Repositórios isolados
Se o sistema é desenvolvido em repositório próprio:
- O frontmatter do sistema traz `repositorio: <URL>` e o `README.md` do sistema tem a seção `## Onde está o código` com o link.
- O README do repositório isolado deve conter um link de volta para a documentação no acervo.

### 10.5 Consultar antes de codar
Antes de iniciar ou alterar um sistema, a IA lê `INDICE.md`, o sistema afetado, seus dependentes e os ADRs vigentes. Se o código for contrariar um ADR aceito, avisa o dev e propõe um novo ADR antes de seguir.

### 10.5.1 Sistema já existente ou em andamento por outra pessoa
Se o dev tentar criar, desenvolver ou alterar um sistema que **já existe no acervo** (mesmo nome, mesma função ou sobreposição clara de escopo), a IA **deve parar antes de escrever código ou documentação** e:
1. **Explicar ao dev** o que já existe: link do sistema no acervo, `fase`, `responsaveis`, `repositorio`, data da última atualização e as entradas mais recentes do `diario.md`.
2. **Pedir confirmação explícita**: *"O sistema <nome> já está no acervo, sob responsabilidade de <responsáveis> (fase: <fase>, última atualização: <data>). Deseja continuar mesmo assim, sabendo que outra pessoa está atuando nele?"*
3. Só prosseguir após um "sim". Se o dev confirmar:
   - adicioná-lo em `responsaveis` e registrar no `diario.md` que ele passou a atuar no sistema e com qual objetivo;
   - recomendar que ele combine a divisão do trabalho com os demais responsáveis.
4. Se o dev desistir, não alterar nada e, se útil, sugerir sistemas relacionados ou outra frente de trabalho.

Esta verificação vale também quando o sistema está documentado como `planejado` ou foi apenas mencionado em um ADR.

### 10.6 Decisões, dependências e changelog
- Toda escolha relevante durante o desenvolvimento vira ADR automaticamente.
- Cada sistema declara `depende_de:` no frontmatter. Ao alterar um sistema, a IA verifica quem depende dele e registra o possível impacto no diário dos dependentes.
- Entregas concluídas geram linha no `04-historico/CHANGELOG.md`.

### 10.7 Segurança no envio automático
Nunca enviar tokens, senhas, IPs de produção, dados de jogadores ou trechos de `.env`. Na dúvida, omitir e perguntar ao dev.

### 10.8 Documentos parados
Sistemas com `fase: em-desenvolvimento` sem atualização há mais de 30 dias são sinalizados semanalmente por uma issue automática (`.github/workflows/documentos-parados.yml`).
