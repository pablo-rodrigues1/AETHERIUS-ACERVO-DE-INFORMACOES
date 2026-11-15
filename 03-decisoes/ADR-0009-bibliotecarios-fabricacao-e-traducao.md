---
titulo: Vários bibliotecários, fabricação com duração e tradução da Biblioteca
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-15
tags: [guildas, biblioteca, permissoes, traducao]
relacionados: [../02-sistemas/guildas/README.md, ADR-0006-biblioteca-e-indicacoes-de-magias.md, ADR-0007-biblioteca-primeiro-a-classe.md]
---

# ADR-0009 — Bibliotecários, fabricação e tradução

## Resumo

Permitir vários bibliotecários nomeados pelo líder, apresentar fabricação de tomos com duração e traduzir a Biblioteca para português.

## Contexto

O dev solicitou vários bibliotecários com fabricação exclusiva, animação ao fabricar, três integrantes fictícios no painel privado e correção do título da classe. Acrescentou a tradução dos nomes e descrições das magias.

## Decisão

1. Migrar o bibliotecário anterior para uma lista, preservando a nomeação. Somente o líder adiciona ou revoga bibliotecários; cada revogação individual preserva os demais. Expulsão remove a função. Cargos personalizados não concedem acesso.
2. Somente bibliotecários nomeados fabricam tomos. Um líder precisa receber também essa função para fabricar. Manter uma escola por estudante e um professor por escola.
3. Cada lote leva 3.000 ms. O servidor valida identidade, sessão, função, proximidade da bancada e materiais antes e durante a fabricação. Na conclusão, o diário de inventário realiza a entrega e o débito uma única vez: 5 papéis e 1 pedra da alma por tomo.
4. Fechar, cancelar, sair de perto, desconectar, trocar sessão, perder a função ou ficar sem os materiais interrompe o lote sem débito pela fabricação. Um jogador não inicia dois lotes simultâneos; bibliotecários diferentes podem trabalhar ao mesmo tempo.
5. Exibir progresso, livro animado e cancelamento na interface. O cliente solicita uma animação nativa de leitura de livro durante a fabricação, em atualização segura, sem alterar câmera ou controles. A propriedade é distribuída ao dono e aos vizinhos; confirmar aparência com clientes reais separadamente.
6. Traduzir títulos e descrições dos 104 tomos autorizados no painel, por identificador estável de magia. Preservar BOOK/SPEL, plugins, efeitos, requisitos e política de classes. A busca aceita português e o nome original. Não modificar nomes dos itens nos plugins ou no inventário nativo.
7. Usar os textos e valores disponíveis nos registros instalados. Não inventar percentuais, alcance ou custo ausentes; descrever o efeito sem esses valores ou indicar a limitação. A tradução de Expulsar daedra usa a identificação de banimento do efeito, pois o texto original é uma referência localizada indisponível ao leitor atual.
8. Separar o título da classe e o botão de retorno com espaçamento próprio. Os três integrantes fictícios são dados de teste exclusivos do privado, identificados no painel, sem criação de contas ou personagens no mundo.

## Consequências

- Nomeação e fabricação permanecem autoritativas no servidor; o progresso visual não entrega itens.
- A tradução é uma camada de apresentação para o catálogo autorizado atual. Revisões de mods ou da política exigem revisar cobertura e descrições.
- A atualização não reativa livros autorais nem altera conjuração, baús, Grupo, Reis ou o contrato do ritual de coleta.
- Homologação no oficial e verificação visual da animação por dois jogadores continuam necessárias.
