# Etapa 2 — Plugin AETHERIUS - SOBRENATURAL e animação do lobisomem (Pandora)

Data: 2026-11-15. Autor: Opus 5.5, com autorização explícita do dev para escrever no modpack. **Sem revisão cruzada e sem teste em jogo.**
Servidor local: commit raiz `2139a74`.

## 1. Plugin `AETHERIUS - SOBRENATURAL.esp`

- Criado pelo houseCARL (`housecarl_create`). Fica no mod MO2 `AETHERIUS - SOBRENATURAL`; masters Skyrim.esm e Update.esm; 45 records. `housecarl_check`: 0 refs quebradas e 0 masters faltando.
- **8 efeitos** (MGEF ValueModifier, constante, em si, Recover/NoDuration/HideInUI): `SN_MGEF_Health` 0x800, `Magicka` 0x801, `Stamina` 0x802, `Unarmed` (UnarmedDamage) 0x803, `Armor` (DamageResist) 0x804, `MagicResist` 0x805, `Speed` (SpeedMult) 0x806, `AttackDamage` (AttackDamageMult) 0x807.
- **37 habilidades** (SPEL Ability):

| Records | Conteúdo |
|---|---|
| `SN_Vampiro_Estagio1..6` (0x808–0x80D) | Vida, magicka e vigor +50/75/100/125/150/200; desarmado +5/10/15/20 nos estágios 2–5 e **+20 no 6** (célula vazia na planilha: assumido igual ao 5, **confirmar**). Só a do estágio atual fica ativa (a planilha dá o total do estágio). |
| `SN_Disciplina_<Nome><1..5>` (0x80E–0x826) | Celeridade +10–50 SpeedMult; Fortitude +25–200 vida; Potência +0,05–0,25 AttackDamageMult (+5–25% de dano físico, armas inclusive); Feitiçaria do Sangue +25–200 magicka; Tenacidade +25–200 vigor. Grau = estágio − 1. |
| `SN_Lobisomem_Estagio1..6` (0x827–0x82C) | Nas duas formas: vida e vigor +125–250 e desarmado +5–30. Só na fera (condição `GetIsRace WerewolfBeastRace` 0CDD84): vida e vigor +150–400 (somam aos anteriores), armadura +150–400, resistência mágica +10–35%. |

- O servidor dá e tira essas habilidades por `AddSpell`/`RemoveSpell`, junto com o resto (`vampire-grants.js` / `werewolf-grants.js`). Boot: `forms 102/102`.

## 2. Animação do lobisomem (mod do kdu) + Pandora

- Mods instalados no MO2 e ligados **só no perfil `AETHERIUS - LOCAL`**: `AETHERIUS - LOBISOMEM TRANSFORMACAO` (animação, OAR do uivo, sons) e `AETHERIUS - LOBISOMEM` (plugin `AETHERIUS - LOBISOMEM.esp`). Ordem dos plugins: logo após `Manbeast.esp`, depois `AETHERIUS - SOBRENATURAL.esp`. Backup dos três arquivos de perfil: `*.bak-sobrenatural`.
- **Pandora:** rodou pelo MO2 com o perfil LOCAL (`ModOrganizer.exe -p "AETHERIUS - LOCAL" run …`): "Launch completed successfully", 423 animações, `FNIS_AethLobisomem_List` processada. `defaultmale.hkx` passou a conter `aethlobisomem`.
- **Isolamento:** o Pandora grava nos mods que já têm os arquivos, e o mod BFCO é compartilhado com o perfil oficial. Os 5 arquivos alterados (`0_master.hkx`, `defaultmale.hkx`, `defaultfemale.hkx`, `animationdatasinglefile.txt`, `animationsetdatasinglefile.txt`) foram copiados para o mod novo `AETHERIUS - LOCAL - PANDORA`, logo abaixo de `AETHERIUS - SERVIDOR LOCAL` no perfil LOCAL. BFCO, `Pandora - Output` e TK Dodge voltaram do backup e ficaram **idênticos ao original** (`diff -rq` vazio). Backup em `C:\Code\Aetherius - Servidor Local\backups\pandora-antes-20261115-160857\`.
- O servidor voltou a usar a animação própria (`WEREWOLF_ANIMATION` padrão `aeth`, troca de raça aos 10,5 s).
- Ocorrência: um teste de linha de comando abriu o Pandora **fora** do MO2 por alguns segundos. Ele foi fechado antes de gerar qualquer arquivo; a pasta Data não foi alterada.

## 3. Servidor local com os plugins novos

- O perfil `AETHERIUS - LOCAL` foi versionado em `client-source/client/profiles/` (commit `ac1d981` do git do client-source). `Prepare.ps1` passou a sincronizar com esse perfil (parâmetros `-PluginClient`/`-PluginProfile`; o oficial continua disponível pelos parâmetros).
- Resultado: `loadOrder 435 plugins`, `+2 AETHERIUS - LOBISOMEM.esp, AETHERIUS - SOBRENATURAL.esp`.

## Pendências

- Teste em jogo: bônus visíveis no menu de atributos, fera e Lorde com 2 jogadores, animação própria.
- Confirmar o desarmado do Apoteótico (assumido +20).
- Na compilação final para o oficial: o plugin e a animação entram no modpack oficial, o Pandora é rodado de novo no perfil oficial e os perfis do cliente oficial são atualizados.
