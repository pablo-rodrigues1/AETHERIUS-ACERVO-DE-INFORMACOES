---
titulo: Sistema de Artesanato (CraftingSystem)
tipo: sistema
status: ativo
fase: em-desenvolvimento
responsaveis: [pablo-rodrigues1, mercurius17]
repositorio: https://github.com/pablo-rodrigues1/AetheriusGameplayCore
depende_de: [README.md, catalogo-e-progressao.md]
atualizado: 2026-11-18
tags: [profissoes, artesanato, crafting, ferreiro, curtidor, cozinheiro, artifice, alfaiate]
relacionados: [README.md, catalogo-e-progressao.md, refino-e-encantamento.md, housecarl-e-persistencia.md, ../../07-guia-da-staff/itens/manutencao-de-equipamentos.md]
---

# Sistema de Artesanato (`crafting-system`)

## Resumo

Especificação do artesanato server-authoritative (`crafting-system`): autorização de bancadas com isolamento de receitas por profissão, política de crafting-base tolerante, workflows de Ferreiro e Curtidor, regras de Cozinheiro/Artífice/Alfaiate, conversão de materiais, orçamentos de sets, receitas de kits e diretrizes visuais CEF.

## 1. Princípios e Autorização de Workstations

- **Precedência:** em conflito entre planos antigos do Ferreiro e o plano mais recente do Curtidor, prevalece a política de **crafting-base tolerante** — rank e material controlam receitas e economia, sem tornar minigames mais difíceis; risco e precisão ficam no `refinement-system`.
- **Dois gates distintos:**
  - **Gate A (Bancada):** a profissão escolhida pode usar esta workstation?
  - **Gate B (Catálogo):** quais receitas daquela profissão pertencem a esta workstation, rank, gate global e membership?
- **Invariante de isolamento:** receitas de outras profissões **nunca entram no read model** enviado ao cliente. O menu vanilla deve ser suprimido para evitar *recipe leakage*.

| Profissão | Workstations autorizadas (data-driven) | Regra de isolamento |
|---|---|---|
| Ferreiro | Forja e bancadas de ferraria | Vê só receitas de Ferreiro |
| Curtidor | Curtume (`Tanning Rack`) e bancadas de couro | Vê só receitas de Curtidor |
| Artífice | Múltiplas, incluindo **Forja** e **Curtume** | Na Forja vê só Artífice; no Curtume vê só Artífice |
| Cozinheiro | Estações de cozinha e bancadas de fermentação/bebidas | Inclui todo o antigo escopo do Cervejeiro |
| Alfaiate | Bancada própria a definir no catálogo | Não presumir bancada vanilla incorreta |
| Alquimista / Encantador | Roteados a módulos externos | Não usam o `CraftingSystem` genérico |

## 2. Workflows dos Artesãos

### 2.1. Ferreiro

Fluxo: `SELECT_PROJECT → Aquecer Forja → Derreter Metal → Moldar Metal → Resfriamento → FINAL_COMMIT`.

- `forge.heated` pertence à sessão (`session-scoped`), não ao personagem.
- O item só é criado no inventário após o Resfriamento; falhas intermediárias seguem política configurável sem conceder XP e sem punição maior que a do refino.
- A conversão `3 minérios → 1 lingote` é operação separada de processamento de matéria-prima (não confundir com a etapa visual "Derreter Metal").

### 2.2. Curtidor

- **Produção de couro (`3 peles de animal → 1 couro`):** operação server-authoritative e transacional **sem minigame**.
- **Equipamentos:** `SELECT_RECIPE → Preparar Mesa (vale pela sessão) → Construir Molde (2 Leather Strips, 10 s, cobertura tolerante com backtracking; falha perde 1 tira) → Costura (8 acertos em peças pequenas / 10 em grandes, 2,25 s/ponto, 5 erros tolerados, 6º falha e reinicia apenas Costura) → Retoques Finais (Espaço em zona ampla, sem punição econômica) → FINAL_COMMIT`.

### 2.3. Cozinheiro, Artífice e Alfaiate

- **Cozinheiro:** reúne culinária e todo o antigo escopo de Cervejeiro na mesma carreira. **Exceção econômica:** `Skooma` ilegal de Mestre não recebe preservação de ingrediente nem duplicação de output.
- **Artífice:** itens utilitários/acessórios e soul gems preenchidas; transversais a múltiplas bancadas.
- **Alfaiate:** roupas, capas, capuzes e vestimentas; categorias descobertas dinamicamente por release (sem hardcode de mod index).

Todos os bônus de preservação de material (`10%` Aprendiz Ferreiro/Curtidor; `20%` Especialista Cozinheiro; `20%` Mestre Artífice/Alfaiate) e duplicação (`20%` Mestre Cozinheiro) são rolados server-side e gravados no ledger para impedir reroll por retry.

## 3. Conversão de Materiais, Orçamentos de Sets e Kits

Todos os números abaixo são **defaults configuráveis** (`AETHERIUS_BALANCE_CONFIGURATION_POLICY.md`):

| Item / Conjunto | Orçamento / Regra default | Observação |
|---|---|---|
| Conversão minério → lingote | `3 minérios → 1 lingote` | Aplicada em cadeias equivalentes |
| Conversão pele → couro | `3 peles de animal → 1 couro` | Sem minigame |
| Set completo metálico | `30 lingotes` no total (`~90 minérios`) | Soma de armadura, peito, bota, luva e escudo; divisão por peça configurável |
| Set completo de Couro | `30 couros` no total (`~90 peles`) | Divisão por peça configurável |
| Set completo de Pele Pura | `15 couros + 5 peles brutas` | Perfil `materialProfile = pure-hide` separado de `leather` |
| Kits de manutenção (`50` cargas) | Armas: `2 lingotes + 3 tiras`<br/>Armadura metal: `2 lingotes + 2 couros + 2 tiras`<br/>Couro/pele: `6 couros + 3 tiras` | 30 definições no `durability-system`; materiais sem lingote validado ficam `UNRESOLVED`. Ver [Manutenção](../../07-guia-da-staff/itens/manutencao-de-equipamentos.md) |

A camada de balanceamento do Aetherius sobrepõe quantidades vanilla dos registros `COBJ` descobertos via Housecarl (prioridade: override explícito → profile Aetherius → COBJ estrutural).

## 4. Diretrizes Visuais CEF (`CRAFTING_UI_VISUAL_GUIDELINES.md`)

As referências visuais de Ferreiro (`blacksmith-crafting-ui-reference.webp`) e Curtidor (`tanner-crafting-ui-reference.webp`) no repositório `AetheriusGameplayCore` orientam layout e UX (não paleta ou arte final):

- **Proibição de guildas inexistentes:** no Aetherius **não existe Guilda dos Ferreiros nem Guilda dos Curtidores**. A UI nunca pode exibir "Guilda", rank/brasão/reputação de guilda profissional (cabeçalho usa apenas o ofício, ex.: `FERREIRO — Bancada de Criação`). Restrições reais de guildas do mundo continuam válidas por receita.
- **Autoridade visual:** cores, bordas, foco e estados disabled vêm exclusivamente dos tokens do `AetheriusUI_Core`.
- **Estética:** elementos gráficos próprios em *line art* desenhada à mão; proibido render 3D hiper-realista, fotografia ou ícones genéricos incompatíveis.
- **Composição recomendada (3 áreas):** esquerda (`RECEITAS` com busca/filtros/bloqueios), centro (`ITEM + PROCESSO` com etapas visíveis e minigame integrado) e direita (`REQUISITOS` + `RESULTADO`).
