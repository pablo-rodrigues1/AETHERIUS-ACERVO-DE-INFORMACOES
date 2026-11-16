---
titulo: Baú exclusivo do Arquimago
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-15
tags: [guildas, magos, arquimago, inventario]
relacionados: [../02-sistemas/guildas/README.md, ADR-0016-quartos-escolas-e-cronograma-dos-magos.md]
---

# ADR-0018 — Baú exclusivo do Arquimago

## Contexto

O dev indicou o Chest à sua frente nos aposentos do Arquimago, pediu acesso particular do cargo e o nome Baú do Arquimago. A identificação pelo cliente e servidor confirmou o compartimento existente; não se tratava do Quarto 01 dos alunos.

## Decisão

- Usar o compartimento existente dos aposentos do Arquimago, separado dos dez quartos de alunos. Na instalação privada, referência estável `77d7e:Skyrim.esm`, célula `cab92:Skyrim.esm`.
- Configurar o ponto dos Magos com `leaderOnly: true` e `roleExclusive: true`. Abrir, depositar e retirar exigem o Arquimago atual, proximidade, mesma instância e inventário disponível. Professor, bibliotecário, membro, antigo líder e pessoa externa não têm acesso. Sem líder, o baú permanece inacessível.
- Vincular a permissão ao cargo, sem reivindicar outro baú pessoal ou migrar os bens existentes. Ao mudar a liderança, a autorização acompanha o cargo; os itens permanecem no compartimento físico.
- Apresentar Baú do Arquimago por uma propriedade de cliente persistente, com aplicação nativa em update e novas consultas de handles. Aplicar novamente após entrada/streaming; não alterar plugins nem controles/câmera.
- Preservar inventários e o cadastro de baús pessoais anteriores. Esta regra específica não amplia o acesso de professores aos pertences dos dez quartos.

## Evidência e limites

136 testes completos aprovados na autorização; quatro rechecagens de baús após conectar o nome. Teste pelo cliente real, usando o transporte existente: depositar um septim, fechar/reabrir o baú com o septim presente e retirar a mesma unidade aprovados, com os saldos iniciais restaurados. Nome nativo conferido no cliente, com conteúdo preservado e sem reinício. Troca de liderança e recusas cobertas nos testes automatizados; homologação oficial e dois clientes reais permanecem pendentes.
