---
titulo: Housecarl, Balanceamento Configurável e Persistência das Profissões
tipo: sistema
status: ativo
fase: em-desenvolvimento
responsaveis: [pablo-rodrigues1, mercurius17]
repositorio: https://github.com/pablo-rodrigues1/AetheriusGameplayCore
depende_de: [README.md, ../gameplay-core/README.md, ../ui-core/README.md]
atualizado: 2026-11-18
tags: [profissoes, housecarl, stableformkey, balanceamento, postgresql, migrations]
relacionados: [README.md, catalogo-e-progressao.md, coleta.md, artesanato.md, ../gameplay-core/README.md, ../../03-decisoes/ADR-0002-sincronizacao-dos-cores-com-os-repositorios-oficiais.md]
---

# Housecarl, Balanceamento Configurável e Persistência das Profissões

## Resumo

Consolida os contratos transversais das profissões no `AetheriusGameplayCore`: catálogos dinâmicos via Housecarl (`StableFormKey` e winning records), política de balanceamento configurável sem constantes hardcoded, integração com a `Aetherius UI Core` e a persistência já sincronizada do servidor (`0013`, `0014` e `professionPersistence`).

## 1. Housecarl, `StableFormKey` e Catálogos Dinâmicos

Profissões nunca podem depender de posições fixas de plugins na load order (`modIndex`, `lightIndex` ou `0xXX......`).

- **Identidade persistente:** usar `StableFormKey` (`pluginName + localFormId`). `Runtime FormID` é apenas resolução temporária de runtime.
- **Winning records:** o catálogo usa o estado efetivo resultante após todos os patches (`ArmorMod.esp → BalancePatch.esp → AetheriusPatch.esp`).
- **Housecarl fora do hot path:** Housecarl escaneia e audita a load order na preparação da release; o runtime consome apenas um `RecordCatalog` / definitions imutáveis e versionadas com `FeatureCompatibilitySignature` (hash).
- **Falha explícita sem fallback:** se um record estiver ausente ou ambíguo (`missing-winning-record`, `ambiguous-profession-classification`), a receita/recurso fica `resolved = false` / `UNRESOLVED`, nunca caindo silenciosamente no vanilla.
- **Auditoria obrigatória de materiais (`WEAP`/`ARMO`):** na futura varredura da load order, todo equipamento elegível sem `materialKey` confiável deve ser marcado `UNRESOLVED_MATERIAL` e listado integralmente (sem truncamento) para revisão do responsável, separado do relatório de receitas sem lingote validado.

## 2. Política Transversal de Balanceamento (`AETHERIUS_BALANCE_CONFIGURATION_POLICY.md`)

Todos os valores numéricos de profissões, coleta, artesanato e refino na documentação são **defaults configuráveis** por definitions versionadas, salvo quando marcados explicitamente como invariante estrutural:

- **ProfessionSystem:** thresholds de XP, XP por atividade, anti-powerlevel, multiplicadores de Vigor por defasagem de rank, Vigor máximo/regen, gates globais e chances de bônus.
- **GatheringSystem:** capacidade/estoque de minas (`500`) e herbal sites, cooldown (`72 h`), trabalhadores simultâneos (`2`), ciclo (`60 s`), yields, chances de gemas/duplicação e pools/ciclos de fazendas.
- **CraftingSystem:** conversões (`3:1`), orçamento de sets (`30` lingotes/couros; `15+5` pele pura), custos de kits, distribuição por peça, chances de preservar/duplicar e tolerâncias de etapas.
- **RefinementSystem:** multiplicadores (`+25/+50/+75%`), moldes (`1/2/3`), ciclos (`5/10/15`), tempo (`5 s`) e falhas toleradas (`3/2/0`).

A persistência guarda apenas o **estado efetivo** do mundo (ex.: `stockRemaining`, `availableAt`, `definitionRevision`), nunca duplicando constantes de balanceamento no código.

## 3. Integração com `Aetherius UI Core`

- Usa exclusivamente `UiTransportPort`, o roteamento autenticado e os design tokens do `Aetherius UI Core`.
- Proibido criar segundo browser/CEF, canal paralelo (`mp.events.callRemote` avulso) ou expor `Runtime FormID` como contrato de UI (preferir `definitionId` lógico).
- A UI envia intenções e renderiza read models já filtrados pelo servidor.

## 4. Persistência Sincronizada no `AetheriusGameplayCore` (SYNC-1114)

Em 14/11/2026 (`commit 05f5a0f`), a sincronização a partir de `server/gameplay/core` do `aetherius-server` (v0.37.0) trouxe para o Core a camada de persistência usada pelo sistema de profissões do servidor:

| Artefato no `AetheriusGameplayCore` | Conteúdo / Função | Observação |
|---|---|---|
| `runtime/src/index.ts` (`professionPersistence`) | Exporta `{ withTransaction, executeCommand, assertCharacterKey }` | Aceita chaves `profile:N` e `character:<uuid>` (com lock em namespace próprio) |
| `database/migrations/0013_gc_professions_uuid_v2.sql` | `gc_profession_state_v2`, `gc_profession_operations_v2`, `gc_profession_outbox_v2`, `gc_profession_admin_audit_v2` | **Pendência:** está em LF no Core e CRLF no servidor, gerando divergência de hash em `gc_schema_migrations` |
| `database/migrations/0014_gc_profession_world.sql` | `gc_profession_world_sequences`, `gc_profession_world_sites`, `gc_profession_world_leases`, `gc_profession_world_operations` | Estado global de sites, leases, sequências e operações de coleta/mundo |
| `export * as professionApi` *(fora do Core)* | Importava `../../../professions/src/index.ts` | Omitido do Core porque o código executor (`server/gameplay/professions`) fica fora de `server/gameplay/core` ([ADR-0002](../../03-decisoes/ADR-0002-sincronizacao-dos-cores-com-os-repositorios-oficiais.md)) |
