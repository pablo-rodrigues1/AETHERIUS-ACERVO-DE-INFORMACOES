---
titulo: Residências e IPTU
tipo: sistema
status: ativo
responsaveis: []
atualizado: 2026-11-14
tags: [residencias]
relacionados: []
fase: em-desenvolvimento
repositorio: https://github.com/eksmurf/aetherius-residencias
depende_de: [../ui-core/README.md]
---

# Residências e IPTU

## Resumo

Compra, acesso, revenda e imposto das propriedades residenciais.

## Onde está o código

A fonte de desenvolvimento é o repositório [aetherius-residencias](https://github.com/eksmurf/aetherius-residencias). A revisão documentada é **0.5.3**. A instalação no servidor oficial depende da integração e homologação pela equipe responsável.

## Propriedade e acesso

O dono gerencia a casa em TAB → Propriedades. Pode cadastrar até cinco pessoas, incluindo até dois familiares. Visitantes podem entrar. Familiares também têm chave vinculada e acesso aos armazenamentos. A porta pública permite visita. A saída continua livre mesmo quando uma permissão termina.

## Vencimentos

O imposto vence a cada 15 dias completos. Cada parcela equivale a 10% do valor cadastrado em Septims, arredondado para cima, inclusive para casas adquiridas com Diademas.

| Parcelas vencidas | Efeito na entrega 0.5.3 |
|---:|---|
| 1 | Cancela o anúncio manual mesmo com o dono offline. Após quitar, o dono precisa informar o preço e anunciar novamente. |
| 2 | Suspende chaves, entradas e armazenamentos. |
| 3 | A casa volta à oferta pelo preço cadastrado. Até outra compra ser concluída, o dono pode quitar todas as parcelas vencidas e recuperar o acesso. |

A quitação gera uma pendência de arrecadação municipal. O Responsável da Moeda recolhe na entrada cadastrada, com crédito ao tesouro da cidade. Uma venda preserva as pendências de arrecadação.

## Venda, visita e ficha

A revenda voluntária transfere ao vendedor o preço anunciado em Septims. O comprador recebe a casa com o conteúdo armazenado. A venda instantânea prevista para casas elegíveis devolve 40% do valor base da aquisição, arredondado para baixo, descontando o IPTU vencido até zerar o ganho. Casas de origem em Diademas ou com origem incerta ficam fora dessa modalidade.

A visita experimental dura 60 segundos a partir da chegada confirmada, somente em oferta sem proprietário. A ficha exibe camas, armazenamentos e atividades confirmados. Venda instantânea e visita dependem da homologação de seus adaptadores.

## Diário

[Histórico da entrega e pendências](diario.md)
