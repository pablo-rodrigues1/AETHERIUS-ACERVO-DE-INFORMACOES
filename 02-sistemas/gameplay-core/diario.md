---
titulo: Diário do AetheriusGameplayCore
tipo: historico
status: ativo
responsaveis: [mercurius17, kdu]
atualizado: 2026-11-14
tags: [diario, gameplay]
relacionados: [README.md]
---

# Diário do AetheriusGameplayCore

## Resumo
Registro cronológico do desenvolvimento, do mais recente para o mais antigo. Entradas curtas.

## 2026-11-14, raças e invocação (kdu / Claude Opus 5.5)
- **Raças:** todo personagem tinha a resistência ao frio e o Grito de Batalha do nórdico. O jogo carrega como Nórdico, e a aparência só troca o campo de raça da base. O cliente agora tira as habilidades das outras raças jogáveis e põe as da raça do personagem a cada aparência aplicada. Patch em `missoes/2026-11-14-bugs-akatosh`.
- **Invocação:** conferida sem o jogo, com addon e load order reais e um bot. O servidor cria o Familiar e o Atronach e eles ficam vivos. O "aparece e some" relatado é a cópia local do jogo, que a limpeza do cliente descarta. Falta descobrir no cliente por que a do servidor não aparece; precisa do log do cliente de uma tentativa.
- **Bloqueios:** invocação aguardando log.

## 2026-11-14, frente de dano (kdu / Claude Opus 5.5)
- **Feito:** kdu entra como responsável no GameplayCore pela frente de dano (confirmado pelo mercurius17 em 14/11). Documentadas as correções de dano do servidor em [correcoes-de-dano.md](correcoes-de-dano.md): fases de ativação, chaves, tabela de armadura e resistências, pendências. Pacote entregue ao mercurius17 em 13/11 sobre a v0.38.
- **Próximo:** confirmar as chaves ligadas no oficial; guia da Staff de dano quando a fase 1b for ligada.
- **Bloqueios:** nenhum

## 2026-11-14, planilha de design (mercurius17 / Claude Opus 5.5)
- **Feito:** planilha "AETHERIUS - Classes, efeitos, encantamentos" conferida aba por aba contra o código ([referência](../../06-referencias/planilha-classes-efeitos-encantamentos.md)); vale o código ([ADR-0003](../../03-decisoes/ADR-0003-codigo-prevalece-sobre-planilhas-de-design.md)). Skills e feitiços por classe idênticos; perks diferem em Arqueiro (sem Steady Aim) e Anti-Mago. Leveling: a tabela "Delta" e o grupo nos chefes da planilha não valem. Fichas por classe geradas de `classes-config.json`, `perks-descriptions.json` e `spells-descriptions.json`. Raças/pedras, poções, encantamentos e maldições levados ao guia da Staff; os três últimos como design.
- **Próximo:** decidir o total de XP das maldições (a planilha se contradiz: 46.500 ou 2 milhões).
- **Bloqueios:** nenhum

## 2026-11-14, guia da Staff (mercurius17 / Claude Opus 5.5)
- **Feito:** guias leigos em `07-guia-da-staff/`: classes, progressão (XP, grupo, fadiga, atributos), inimigos e masmorras, manutenção de equipamentos; glossário e painel de progresso atualizados. Fonte: configs do GameplayCore (`leveling-system/config`, `class-system/config/classes-config.json`, `durability-system/config`) e as portas do host no `aetherius-server` (painel F7, `enemy-spawns-service.js`, `durability-service.js`, `aetheriusCoreClassUi.ts`).
- **Próximo:** atualizar os guias quando o XP por abate for ligado no oficial e quando a aba Manutenção entrar na interface.
- **Bloqueios:** nenhum

## 2026-11-14 (mercurius17 / Claude Opus 5.5)
- **Feito:** sincronização SYNC-1114, commit `05f5a0f` na `main`, a partir da cópia `server/gameplay/core` do servidor oficial de 13/11 (entrega v0.37.0). Entrou:
  - skills de classe entregues ao cliente (`runtime/src/class-skills.ts`, `modules/class-progression-system/src/skills.ts`);
  - capacidade de invocação por classe/raça/pedra (`runtime/src/summon-capacity.ts`);
  - comando `class.level` do painel da staff (`setProgressLevel` em `leveling-system/src/persistence/durable.ts`, `createLevelCommands`); se o novo nível der menos pontos que os gastos, a distribuição de atributos é zerada;
  - Anti-Mago com a Maestria em Conjuração (`skyrim.esm|0F2CA7`) no nível 1, pré-requisito do Mystic Binding no nível 10;
  - chaves `character:<uuid>` aceitas, com lock próprio; migrations `0013_gc_professions_uuid_v2.sql` e `0014_gc_profession_world.sql`.
- **Ficou de fora:** catálogos v0.37.0; o export `professionApi` (importa `../../../professions`, fora do Core); arquivos em que o servidor está atrás do Core (`ds5a/Host.h`, README do `aetherius_combat`, dois testes). Motivos no [ADR-0002](../../03-decisoes/ADR-0002-sincronizacao-dos-cores-com-os-repositorios-oficiais.md).
- **Testes:** 350/381 (antes: 334/365); as 26 falhas são as mesmas da linha de base `efe7e4c`, todas por falta de exports/`dist`. Os 16 testes novos passam. Typecheck: os mesmos 6 erros de antes em `runtime/src/combat-profile.ts`.
- **Próximo:** decidir para quais masters os catálogos são assinados; alinhar o fim de linha da `0013` entre servidor e Core; levar a DS-7 de volta à cópia do servidor.
- **Bloqueios:** nenhum
- **Decisões:** [ADR-0002](../../03-decisoes/ADR-0002-sincronizacao-dos-cores-com-os-repositorios-oficiais.md)
- **Nota completa no repositório:** `docs/operations/SINCRONIZACAO_SERVIDOR_20261114.md`

## 2026-11-02 (mercurius17 / Claude Opus 5.5)
- **Feito:** SYNC-1102 (`7fbf070`, `41e8334`, `efe7e4c`): DS-5c, DS-7 (`hitSeq` no `Host.h`), perfis `mundo.*` de XP e `formId` dos kits de durabilidade trazidos do servidor (`8b5d299`).
- **Decisões:** catálogos não reassinados, pendente do dono. Nota: `docs/operations/SINCRONIZACAO_SERVIDOR_20261102.md`.
