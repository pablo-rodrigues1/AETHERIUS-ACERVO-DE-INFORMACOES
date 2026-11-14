---
titulo: Painel de Casas para portões da cidade
tipo: referencia
status: ativo
responsaveis: []
atualizado: 2026-11-14
tags: [planejamento]
relacionados: [README.md]
fonte: Planejamento dos painéis de 13/11/2026, revisão 3
---

# Painel de Casas para portões da cidade

## Resumo

Controle de portões municipais por jurisdição de Rei e Lorde. O escopo é planejado. Os [contratos compartilhados](README.md) descrevem as dependências dos cinco projetos.

## Distinção em relação aos castelos

A entrega REIS1 já prevê controle das portas dos nove castelos. As 28 passagens documentadas em 11/11 são de interiores dos castelos e não são um catálogo de portões urbanos. Criar um levantamento próprio de portões, preservando a aba/ações dos castelos. Não copiar esses IDs para a nova função.

Para cada cidade, identificar portão principal, folhas de porta/grades, entradas e destinos, sentidos de circulação, referências vinculadas, áreas de aproximação e eventuais passagens alternativas. Uma cidade sem portão físico utilizável deve aparecer como não configurada; o projeto não cria uma barreira invisível ou bloqueia todo o território.

## Interface e fluxo

No painel de Casas, adicionar **Portões da cidade** com município, estado de cada portão, última alteração e ação **Trancar/Destrancar**. Comando para todos os portões é uma melhoria proposta e só se aplica ao conjunto validado da própria cidade.

O servidor resolve a cidade governada pelo operador, verifica cargo vigente, revisão do estado e referências autorizadas, aplica a alteração e registra resultado. A tela distingue comando solicitado, confirmado e falha; não mostra sucesso apenas por alterar o ícone. Reabrir ou reconectar consulta o estado autoritativo.

## Autoridade e política de passagem

**Escopo atualizado na revisão 3:** deixar a função de afiliação a reino fora da entrega atual do painel do Lorde. Ela será tratada futuramente junto do ritual de coleta: cidade afiliada recebe o buff do reino correspondente; cidade sem afiliação não recebe. Isso não exige afiliação para o Lorde controlar os portões da própria cidade nem cria autoridade do Rei sobre portões de cidades afiliadas. Usar o vínculo oficial de governo local para os portões; o vínculo futuro cidade–reino tem finalidade de elegibilidade ao buff neste planejamento.

Rei e Lorde são papéis políticos do cadastro oficial. Definir o alcance de cada um: sua cidade, capital, cidades subordinadas e eventual precedência do Rei sobre ordens do Lorde. O pedido não autoriza estender a ação automaticamente a Mão, guarda, coletor ou staff sem regra específica.

A aba Lords está vazia na entrega administrativa REIS1. Antes de ativar para Lordes, comprovar como são nomeados e vinculados à cidade; se necessário, entregar esse vínculo como pré-requisito separado. A existência de um cargo nominal no cliente não prova autoridade.

É preciso definir se “trancar” bloqueia somente entrada ou ambos os sentidos, quem pode atravessar quando fechado, se o comando exige proximidade e como tratar cerco/guerra. Proposta inicial: bloquear entrada de não autorizados e conservar saída, com intervenção S+ auditada para recuperação. Não implementar aprisionamento involuntário ou exceções universais de guarda sem decisão.

Definir a transição quando a passagem já começou. Validar antes de conceder entrada e não desfazer um teleporte já confirmado de modo que o personagem fique entre células. Fechar portão não deve alterar inventário, combate ou posição de quem já está dentro da cidade.

## Runtime e persistência

Reutilizar os padrões de `castles.js`, governo/cargos, registros e ponte Core, sem assumir que portão urbano tem o mesmo comportamento de porta interior. Alguns portões podem depender de animação, ativador, múltiplas referências, scripts ou teleporte. Cada tipo exige contrato de bloqueio comprovado no servidor e sincronização nos clientes.

Validar tentativa por E, lockpick/chave nativa, scripts, entradas alternativas e pedidos forjados. Não bloquear outros teletransportes do jogo sem escopo explícito. O critério é impedir o acesso pelo conjunto de portões configurado, com o alcance documentado.

Persistir estado desejado, revisão, operador e resultado observado; reconciliar na inicialização. Um comando em lote precisa de resultados por referência e estratégia de recuperação. Troca de governo revoga controle do líder anterior; definir se o estado físico é mantido ou reinicializado na sucessão, sem abrir tudo por ausência temporária de sessão.

## Etapas

1. Validar autoridade de Lordes/Reis e política de passagem.
2. Mapear portões da base oficial recebida, com proveniência de plugins e validação dos tipos de referência. Usar houseCARL e skills específicas para leituras/alterações de dados nativos necessárias.
3. Implementar cadastro e serviço de portas por jurisdição; testar fronteiras e conflito com castelo/residência/comércio.
4. Integrar a tela e registros, persistência e recuperação.
5. Entregar pacote e homologar com dois clientes e papéis diferentes.

## Aceite

| Cenário | Resultado esperado |
| --- | --- |
| Rei/Lorde com vínculo correto | Altera somente portões do escopo autorizado |
| Cargo revogado ou cidade diferente | Pedido negado, inclusive depois de abrir a tela |
| Castelos e portões coexistem | Estados e listas independentes |
| Entrada em andamento e fechamento | Transição consistente, sem personagem preso |
| Pedido direto, lockpick ou script | Respeita a política nas rotas cobertas |
| Reinício e reconexão | Estado reconciliado e visível aos clientes |
| Falha em uma folha do portão | Falha parcial identificada, sem sucesso genérico |
| Referência removida ou mod alterado | Operação suspensa para o portão afetado |

O pacote deve incluir catálogo separado, política aprovada de travessia, migração preservadora e procedimento de recuperação. Desativar comandos não apaga a decisão sobre portões já trancados; definir como reconciliá-los antes de retirar o serviço.
