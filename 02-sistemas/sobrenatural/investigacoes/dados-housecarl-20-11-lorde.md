# Dados houseCARL — Lorde Vampiro (20/11/2026)

## Artefato e leitura

- Plugin consultado: `C:\modOrganizer\Mods\AETHERIUS - SOBRENATURAL\AETHERIUS - SOBRENATURAL.esp`
- SHA-256 declarado: `7A1397FE2B1DE84016E9CE688C3F06DFC19E9F9143C144806FDE1972DF2CE110`
- SHA-256 confirmado por `Get-FileHash`: `7A1397FE2B1DE84016E9CE688C3F06DFC19E9F9143C144806FDE1972DF2CE110`
- Perfil houseCARL: `AETHERIUS - LOCAL`; plugin ativo; build houseCARL `2.0.0+5e3c744862bc17c85ebe850830603a9a3e6f0779`; epoch de leitura `e2-1e4c187a4a284e4a`.
- Consultas de records foram feitas pelo load order ativo, por plugin/FormID. Nenhuma escrita foi feita no MO2/modpack.

## 1. SoundMarker (SOUN) e SoundDescriptor (SNDR) novos

Busca houseCARL por records definidos em `AETHERIUS - SOBRENATURAL.esp`, tipos `SOUN` e `SNDR`: **37 records — 19 SOUN e 18 SNDR**. Nos SOUN, o loop é o campo do SNDR apontado pelo `SoundDescriptor`; os SOUN não têm flag de loop própria. `LoopAndRumble.Loop` do SNDR é `None` em todos abaixo, exceto `000851`, que é `EnvelopeFast`.

| SOUN FormID / EditorID | SNDR FormID / EditorID | `SoundFiles[0]` | `LoopAndRumble.Loop` | VFS: arquivo vencedor |
|---|---|---|---|---|
| `00083F` `SN_Veu_Breathing` | `00083B` `SN_Veu_BreathingSD` | `Data\Sound\FX\Aetherius\Witch\breathing_witch.wav` | `None` | `AETHERIUS - SERVIDOR LOCAL` |
| `000840` `SN_Veu_Scream` | `00083C` `SN_Veu_ScreamSD` | `Data\Sound\FX\Aetherius\Witch\scream_witch.wav` | `None` | `AETHERIUS - SERVIDOR LOCAL` |
| `000841` `SN_Veu_Whisper2` | `00083D` `SN_Veu_Whisper2SD` | `Data\Sound\FX\Aetherius\Witch\whisper2_witch.wav` | `None` | `AETHERIUS - SERVIDOR LOCAL` |
| `000842` `SN_Veu_Whispering` | `00083E` `SN_Veu_WhisperingSD` | `Data\Sound\FX\Aetherius\Witch\whispering_witch.wav` | `None` | `AETHERIUS - SERVIDOR LOCAL` |
| `000843` `SN_Veu_RitualCharge` | `073FD9:Skyrim.esm` `MAGDestructionFireCharge300` (descriptor vanilla, não novo no plugin) | `Data\Sound\FX\MAG\Destruction\MAG_Destruction_Fire_Charge_300.wav` | `None` | `Sonorum Arcana - The Magic Sound Compendium` |
| `000845` `SN_VampireSwarmIn` | `000850` `SN_VampireSwarmInSD` | `Data\Sound\FX\DLC01\MAG\nightcloak\mag_night_cloak_bats_in_01.wav` | `None` | `AETHERIUS - Vampire Lord and Werewolf SFX` |
| `000846` `SN_VampireSwarmLoop` | `000851` `SN_VampireSwarmLoopSD` | `Data\Sound\FX\DLC01\MAG\nightcloak\mag_night_cloak_bats_hit_01_lpm.wav` | `EnvelopeFast` | `AETHERIUS - Vampire Lord and Werewolf SFX` |
| `000847` `SN_VampireSwarmOut` | `000852` `SN_VampireSwarmOutSD` | `Data\Sound\FX\DLC01\MAG\nightcloak\mag_night_cloak_bats_out_01.wav` | `None` | `AETHERIUS - Vampire Lord and Werewolf SFX` |
| `000848` `SN_VampireBatIn` | `000853` `SN_VampireBatInSD` | `Data\Sound\FX\DLC01\MAG\powers\mag_powers_bats_01.wav` | `None` | `AETHERIUS - Vampire Lord and Werewolf SFX` |
| `000849` `SN_VampireBatOut` | `000854` `SN_VampireBatOutSD` | `Data\Sound\FX\DLC01\MAG\powers\mag_powers_batsfinish_01.wav` | `None` | `AETHERIUS - Vampire Lord and Werewolf SFX` |
| `00084A` `SN_VampireMistIn` | `000855` `SN_VampireMistInSD` | `Data\Sound\FX\DLC01\MAG\powers\mag_powers_mistform_in.wav` | `None` | `AETHERIUS - Vampire Lord and Werewolf SFX` |
| `00084B` `SN_VampireMistOut` | `000856` `SN_VampireMistOutSD` | `Data\Sound\FX\DLC01\MAG\powers\mag_powers_mistform_out.wav` | `None` | `AETHERIUS - Vampire Lord and Werewolf SFX` |
| `00084C` `SN_VampireCelerityIn` | `000857` `SN_VampireCelerityInSD` | `Data\Sound\FX\DLC01\MAG\powers\mag_powers_supernaturalreflexes_in_01.wav` | `None` | `AETHERIUS - Vampire Lord and Werewolf SFX` |
| `00084D` `SN_VampireCelerityOut` | `000858` `SN_VampireCelerityOutSD` | `Data\Sound\FX\DLC01\MAG\powers\mag_powers_supernaturalreflexes_out_01.wav` | `None` | `AETHERIUS - Vampire Lord and Werewolf SFX` |
| `00084E` `SN_VampireStrengthIn` | `000859` `SN_VampireStrengthInSD` | `Data\Sound\FX\DLC01\MAG\powers\mag_alteration_telekinesis_vampire_throw.wav` | `None` | `AETHERIUS - Vampire Lord and Werewolf SFX` |
| `00084F` `SN_VampireStrengthOut` | `00085A` `SN_VampireStrengthOutSD` | `Data\Sound\FX\DLC01\MAG\powers\mag_alteration_telekinesis_vampire_grab.wav` | `None` | `AETHERIUS - Vampire Lord and Werewolf SFX` |
| `000863` `SN_Lorde_Transformacao1` | `000866` `SN_Lorde_Transformacao1SD` | `Data\Sound\FX\DLC01\NPC\VampireLord\NPC_VampireLord_Transformation_01_2D.wav` | `None` | `AETHERIUS - Vampire Lord and Werewolf SFX` |
| `000864` `SN_Lorde_Transformacao2` | `000867` `SN_Lorde_Transformacao2SD` | `Data\Sound\FX\DLC01\NPC\VampireLord\NPC_VampireLord_Transformation_02_2D.wav` | `None` | `AETHERIUS - Vampire Lord and Werewolf SFX` |
| `000865` `SN_Lorde_Transformacao3` | `000868` `SN_Lorde_Transformacao3SD` | `Data\Sound\FX\DLC01\NPC\VampireLord\NPC_VampireLord_Transformation_02_3D.wav` | `None` | `AETHERIUS - Vampire Lord and Werewolf SFX` |

Os 14 caminhos `DLC01\MAG\...` e `DLC01\NPC\VampireLord\...` acima são fornecidos como arquivos soltos por `AETHERIUS - Vampire Lord and Werewolf SFX`. Os quatro WAVs `Aetherius\Witch\...` vencem como arquivos soltos de `AETHERIUS - SERVIDOR LOCAL`. O arquivo usado pelo Ritual vence como arquivo solto de `Sonorum Arcana - The Magic Sound Compendium`.

A consulta `housecarl_asset_status` reportou que não conseguiu ler a lista `[Archive] sResourceArchiveList` de `Skyrim.ini`; arquivos presentes somente em BSA vanilla podem não aparecer no inventário de assets. Os caminhos acima foram reportados diretamente pelos records e os 19 caminhos consultados retornaram fornecedores loose.

## 2. Enxame Devorador — magnitude e duração

- `33EACC:Curse of the Vampire.esp`, `COTV_DevouringSwarm`, tipo `LesserPower`; winner `AETHERIUS - SOBRENATURAL.esp`, override depth 2.
  - `Effects[0]`: `BaseEffect=09832C:Curse of the Vampire.esp` (`COTV_DevouringSwarmEffect`); magnitude `10`, área `0`, duração `15`.
  - `Effects[1]`: `BaseEffect=19A676:Curse of the Vampire.esp` (`COTV_DevouringSwarmCooldownEffect`); magnitude `0`, duração `60`.
- `09832C:Curse of the Vampire.esp`, `COTV_DevouringSwarmEffect`: archetype `Cloak`, associação `33EB02:Curse of the Vampire.esp`; condição `HasMagicEffect(19A676:Curse of the Vampire.esp) == 0`, run-on `Subject`.
- `33EB02:Curse of the Vampire.esp`, `COTV_DevouringSwarmCloakDamage`, tipo `Spell`; winner `AETHERIUS - SOBRENATURAL.esp`, override depth 2.
  - `Effects[0]`: `BaseEffect=0126B6:Dawnguard.esm`; magnitude `20`, área `0`, duração `1`; zero condições.
- Em `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\supernatural\powers.js`, `durationMs` é derivado da duração do efeito Cloak; `entry.dps` é derivado de `absorb.magnitude`. Os valores dos records acima são `15` segundos e `20` por segundo.

## 3. Abilities do Lorde por estágio — 00085B–000861

Records `00085B–000861:AETHERIUS - SOBRENATURAL.esp`: os sete são `Spell`, `Type=Ability`, `CastType=ConstantEffect`, `TargetType=Self`. Cada efeito listado tem duração `0`. Nos seis records de estágio, cada um dos cinco efeitos tem a condição `GetIsRace(00283A:Dawnguard.esm) == 1`, `RunOnType=Subject`; `00283A:Dawnguard.esm` é `DLC1VampireBeastRace` (`Vampire Lord`).

| FormID | EditorID / Name | Vida | Magicka | Vigor (`Stamina`) | Armadura (`DamageResist`) | RM (`ResistMagic`) |
|---|---|---:|---:|---:|---:|---:|
| `00085B` | `SN_Lorde_Estagio1` / Lorde Vampiro: Neófito | 200 | 200 | 200 | 100 | 20 |
| `00085C` | `SN_Lorde_Estagio2` / Lorde Vampiro: Vampiro Pleno | 250 | 250 | 250 | 150 | 25 |
| `00085D` | `SN_Lorde_Estagio3` / Lorde Vampiro: Ancilla | 300 | 300 | 300 | 200 | 30 |
| `00085E` | `SN_Lorde_Estagio4` / Lorde Vampiro: Ancião | 350 | 350 | 350 | 250 | 35 |
| `00085F` | `SN_Lorde_Estagio5` / Lorde Vampiro: Suserano | 400 | 400 | 400 | 300 | 40 |
| `000860` | `SN_Lorde_Estagio6` / Lorde Vampiro: Apoteótico | 450 | 450 | 450 | 350 | 45 |

`000861:AETHERIUS - SOBRENATURAL.esp`, `SN_Lorde_PerksSobrenaturais` / `Lorde Vampiro: Perks sobrenaturais`: três efeitos com `BaseEffect` `000800` (Health), `000801` (Magicka) e `000802` (Stamina), magnitude `50` em cada; duração `0`; cada efeito usa a mesma condição `GetIsRace(00283A:Dawnguard.esm) == 1`, `RunOnType=Subject`.

## 4. As 15 perks listadas em `VAMPIRE_LORD_PERKS`

Fonte da lista: `C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\vampire-grants.js:50–65`. Os 15 FormIDs consultados existem como records `Perk`. Em cada linha, o estágio é `Effects[0].Stage` do `PerkQuestEffect`; o quest link é `306FBE:Curse of the Vampire.esp` (`COTV_VampireLordPerkTreeQuest`). `Condição` abaixo é `Perk.Conditions` do record.

| FormID | EditorID / Name | Condição | PerkQuestEffect: Stage | EntryPoint adicional |
|---|---|---|---:|---|
| `33EB11:Curse of the Vampire.esp` | `COTV_SupernaturalEndurancePerk` / Resistência Sobrenatural | nenhuma | 90 | — |
| `0CFE3F:Curse of the Vampire.esp` | `COTV_SupernaturalAgilityPerk` / Agilidade sobrenatural | `HasPerk(15DA6B:Curse of the Vampire.esp)` | 110 | — |
| `0CFE3E:Curse of the Vampire.esp` | `COTV_SupernaturalWillPowerPerk` / Força de vontade sobrenatural | `HasPerk(0E9345:Curse of the Vampire.esp)` | 100 | — |
| `33EB0F:Curse of the Vampire.esp` | `COTV_IronWillPerk` / Iron Will | `HasPerk(33EB11:Curse of the Vampire.esp)` | 80 | `ModSpellCost`, `Multiply`, `0.5`; 17 `GetIsID` OR filters |
| `33EB0E:Curse of the Vampire.esp` | `COTV_BloodIsLifePerk` / O sangue é a vida | `HasPerk(33EB11:Curse of the Vampire.esp)` | 70 | — |
| `33EB12:Curse of the Vampire.esp` | `COTV_VampiricGripPerk` / Garra Vampírica | `HasPerk(33EB11:Curse of the Vampire.esp)` | 10 | `AddActivateChoice`, EntryPoint `Activate`, button `Drain Blood` |
| `016908:Dawnguard.esm` | `DLC1GargoylePerk` / Conjurar Gárgula; winner `Curse of the Vampire.esp` | `HasPerk(33EB12:Curse of the Vampire.esp)` | 20 | — |
| `33EB15:Curse of the Vampire.esp` | `COTV_RigorMortisPerk` / Rigor Mortis | `HasPerk(016908:Dawnguard.esm)` | 30 | — |
| `162B6D:Curse of the Vampire.esp` | `COTV_BloodWardPerk` / Ala de Sangue | `HasPerk(33EB11:Curse of the Vampire.esp)` | 40 | — |
| `162B6E:Curse of the Vampire.esp` | `COTV_BloodToPowerPerk` / Sangue ao Poder | `HasPerk(162B6D:Curse of the Vampire.esp)` | 50 | — |
| `33EB13:Curse of the Vampire.esp` | `COTV_SanguineRunePerk` / Runa Sanguinária | `HasPerk(162B6E:Curse of the Vampire.esp)` | 60 | — |
| `0E9345:Curse of the Vampire.esp` | `COTV_BloodMagicLordPerk` / Senhor da Magia Sangrenta | `HasPerk(33EB0E:Curse of the Vampire.esp)` | 130 | `ModSpellMagnitude`, Destruction `0.005`, `MultiplyOnePlusAVMult` |
| `15DA6B:Curse of the Vampire.esp` | `COTV_LordOfSoulsPerk` / Senhor das Almas | `HasPerk(33EB0F:Curse of the Vampire.esp)` | 140 | `ModSpellMagnitude`, Conjuration `0.005`, `MultiplyOnePlusAVMult` |
| `42792F:Curse of the Vampire.esp` | `COTV_VampiricTalonsPerk` / Talões Vampiricos | `HasPerk(33EB0E)` OR `HasPerk(33EB0F)` | 120 | — |
| `025B1A:Curse of the Vampire.esp` | `COTV_WingsOfTheNightPerk` / Asas da Noite | `HasPerk(42792F:Curse of the Vampire.esp)` | 150 | — |

Conditions detalhadas dos EntryPoints:

- **Iron Will**: as 17 formas de `GetIsID` OR são `33EAC4`, `33EAD6`, `33EAF6`, `1C2E83`, `55CA9A`, `473F48`, `33EAF8`, `33EAFA`, `33EAFC`, `33EAFE`, `33EB00`, `33EB04`, `33EB06`, `33EB08`, `33EB0A`, `33EB0C` de `Curse of the Vampire.esp`, e `016909:Dawnguard.esm`.
- **Garra Vampírica**: `RunOnTabIndex=0` contém `HasMagicEffect(007EBD:Dawnguard.esm) == 1` e `GetIsRace(00283A:Dawnguard.esm) == 1`; `RunOnTabIndex=1` contém `HasKeyword(013796:Skyrim.esm) == 0` e `HasKeyword(013794:Skyrim.esm) == 1`.
- **Senhor da Magia Sangrenta**: Tab 0 `HasKeyword(0A82BB:Skyrim.esm) == 1`; Tab 1 OR `GetIsID` para `33EB04`, `33EB06`, `33EB08`, `33EB0A`, `33EB0C` de `Curse of the Vampire.esp`.
- **Senhor das Almas**: Tab 0 `HasKeyword(0A82BB:Skyrim.esm) == 1`; Tab 1 OR `GetIsID` para `33EAF8`, `33EAFA`, `33EAFC`, `33EAFE`, `33EB00` de `Curse of the Vampire.esp`.
- Os demais records têm somente as condições `HasPerk` da tabela ou lista vazia. Os `PerkQuestEffect` listados têm `Conditions=[]` no próprio efeito.

Evidência de concessão e forma mortal no código:

- `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\supernatural-service.js:107–109`: `perks` é `vampire.VAMPIRE_LORD_PERKS` quando `lordGranted(c)`; essa seleção não consulta `lordActive`.
- `C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\vampire-grants.js:97–109`: `lordActive` controla a inclusão das magias da lista de favoritos, Dreno, abilities e ability de estágio em `spellsFor`; a lista de perks está separada.
- Nos records das perks, só a ativação `Activate` de `COTV_VampiricGripPerk` tem condição direta `GetIsRace(DLC1VampireBeastRace)`. Os EntryPoints de custo/magnitude listados acima filtram IDs de spells/keywords sem condição de raça. Os outros efeitos principais são `PerkQuestEffect` para o quest `306FBE` e estágio listado.
- O record `306FBE:Curse of the Vampire.esp` contém os estágios 10–150 e fragments VMAD vinculados ao script `QF__06306FBE`; os campos `QuestStage.LogEntries[*].Conditions` estão vazios. O corpo desse script não é exibido por `housecarl_records`; não há conclusão de execução das ações de QuestStage em forma mortal nesta coleta.

## 5. SNDR Dawnguard vanilla de transformação

Consulta do polo de origem `Dawnguard.esm`; os records abaixo têm winner atual `AETHERIUS - SOBRENATURAL.esp` (override depth 2). Valores listados são os do record definido em Dawnguard.

| FormID Dawnguard | EditorID | WAV | Loop |
|---|---|---|---|
| `005050:Dawnguard.esm` | `NPCVampireLordTransformation012D` | `Data\Sound\FX\DLC01\NPC\VampireLord\NPC_VampireLord_Transformation_01_2D.wav` | `None` |
| `005051:Dawnguard.esm` | `NPCVampireLordTransformation022D` | `Data\Sound\FX\DLC01\NPC\VampireLord\NPC_VampireLord_Transformation_02_2D.wav` | `None` |
| `005052:Dawnguard.esm` | `NPCVampireLordTransformation023D` | `Data\Sound\FX\DLC01\NPC\VampireLord\NPC_VampireLord_Transformation_02_3D.wav` | `None` |

## 6. housecarl_check — references e masters

Consulta `housecarl_check(findings=["errors"], plugins=["AETHERIUS - SOBRENATURAL.esp"])`:

- plugins examinados: `1`
- referências pendentes: `0`
- masters ausentes: `0`
- records não analisáveis: `0`
- resultado: `No errors found in the scanned scope.`

Scope da família `errors`: resolução de FormLinks, masters ausentes e falhas de parse.
