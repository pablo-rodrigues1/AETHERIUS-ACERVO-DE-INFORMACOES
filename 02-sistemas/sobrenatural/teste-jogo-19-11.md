# Teste em jogo — 19/11/2026 (dev, solo, perfil AETHERIUS - LOCAL, conta 1) — PENDÊNCIAS PARA A PRÓXIMA SESSÃO

> **Status: apenas registrado. Nada foi despachado.** O dev pediu para corrigir em outra sessão (contexto novo). Comece por aqui + `RETOMADA.md`.

## Ambiente testado
- Servidor local iniciado em 19/11 00:30 (PID 7336), log `C:\Code\Aetherius - Servidor Local\logs\server-20261119-003042.stdout.log` / `.stderr.log`.
- Laboratório `C:\Code\Aetherius - Servidor Local` HEAD `5effbd8` (inclui 7435282, 77ea350, 850211c, 09c7767, 01056e2, 42315d1, 1887fd0, 65edf8c); addon nativo `tools\native\scam_native.node` SHA-256 `4f622a37…` (com patch 026); cliente `skymp5-client.js` SHA-256 `fd61c198…` (client-source `295f02e`), UI CORE (LOCAL) sincronizada. Backups em `backups\deploy-20261119-002955\`.
- Screenshots: `C:\Users\pablo\AppData\Local\Temp\claude\C--Code-Aetherius---New-Systems\25a0029f-cff2-4576-bc6c-975bce6a82ef\images\9.png` e `10.png`.

## Resultado

### OK
- Vampiro: HUD da Reserva de Sangue apareceu.
- Bruxa: selo (gauge) apareceu; HUD/timer do ritual apareceu; sussurros da Sobrecarga funcionando.
- Lobisomem: transformação forçada pelo painel (F7) funcionou.
- Vampiro: **Vampiric Vision** funcionou.

### Defeitos a corrigir
| # | Sistema | Defeito | Observação / pista |
|---|---|---|---|
| T1 | Lobisomem | Equipar Beast Form + Z ainda **não transforma**. | Mesmo após 7435282/77ea350/850211c (Opção A). Conferir no log `evidence voice`, `refused reason=` e `command ... accepted`. Ver `investigacoes/INVESTIGACAO_18-11_transformacoes.md` e `REVISAO-L1-V6_*.md` (risco A1: `actionVoiceFire` pode não chegar). |
| T2 | Vampiro | Z para **Lorde Vampiro não transforma** (provavelmente mesma causa de T1). | Idem. |
| T3 | Lobisomem | Console em loop: `[Script] Trace in GamemodeUpdateService: updateOwner aetheriusBeastCompulsion - TypeError: fx.cleanup is not a function at effects (e…` (prints 9 e 10, com o personagem humano). | Cliente da transformação forçada (`forced-werewolf-client.js`): `fx` antigo em `sp.storage` sem `cleanup` (provável storage legado sem versão, mesmo padrão já corrigido na vinheta/heartbeat). |
| T4 | Lobisomem | Na forma de fera o personagem fica **muito lento**. | Dev acredita que precisa **+50% de velocidade** na fera (verificar se é bug da transformação ou valor de SpeedMult da raça/kdu). |
| T5 | Lobisomem | Transformado com **dois feitiços** equipados: **não consegue atacar**. | Desequipar/limpar mãos ao transformar. |
| T6 | Lobisomem | Transformado com espada de duas mãos: a **espada fica na mão da fera**, ataca com uma mão só, sem combos, e o **Z não uiva**. | Dev atribui o ataque de uma mão ao **Precision Creatures** e **removeu o mod**. Também deve desequipar armas ao transformar e garantir o poder de uivo no Z da fera. |
| T7 | Vampiro | **Aura não funciona** (Feral e afins). | Trocar para o mod de auras abaixo, **aura vermelha (Red)**. |
| T8 | Bruxa | **Aura da Sobrecarga não funciona.** | Trocar para o mod de auras abaixo, **aura roxa (Purple)**. |
| T10 | Vampiro | **Bat Form: o avanço (dash) não funcionou.** | Implementação nova de 09c7767/01056e2 (movimento no dono via eventos COTV + guarda nativa 026, limite 256 u, recarga 3 s). Conferir no log do servidor recusas da guarda/actionPolicy e se o evento de movimento chega; ver `REVISAO-PODERES-VAMPIRO_neon.md` e ETAPA-3. |
| T9 | Bruxa | **Runa de Conjuração sumiu**: não aparece mais durante o ritual. | Regressão do lote B5/B7 (42315d1/65edf8c; ver `DADOS-HOUSECARL_19-11_ritual.md` e `REVISAO-BRUXA-HUD-RITUAL_neon.md`). Conferir se B7 (todo feitiço invocando a runa) segue corrigido junto. |

### Mudanças no modpack feitas pelo dev (19/11)
- **Removido:** Precision Creatures.
- **Adicionado:** `C:\modOrganizer\mods\New Creature Animation - Werewolf`.
- **Ambos exigem regerar o Pandora** (Pandora Behaviour Engine; saída no mod `AETHERIUS - LOCAL - PANDORA` do perfil LOCAL) antes do próximo teste.
- **Auras:** usar **Colored Magic Auras** (Nexus 25773), já em `C:\modOrganizer\mods\Colored Magic Auras\` com `Colored Magic Auras.esp`. Vampiro = **Red**, Bruxa = **Purple**. Verificar via houseCARL os records das auras, ativação no perfil LOCAL, load order do servidor (`Prepare` → `aetherius-loadorder.json`) e a sincronização para observadores.

## Sugestão de distribuição (para a próxima sessão)
- T1/T2: Opus 5.5 Low (continua a investigação) ou Sol High; exige log de teste com Z.
- T3: Terra High (storage versionado, padrão já existente).
- T4/T5/T6: Sol High (transformação, equipamento e raça da fera) + Luna MAX (houseCARL: SpeedMult da raça/kdu, poderes de uivo) + regerar Pandora.
- T7/T8: Sol High (sincronização) + Luna MAX (records do Colored Magic Auras).
- T9: Sol High (autor do lote do ritual).
- T10: Sol High (autor do dash).
- Revisão: Gemini 4 Neon (regra de revisão honesta).
