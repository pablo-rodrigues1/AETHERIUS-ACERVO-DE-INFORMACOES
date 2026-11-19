# Dados houseCARL — Ritual / runa / áudio (19/11/2026)

## Escopo e origem dos dados

- Perfil MO2 lido: `AETHERIUS - LOCAL`.
- Instância MO2: `C:\modOrganizer`.
- Jogo/Data: `C:\Games\Steam\steamapps\common\Skyrim Special Edition\Data`.
- Plugin analisado nos records próprios: `AETHERIUS - SOBRENATURAL.esp`.
- houseCARL foi apontado para a instância LOCAL e confirmou 569 mods habilitados, 437 plugins no load order e 436 plugins ativos.
- Records lidos pelo `housecarl_records`, fonte `winner`, epoch do load order `e2-22a0c680484df11f`.
- Referências reversas lidas por `housecarl_records(references=...)`; o índice reportou 436 partições de plugin e 1 record não parseável excluído da caminhada.
- Ativos lidos por `housecarl_asset_status` no VFS do perfil LOCAL. Os cinco caminhos solicitados foram encontrados como arquivos loose.
- Duração e propriedades dos WAVs foram lidas do conteúdo local dos arquivos por parser WAV PCM; caminhos completos e tamanhos estão nas tabelas abaixo.
- O asset scan reportou que não conseguiu ler a lista `sResourceArchiveList` de `Skyrim.ini`; os BSA base do jogo não entraram no scan. Os cinco caminhos desta coleta foram encontrados em mods loose, com vencedor reportado.

## 1. SOUN, SNDR e SOPM

### SOUN `000843:AETHERIUS - SOBRENATURAL.esp` — `SN_Veu_RitualCharge`

```text
type=SoundMarker
formid=000843:AETHERIUS - SOBRENATURAL.esp
runtime=5C000843
editorid=SN_Veu_RitualCharge
winner=AETHERIUS - SOBRENATURAL.esp
override_depth=1

EditorID = SN_Veu_RitualCharge
FNAM = (absent)
FormKey = 000843:AETHERIUS - SOBRENATURAL.esp (→ SN_Veu_RitualCharge)
FormVersion = 44
IsCompressed = False
IsDeleted = False
MajorRecordFlagsRaw = 0
ObjectBounds = [ObjectBounds]
ObjectBounds.First = 0,0,0
ObjectBounds.Second = 0,0,0
SNDD = (absent)
SkyrimMajorRecordFlags = 0
SoundDescriptor = 073FD9:Skyrim.esm (→ MAGDestructionFireCharge300)
Version2 = 0
VersionControl = 0
```

### SNDR `073FD9:Skyrim.esm` — `MAGDestructionFireCharge300`

```text
winner=Skyrim.esm
override_depth=1

AlternateSoundFor = (null link)
Category = 01A0BD:Skyrim.esm (→ AudioCategoryMAG "Magic")
Conditions = [list: 0 item(s)]
EditorID = MAGDestructionFireCharge300
FormKey = 073FD9:Skyrim.esm (→ MAGDestructionFireCharge300)
FormVersion = 40
IsCompressed = False
IsDeleted = False
LoopAndRumble = [SoundLoopAndRumble]
LoopAndRumble.Unknown = 1
LoopAndRumble.Loop = None
LoopAndRumble.Unknown2 = 0
LoopAndRumble.RumbleValues = 0
MajorRecordFlagsRaw = 0
OutputModel = 07E5DC:Skyrim.esm (→ SOMMono01800)
PercentFrequencyShift = 0
PercentFrequencyVariance = 0
Priority = 128
SkyrimMajorRecordFlags = 0
SoundFiles = [list: 1 item(s)]
SoundFiles[0] = Data\Sound\FX\MAG\Destruction\MAG_Destruction_Fire_Charge_300.wav
StaticAttenuation = 6.61
String = (absent)
Type = Standard
Variance = 0
Version2 = 2
VersionControl = 2582785
```

Arquivo resolvido no VFS: `C:\modOrganizer\mods\Sonorum Arcana - The Magic Sound Compendium\sound\fx\mag\destruction\mag_destruction_fire_charge_300.wav`; vencedor `Sonorum Arcana - The Magic Sound Compendium` (loose); existe; 591358 bytes; WAV PCM, 2 canais, 16-bit, 44100 Hz, 147780 frames, duração `3.351020 s`, compressão `NONE`.

### SOPM `07E5DC:Skyrim.esm` — `SOMMono01800`

```text
winner=Skyrim.esm
override_depth=1

Attenuation = [SoundOutputAttenuation]
Attenuation.Unknown = 16424320
Attenuation.MinDistance = 150
Attenuation.MaxDistance = 1800
Attenuation.Curve = 6432140500
Attenuation.Unknown2 = 000000
CNAM = (absent)
Data = [SoundOutputData]
Data.Flags = AttenuatesWithDistance
Data.Unknown = 0
Data.ReverbSendPercent = 0.11764705882352941
EditorID = SOMMono01800
FNAM = (absent)
FormKey = 07E5DC:Skyrim.esm (→ SOMMono01800)
FormVersion = 40
IsCompressed = False
IsDeleted = False
MajorRecordFlagsRaw = 0
OutputChannels = (absent)
SNAM = (absent)
SkyrimMajorRecordFlags = 0
Type = UsesHrtf
Version2 = 5
VersionControl = 2582794
```

## 2. Feitiço do Ritual e MGEF da runa

### SPEL `00083A:AETHERIUS - SOBRENATURAL.esp` — `SN_Veu_RitualRune`

```text
type=Spell
runtime=5C00083A
winner=AETHERIUS - SOBRENATURAL.esp
override_depth=1

BaseCost = 0
CastDuration = 0
CastType = ConstantEffect
ChargeTime = 0
Description = 
EditorID = SN_Veu_RitualRune
Effects = [list: 1 item(s)]
Effects[0].BaseEffect = 00000F:MasterSpellsRunes.esp (→ FXSummonMassBodyHolderRune "SummonMassBodyArtHolderRune")
Effects[0].Data.Magnitude = 0
Effects[0].Data.Area = 0
Effects[0].Data.Duration = 0
Effects[0].Conditions = [list: 0 item(s)]
EquipmentType = (null link)
Flags = 0
FormKey = 00083A:AETHERIUS - SOBRENATURAL.esp (→ SN_Veu_RitualRune)
FormVersion = 44
HalfCostPerk = (null link)
IsCompressed = False
IsDeleted = False
Keywords = (absent)
MajorRecordFlagsRaw = 0
MenuDisplayObject = (null link)
Name = (absent)
ObjectBounds.First = 0,0,0
ObjectBounds.Second = 0,0,0
Range = 0
SkyrimMajorRecordFlags = 0
TargetType = Self
Type = Ability
Version2 = 0
VersionControl = 0
```

### MGEF `00000F:MasterSpellsRunes.esp` — `FXSummonMassBodyHolderRune`

```text
type=MagicEffect
runtime=FE05200F
winner=MasterSpellsRunes.esp
override_depth=1

Archetype = [MagicEffectArchetype] Association=Null
Archetype.AssociationKey = (null link)
Archetype.Type = Script
Archetype.Association = (null link)
Archetype.ActorValue = None
BaseCost = 0
CastType = ConstantEffect
CastingArt = (null link)
CastingLight = (null link)
CastingSoundLevel = Loud
Conditions = [list: 0 item(s)]
CounterEffects = [list: 0 item(s)]
Description = 
DualCastArt = (null link)
DualCastScale = 1
EditorID = FXSummonMassBodyHolderRune
EnchantArt = (null link)
EnchantShader = (null link)
EnchantVisuals = (null link)
EquipAbility = (null link)
Explosion = (null link)
Flags = NoDuration, FXPersist, HideInUI
FormKey = 00000F:MasterSpellsRunes.esp (→ FXSummonMassBodyHolderRune "SummonMassBodyArtHolderRune")
FormVersion = 44
HitEffectArt = 00000E:MasterSpellsRunes.esp (→ SummonMassCastBodyFX02)
HitShader = (null link)
HitVisuals = (null link)
ImageSpaceModifier = (null link)
ImpactData = (null link)
IsCompressed = False
IsDeleted = False
Keywords = (absent)
MagicSkill = None
MajorRecordFlagsRaw = 0
MenuDisplayObject = (null link)
MinimumSkillLevel = 0
Name = SummonMassBodyArtHolderRune
PerkToApply = (null link)
Projectile = (null link)
ResistValue = None
ScriptEffectAIDelayTime = 0
ScriptEffectAIScore = 0
SecondActorValue = None
SecondActorValueWeight = 0
SkillUsageMultiplier = 0
SkyrimMajorRecordFlags = 0
Sounds = [list: 0 item(s)]
SpellmakingArea = 0
SpellmakingCastingTime = 0
TaperCurve = 0
TaperDuration = 0
TaperWeight = 0
TargetType = Self
Unknown1 = 0
Version2 = 0
VersionControl = 0
VirtualMachineAdapter = (absent)
```

### Records que referenciam o MGEF `00000F` (`housecarl_records(references=...)`)

| Record com referência direta | Vencedor na leitura | Campo com referência |
|---|---|---|
| SPEL `044F59:Skyrim.esm` — `AbSummonMassCastBodyFX` | `MasterSpellsRunes.esp` | `Effects[1].BaseEffect = 00000F:MasterSpellsRunes.esp` |
| SPEL `00083A:AETHERIUS - SOBRENATURAL.esp` — `SN_Veu_RitualRune` | `AETHERIUS - SOBRENATURAL.esp` | `Effects[0].BaseEffect = 00000F:MasterSpellsRunes.esp` |
| SPEL `2590AD:LostGrimoire.esp` — `GRIM_AB_CON_SummonBodyFX_familiar` | `AETHERIUS - BALANCE.esp` | registro retornado na busca reversa para `00000F` |

O record `044F59:Skyrim.esm` (`AbSummonMassCastBodyFX`) é um SPEL com `Type = Ability`. Na versão vencedora de `MasterSpellsRunes.esp`, os efeitos são:

```text
Effects = [list: 2 item(s)]
Effects[0].BaseEffect = 044F55:Skyrim.esm (→ FXSummonMassBodyHolder "SummonMassBodyArtHolder")
Effects[0].Data.Magnitude = 100
Effects[0].Data.Area = 0
Effects[0].Data.Duration = 0
Effects[0].Conditions = [list: 0 item(s)]
Effects[1].BaseEffect = 00000F:MasterSpellsRunes.esp (→ FXSummonMassBodyHolderRune "SummonMassBodyArtHolderRune")
Effects[1].Data.Magnitude = 100
Effects[1].Data.Area = 0
Effects[1].Data.Duration = 0
Effects[1].Conditions = [list: 0 item(s)]
```

O campo `EquipAbility` de 62 MGEFs retornou `044F59:Skyrim.esm` (`AbSummonMassCastBodyFX`) em `housecarl_records(references=...)`. Exemplos retornados: `099F35:Skyrim.esm` (`MGRSummonDremoraEffect`), `000845:Update.esm` (`EnchSummonStormThrall`), `10DDED:Skyrim.esm` (`MAG_SummonDremoraLord`) e `00FFF9:MysticismMagic.esp` (`MAG_SummonDremoraHunter`).

### Perks, abilities e scripts no `MasterSpellsRunes.esp`

- A busca de registros definidos pelo plugin encontrou 24 registros no total; nos tipos `Spell`, `Perk`, `Quest` e `MagicEffect`, foram retornados 12 records, todos MGEF. O restante são records `ArtObject`.
- O registro definido pelo plugin que referencia diretamente o MGEF `00000F` é `044F59:Skyrim.esm` (`AbSummonMassCastBodyFX`), tipo `Spell`, `Type = Ability`, vencedor `MasterSpellsRunes.esp`.
- `housecarl_check(findings=["scripts"], plugins=["MasterSpellsRunes.esp"])`: 1 plugin varrido; 0 records com scripts; 0 propriedades não vinculadas; 0 vinculadas a null; 0 não verificáveis.
- No MGEF `00000F`, `VirtualMachineAdapter = (absent)`. Não houve VMAD/script associado a este record para decompilar.
- A consulta `housecarl_records(references=["00000F:MasterSpellsRunes.esp"])` retornou 3 referências diretas, listadas acima. A consulta reversa do helper `044F59` retornou 62 MGEFs que apontam para ele por `EquipAbility`.

## 3. WAVs dos SNDR `00083B` a `00083E` no perfil LOCAL

| SNDR / EditorID | `SoundFiles[0]` no record | Vencedor VFS | Existe | Dados do arquivo local |
|---|---|---|---|---|
| `00083B` `SN_Veu_BreathingSD` | `Sound\FX\Aetherius\Witch\breathing_witch.wav` | `AETHERIUS - SERVIDOR LOCAL` (loose) | Sim | `C:\modOrganizer\mods\AETHERIUS - SERVIDOR LOCAL\Sound\FX\Aetherius\Witch\breathing_witch.wav`; 1667150 bytes; mono, 16-bit, 44100 Hz; 833536 frames; 18.901043 s; PCM `NONE` |
| `00083C` `SN_Veu_ScreamSD` | `Sound\FX\Aetherius\Witch\scream_witch.wav` | `AETHERIUS - SERVIDOR LOCAL` (loose) | Sim | `C:\modOrganizer\mods\AETHERIUS - SERVIDOR LOCAL\Sound\FX\Aetherius\Witch\scream_witch.wav`; 968782 bytes; mono, 16-bit, 44100 Hz; 484352 frames; 10.983039 s; PCM `NONE` |
| `00083D` `SN_Veu_Whisper2SD` | `Sound\FX\Aetherius\Witch\whisper2_witch.wav` | `AETHERIUS - SERVIDOR LOCAL` (loose) | Sim | `C:\modOrganizer\mods\AETHERIUS - SERVIDOR LOCAL\Sound\FX\Aetherius\Witch\whisper2_witch.wav`; 534606 bytes; mono, 16-bit, 44100 Hz; 267264 frames; 6.060408 s; PCM `NONE` |
| `00083E` `SN_Veu_WhisperingSD` | `Sound\FX\Aetherius\Witch\whispering_witch.wav` | `AETHERIUS - SERVIDOR LOCAL` (loose) | Sim | `C:\modOrganizer\mods\AETHERIUS - SERVIDOR LOCAL\Sound\FX\Aetherius\Witch\whispering_witch.wav`; 987214 bytes; mono, 16-bit, 44100 Hz; 493568 frames; 11.192018 s; PCM `NONE` |

Dados adicionais dos quatro SNDR na leitura de winner: todos `Category = 064451:Skyrim.esm` (`AudioCategoryUI`, "Menus"); todos `LoopAndRumble.Loop = None`, `LoopAndRumble.Unknown = 2`, `Unknown2 = 0`, `RumbleValues = 0`; todos `OutputModel = 07EDCA:Skyrim.esm` (`SOMStereo`); todos `Priority = 128`, `Type = Standard`, frequência shift/variance `0`, `Variance = 0`, `Conditions = [0]`. `StaticAttenuation` dos records 83B/83C = 8; 83D/83E = 12.
