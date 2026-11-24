---
titulo: Livros comuns e catálogo dos bibliotecários
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-23
tags: [guildas, biblioteca, livros, permissoes]
relacionados: [../02-sistemas/guildas/README.md, ../02-sistemas/guildas/escopo.md, ADR-0015-seletores-e-bancada-do-lider.md]
---

# ADR-0033 — Livros comuns e catálogo dos bibliotecários

## Resumo

Reativar os livros comuns na bancada dos Magos para todos os membros e reservar o catálogo/fabricação de tomos mágicos exclusivamente aos bibliotecários atuais.

## Contexto

O dev pediu o retorno dos livros em branco na Biblioteca e determinou que o restante dos membros não veja livros mágicos ou classes. Essa instrução mais recente altera o acesso do Arquimago aos tomos previsto no ADR-0015; mantém as regras de seletores, funções incompatíveis e administração.

## Opções consideradas

| Opção | Prós | Contras |
|---|---|---|
| Esconder apenas os botões mágicos | Mudança pequena | Ainda transmite catálogo e permite pedidos forjados |
| Liberar o catálogo ao líder também | Mantém regra anterior | Contraria a nova exclusividade dos bibliotecários |
| Separar dados e ações por função no servidor | Cumpre privacidade e receita | Requer revalidar permissão após revogação e ao concluir fabricação |

## Decisão

- A bancada do Arcanaeum abre para todos os membros atuais dos Magos. A aba Biblioteca fabrica livro em branco por **5 papéis e 1 carvão**. Fora da bancada não há aba de fabricação no painel Y.
- Somente bibliotecários recebem o catálogo e a aba Livros de magia: primeiro classe, depois seus tomos. Arquimago, professores e membros sem função de bibliotecário não veem, não consultam e não fabricam tomos, mesmo enviando ações manualmente. A regra permanece no servidor, além da interface.
- Cada tomo continua custando **5 papéis e 1 pedra da alma**, com duração/animação e revalidação antes de cobrar. Revogar a função interrompe a autorização.
- Livros comuns reusam o editor nativo já existente: abrir pelo inventário, primeiro leitor como autor, título, assinatura definitiva e leitura posterior. Não há editor/prévia no painel nem botão Nova página. Enter alterna escrita/cursor; navegação do livro é do menu nativo. Exemplar original tem identidade própria; cópias da mesma obra podem agrupar.
- Fazer cópia de obra assinada continua exclusivo de bibliotecário e custa **5 papéis e 2 septins**. Conteúdo e título persistem; ninguém edita depois de assinar.
- Não conceder outro cargo ao Arquimago para contornar a exclusividade, não reinstalar conjuração ou quartos, não sobrescrever documentos/inventários antigos com backups históricos.

## Consequências

O adaptador nativo do cliente e os contratos reais de inventário são necessários. Testes de backend não certificam câmera, cursor, fonte, página ou estabilidade no Skyrim; essa conferência continua com o dev. Arquivos de origem e estado dos jogadores ficam privados.
