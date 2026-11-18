---
titulo: Refino por Instância e Encantamento
tipo: sistema
status: ativo
fase: em-desenvolvimento
responsaveis: [pablo-rodrigues1, mercurius17]
repositorio: https://github.com/pablo-rodrigues1/AetheriusGameplayCore
depende_de: [README.md, catalogo-e-progressao.md]
atualizado: 2026-11-18
tags: [profissoes, refino, encantamento, ferreiro, curtidor, damage-system]
relacionados: [README.md, catalogo-e-progressao.md, artesanato.md, ../gameplay-core/README.md]
---

# Refino por Instância (`refinement-system`) e Encantamento (`enchantment-system`)

## Resumo

Define o conceito do `refinement-system` (aprimoramento por instância de item compartilhado por Ferreiro e Curtidor, Tiers I–III, risco, regressão e integração futura com `damage-system`) e registra o estado de placeholder documental do `enchantment-system` (**excluído da release Alpha**).

> [!IMPORTANT]
> Ambos os módulos no `AetheriusGameplayCore` possuem **apenas definição conceitual / placeholder documental** nesta etapa. Ainda faltam contratos finais, schemas, locks de item, integração de inventário, CEF e testes antes de qualquer implementação.

## 1. Sistema de Refino (`refinement-system`)

### 1.1. Refino por instância e Tiers

- **Sem Forms separadas:** não existem `SteelSword_Tier1/2/3`. Uma única Form representa o item-base; o tier é estado persistido da **instância individual** e acompanha o item ao equipar, negociar, guardar em baú, dropar, relogar ou trocar de dono.
- **Compartilhado:** utilizado por **Ferreiro** e **Curtidor** sem duplicação de lógica; perks vanilla de Smithing não autorizam nem controlam o refino.
- **Progressão sequencial (`0 → I → II → III`, sem pular tiers):**

| Tier | Bônus sobre valor-base autorizado | Atributos afetados |
|---|---:|---|
| Sem refino (Tier 0) | `0%` | — |
| Tier I | `+25%` | Armas corpo a corpo, arcos e bestas: **dano físico base** |
| Tier II | `+50%` | Armaduras e escudos: **Armor Rating** |
| Tier III | `+75%` | *(Não altera peso, valor monetário, velocidade, magia ou encantamentos)* |

### 1.2. Persistência em combate e relação com o `DamageSystem`

- **Tier não é carga de uso:** o bônus permanece ativo em **todos** os golpes causados ou cálculos de dano recebido enquanto a instância possuir Tier I, II ou III. Combate, bloqueios, mortes, relog e desgaste de manutenção **não consomem nem regridem o tier**.
- **Fórmula obrigatória:** `baseEfetiva = baseAutorizadaDaInstancia * (1 + bonus(tierDaInstancia))`. Por exemplo, arma base `20` com Tier II entrega `30` em cada golpe, mantendo o Tier II.
- **Prevenção de *double-dip*:** o servidor não pode somar o bônus sobre valores já temperados no cliente Skyrim nem confiar em dano/Armor Rating enviado pelo cliente. O `DamageSystem` não realiza escrita ou consumo de tier por golpe; após sucesso ou falha de refino, o cache/projeção do equipamento é invalidado antes dos próximos cálculos.

### 1.3. Moldes, minigame e risco de regressão

Todos os parâmetros abaixo são **defaults configuráveis** (`AETHERIUS_BALANCE_CONFIGURATION_POLICY.md`), calculados e validados no servidor:

| Target Tier | Molde (custo-base) | Ciclos | Falhas toleradas | Encerra tentativa na... | Se houver falha total |
|---|---|---:|---:|---|---|
| Tier I | `1` unidade de couro | `5` | `3` | 4ª falha | Permanece Tier 0 |
| Tier II | `2` unidades de couro | `10` | `2` | 3ª falha | Regride para **Tier 0** |
| Tier III | `3` unidades de couro | `15` | `0` | 1ª falha | Regride para **Tier I** |

- **Molde:** comprometido na tentativa; não possui minigame próprio e não se confunde com moldes de crafting-base.
- **Ciclos do minigame (gerados *just-in-time*):**
  1. Ajustar e rotacionar visualmente a peça até o molde-alvo (posição, rotação e tolerância server-side).
  2. Executar sequência aleatória de teclas em **5 segundos**.
- **Falha total:** molde e materiais da tentativa são consumidos sem reembolso; o item **não é destruído**, mas **regride 1 tier** em relação ao estado anterior.

## 2. Sistema de Encantamento (`enchantment-system`)

### 2.1. Estado na Release Alpha

- O `enchantment-system` **ainda não foi conceitualmente definido nem desenvolvido** e **não fará parte da release Alpha**.
- Para a Alpha: módulo desativado, UI apresenta capability como indisponível, nenhuma dependência crítica pode exigi-lo e **é proibido usar enchanting vanilla como fallback**.

### 2.2. Definição conceitual prevista para o Encantador (pós-Alpha)

O `profession-system` mantém apenas a definição conceitual da carreira **Encantador**:

| Rank | Escopo conceitual previsto | Observação |
|---|---|---|
| Novato | Encantar armas/equip. de Ferro e Pele; Anéis | — |
| Aprendiz | Aço e Couro; Colares e Amuletos | — |
| Adepto | Élficos e Steel Plate; Tiaras | — |
| Especialista | Materiais culturais: Dwemer, Nordic e Orcish | — |
| Mestre | Ébano e Stalhrim; conversão Grand White → Black Soul Gem *(ilegal)* | Só trabalha Ebony/Stalhrim se existirem por origem autorizada |

- **Pendências explícitas:** política sobre perks vanilla de Enchanting (potência, elegibilidade, custos, efeitos, progressão), relação com Soul Gems e mods (`Thaumaturgy`, `Grimoire of Sentinel`), workstations e persistência.
