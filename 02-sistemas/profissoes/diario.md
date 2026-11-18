---
titulo: Diário de Profissões
tipo: historico
status: ativo
responsaveis: [pablo-rodrigues1, mercurius17]
atualizado: 2026-11-18
tags: [diario, profissoes, coleta, artesanato, refino]
relacionados: [README.md, ../gameplay-core/diario.md]
---

# Diário de Profissões

## Resumo

Registro cronológico da evolução documental, regras de balanceamento e sincronizações das profissões no `AetheriusGameplayCore` e no Acervo, do mais recente para o mais antigo.

## 2026-11-18 — Consolidação no Acervo Aetherius (Gemini)

- **Feito:** Documentação completa dos cinco módulos de profissões do `AetheriusGameplayCore` (`profession-system`, `gathering-system`, `crafting-system`, `refinement-system`, `enchantment-system`), receitas de kits de manutenção (`durability-system`), política de balanceamento configurável e migrations `0013`/`0014` estruturada em `02-sistemas/profissoes/`. Criados guias didáticos para a Staff em `07-guia-da-staff/profissoes/` e atualizados `INDICE.md`, `02-sistemas/README.md`, `07-guia-da-staff/progresso.md`, `07-guia-da-staff/glossario.md` e `04-historico/CHANGELOG.md`.
- **Próximo:** Acompanhar implantação da nova interface CEF de profissões e futura implementação dos módulos no Core / integração no servidor oficial.
- **Bloqueios:** Nenhum para documentação; implementação funcional no Core permanece planejada.

## 2026-11-14 — SYNC-1114 no GameplayCore (mercurius17 / Claude Opus 5.5)

- **Feito:** Trazidas de `server/gameplay/core` do `aetherius-server` (v0.37.0) para o Core as migrations `0013_gc_professions_uuid_v2.sql` e `0014_gc_profession_world.sql`, o suporte a chaves `character:<uuid>` com lock próprio e o export `professionPersistence` em `runtime/src/index.ts`.
- **Ficou de fora:** `export * as professionApi from '../../../professions/src/index.ts'`, pois o módulo executor `server/gameplay/professions` vive fora do Core no servidor.
- **Pendência:** Alinhar fim de linha (LF vs CRLF) da `0013` entre servidor e Core. Ver [ADR-0002](../../03-decisoes/ADR-0002-sincronizacao-dos-cores-com-os-repositorios-oficiais.md).

## 2026-10-23 — Receitas de kits de manutenção e auditoria de materiais (pablo-rodrigues1)

- **Feito:** Kits de manutenção ajustados para **50 cargas** no `durability-system` (máximo 100 por material/tipo) e documentadas as receitas para futura integração ao `crafting-system` (`KIT_RECIPES_AND_BALANCE.md` e `ITEM_RECIPE_CATALOG_AND_HOUSECARL.md`): `2 lingotes + 3 tiras` (armas), `2 lingotes + 2 couros + 2 tiras` (armaduras metálicas) e `6 couros + 3 tiras` (couro/pele). Incluída exigência de relatório completo de equipamentos `WEAP`/`ARMO` sem material identificado (`UNRESOLVED_MATERIAL`) na futura varredura de load order.
- **Próximo:** Implementar varredura Housecarl para resolução de `StableFormKey` e habilitar receitas apenas quando os ingredientes estiverem validados.

## 2026-10-14 — Persistência do refino em combate (pablo-rodrigues1)

- **Feito:** Esclarecido em `modules/refinement-system/README.md` que o tier de refino (`Tier I/II/III`) é atributo permanente da instância, aplicado em **todos** os golpes/defesas físicas pertinentes sem consumo ou regressão em combate; regressão ocorre apenas por falha total em tentativa de refino.

## 2026-10-06 — Unificação de Cervejeiro no Cozinheiro (mercurius17)

- **Feito:** Removido o identificador isolado `brewer`; todo o escopo de bebidas, fermentação, vinhos, hidroméis e Skooma ilegal (Mestre, sem bônus de preservação/duplicação) foi incorporado à profissão **Cozinheiro**.

## 2026-10-05 — Base documental de Profissões, Coleta, Artesanato, Refino e Balanceamento

- **Feito:** Publicados no `AetheriusGameplayCore` os documentos normativos de `profession-system`, `gathering-system`, `crafting-system`, `refinement-system`, `enchantment-system` (placeholder fora da Alpha) e a política transversal `AETHERIUS_BALANCE_CONFIGURATION_POLICY.md`, incluindo a regra de penalidade progressiva de Vigor para artesãos produzindo receitas abaixo do próprio rank (`1,00x` a `4,00x`) e a filosofia de crafting-base tolerante com risco concentrado no refino.
