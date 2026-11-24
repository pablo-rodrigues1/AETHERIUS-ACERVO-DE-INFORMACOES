---
titulo: Pools do Fazendeiro por posse, editáveis sem recompilar
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [pablo-rodrigues1]
atualizado: 2026-11-24
tags: [profissoes, fazendeiro, coleta, economia, configuracao]
relacionados: [../02-sistemas/profissoes/coleta.md, ADR-0005-comercio-empresas-e-profissoes.md]
---

# ADR-0031 — Pools do Fazendeiro por posse, editáveis sem recompilar

## Resumo
Fazendas autorizadas têm centro e raio; cada colheita sorteia de uma pool composta (planta, fazenda, região, rotação e importação). A posse é por Hold, com adaptador para outro sistema assumir em tempo real. Tudo em `farm-pools.json`, relido sem reiniciar.

## Contexto
A especificação previa `FarmSiteDefinition` com `fixedPool`, `regionalPool`, `rotationPool` e `importPool`, mas nada disso existia. Só 12 pontos de trigo estavam cadastrados e a ferramenta (`AetheriusFarmerTools`) não existia no modpack.

## Opções consideradas
| Opção | Prós | Contras |
|---|---|---|
| Lista de pontos (REFR) por fazenda | Precisão | Inviável manter à mão |
| Centro + raio + tipos de planta de cultivo | Cobre a propriedade inteira | Raio pode alcançar plantas vizinhas |

## Decisão
Decisões do dev (23–24/11/2026):
- Ferramenta: forcado vanilla (Pitchfork001/002).
- 13 fazendas da lista da staff, centro no marcador de mapa, raio 4000.
- Itens = matéria-prima das receitas do Cozinheiro, distribuída por lore (ex.: peixe de água doce e mel no Rift; peixe do mar e sal em Haafingar). Peixe cru vem das fazendas.
- Posse por Hold, com `globalThis.aetheriusFarmOwnership.register(siteId => hold)`.
- Controle da staff só por arquivo, por enquanto.

## Consequências
- (+) Staff ajusta pools, pesos, ciclo e posse editando JSON.
- (+) Sistema de governo/território futuro pode mudar a posse sem tocar nas profissões.
- (−) Sem interface no F7 para isso ainda.
