---
titulo: Quartos, escolas e cronograma dos Magos
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-15
tags: [guildas, magos, quartos, escolas, cronograma]
relacionados: [../02-sistemas/guildas/README.md, ADR-0011-bancada-e-insignias-das-escolas.md, ADR-0012-portas-do-colegio-e-funcoes-incompativeis.md, ADR-0015-seletores-e-bancada-do-lider.md]
---

# ADR-0016 — Quartos, escolas e cronograma dos Magos

## Resumo

Cadastrar os dez quartos com cama do Hall of Attainment, usar somente seus compartimentos existentes, reservar o controle geral de portas e a retirada de escola ao Arquimago e apresentar aulas na Visão geral.

## Contexto

O dev confirmou dez quartos e esclareceu que professores devem abrir portas apenas por proximidade, com controle geral exclusivo do Arquimago. Pediu retirada de escola exclusiva dele, retirada de uma legenda de cargos, contador de bibliotecários e cronograma no lugar de seus nomes. Após a primeira instalação dos quartos, pediu explicitamente que não fossem acrescentados baús: nos quartos sem baú, aproveitar estantes ou outro compartimento existente.

## Opções consideradas

| Opção | Efeito |
|---|---|
| Criar novos baús nos quartos sem um | Rejeitada pelo pedido posterior do dev |
| Usar compartimentos existentes e preservar o conteúdo | Atende à disposição atual sem acrescentar móveis |
| Dar controle geral a professores | Contraria o esclarecimento confirmado |

## Decisão

1. A aba Quartos permite a professores atuais e ao Arquimago atribuir/liberar um membro por quarto e um quarto por membro. Cadastrar exatamente dez quartos, seis na entrada e quatro no andar inferior; conferir cama, porta e compartimento pelos registros vencedores dos plugins.
2. Reutilizar seis baús, três estantes e um armário. Não criar, mover nem substituir móveis. Na revisão privada, quatro baús recém-criados e ainda vazios foram desativados, mantendo referências e backup local; os conteúdos originais não foram apagados nem transferidos.
3. Portas de quarto exigem mesma instância e distância máxima de 220 unidades. Dono, professores e Arquimago podem ativar por perto. Outros alunos não podem ativar a porta. A tranca geral prevalece sobre todos.
4. Somente o Arquimago acessa Portas do Colégio e altera as políticas globais/individuais de suas 68 passagens. Esta autoridade substitui a parte do ADR-0012 que também a concedia aos professores. Não é uma barreira global contra teleporte.
5. Compartimento atribuído é privado do dono, inclusive para itens e septins. Professores não recebem acesso automático aos pertences. Enquanto livre, a administração pode abri-lo. Troca ou liberação é permitida mesmo com itens/septins: tudo permanece no compartimento. O novo dono recebe acesso ao conteúdo existente, e o anterior perde o acesso após a troca. Enquanto o antigo vínculo de quarto persistir, ex-membro pode retirar, mas não depositar. Esta regra posterior do dev substitui a proposta inicial de exigir esvaziamento.
6. Exibir o dono na porta e no nome do compartimento no cliente. Resolver referências nativas novamente em update; não reter handles entre quadros. A propriedade da porta inclui o compartimento estático correspondente para alcançar também móveis que não recebem atualização própria de vizinho.
7. Somente o Arquimago pode retirar o vínculo de escola pela aba Professores. A ação preserva o membro e suas funções, registra a escola anterior e revoga a insígnia pelo diário de inventário quando disponível. Professor pode atribuir escola a membro sem vínculo, mas não retirar. Isto substitui somente a antiga necessidade de expulsão para encerrar o vínculo do ADR-0011.
8. Na Visão geral, substituir o contador de cargos dos Magos pela quantidade de bibliotecários. Preservar a aba Cargos. No antigo cartão de nomes de bibliotecários, mostrar Cronograma de aulas a todos os membros. Somente o Arquimago adiciona/remove escola, dia semanal ou data específica e horário de Brasília (HH:mm). Validar no servidor, persistir e auditar; limite de 100 aulas, sem duplicata da mesma escola/dia/horário. Não criar notificações automáticas.
9. Retirar a legenda de função exclusiva indicada pelo dev sem remover restrições de cargos. Preservar a bancada do líder/bibliotecários da R24, livros autorais arquivados e separação da conjuração.

## Consequências

- A instalação privada R25 foi aplicada sem reiniciar jogo/servidor, com backup, inventários, escolas, registros e arquivos protegidos preservados. O cliente confirmou R25 e aplicação dos 20 nomes (dez portas e dez compartimentos), sem erro de nomes na checagem final.
- Teste nativo isolado, sem clientes humanos: 60 ativações de portas, depósitos e retiradas autorizadas de septins nos dez compartimentos, guardas contra acesso alheio e troca com conteúdo preservado aprovados. Não foi certificado pacote de retirada malformado: esse ensaio isolado foi interrompido e não foi usado no mundo privado.
- A suíte completa final tem 134 testes aprovados. Prévia/testes de interface não equivalem à travessia humana nem à homologação com dois clientes reais; esses aceites e o oficial permanecem pendentes.
