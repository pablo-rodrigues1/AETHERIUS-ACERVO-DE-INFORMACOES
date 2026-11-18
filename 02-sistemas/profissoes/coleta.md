---
titulo: Sistema de Coleta (GatheringSystem)
tipo: sistema
status: ativo
fase: em-desenvolvimento
responsaveis: [pablo-rodrigues1, mercurius17]
repositorio: https://github.com/pablo-rodrigues1/AetheriusGameplayCore
depende_de: [README.md, catalogo-e-progressao.md]
atualizado: 2026-11-18
tags: [profissoes, coleta, minerador, herbalista, cacador, fazendeiro]
relacionados: [README.md, catalogo-e-progressao.md, housecarl-e-persistencia.md]
---

# Sistema de Coleta (`gathering-system`)

## Resumo

Especificação do domínio executor das 4 profissões de coleta (**Minerador, Herbalista, Caçador e Fazendeiro**): detecção dinâmica de recursos via Housecarl, estoques globais e cooldowns, concorrência por leases, carcaças únicas, pools agrícolas, sessões CEF server-authoritative e persistência.

## 1. Domínio, Ownership e Coleta Geral

- O `gathering-system` é owner de: estoque do site (`stock`), estado de esgotamento, `availableAt` (cooldown), `gathering_worker_lease`, sessões de coleta, `gathering_cycle_ledger`, `carcass processed state` e estado de ciclo agrícola.
- Não escreve XP, rank ou Vigor: consulta e liquida pelo `GatheringProfessionAdapter` usando `operationId` único e reautorização antes do commit de cada ciclo.
- **Coleta Novato universal:** qualquer personagem pode minerar, colher plantas, cultivar ou processar carcaças em nível `Novato` (desde que possua a ferramenta exigida e Vigor). Se sua profissão selecionada for outra, `effectiveRank = novice` e `xpEligible = false`.

| Coleta | Ferramenta (validada server-side) | Timer de ciclo | Cooldown após esgotar (default) |
|---|---|---|---|
| Minerador | Picareta | `60 s` contínuo | `72 h` (`259.200 s`) |
| Herbalista | Bolsa / `Satchel` | `60 s` | `72 h` (capacidade por catálogo, não herda `500`) |
| Fazendeiro | Enxada / `Hoe` | `60 s` | Configurável por fazenda/ciclo |
| Caçador | Nenhuma adicional | Sem timer obrigatório de 60 s | Sem cooldown de site |

## 2. Mecânicas por Coletor

### 2.1. Minerador

- **Mina lógica (`MineSiteDefinition`):** agrupa múltiplas veins/referências (`MineEntryDefinition`) em um `mineSiteId`.
- **Defaults configuráveis:** estoque global de **500 unidades**, cooldown de **72 horas**, máximo de **2 trabalhadores simultâneos** por mina e ciclo de **60 segundos**.
- **Consumo de estoque:** cada ciclo consome **1 unidade** do estoque principal. Carvão do Novato, gemas e duplicações de Mestre são bônus profissionais e não consomem estoque adicional.
- **Gemas e Dwemer:** um único roll verifica se saiu gema (`1% / 2% / 3%` comum; `1%` perfeita no Mestre) e um segundo roll escolhe a gema da pool. **Metal Dwemer** vem apenas de depósitos controlados (proibido usar clutter Dwemer vanilla).
- **Minas mistas:** compartilham estoque global de 500, mas cada node tem tipo de minério e rank gate próprios.
- **Smelting (`3 minérios → 1 lingote`):** é transformação de material; executada em domínio de processamento/crafting externo consultando o rank do Minerador.

### 2.2. Herbalista

- Intercepta/substitui o harvest vanilla para impedir duplicação gratuita de ingredientes.
- Cada referência mapeia `herbalEntryId`, `herbalSiteId`, `basePlantForm`, `baseIngredient` e perfil de recurso.
- Usa a mesma infraestrutura de leases e cooldown base de 72 h, mas a capacidade de cada site vem do catálogo (nunca assume `500`).
- **Pool adicional consolidada (Adepto/Especialista):** decisão posterior superou a restrição apenas botânica; o bônus aleatório (`10%` / `20%`) pode sortear **qualquer ingrediente alquímico autorizado** no catálogo da release (incluindo itens de criaturas como *Vampire Dust* e *Sabre Cat Eye*).

### 2.3. Caçador

- Processa animais mortos elegíveis **uma única vez** por geração de spawn.
- **Identidade da carcaça (`carcassInstanceKey`):** `world reference identity + spawn generation + authoritative death generation`, evitando que respawns herdem `processed = true`.
- **Ownership de loot:** o loot profissional substitui/impede duplicação com loot vanilla, `EnemySystem` ou scripts de criaturas.
- O painel CEF contextual ("Processar carcaça") valida morte, espécie e disponibilidade sem exigir ciclo de 60 s.

### 2.4. Fazendeiro

- Fazendas mapeadas em `FarmSiteDefinition` (localização/hold, `interaction markers`, `fixedPool`, `regionalPool`, `rotationPool`, `importPool` opcional).
- A staff pode trocar o ciclo ativo, ajustar duração, deslocar pools entre fazendas ou bloquear pools sem recompilar código.
- Ervas/plantas de alquimia pertencem ao Herbalista e ficam fora das pools normais de fazenda.

## 3. Sessões CEF, Animações, Concorrência e Persistência

- **CEF contextual:** abre via `UiTransportPort` da `Aetherius UI Core` (nunca em browser paralelo). O cliente envia apenas intenção (`gathering.interaction.open`, `gathering.session.start`, `gathering.session.stop`, `gathering.carcass.process`) e nunca decide ore, RNG, stock ou XP.
- **Animação é apresentação, não prova:** o servidor controla sessão por alvo, distância, tempo (`ClockPort`), lease e estoque.
- **Concorrência de 2 mineradores:** aquisição de lease é atômica (lock de linha / transação / advisory lock no PostgreSQL) com TTL e heartbeat para evitar slot preso após desconexão.
- **Cooldown determinístico:** persiste `available_at` (nunca contador por segundo); ao acessar após `now >= available_at`, restaura o estoque a partir da `definition` ativa.

**Tabelas conceituais previstas:** `gathering_site_state`, `gathering_worker_lease` (`UNIQUE(site_id, character_id)`), `gathering_session`, `gathering_cycle_ledger` (`operation_id PK`), `gathering_carcass_state` (`carcass_instance_key PK`) e `gathering_farm_cycle_state`.
