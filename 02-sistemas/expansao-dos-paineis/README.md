---
titulo: Expansão dos painéis
tipo: sistema
status: ativo
responsaveis: []
atualizado: 2026-11-14
tags: [expansao-dos-paineis]
relacionados: []
fase: planejado
repositorio: null
depende_de: [../residencias/README.md, ../governos/README.md, ../painel-adm/README.md]
---

# Expansão dos painéis

## Resumo

Cinco projetos para bancos, comércios, quartos, portões e ritual de coleta.

## Onde está o código

As especificações desta pasta reproduzem o pacote de planejamento, **revisão 3 de 13/11**. A implementação pertence aos painéis de destino identificados em cada projeto.

## Projetos

| Projeto | Condição principal |
|---|---|
| [Banco de Cyrodiil](bancos-e-adm.md) | Somente o Rei habilitado pelo ADM contrata empréstimo. Juros e dívida recaem sobre a conta institucional. |
| [Alocar Comércio](alocar-comercio.md) | Cadastrar interiores e seus acessos antes de delegar portas ao responsável. |
| [Portões da cidade](portoes-da-cidade.md) | Rei e Lorde controlam portões dentro da própria jurisdição. |
| [Comércios e quartos](comercios-e-quartos.md) | O hóspede pode operar a fechadura e o baú durante o aluguel. |
| [Ritual de coleta](ritual-de-coleta.md) | O Rei inicia a preparação com materiais, mago, alquimista e membro do clã Companion. O efeito é temporário e territorial. |

## Contratos compartilhados

O servidor determina identidade, cargo, jurisdição e validade das delegações. Portas e baús precisam de um único responsável por decidir o acesso. Pagamentos, entregas e efeitos no jogo devem ter recibos que permitam recuperar uma operação interrompida. Cada projeto especifica os cenários de aceite e as escolhas ainda em aberto.

O cadastro comercial precede o aluguel. O levantamento de portões é independente dos castelos. A afiliação da cidade ao reino pertence à etapa futura do ritual.

## Diário

[Histórico da entrega e pendências](diario.md)
