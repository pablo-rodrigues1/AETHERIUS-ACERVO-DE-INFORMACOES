---
titulo: Banco de Cyrodiil e empréstimos aos Reis
tipo: referencia
status: ativo
responsaveis: []
atualizado: 2026-11-14
tags: [planejamento]
relacionados: [README.md]
fonte: Planejamento dos painéis de 13/11/2026, revisão 3
---

# Banco de Cyrodiil e empréstimos aos Reis

## Resumo

Empréstimos com juros para o Rei autorizado, com cobrança na conta institucional. O escopo é planejado. Os [contratos compartilhados](README.md) descrevem as dependências dos cinco projetos.

## Regras confirmadas

- Juros são cobrados sobre empréstimos; não há remuneração de depósitos neste projeto.
- O credor é o **Banco de Cyrodiil**, administrado pelos ADMs como instrumento de controle da economia do servidor. É o banco chamado “Corydill” pelo usuário e já identificado como Cyrodiil nas fontes.
- Somente o Rei pode pedir empréstimo, após liberação específica pelo ADM.
- A aba **Empréstimos** aparece no painel do Rei em estado neutro e naturalmente indisponível. Nomear o Rei não a habilita automaticamente.
- A liberação acontece na área de setagem/nomeação do Rei no painel ADM, vinculada àquele Rei e reino.
- Crédito, pagamento e negativação pertencem à conta institucional do reino exibida no painel do Rei. A conta pessoal do jogador fica fora desse fluxo.
- Não pagar o empréstimo pode negativar a conta institucional. Tesouro vazio sem obrigação vencida não basta para negativar.

## Painel ADM

Adicionar à área de nomeação **Liberar empréstimos do Banco de Cyrodiil**, inicialmente desligado. Mostrar Rei, reino, estado da autorização e histórico. Salvar exige permissão oficial de staff, motivo e revisão atual. Não reutilizar a permissão de edição da taxa bancária delegada ao coletor.

A gestão do banco inclui políticas de crédito, propostas, contratos, vencimentos, dívida, negativação e pagamentos. Definir se cada pedido exige aprovação de ADM ou se é concedido dentro de condições previamente autorizadas. A liberação da aba não permite solicitar valor ilimitado ou escolher uma taxa arbitrária.

Proposta para sucessão: substituir/destituir o Rei revoga a liberação do anterior; o novo começa sem liberação e precisa de decisão administrativa. A dívida permanece na entidade institucional do reino. Confirmar a política de liberação do sucessor antes de ativar essa transição, sem apagar contratos ou cobrar o ex-Rei pessoalmente.

## Painel do Rei

Sem liberação, mostrar aba neutra/desabilitada e **“Empréstimos indisponíveis. Aguardando liberação da administração.”** Não carregar contratação e recusar a operação também por pedido direto ao servidor.

Com liberação e cargo vigente, mostrar condições, solicitação, revisão de principal/juros/vencimentos, confirmação, recibo e acompanhamento. Identificar **Conta do reino**, saldo disponível, dívida e valor vencido, sem misturar carteira pessoal. Lorde, Mão, coletor e jogador comum não podem contratar por esta função.

Revogar a autorização bloqueia novos pedidos e confirmações pendentes, sem cancelar contratos ou apagar dívidas. Prever consulta e canal de regularização dos contratos existentes pela autoridade competente mesmo com novas contratações desabilitadas; definir sua apresentação na UI. Não exigir nova liberação de crédito para liquidar obrigação já existente.

## Parâmetros ainda necessários

Definir limite, taxa, juros simples/compostos, base de cálculo, periodicidade, arredondamento, vencimento, parcelas, carência, amortização, pagamento antecipado/parcial e multas, se houver. Definir modalidade de aprovação, fonte/limite de recursos administrados pelo Banco de Cyrodiil e regras de renegociação/perdão.

Não reutilizar a taxa diária de Cyrodiil como juros: são obrigações diferentes. A negativação aqui decorre do empréstimo inadimplido; estendê-la a impostos, salários ou outras taxas exige regra específica.

Definir cobrança no vencimento, uso de futuros créditos do tesouro, efeito sobre saques/salários e condições de regularização. Nenhuma dessas consequências decorre automaticamente da palavra “negativada”.

## Conta institucional e saldo devedor

Registrar capital, juros apropriados, vencido, pagamentos e saldo devedor por contrato. A conta institucional deve refletir a negativação/dívida, inclusive valor contábil negativo se essa for a representação adotada, sem inserir quantidade negativa de Septims no inventário ou no cofre físico.

Separar saldo disponível, lastro físico e obrigação financeira. Dívida não cria dinheiro sacável. Registrar inadimplência não deve debitar novamente um valor já cobrado: cada apropriação e liquidação têm recibo próprio. Adaptar consumidores que hoje pressupõem saldo não negativo sem corromper depósito, saque, folha ou IPTU.

Usar ledger e saldo oficiais, sem carteira paralela. Diademas e contas pessoais não participam de desembolso, garantia, cobrança ou negativação. Não financiar empréstimos com depósitos de terceiros por suposição.

## Fluxo financeiro

1. ADM concede a liberação ao Rei/reino e registra a decisão.
2. O Rei solicita condições. O servidor gera proposta vinculada ao reino, nomeação, política, principal, taxa, destino, validade e cronograma.
3. Aplicar a modalidade de aprovação definida; bloquear limite/fonte de recursos; revalidar cargo, liberação e revisão antes de desembolsar. Propostas concorrentes não podem exceder o limite juntas.
4. Registrar contrato e crédito institucional uma vez. Se diário/cofre e SQL forem separados, usar intenção durável, recibo e conciliação.
5. Apropriar juros por períodos únicos, com relógio do servidor e recuperação após reinício/offline conforme política.
6. Efetuar pagamentos da conta institucional e distribuir capital/juros/multas pela ordem aprovada, sem dupla cobrança junto de saque, salários e IPTU.
7. Registrar inadimplência real e negativar conforme critérios aprovados. Indisponibilidade do banco não equivale a saldo zero.
8. Regularizar conforme a política, preservando histórico. Nova liberação, sucessão ou reinício não perdoa dívida.

## Implementação

Revisar `adm/painel/gamemode/jarl-admin.cjs`, interface F7 e os módulos `bank`, `finance`, `finance-runtime`, `staff-finance`, `residence-treasury`, `city-governments` e `records` em SkyHouses. São pontos de integração a conferir, não APIs de empréstimo já existentes.

Modelar política versionada, concessão/revogação, proposta, contrato, períodos de juros, pagamentos e negativação. Separar devedor institucional e operador que contratou. Migração inicia a liberação desligada para todos, preserva saldos e não inventa dívidas retroativas.

Entregar primeiro controle ADM e aba neutra; depois proposta/contrato e ledger; depois cobrança, negativação e recuperação. Habilitar crédito somente com política e adaptadores reais completos. Suspender novas contratações mantém atendimento aos contratos existentes.

## Aceite

| Cenário | Resultado esperado |
| --- | --- |
| Rei nomeado sem liberação | Aba neutra e contratação recusada no servidor |
| ADM libera um Rei/reino | Somente a nomeação autorizada contrata naquele reino |
| Permissão revogada durante confirmação | Sem novo desembolso; contratos anteriores preservados |
| Lorde, Mão ou jogador forja pedido | Negado |
| Crédito, pagamento e inadimplência | Somente conta institucional afetada; conta pessoal intacta |
| Conta negativada | Sem inventário negativo ou dinheiro fictício sacável |
| Repetição, resposta perdida ou reinício | Crédito/baixa únicos e juros únicos por período |
| Troca de Rei | Dívida do reino preservada e permissão conforme política de sucessão |
| Erro de leitura do banco | Sem negativação baseada em saldo presumido |
| Concorrência com salários, saque e IPTU | Valores e lastro reconciliados |

Entregar cálculos da política aprovada, guia de migração, testes e roteiro de homologação de crédito e negativação institucional. Publicação de pacote não comprova implantação.
