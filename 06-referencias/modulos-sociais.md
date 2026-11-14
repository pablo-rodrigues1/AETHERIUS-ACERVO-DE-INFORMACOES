---
titulo: Módulos sociais do pacote SkyPaineis
tipo: referencia
status: ativo
responsaveis: []
atualizado: 2026-11-14
tags: [governos, social]
relacionados: []
fonte: Guias 02 a 12 da distribuição SkyPaineis no repositório do painel Jarl
---

# Módulos sociais do pacote SkyPaineis

## Resumo

A referência de integração do painel Jarl inclui murais, grupos, negociação, apresentação e acampamentos.

## Murais

Nove cidades têm murais independentes. No pacote, cada mural comporta 50 cartas comuns e 20 oficiais. A carta comum custa cinco Septims, com uma publicação ativa por perfil e cidade. A oficial é gratuita. O prazo varia de uma a 168 horas e o padrão é 24. O Responsável da Moeda da capital correspondente recolhe o caixa. A expiração do aviso continua correndo com o jogador offline.

## Grupos, negociação e apresentação

A referência prevê grupo de até cinco e raide de até 20, incluindo o líder. O convite expira em 30 segundos. A negociação requer confirmação de ambos sobre a mesma oferta e uma espera de dez segundos. Alterar uma oferta invalida as confirmações. Os testes de integração precisam verificar recuperação de ambos os inventários após uma interrupção. O progresso da staff registra separadamente o estado do Trade no código do servidor examinado.

## Acampamentos

O líder monta ou desmonta o acampamento e cada integrante pode liberar a própria tenda. Terreno, materiais e autoridade são validados pelo servidor. Os projetos podem ser salvos por perfil. A integração com o cliente distribuído requer validação de câmera, repouso, colisão, reconstrução do cenário para observadores e retorno do mapa.

## Fonte

O catálogo reúne as revisões de Casas e bancos, murais e taxas, grupos e negociação, acampamentos, marcadores de mapa e testes conjuntos. [Documentação das entregas](documentacao-das-entregas.md)
