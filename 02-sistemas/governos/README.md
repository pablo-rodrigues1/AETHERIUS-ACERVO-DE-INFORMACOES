---
titulo: Governos, Jarl e castelos
tipo: sistema
status: ativo
responsaveis: []
atualizado: 2026-11-14
tags: [governos]
relacionados: []
fase: em-desenvolvimento
repositorio: https://github.com/eksmurf/aetherius-painel-jarl
depende_de: [../residencias/README.md]
---

# Governos, Jarl e castelos

## Resumo

Governos municipais, nomeação de Reis, portas dos castelos e tesouro.

## Onde está o código

A fonte de desenvolvimento é o repositório [aetherius-painel-jarl](https://github.com/eksmurf/aetherius-painel-jarl). A revisão documentada é **ADM1 / REIS1 / TABFIX1**. A instalação no servidor oficial depende da integração e homologação pela equipe responsável.

## Governos e acesso

ADM1 é a base municipal. REIS1 permite ao ADM nomear, substituir e destituir Reis. O Rei nomeado usa TAB → Casas → Castelo para trancar ou destrancar uma passagem ou o conjunto. A troca de titular precisa retirar a autoridade anterior. TABFIX1 trata a abertura de Casas na primeira instalação.

O [levantamento de 11/11](levantamento-dos-castelos.md) identificou nove interiores e 28 pares de portas. Whiterun, Windhelm e Markarth têm cinco pares cada. Falkreath e Riften têm três cada. Solitude e Winterhold têm dois cada. Dawnstar e Morthal têm um cada. Os portões principais das cidades exigem levantamento próprio.

## Tesouro, banco e guarda

Somente o Responsável da Moeda nomeado recolhe o IPTU presencialmente. O coletor deposita no tesouro. Saque e alteração da taxa bancária exigem delegações específicas. Transferências do tesouro ocorrem no banco do reino. Rei e Mão administram a guarda e o tesouro. A nomeação do Mão cabe ao Rei.

A referência r11 prevê salários da guarda a cada 30 minutos reais e ciclos de 24 horas para as taxas pessoais e a taxa de Cyrodiil. A cobrança atual é limitada ao saldo disponível. Os empréstimos com juros e eventual saldo institucional negativo estão [em planejamento](../expansao-dos-paineis/bancos-e-adm.md).

## Outros módulos do pacote

Os murais, grupos, trocas e acampamentos têm documentação própria no pacote SkyPaineis. A revisão de integração desses módulos precisa ser conferida em separado. [Referência dos módulos](../../06-referencias/modulos-sociais.md)

## Diário

[Histórico da entrega e pendências](diario.md)
