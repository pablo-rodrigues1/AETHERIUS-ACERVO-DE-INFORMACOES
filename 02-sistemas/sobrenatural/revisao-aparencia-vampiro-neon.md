# Revisão Cruzada — Aparência Vampírica por Estágio (DVA Visuals)

- **Data:** 21/11/2026
- **Revisor:** Gemini 4 Neon
- **Veredito:** **APROVADO (sem achados)** — 0 achados P1, 0 achados P2, 0 achados P3. A implementação atende integralmente às decisões do dev, preserva isolamento visual do pacote ESPFE+BSA, mantém convivência segura com olhos da Bruxa/Lobisomem e evita recriação do ator remoto em combate/alimentação. Aprovado para autorização de instalação e ensaio em jogo.

## Escopo e artefatos verificados

| Alvo | Caminho / Repositório | Referência / Hash |
|---|---|---|
| Servidor (Sol High) | `C:\Code\Aetherius - Servidor Local\source` | Commit `9f01499a20f52661274891b387085eb4819a8670` sobre `e8a4535` (7 módulos) |
| Cliente (Sol High) | `C:\Code\Aetherius - Servidor Local\client-source` | Commit `3c80c2c79831e4df7b63091e75523362a1c4ac0f` sobre `8cfa553` (`appearance.ts`, `formView.ts`) |
| Overlay e patches | `C:\Code\Aetherius - Servidor Local` | Commit `f6e00922072d3ea3dfc1f16eb565bcfe613c96c9`, `overlays\sobrenatural\037-vampire-appearance.patch` (`0D28149F...`), `overlays\cliente-local\042-vampire-appearance.patch` (`8DCFA815...`) |
| Pacote visual | `C:\Code\Aetherius - New Systems\Sobrenatural\entregas\AETHERIUS - DVA Visuals\` | `AETHERIUS - DVA Visuals.esp` (`CE1917E2...`), `AETHERIUS - DVA Visuals.bsa` (`3C17595C...`), gerador `overlays\sobrenatural\gerar-dva-visual.py` |
| Documentação | `C:\Code\Aetherius - New Systems\Sobrenatural\docs\` | `ETAPA-3_aparencia-vampiro.md`, `investigacoes\INVESTIGACAO_21-11_aparencia-vampiro.md` |

---

## Achados

Nenhum defeito real (P1, P2 ou P3) encontrado.

---

## Verificação detalhada por critério

### 1. Limites dos estágios e prioridade — OK
- **Limites contínuos (`source\server\gamemode\supernatural\vampire-appearance.js:12-17`):**
  - `>= 70` → Stage 1 (`100–70`)
  - `>= 50` e `< 70` → Stage 2 (`69,999..50`)
  - `> 30` e `< 50` → Stage 3 (`49,999..30,001`)
  - `<= 30` → Stage 4 (`30..0`)
  - Valores fora de `[0, 100]` ou não-finitos retornam `null` (preservando a aparência vigente enquanto o saldo estiver temporariamente indisponível, `veil-eyes.js:151`).
- **Prioridade (`veil-visuals.js:32-33`, `vampire-appearance.js:30-35`):**
  - `combat = alive && (blood?.blood === 0 || powers?.combat)` (Feral, `COTV_ImmeasurableStrength`, `COTV_UnnaturalCelerity`), `vision = alive && powers?.vision`.
  - `eyeModes = [...(combat ? [5] : []), ...(vision ? [6] : []), ...stages]` garante **combate (`Glow1`: `0x812`/`0x818`) > Visão Noturna (`Glow2`: `0x813`/`0x819`) > Stage (`0x80e..0x811` / `0x814..0x817`)**.
  - Fallback por família e sexo escolhe o outro especial ativo se faltar o primeiro e, em seguida, o Stage mais próximo com preferência pelo menos intenso em empates (sort estável sobre `[1, 2, 3, 4]`).
- **Argoniano e Khajiit:** `vampire-appearance.js:5-10` restringe o catálogo DVA às famílias Humana, Elfa e Orc; Argonianos (`0x8883a`) e Khajiit (`0x88845`) não sofrem nenhuma escrita no servidor (`candidates` vazio e `recordFeeding` ignorado em `veil-eyes.js:198`) e preservam seus olhos vampíricos raciais no cliente (`appearance.ts:147-156`).

### 2. Prazo da boca e gatilhos autoritativos — OK
- **Prazo (`vampire-appearance.js:19-22`, `veil-eyes.js:138-146,194-200`):** padrão **180 s reais** (3 min), configurável via `AETHERIUS_VAMPIRE_BLOODY_MOUTH_SECONDS` no intervalo `[1..86400]`. Grava prazo absoluto `until: now() + mouthDuration` por personagem e `grant` no journal `aetherius-veil-eyes.json`, renovando a cada nova alimentação confirmada e sobrevivendo a relog/restart.
- **Limpeza segura:** morte/abatimento (`alive === false`), troca de concessão ou perda da maldição encerram `mouth` imediatamente; se expirar durante a forma de Lorde, `veil-eyes.js:140-146` limpa `mouth` do registro sem alterar a forma de criatura e sem transportar a boca expirada para o retorno.
- **Somente crédito autoritativo:**
  - `feeding.js:224-232` chama `recordFeeding(vampire, row.grantId)` **exclusivamente** após `binding`, `grantId` e `mp.__aetheriusBloodReserve?.add(vampire, settings.blood) === true`. Cancelamentos, `CLOSED` isolado em recuperação de journal e Poção de Sangue (`blood-reserve.js:145`) nunca acionam a boca; erro cosmético não reabre nem desfaz o crédito.
  - `power-input.js:132-134` e `powers.js:349-364` só marcam `appearanceGrant` após `blood.commit(reservation.token) === true`, validando concessão, personagem e prazo (`at < value.until`).

### 3. Restauração, troca entre maldições e convivência com `veil-eyes` / olhos Bronze — OK
- **Journal unificado e retrocompatível (`veil-eyes.js:49-65,90-95,118-183`):**
  - Mantém compatibilidade com entradas anteriores de Bruxa e Lobisomem no mesmo `data\aetherius-veil-eyes.json`, acrescentando `face` (`before`/`after`/`prior`, aceitando `'0'` como TXST original) e `mouth`.
  - `compatible()` (`:90-95`) pareia raças mortais e vampíricas via `RACE_TO_VAMPIRE`, preservando a base original (`before` do olho e `face.before`) em trocas diretas Bruxa ↔ Vampiro ↔ Lobisomem ↔ Mortal.
  - Grava a intenção + `prior` em disco antes de `mp.set(id, 'appearance', changed)` (`:169-181`), mantendo rastreabilidade em caso de interrupção de escrita; após restaurar slots com confirmação de readback, remove os slots restaurados do journal (`:182`).
  - Edição autorizada no RaceMenu com novo olho/TXST torna `owns(...) === false`, adotando a nova base como `before` ao reaplicar DVA e restaurando essa nova base ao perder a maldição.

### 4. Envio por borda, entrada tardia, estabilidade remota, privacidade e performance — OK
- **Envio só na borda:** `remember()` ignora gravações sem mudança via `isDeepStrictEqual` (`veil-eyes.js:80`); `mp.set(id, 'appearance', changed)` só dispara quando `eyes.id !== currentEye || face.id !== originalTexture` (`:170`).
- **Entrada tardia:** `appearance` persistido no `MpActor` é replicado via `CreateActorMessage.appearance` (`PartOne.cpp:797`); no cliente, `applyAppearanceCommon()` → `vampireEye()` (`appearance.ts:144-167`) reconhece o olho DVA pelo ESPFE (`0xFE`, faixa local, `Game.getFormFromFile`, `getType() === 2`) e aplica `headTextureSetId` via `setFaceTextureSet`.
- **Estabilidade da cópia remota em combate (`formView.ts:104,704-715`, `appearance.ts:201-230`):**
  - `tryApplyDvaDelta()` valida ator carregado e `base.getFormID() === this.appearanceBasedBaseId`, e `applyDvaDelta()` confirma mudança exclusivamente de olho/TXST DVA (mesma raça, sexo, morfos, presets, tints, peso e contagem de headparts).
  - Atualiza `base.setNthHeadPart(eye, eyeIndex)` e/ou `base.setFaceTextureSet(texture)` + `actor.queueNiNodeUpdate()` **sem** zerar `appearanceBasedBaseId` (sem recriar o ator remoto, preservando referência, equipamento, animações e alvo em combate). Feral altera apenas a nameplate do SkySocial (`skysocial\src\identity.js:42-43`), não `appearance.name`, garantindo acerto no caminho in-place.
  - No dono (`appearance.ts:325-328,342-346`), também usa `applyDvaDelta` (evitando `resizeHeadpartsArray` no heap CRT) e adia aplicação enquanto `RaceSex Menu` estiver aberto.
- **Privacidade:** trafega apenas `headpartIds` e `headTextureSetId` no snapshot padrão de `appearance`, sem expor `JogadorVampiro`, `curse`, `grantId` ou saldo.
- **Performance:** `isEye` (`cache`) e resolução DVA (`visualForms`, via `PluginIndexMapping` ESL/ESPFE em `ScampServer.cpp:1645-1651`) ficam em cache; em regime estacionário, o tick de 1 s não faz I/O de disco nem envia pacotes.

### 5. Auditoria binária do pacote `AETHERIUS - DVA Visuals` — OK
Inspeção binária direta de `AETHERIUS - DVA Visuals.esp` e `AETHERIUS - DVA Visuals.bsa` (além da revisão de `gerar-dva-visual.py`):
- **`AETHERIUS - DVA Visuals.esp`** (SHA-256 `CE1917E24C42E1E31FB9446C326452EBAAABAC16B290CBA7EBA11EFDC53BDE73`):
  - Header `TES4`: `flags = 0x200` (ESL / ESPFE), `masters = ['Skyrim.esm', 'Dawnguard.esm']`, `HEDR.numRecords = 53` (`50 records + 3 GRUPs`, exato).
  - Grupos presentes: **apenas** `TXST` (36), `HDPT` (12) e `FLST` (2). **Zero** `QUST`, **zero** `GLOB`, **zero** `MGEF` (sem overrides de `DLC1VampireChangeEffect` ou `DLC1RevertEffect`) e **zero** `VMAD`.
  - Cruzamento automatizado contra `vampire-appearance.js:5-37`: **42/42 records conferidos** (12 `HDPT` de olhos com `PNAM = 2` + 30 `TXST` de rosto/boca), **0 divergências** de FormID local ou EDID (os outros 6 `TXST` são `0x800..0x805` dos olhos).
- **`AETHERIUS - DVA Visuals.bsa`** (SHA-256 `3C17595C49DB14EAF8D2DD45651D60B21F483D66DACB6FF249AFCF6DBB85CF15`):
  - BSA `v105`, 4 pastas sob `meshes/textures\actors\character\dva`, **46 arquivos** (`6 .nif` + `40 .dds`), **zero `.pex`**, **zero `.psc`** e **zero** imagem de MCM.

---

## Testes e integridade executados na revisão

| Verificação | Resultado |
|---|---|
| Bateria ampliada (`AETHERIUS_VAMPIRE_FEED_ENABLED=true`) | **568/568 OK** |
| Bateria ampliada (`AETHERIUS_VAMPIRE_FEED_ENABLED=false`) | **568/568 OK** |
| `node --test overlays/sobrenatural/teste-painel-f7.cjs overlays/sobrenatural/teste-revisao-f7.cjs` | **2/2 OK** |
| `node overlays/sobrenatural/teste-servico.cjs` | **OK** |
| `tsc --noEmit` (`source/server` e `client-source/client`) | **OK** (`exit=0` em ambos) |
| `webpack` (compilado em pasta temporária com `DEPLOY_PLUGIN=false ZIP_PLUGIN=false`) | **OK** (`webpack 5.94.0`, SHA-256 `93FD7C93DC3EA4C96A55B18B22B56A40A9823D43E2B8090984464874C64F4854` idêntico ao bundle em `client-source\build\...`) |
| `git apply --reverse --check --whitespace=error` (`037-vampire-appearance.patch` e `042-vampire-appearance.patch`) | **OK** (`exit=0` em ambos; SHA-256 conferidos) |
| Conferência SHA-256 dos 7 módulos espelhados em `overlays\sobrenatural\files\server\gamemode\` | **7/7 idênticos** |
| Auditoria binária do ESPFE + BSA + cruzamento dos 42 records de `vampire-appearance.js` | **OK** (0 divergências, 0 QUST/GLOB/MGEF/VMAD/PEX) |

---

## Observações opcionais (não bloqueantes)

1. **Latência de até 1 s nos olhos de poder (`power-input.js:133`):** `confirmAppearance()` marca `appearanceGrant` e aguarda o próximo tick de 1 s de `veil-visuals` (mesmo padrão dos olhos de Bruxa e Bronze de Lobisomem). Se desejarem troca instantânea ao ativar Visão/Força/Celeridade, basta acionar `mp.__aetheriusVeilVisuals?.refreshAppearance?.(id)` como feito em `recordFeeding`.
2. **Estabilidade visual do `queueNiNodeUpdate()` em jogo:** o código já evita recriação do ator remoto (`applyDvaDelta`) e adia updates com `RaceSex Menu` aberto; ainda assim, confirmar no ensaio com dois jogadores se o `queueNiNodeUpdate()` mantém perfeitamente a continuidade de animação/ataque e composição do FaceGen com Tempered Skins.
