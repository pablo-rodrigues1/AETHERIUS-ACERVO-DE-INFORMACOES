# Etapa 2 (parcial) — Lobisomem funcionando no servidor local

Data: 2026-11-15. Autor: Opus 5.5 (orquestrador), a pedido do dev ("faça você"). Codex fora por limite. **Ainda sem revisão cruzada.**
Ambiente: `C:\Code\Aetherius - Servidor Local` (commit do repositório raiz `e71140e`).

## O que foi feito

1. **Base do lobisomem (kdu):** `overlays/lobisomem-kdu/001-servidor-lobisomem.patch` (SHA-256 `B7E2A8EF…3662`, cópia do pacote 14-11) aplicado em `source/`. O `werewolf-service` é dono da forma de fera e exige o poder Beast Form (`WerewolfChange`) em `knownSpells`.
2. **Serviço Sobrenatural** (`overlays/sobrenatural/`):
   - **Estado autoritativo** em `runtime/server/data/aetherius-supernatural.json`, por `characterId`. A keyword (`JogadorLobisomem`/`JogadorVampiro`/`JogadorBruxa`) existe só ali. `revision` monotônica (nunca reinicia), `grantId` novo a cada setagem, operações serializadas por personagem e gravação atômica (tmp + rename).
   - **UI Core:** módulo externo `supernatural` com `snapshot`/`choose` em `AetheriusUiSystem`, via a ponte `globalThis.aetheriusSupernaturalBridge` (mesmo padrão de residências e profissões), e push pós-commit (`event`/`changed`) para a sessão do ator.
   - **Staff:** `/maldicao <eu|0xATOR> <lobisomem|vampiro|bruxa|remover|xp N|estado>`, com a permissão `retire_character` (admin/owner). O painel externo deve chamar a mesma API (`mp.__aetheriusSupernatural.grant/remove/addXp`).
   - **Setagem:** cria o estado e pede o reset de classe pela ponte `globalThis.aetheriusClassReset`. **A ponte ainda não existe**: o comando avisa "reset de classe PENDENTE".
   - **Remoção:** apaga todo o progresso, tira o poder e as habilidades, remove as perks e encerra a forma de fera se ela estiver ativa.
3. **Efeitos do lobisomem por estágio** (records conferidos pelo houseCARL no perfil oficial):
   - **Magias (Papyrus do servidor, `AddSpell`):** `WerewolfChange` (Beast Form) e só a habilidade de nível do estágio atual: `MAG_WWLevelAbility15/20/25/30/35/40` (Skyrim.esm, alteradas pelo Manbeast).
   - **Perks (cliente):** o Papyrus do servidor não tem `AddPerk`. A propriedade `aetheriusSupernaturalPerks`, visível só ao dono, leva a lista e um trecho do cliente aplica e remove as perks, só as que ele mesmo pôs.

     | Estágio | Perks (acumulam) |
     |---|---|
     | Iniciado | MAG_AnimalVitality01, MAG_BeastlyMight, MAG_BloodyClaws01, MAG_FeralSpeed01 |
     | Ômega | MAG_Manbeast01 (Dawnguard 0x59A4), MAG_AnimalStrength01, MAG_PrimalInstinct01 (Uivo do Terror), MAG_CalloftheBlood01 |
     | Beta | MAG_AnimalVitality02, MAG_AnimalVigor01, MAG_HircinesBounty |
     | Gama | MAG_Manbeast02 (Dawnguard 0x7A3F), MAG_AnimalStrength02, MAG_ThickHide |
     | Delta | MAG_AnimalVigor02, MAG_SavageStrength (Dawnguard 0x11CFB), MAG_BloodyClaws02 |
     | Alfa | MAG_HircinesRage, MAG_CalloftheBlood02, MAG_PrimalInstinct02 (Uivo da Fúria) |

   - Os FormIDs de runtime são resolvidos no servidor pelo plugin + id local e conferidos pelo editorId. Boot: **27/27 encontrados**.
4. **Cliente local:** o módulo de UI foi instalado no mod MO2 `AETHERIUS - SERVIDOR LOCAL` (perfil `AETHERIUS - LOCAL`), em `MeridianUI/aetheriusui/modules/supernatural/`. O `modules-config.js` do mod é o do UI Core oficial mais a entrada `supernatural`.

## Evidências

- `node --check` em todos os arquivos novos; trecho do cliente compilado com `new Function`.
- `overlays/sobrenatural/teste-servico.cjs` passou: estado vazio; setagem (rev 1, Beast Form + nível 15, 4 perks); +190.000 XP (Gama: só nível 30, 14 perks); keyword ausente do estado; recusa de dupla maldição; remoção (rev 3, tudo retirado); vampiro com `NO_SLOT`, `STALE_GRANT` e escolha válida de Celeridade; comando `/maldicao eu estado`; keyword gravada só em disco.
- `Build.ps1` + `Prepare.ps1` + `Start.ps1` sem erros. Log: `[werewolf] Lobisomem sincronizado ativo…` e `[supernatural] Sobrenatural ativo: 0 portador(es); forms do lobisomem 27/27`.

## Pendências (não feitas)

- **Teste em jogo** (TAB → Sobrenatural, `/maldicao eu lobisomem`, transformação).
- **Lado cliente do kdu:** `cliente-lobisomem.patch` (troca de raça sincronizada em `remoteServer.ts`/`appearance.ts`) exige rebuild do `skymp5-client.js`. O plugin `AETHERIUS - LOBISOMEM.esp` e o mod de animação/sons precisam entrar no MO2 **e** no manifesto de plugins do servidor. Sem isso, a transformação não aparece corretamente para os outros jogadores.
- **Bônus sem record:** vida e vigor da forma humana (125–250), vida e vigor somados na fera, armadura/RM da fera (150–400 / 10–35%) e dano desarmado (5–30) **não existem como records**. Precisam de habilidades (SPEL/MGEF) num plugin, por exemplo no `AETHERIUS - LOBISOMEM.esp`, o que é escrita no modpack e depende de autorização. Hoje vale o que as `MAG_WWLevelAbility` do Manbeast dão.
- **XP de maldição** pelas mesmas ações da classe: falta o gancho no GameplayCore. Por enquanto só `/maldicao … xp N`.
- **Reset de classe** na setagem: falta `globalThis.aetheriusClassReset` no AetheriusClassStore.
- Achados 4 e 5 da revisão 1.2 (mensagens de recusa na UI) e revisão cruzada desta etapa.

## Painel de teste (F8)

Só no servidor local, para staff. `client-test-panel/supernatural-test.js` fica instalado só no mod MO2 `AETHERIUS - SERVIDOR LOCAL`. Ação `test` no servidor (`grant`/`remove`/`xp`/`status`), ligada por `AETHERIUS_SUPERNATURAL_TEST_PANEL=true`. A tecla é lida pelo trecho do cliente (`sp.Input.isKeyPressed(0x42)`), que manda `{type:"supernaturalHotkey"}` à página por `AetheriusUI.ToView`. A página abre a UI como o TAB e vai para o módulo. Não testado em jogo.
