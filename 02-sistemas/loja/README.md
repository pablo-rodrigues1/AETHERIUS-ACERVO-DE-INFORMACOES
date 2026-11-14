---
titulo: Loja e apoiadores
tipo: sistema
status: ativo
responsaveis: []
atualizado: 2026-11-14
tags: [loja]
relacionados: []
fase: em-desenvolvimento
repositorio: https://github.com/eksmurf/aetherius-loja
depende_de: [../ui-core/README.md]
---

# Loja e apoiadores

## Resumo

Loja do TAB, carteira de Diademas e benefícios de apoiador.

## Onde está o código

A fonte de desenvolvimento é o repositório [aetherius-loja](https://github.com/eksmurf/aetherius-loja). A revisão documentada é **0.7.0**. A instalação no servidor oficial depende da integração e homologação pela equipe responsável.

## Compra e direito ao Beta

O site vende pacotes de Diademas em reais. No jogo, TAB → Loja consulta o saldo oficial e o catálogo. Uma compra do Beta debita **800 Diademas** e vincula o direito à conta. A mesma transação grava o recibo e as seis entregas previstas. Repetir um pedido após uma resposta perdida deve recuperar o resultado anterior.

| Benefício Beta | Disponibilidade prevista pela oferta |
|---|---|
| Tag Beta Tester no Discord | Depende da integração com o Discord |
| Roupa cosmética exclusiva | Durante o Beta e após o lançamento |
| Mochila personalizada, 20 bebidas, 20 comidas e um título | Após o lançamento |

Os níveis I–IV estão ocultos durante a campanha Beta. O desenho da loja completa prevê 250, 550, 800 e 2.000 Diademas, respectivamente, e 30 dias de validade para o personagem escolhido. Preço de upgrade, teto e arredondamento de XP, ticket de troca de classe e integração dos benefícios continuam pendentes. Esses níveis têm regras próprias de morte, progressão e residência adicional.

## Pacotes do site

Valores de catálogo registrados em 07/11/2026. A equipe do site confirma a oferta publicada.

| Preço do pacote | Diademas base | Bônus | Total em Diademas |
| ---: | ---: | ---: | ---: |
| R$ 9,90 | 100 | 0 | 100 |
| R$ 29,90 | 200 | 50 | 250 |
| R$ 49,90 | 500 | 50 | 550 |
| R$ 99,90 | 1.000 | 200 | 1.200 |
| R$ 199,90 | 2.000 | 500 | 2.500 |
| R$ 499,90 | 5.000 | 1.000 | 6.000 |

## Integração e validação

O cadastro de personagem ativo precisa fornecer conta, personagem e perfil e preservar a referência enquanto durar o vínculo. A revisão de 13/11 corrigiu o exemplo de inicialização. A fonte registra 34 testes aprovados com dados fictícios. Para homologar, a equipe precisa conferir o cadastro efetivamente carregado, a carteira das casas e a consulta ao catálogo. Sem saldo disponível, a interface exibe um traço. Septims são a moeda do jogo e continuam separados dos Diademas.

## Diário

[Histórico da entrega e pendências](diario.md)
