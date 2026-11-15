---
titulo: Painel de Comércios e hospedagem
tipo: referencia
status: ativo
responsaveis: []
atualizado: 2026-11-15
tags: [planejamento]
relacionados: [README.md, comercio-empresas-e-profissoes.md]
fonte: Planejamento dos painéis de 13/11/2026, revisão 3
---

# Painel de Comércios e hospedagem

## Resumo

Aluguel de quartos com direitos de entrada e uso do baú durante o contrato. O escopo é planejado. Os [contratos compartilhados](README.md) descrevem as dependências dos cinco projetos.

## Relação com o modelo COMÉRCIO

O [planejamento aprovado de empresas e profissões](comercio-empresas-e-profissoes.md) acrescenta titularidade comercial, contratação, salários e imposto mensal. Não resolve automaticamente os preços/prazos de quartos, o destinatário dos aluguéis ou direitos sobre bens dos hóspedes. Hospedagem continua limitada a estabelecimentos habilitados e exige preservação dos contratos na venda, no encerramento e em eventual inadimplência comercial. Nenhum mecanismo de salário ou cobrança foi implementado nesta publicação.

## Áreas do painel

**Responsável pelo comércio:** dados do estabelecimento, portas gerais, quartos, disponibilidade, contratos ativos e registros. Pode trancar/destrancar quartos dentro da política do contrato; não deve sobrepor silenciosamente a privacidade do hóspede.

**Jogador hospedado:** identificar taverna e quarto, consultar prazo, trancar/destrancar, localizar o baú e encerrar/renovar quando essas opções forem aprovadas. Integrar pelo router oficial da Core, confirmando nome da aba e slot disponíveis; não inventar um slot ou substituir Propriedades.

O painel comercial pode atender ferrarias e outros negócios para portas gerais. Quartos e hospedagem só aparecem em estabelecimentos habilitados para esse serviço.

## Cadastro de quartos

Cada quarto tem ID estável subordinado ao comércio, nome, porta(s), limites, capacidade aprovada, referências de cama e baú, condições de uso, revisão e situação. O mesmo baú ou porta não pode pertencer a dois contratos ativos. Corredor, salão e dormitório compartilhado exigem delimitação explícita; não usar uma célula inteira como quarto se ela contiver outros hóspedes.

O baú deve ser persistente e exclusivo do contrato durante a locação, protegido contra ativação, transferência e acesso por menus nativos de terceiros. Recipiente com respawn ou propriedade de missão não serve sem adaptação validada. Nunca usar seu conteúdo anterior como brinde automático ao próximo hóspede.

## Fluxo de aluguel proposto

1. O responsável publica preço, moeda, duração, regras de renovação/cancelamento e disponibilidade do quarto. Como valores e condições não foram fornecidos, ficam configuráveis e não habilitados por padrão.
2. O jogador consulta oferta com quarto, prazo, valor e direitos. O servidor verifica disponibilidade, identidade e revisão.
3. Confirmar sob bloqueio do quarto: débito oficial, registro do contrato e crédito ao destinatário autorizado ficam consistentes. Destinatário do aluguel e eventual tributação ainda precisam ser definidos; não criar uma carteira comercial presumida.
4. Conceder acesso temporário por identidade, com prazo do servidor. Uma chave visual não deve ser item transferível que contorne o contrato.
5. Durante o contrato, o hóspede controla a fechadura e o baú; o comércio fechado deve conservar sua entrada/saída autorizada. Abrir a porta do quarto não torna o baú público.
6. No vencimento, bloquear novas operações incompatíveis, encerrar o acesso e encaminhar os pertences conforme a política de recuperação aprovada. Reabrir oferta para outra pessoa somente após isolamento/limpeza segura dos bens do contrato anterior.

## Prazo e pertences

Definir duração em tempo real, início do prazo, renovação, pagamento antecipado, limite de quartos por jogador, hóspedes convidados e se o personagem pode alugar offline. Reiniciar o servidor ou desconectar não pode renovar ou apagar o prazo.

A política de bens é indispensável: retirada assistida, depósito de recuperação vinculado ao ex-hóspede ou outra regra expressa. Proposta de segurança: manter bens inacessíveis a terceiros e quarto fora de oferta até recuperação durável. Não apagar conteúdo nem entregá-lo ao taverneiro/próximo locatário por analogia com a venda de residência. Retenção, custos e prazo para recuperação precisam de definição própria.

Encerrar a locação ou perder o cargo não deve prender o jogador; conservar saída. Um menu de baú já aberto deve perder permissão de transferência quando o contrato vencer. Venda/transferência do comércio preserva contratos e deveres do novo responsável, ou exige regra de rescisão previamente aprovada. Não cancelar tudo por troca de taberneiro.

## Poder do responsável e conflitos

Definir se o taberneiro tem chave mestra, pode entrar durante aluguel, acessar o baú, expulsar ou encerrar antecipadamente. O pedido confirma gestão de fechaduras, mas não autoriza acesso irrestrito a bens de hóspedes. Proposta inicial: gestão da disponibilidade e portas de quartos vagos; quarto ocupado protegido pelo contrato; exceções de staff registradas e sem transferência automática de conteúdo.

Resolver a hierarquia de acesso entre porta externa, corredor, quarto e baú no servidor. O direito residencial anterior ou o cargo político não deve liberar automaticamente o baú do hóspede. Consultas do responsável mostram situação do contrato, sem expor conteúdo privado quando não autorizado.

## Implementação e recuperação

Criar módulo de contratos/quartos com adaptadores para identidade, economia, relógio, portas e inventários oficiais. Reutilizar componentes residenciais de referência e revisão, não suas regras de venda, família ou IPTU. Esquema lógico: quartos, ofertas, contratos, recibos, direitos temporários e recuperação de bens.

Usar identificador de operação e resultado consultável. Para inventário nativo, diário e SQL separados, registrar etapas recuperáveis. Transição sugerida: disponível → contratado → expirado/encerrado → recuperação → disponível. Não usar “disponível” enquanto ainda existir acesso ou conteúdo de outro contrato sem isolamento validado.

## Aceite

| Cenário | Resultado esperado |
| --- | --- |
| Dois jogadores alugam o mesmo quarto | Um contrato confirmado; outro sem débito indevido |
| Resposta perdida no pagamento | Mesmo recibo e acesso, sem cobrança repetida |
| Hóspede tranca/destranca | Só quarto e contrato autorizados; baú continua privado |
| Comércio fechado | Hóspede vigente mantém o acesso contratado e saída |
| Vencimento com baú aberto | Transferências recusadas e bens preservados |
| Troca de responsável/reinício | Contrato, prazo e bens mantidos |
| Novo aluguel após vencimento | Nenhum acesso aos itens do hóspede anterior |
| Identidade trocada ou pedido forjado | Sem chave ou acesso herdados |
| Recuperação indisponível | Quarto não é reutilizado expondo bens |

Entregar UI de responsável e hóspede, contrato de expiração, procedimento de recuperação e testes com dois hóspedes. Desativar novos aluguéis deve manter o atendimento aos contratos e bens já existentes.
