# Dados houseCARL — velocidade da fera (19/11/2026)

## Escopo da coleta

- Plugin solicitado: `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\plugin\AETHERIUS - SOBRENATURAL.esp`.
- Commit informado: `535c284` (`feat(sobrenatural): aumenta velocidade da fera por habilidade de estágio`).
- OID do blob Git no commit e do arquivo de trabalho local: `1faf4b82d8a3b7e5cded38ed5c5040703b41727c` (igual nos dois).
- Leitura de records via houseCARL no perfil MO2 `AETHERIUS - LOCAL`, epoch `e2-22a0c680484df11f`; plugin ativo no load order.
- Leitura somente leitura. Nenhuma operação de escrita foi executada no modpack.

## MGEF solicitado — FormID local `000844`

houseCARL não encontrou `000844:AETHERIUS - SOBRENATURAL.esp`: o plugin está no load order, mas não define esse record e nenhum outro plugin o sobrescreve. Portanto, não há dados de arquétipo, Actor Value ou flags para o FormID `000844` no plugin indicado.

O record de velocidade definido pelo plugin é `000806:AETHERIUS - SOBRENATURAL.esp` (`SN_MGEF_Speed`), lido separadamente:

| Campo | Valor bruto |
|---|---|
| Nome | `Sobrenatural: Velocidade` |
| Archetype.Type | `ValueModifier` |
| Archetype.ActorValue | `SpeedMult` |
| MagicSkill | `None` |
| SecondActorValue | `None` |
| Flags | `Recover, NoDuration, HideInUI` |

## Abilities `SN_Lobisomem_Estagio1..6`

Cada ability é um record `Spell`, tipo `Ability`, `CastType=ConstantEffect`, `TargetType=Self`, `BaseCost=0`. Os efeitos abaixo reproduzem `BaseEffect`, magnitude, área, duração e a quantidade/forma das condições observadas no record.

Em todos os seis records, `Effects[0]`, `[1]` e `[2]` não têm condições. Correspondem, respectivamente, a `SN_MGEF_Health`, `SN_MGEF_Stamina` e `SN_MGEF_Unarmed`. `Effects[3]`, `[4]`, `[5]` e `[6]` têm uma condição cada: `GetIsRace`, `EqualTo`, valor de comparação `1`, `RunOnType=Subject`, raça `0CDD84:Skyrim.esm` (`WerewolfBeastRace`, nome `Werewolf`). A área e a duração de todos os efeitos são `0`.

| Ability (FormID) | Efeito | BaseEffect (EditorID) | Magnitude | Condição |
|---|---:|---|---:|---|
| Estágio 1 (`000827`) | 0 | `000800` `SN_MGEF_Health` | 125 | nenhuma |
|  | 1 | `000802` `SN_MGEF_Stamina` | 125 | nenhuma |
|  | 2 | `000803` `SN_MGEF_Unarmed` | 5 | nenhuma |
|  | 3 | `000800` `SN_MGEF_Health` | 150 | `GetIsRace` = `0CDD84:Skyrim.esm` |
|  | 4 | `000802` `SN_MGEF_Stamina` | 150 | `GetIsRace` = `0CDD84:Skyrim.esm` |
|  | 5 | `000804` `SN_MGEF_Armor` | 150 | `GetIsRace` = `0CDD84:Skyrim.esm` |
|  | 6 | `000805` `SN_MGEF_MagicResist` | 10 | `GetIsRace` = `0CDD84:Skyrim.esm` |
| Estágio 2 (`000828`) | 0 | `000800` `SN_MGEF_Health` | 150 | nenhuma |
|  | 1 | `000802` `SN_MGEF_Stamina` | 150 | nenhuma |
|  | 2 | `000803` `SN_MGEF_Unarmed` | 10 | nenhuma |
|  | 3 | `000800` `SN_MGEF_Health` | 200 | `GetIsRace` = `0CDD84:Skyrim.esm` |
|  | 4 | `000802` `SN_MGEF_Stamina` | 200 | `GetIsRace` = `0CDD84:Skyrim.esm` |
|  | 5 | `000804` `SN_MGEF_Armor` | 200 | `GetIsRace` = `0CDD84:Skyrim.esm` |
|  | 6 | `000805` `SN_MGEF_MagicResist` | 15 | `GetIsRace` = `0CDD84:Skyrim.esm` |
| Estágio 3 (`000829`) | 0 | `000800` `SN_MGEF_Health` | 175 | nenhuma |
|  | 1 | `000802` `SN_MGEF_Stamina` | 175 | nenhuma |
|  | 2 | `000803` `SN_MGEF_Unarmed` | 15 | nenhuma |
|  | 3 | `000800` `SN_MGEF_Health` | 250 | `GetIsRace` = `0CDD84:Skyrim.esm` |
|  | 4 | `000802` `SN_MGEF_Stamina` | 250 | `GetIsRace` = `0CDD84:Skyrim.esm` |
|  | 5 | `000804` `SN_MGEF_Armor` | 250 | `GetIsRace` = `0CDD84:Skyrim.esm` |
|  | 6 | `000805` `SN_MGEF_MagicResist` | 20 | `GetIsRace` = `0CDD84:Skyrim.esm` |
| Estágio 4 (`00082A`) | 0 | `000800` `SN_MGEF_Health` | 200 | nenhuma |
|  | 1 | `000802` `SN_MGEF_Stamina` | 200 | nenhuma |
|  | 2 | `000803` `SN_MGEF_Unarmed` | 20 | nenhuma |
|  | 3 | `000800` `SN_MGEF_Health` | 300 | `GetIsRace` = `0CDD84:Skyrim.esm` |
|  | 4 | `000802` `SN_MGEF_Stamina` | 300 | `GetIsRace` = `0CDD84:Skyrim.esm` |
|  | 5 | `000804` `SN_MGEF_Armor` | 300 | `GetIsRace` = `0CDD84:Skyrim.esm` |
|  | 6 | `000805` `SN_MGEF_MagicResist` | 25 | `GetIsRace` = `0CDD84:Skyrim.esm` |
| Estágio 5 (`00082B`) | 0 | `000800` `SN_MGEF_Health` | 225 | nenhuma |
|  | 1 | `000802` `SN_MGEF_Stamina` | 225 | nenhuma |
|  | 2 | `000803` `SN_MGEF_Unarmed` | 25 | nenhuma |
|  | 3 | `000800` `SN_MGEF_Health` | 350 | `GetIsRace` = `0CDD84:Skyrim.esm` |
|  | 4 | `000802` `SN_MGEF_Stamina` | 350 | `GetIsRace` = `0CDD84:Skyrim.esm` |
|  | 5 | `000804` `SN_MGEF_Armor` | 350 | `GetIsRace` = `0CDD84:Skyrim.esm` |
|  | 6 | `000805` `SN_MGEF_MagicResist` | 30 | `GetIsRace` = `0CDD84:Skyrim.esm` |
| Estágio 6 (`00082C`) | 0 | `000800` `SN_MGEF_Health` | 250 | nenhuma |
|  | 1 | `000802` `SN_MGEF_Stamina` | 250 | nenhuma |
|  | 2 | `000803` `SN_MGEF_Unarmed` | 30 | nenhuma |
|  | 3 | `000800` `SN_MGEF_Health` | 400 | `GetIsRace` = `0CDD84:Skyrim.esm` |
|  | 4 | `000802` `SN_MGEF_Stamina` | 400 | `GetIsRace` = `0CDD84:Skyrim.esm` |
|  | 5 | `000804` `SN_MGEF_Armor` | 400 | `GetIsRace` = `0CDD84:Skyrim.esm` |
|  | 6 | `000805` `SN_MGEF_MagicResist` | 35 | `GetIsRace` = `0CDD84:Skyrim.esm` |

## Condição `GetIsRace` — campos observados

- Tipo de condição: `ConditionFloat`.
- `Function`: `GetIsRace`.
- `CompareOperator`: `EqualTo`.
- `ComparisonValue`: `1`.
- `Data.Race` / `Data.Parameter1.Link`: `0CDD84:Skyrim.esm` (`WerewolfBeastRace`, nome `Werewolf`).
- `Data.RunOnType`: `Subject`.
- `Data.Reference`: nulo; `Data.RunOnTypeIndex=-1`; `UseAliases=False`; `UsePackageData=False`.
- `Flags=0`; `Unknown2=0`; `Unknown1=000000`.

## Verificação de records RACE e MOVT

Consulta houseCARL com escopo `plugins={defined_in:true,names:["AETHERIUS - SOBRENATURAL.esp"]}` e tipos `RACE`, `MOVT`: `total=0`, `matches=[]` no epoch consultado.

