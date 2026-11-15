---
titulo: Registros detalhados e históricos de gestão das Guildas
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-15
tags: [guildas, registros, auditoria, lideranca]
relacionados: [../02-sistemas/guildas/README.md, ADR-0009-bibliotecarios-fabricacao-e-traducao.md]
---

# ADR-0010 — Registros detalhados de Guildas

## Resumo

Gravar quem fez a alteração, quem foi afetado e o que mudou, com nomes e IDs de perfil preservados no momento da ação.

## Contexto

O dev pediu que a aba Registros indique, por exemplo, quem foi nomeado para qual função e por quem, identificando ambos por nome e ID. O histórico anterior armazenava principalmente tipo de ação, autor, destinatário e data; não guardava todos os nomes de funções ou cargos.

## Decisão

1. O servidor captura nomes e IDs de perfil de autor e envolvidos no mesmo fluxo persistente da alteração. Nomes vêm do personagem conectado ou do cadastro confiável da guilda/convite; nomes de autor enviados pelo navegador não são autoridade.
2. Guardar uma descrição legível e os dados da ação no registro, sem depender de consultas futuras ao cargo ou ao membro. Renomear um cargo ou expulsar alguém não reescreve registros anteriores.
3. Registrar nomeações, revogações, substituições, mudanças de liderança, convites e respostas, inclusão e remoção de membros, cargos personalizados, professores/instrutores, escolas e mensagem da guilda.
4. Nas substituições, identificar o ocupante anterior. Na exclusão de cargo, listar os membros afetados; na expulsão, listar funções, cargos e escola removidos. Na mudança de mensagem e na renomeação de cargo, preservar o antes e o depois. Um pedido sem mudança efetiva deve ser descrito como tal.
5. Manter data e hora, restrição da aba à liderança, exibição dos 100 registros mais recentes por guilda e retenção existente de até 5.000 registros no estado global. A falha de persistência desfaz alteração e registro juntos.
6. Registros anteriores continuam intactos e identificados como antigos. Não inferir a função ou os nomes históricos que não foram armazenados. Os IDs de perfil e demais dados originais continuam disponíveis.
7. Renderizar nomes e mensagens como texto, com quebra de linhas longas. Não interpretar nomes ou mensagens dos jogadores como HTML.
8. Conforme esclarecimento do dev, não existe saída voluntária: membros não podem abandonar a guilda nem remover o próprio perfil. Somente a remoção autorizada pelo líder encerra essa participação e gera o registro correspondente. A tentativa recusada não deve constar como uma saída efetiva.

## Consequências

- A liderança passa a consultar frases completas sobre as alterações e os envolvidos, inclusive após mudanças posteriores de nomes ou cargos.
- A melhoria vale para novas ações; o histórico antigo não contém informação suficiente para reconstrução completa.
- Esta mudança não amplia permissões, não altera inventários ou fórmulas e não muda o contrato Companion do ritual de coleta. Registros de inventário continuam em seu diário próprio.
- A atualização privada por fonte suportada dispensa reinício e preserva os registros existentes. Não comprova homologação oficial ou aceite visual no jogo.
