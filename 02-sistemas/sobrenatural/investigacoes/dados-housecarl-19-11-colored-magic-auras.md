# Dados houseCARL — Colored Magic Auras

**Coleta:** 19/11/2026  
**Perfil MO2:** `AETHERIUS - LOCAL`  
**Instância MO2:** `C:\modOrganizer`  
**Plugin examinado:** `C:\modOrganizer\mods\Colored Magic Auras\Colored Magic Auras.esp`  
**Nexus:** mod 25773, Colored Magic Auras, versão 1.0  
**Estado no perfil:** mod `Colored Magic Auras` desabilitado; `Colored Magic Auras.esp` ausente de `loadorder.txt`.

## Fontes e método

- `housecarl_load_order_status(profile="AETHERIUS - LOCAL", filter="Colored Magic Auras")`: confirmou o perfil consultado, estado desabilitado e ausência do ESP na ordem ativa. Na leitura, o perfil tinha 568 mods habilitados, 11 desabilitados; 437 plugins listados, 436 ativos e 1 inativo. Epoch do resolver: `e2-22a0c680484df11f`.
- `housecarl_records(source="Colored Magic Auras.esp", types=[...])`: leitura do arquivo fora da ordem ativa. Foram consultados os tipos Bethesda suportados comuns, incluindo os relevantes `SPEL`, `BOOK`, `ARMO`, `ENCH`, `MGEF`, `ARTO`, `RFCT`, `LVLI`, `LVLN`, `CONT`, `NPC_`, `COBJ`, `QUST`, `DIAL` e `INFO`. Os únicos tipos com registros encontrados foram os sete detalhados abaixo.
- `housecarl_nexus_mod(mod="25773", description=true, files=true)`: metadados, versão e descrição pública do mod.
- **Masters:** leitura direta, local e somente leitura do registro de cabeçalho `TES4` do ESP, autorizada pelo coordenador. Não foi feita escrita, ativação, cópia ou alteração no modpack. SHA-256 do arquivo lido: `046ab9e64f001158febb6f83f0e7228592fb3571c2eb3ba050afb2f02eea6d56` (23.204 bytes; cabeçalho `TES4` de 228 bytes).

## Masters do ESP

Obtidos do subregistro `MAST` do cabeçalho local do arquivo:

1. `Skyrim.esm`
2. `Update.esm`
3. `Dawnguard.esm`

## Registros novos por tipo

HouseCARL retornou **88 registros** nestes sete tipos. Todos os FormIDs abaixo são definidos por `Colored Magic Auras.esp` (`...:Colored Magic Auras.esp`); nenhum registro retornado usa FormID pertencente a um master.

### `SPEL` — Spell: 16

| FormID | EditorID |
|---|---|
| `000813:Colored Magic Auras.esp` | `spgAuraBlue` |
| `000814:Colored Magic Auras.esp` | `spgAuraGreen` |
| `000815:Colored Magic Auras.esp` | `spgAuraYellow` |
| `000816:Colored Magic Auras.esp` | `spgAuraWhite` |
| `000817:Colored Magic Auras.esp` | `spgAuraOrange` |
| `000818:Colored Magic Auras.esp` | `spgAuraPurple` |
| `000821:Colored Magic Auras.esp` | `spgAuraBlack` |
| `000D63:Colored Magic Auras.esp` | `spgAuraRed` |
| `00082D:Colored Magic Auras.esp` | `spgAuraSelfBlack` |
| `00082E:Colored Magic Auras.esp` | `spgAuraSelfBlue` |
| `00082F:Colored Magic Auras.esp` | `spgAuraSelfGreen` |
| `000830:Colored Magic Auras.esp` | `spgAuraSelfOrange` |
| `000831:Colored Magic Auras.esp` | `spgAuraSelfPurple` |
| `000832:Colored Magic Auras.esp` | `spgAuraSelfRed` |
| `000833:Colored Magic Auras.esp` | `spgAuraSelfWhite` |
| `000834:Colored Magic Auras.esp` | `spgAuraSelfYellow` |

### `BOOK` — Book / tomo: 16

| FormID | EditorID |
|---|---|
| `000819:Colored Magic Auras.esp` | `spgSpellTomeAuraRed` |
| `00081A:Colored Magic Auras.esp` | `spgSpellTomeAuraBlue` |
| `00081B:Colored Magic Auras.esp` | `spgSpellTomeAuraGreen` |
| `00081C:Colored Magic Auras.esp` | `spgSpellTomeAuraPurple` |
| `00081D:Colored Magic Auras.esp` | `spgSpellTomeAuraYellow` |
| `00081E:Colored Magic Auras.esp` | `spgSpellTomeAuraOrange` |
| `00081F:Colored Magic Auras.esp` | `spgSpellTomeAuraWhite` |
| `000822:Colored Magic Auras.esp` | `spgSpellTomeAuraBlack` |
| `000835:Colored Magic Auras.esp` | `spgSpellTomeAuraSelfBlack` |
| `000836:Colored Magic Auras.esp` | `spgSpellTomeAuraSelfBlue` |
| `000837:Colored Magic Auras.esp` | `spgSpellTomeAuraSelfGreen` |
| `000838:Colored Magic Auras.esp` | `spgSpellTomeAuraSelfOrange` |
| `000839:Colored Magic Auras.esp` | `spgSpellTomeAuraSelfPurple` |
| `00083A:Colored Magic Auras.esp` | `spgSpellTomeAuraSelfRed` |
| `00083B:Colored Magic Auras.esp` | `spgSpellTomeAuraSelfWhite` |
| `00083C:Colored Magic Auras.esp` | `spgSpellTomeAuraSelfYellow` |

### `MGEF` — MagicEffect: 24

| FormID | EditorID |
|---|---|
| `000800:Colored Magic Auras.esp` | `spgAuraEffectBlue` |
| `00080E:Colored Magic Auras.esp` | `spgAuraEffectOrange` |
| `00080F:Colored Magic Auras.esp` | `spgAuraEffectGreen` |
| `000810:Colored Magic Auras.esp` | `spgAuraEffectYellow` |
| `000811:Colored Magic Auras.esp` | `spgAuraEffectWhite` |
| `000812:Colored Magic Auras.esp` | `spgAuraEffectPurple` |
| `000820:Colored Magic Auras.esp` | `spgAuraEffectBlack` |
| `000D61:Colored Magic Auras.esp` | `spgAuraEffectRed` |
| `000825:Colored Magic Auras.esp` | `spgAuraEffectSelfBlack` |
| `000826:Colored Magic Auras.esp` | `spgAuraEffectSelfBlue` |
| `000827:Colored Magic Auras.esp` | `spgAuraEffectSelfGreen` |
| `000828:Colored Magic Auras.esp` | `spgAuraEffectSelfOrange` |
| `000829:Colored Magic Auras.esp` | `spgAuraEffectSelfPurple` |
| `00082A:Colored Magic Auras.esp` | `spgAuraEffectSelfRed` |
| `00082B:Colored Magic Auras.esp` | `spgAuraEffectSelfWhite` |
| `00082C:Colored Magic Auras.esp` | `spgAuraEffectSelfYellow` |
| `00083D:Colored Magic Auras.esp` | `spgEnchAuraEffectBlack` |
| `00083E:Colored Magic Auras.esp` | `spgEnchAuraEffectBlue` |
| `00083F:Colored Magic Auras.esp` | `spgEnchAuraEffectGreen` |
| `000840:Colored Magic Auras.esp` | `spgEnchAuraEffectOrange` |
| `000841:Colored Magic Auras.esp` | `spgEnchAuraEffectPurple` |
| `000842:Colored Magic Auras.esp` | `spgEnchAuraEffectRed` |
| `000843:Colored Magic Auras.esp` | `spgEnchAuraEffectWhite` |
| `000844:Colored Magic Auras.esp` | `spgEnchAuraEffectYellow` |

### `ARMO` — Armor / wearable: 8

| FormID | EditorID |
|---|---|
| `00084D:Colored Magic Auras.esp` | `spgRingAuraOrange` |
| `00084E:Colored Magic Auras.esp` | `spgRingAuraBlack` |
| `00084F:Colored Magic Auras.esp` | `spgRingAuraBlue` |
| `000850:Colored Magic Auras.esp` | `spgRingAuraGreen` |
| `000851:Colored Magic Auras.esp` | `spgRingAuraYellow` |
| `000852:Colored Magic Auras.esp` | `spgRingAuraPurple` |
| `000853:Colored Magic Auras.esp` | `spgRingAuraRed` |
| `000854:Colored Magic Auras.esp` | `spgRingAuraWhite` |

### `ENCH` — ObjectEffect / enchantment: 8

| FormID | EditorID |
|---|---|
| `000845:Colored Magic Auras.esp` | `spgEnchAuraBlack` |
| `000846:Colored Magic Auras.esp` | `spgEnchAuraWhite` |
| `000847:Colored Magic Auras.esp` | `spgEnchAuraRed` |
| `000848:Colored Magic Auras.esp` | `spgEnchAuraPurple` |
| `000849:Colored Magic Auras.esp` | `spgEnchAuraOrange` |
| `00084A:Colored Magic Auras.esp` | `spgEnchAuraGreen` |
| `00084B:Colored Magic Auras.esp` | `spgEnchAuraBlue` |
| `00084C:Colored Magic Auras.esp` | `spgEnchAuraYellow` |

### `ARTO` — ArtObject: 8

| FormID | EditorID |
|---|---|
| `000807:Colored Magic Auras.esp` | `spgFXAuraParticlesObjectGreen` |
| `000808:Colored Magic Auras.esp` | `spgFXAuraParticlesObjectWhite` |
| `000809:Colored Magic Auras.esp` | `spgFXAuraParticlesObjectYellow` |
| `00080A:Colored Magic Auras.esp` | `spgFXAuraParticlesObjectBlue` |
| `00080B:Colored Magic Auras.esp` | `spgFXAuraParticlesObjectRed` |
| `00080C:Colored Magic Auras.esp` | `spgFXAuraParticlesObjectOrange` |
| `00080D:Colored Magic Auras.esp` | `spgFXAuraParticlesObjectPurple` |
| `000824:Colored Magic Auras.esp` | `spgFXAuraParticlesObjectBlack` |

### `RFCT` — VisualEffect: 8

| FormID | EditorID |
|---|---|
| `000801:Colored Magic Auras.esp` | `spgFXAuraParticlesEffectPurple` |
| `000802:Colored Magic Auras.esp` | `spgFXAuraParticlesEffectGreen` |
| `000803:Colored Magic Auras.esp` | `spgFXAuraParticlesEffectWhite` |
| `000804:Colored Magic Auras.esp` | `spgFXAuraParticlesEffectOrange` |
| `000805:Colored Magic Auras.esp` | `spgFXAuraParticlesEffectYellow` |
| `000806:Colored Magic Auras.esp` | `spgFXAuraParticlesEffectBlue` |
| `000823:Colored Magic Auras.esp` | `spgFXAuraParticlesEffectBlack` |
| `000D62:Colored Magic Auras.esp` | `spgFXAuraParticlesEffectRed` |

## Sobrescritas e conflitos no load order LOCAL

- **Overrides do plugin sobre outros plugins:** nenhum dos 88 registros retornados tem FormID cujo plugin definidor seja um master; todos são registros locais definidos por `Colored Magic Auras.esp`. Portanto, não há overrides de records de outros plugins entre os registros lidos.
- **Conflitos/winners no perfil:** não avaliáveis como conflito de load order no estado coletado. O mod está desligado e o ESP não consta em `loadorder.txt`; houseCARL retornou `winner: (not in the active order)` para os registros locais. Não foi ativado nem ordenado para esta coleta.
- O plugin contém recursos usados pelo Sobrenatural: `000D62:Colored Magic Auras.esp` (`spgFXAuraParticlesEffectRed`) e `000801:Colored Magic Auras.esp` (`spgFXAuraParticlesEffectPurple`).

## Itens, magias e distribuição ao jogador

- Foram encontrados 16 records `SPEL` e 16 records `BOOK` cujos EditorIDs são spell tomes (`spgSpellTomeAura...`), além de 8 anéis `ARMO` com 8 encantamentos `ENCH`.
- Nas consultas de tipo ao ESP, **0** records `LVLI`, `LVLN`, `CONT`, `NPC_`, `COBJ`, `QUST`, `DIAL` ou `INFO` foram encontrados. Assim, nos tipos consultados, não há leveled lists, inventários de NPC/vendedor, recipientes, receitas ou quests de distribuição no plugin.
- A página Nexus lida por houseCARL descreve as magias e wearables e diz que não são craftáveis; orienta uso de AddItemMenu ou console. Esse texto é metadado da página Nexus, não leitura do ESP.

## Contagem

| Tipo | Registros |
|---|---:|
| `SPEL` Spell | 16 |
| `BOOK` Book/tomo | 16 |
| `MGEF` MagicEffect | 24 |
| `ARMO` Armor/anel | 8 |
| `ENCH` ObjectEffect/encantamento | 8 |
| `ARTO` ArtObject | 8 |
| `RFCT` VisualEffect | 8 |
| **Total** | **88** |

## Limites da coleta

O perfil foi consultado sem alterar o modpack. Como o plugin está desativado, o resultado de conflito/winner representa apenas o estado atual sem o ESP. Os masters vieram de leitura local direta do cabeçalho `TES4`, conforme autorizado pelo coordenador; as listagens e contagens de records vieram de houseCARL. Nenhuma escrita foi feita em `C:\modOrganizer`.
