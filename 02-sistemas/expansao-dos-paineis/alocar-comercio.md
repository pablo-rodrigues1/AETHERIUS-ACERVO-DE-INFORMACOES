---
titulo: Painel de residências e função Alocar Comércio
tipo: referencia
status: ativo
responsaveis: []
atualizado: 2026-11-15
tags: [planejamento]
relacionados: [README.md, comercio-empresas-e-profissoes.md]
fonte: Planejamento dos painéis de 13/11/2026, revisão 3
---

# Painel de residências e função Alocar Comércio

## Resumo

Cadastro de estabelecimentos, portas e responsável comercial. O escopo é planejado. Os [contratos compartilhados](README.md) descrevem as dependências dos cinco projetos.

## Complemento de produto aprovado em 15/11/2026

O [modelo COMÉRCIO](comercio-empresas-e-profissoes.md) acrescenta aquisição de estabelecimentos, contratação de profissionais, remuneração negociada e Imposto de Comércio mensal definido pelo Rei da jurisdição. Usa **Residência** como referência estrutural, com regras comerciais próprias. Este cadastro continua sendo a etapa de identificar espaços/acessos e responsáveis; não executa uma compra nem libera receitas automaticamente. Preços, calendário, arrendamento e consequências de inadimplência permanecem pendentes. Ver [ADR-0005](../../03-decisoes/ADR-0005-comercio-empresas-e-profissoes.md).

## Fluxo proposto

1. O operador autorizado seleciona uma porta e escolhe **Alocar Comércio** na área de cadastro. A definição de quem pode alocar ainda precisa de confirmação; proposta inicial é usar a autoridade S+ existente no cadastro residencial, sem liberar isso a qualquer morador.
2. O sistema resolve entrada, destino, células e passagens, mostra as referências e identifica conflitos com residências, castelos, outros comércios ou quartos já cadastrados.
3. Informar nome do estabelecimento, categoria, cidade oficial e responsável autenticado. Esclarecer a distinção entre responsável operacional e proprietário do imóvel; não criar posse ou compra automaticamente.
4. Revisar os acessos incluídos e confirmar. Gravar cadastro/revisão/auditoria e disponibilizar ao responsável a gestão prevista no projeto de Comércios.
5. O responsável abre ou fecha as portas do próprio comércio pelo painel, sujeito a permissões atuais. O controle por porta ou conjunto deve mostrar o resultado real, inclusive falha parcial.

O exemplo de taverna e ferraria é uma referência de uso. Não cadastrar uma porta específica de Whiterun ou substituir sua referência sem levantamento da instalação oficial.

## Modelo espacial e de responsabilidade

Cada estabelecimento tem identidade estável, nome, categoria, município, entradas pareadas, células/limites exclusivos, passagens internas, responsável, revisão e situação. Portas de quartos são entidades subordinadas, não portas públicas genéricas. Áreas compartilhadas e estabelecimentos sem interior exclusivo exigem delimitação própria antes da ativação.

O cadastro comercial reutiliza descoberta e validação de referências; não reutiliza automaticamente compra residencial, família, limite de casas, venda instantânea, tour, IPTU ou retorno por inadimplência. Criar um tipo/entidade distinto ou contrato equivalente, com migração clara. Não transformar casas ocupadas em comércio apagando titularidade ou conteúdo.

Identificar destinos reversos, entradas alternativas e menus nativos. Não confiar apenas na primeira porta selecionada nem em uma lista de nomes. Consultas de plugins exigem houseCARL e a skill de esquema pertinente; se o runtime não fornecer alguma informação, documentar a dependência, sem inventar API.

## Regras de acesso

O responsável pode controlar somente as referências atribuídas ao estabelecimento. Delegação a funcionários, limite de delegados, substituição e revogação precisam de política explícita. Não presumir que Rei, Mão, Lorde, hóspede ou outro comerciante recebam a mesma permissão.

Usar uma decisão de acesso no servidor antes da ativação/transição da porta. A interface local é apenas apresentação. Trancar para novos visitantes não deve remover acesso aos quartos de hóspedes com contrato vigente: definir o caminho autorizado de entrada/saída em conjunto com o projeto 04. Não prender pessoas dentro do prédio; a proposta é manter saída liberada mesmo com o comércio fechado.

Trocar responsável revoga imediatamente a gestão anterior sem apagar estabelecimento, itens ou contratos de quarto. Remover cadastro com contratos ativos exige plano de encerramento e recuperação; não excluir a linha para retirar permissões.

## Decisões de produto

- Quem aloca, transfere e encerra um comércio: S+, proprietário, Rei/Lorde ou outra autoridade?
- O modelo aprovado prevê estabelecimentos adquiríveis; definir preço, condições de aquisição e eventual arrendamento. O imposto será mensal e definido pelo Rei; base, limites, vigência e significado de mês ainda estão pendentes.
- Quem será o responsável e quais funcionários poderão gerir portas/quartos?
- Haverá horários automáticos? Não estão incluídos no pedido inicial.
- Como ficam residências/comércios mistos e portas de passagem pública?

O cadastro e sua revisão podem ser implementados antes dessas políticas, com ativação condicionada aos contratos aplicáveis. Não inventar um segundo tesouro ou carteira comercial.

## Pontos da base e entrega

Partir do cadastro pela porta integrado no F7 e das versões recebidas de `door-registration`, `host`, `access`, `catalog`, `store` e `service` de Residências. A base residencial é 0.5.3 com integração municipal, não os rascunhos iniciais de 0.4.0. Isolar o domínio comercial e seus eventos, usando a mesma resolução de identidade e referências quando compatível.

Entregar esquema e migração aditiva, formulário **Alocar Comércio**, consulta e revisão do cadastro, resolução de responsável e contrato de portas consumido pelo painel comercial. Preparar mapeamento de um estabelecimento piloto de cada tipo aprovado; não inferir que todos os interiores estão disponíveis.

## Aceite

| Cenário | Resultado esperado |
| --- | --- |
| Taverna/ferraria com várias entradas | Cadastro único e cobertura das portas validadas |
| Referência já controlada por outro módulo | Conflito explícito antes de publicar |
| Tentativa de alocar sem permissão | Negada no servidor |
| Troca de responsável durante comando | Operador antigo não altera a porta |
| Fechar comércio com hóspede dentro/fora | Política de saída e acesso ao quarto preservada |
| Reinício ou falha de aplicar fechadura | Estado persistido e resultado real reconciliados |
| Conversão de local ocupado | Nenhum dono, item ou contrato apagado implicitamente |

Habilitar o cadastro separadamente de aluguel. Desativar novas alocações deve manter os controles e contratos dos estabelecimentos existentes.
