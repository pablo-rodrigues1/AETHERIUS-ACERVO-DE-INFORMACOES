# Revisão Independente — Poderes Vampíricos, P10 e Forma de Lorde Vampiro

- **Revisor:** Gemini 4 Neon (revisão cruzada honesta, somente leitura no código — regra de `AGENTS.md`, 18/11/2026)
- **Data:** 20/11/2026
- **Autor revisado:** Sol High
- **Alvo revisado:**
  - `C:\Code\Aetherius - Servidor Local` — commit `58c9db0aa23d3e219a2398c62a4cd40f65f3a3f6` (`58c9db0`): `feat(sobrenatural): integra poderes vampiricos e concessao de Lorde`
  - `C:\Code\Aetherius - Servidor Local` — commit `d757a8b3d3e8ff86d167e85909371584ac95ed5f` (`d757a8b`): `feat(sobrenatural): projeta favoritos e atributos do Lorde`
  - `C:\Code\Aetherius - Servidor Local` — commit `9ce62373501cdb4c78da0a7d102a461a697fb5d9` (`9ce6237`): `docs(sobrenatural): restringe Grip a NPCs e registra pendências do Lorde`
  - `C:\Code\Aetherius - Servidor Local\client-source` — commit `307d39c12f6d3f9c5afcb08e4a5fbbd2bdd6ca06` (`307d39c`): `feat(sobrenatural): oferece Forma de Lorde no TAB`
  - ESP candidato: `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\plugin\AETHERIUS - SOBRENATURAL.esp` — SHA-256 conferido: `211F983A782EFE553F8DB9E225F50D2BC51EBD2339F8F22A2D6B2462326C74A7`
- **Fontes consultadas:**
  - [INVESTIGACAO_20-11_lorde-poderes.md](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/investigacoes/INVESTIGACAO_20-11_lorde-poderes.md)
  - [DECISOES.md](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/DECISOES.md)
  - [ETAPA-3_vampirismo-mecanicas.md](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/ETAPA-3_vampirismo-mecanicas.md)
  - [ESPECIFICACAO.md](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/ESPECIFICACAO.md)
  - Patches `030-vampire-powers-lord.patch`, `031-vampire-lord-loadout.patch`, `overlays/cliente-local/035-lord-tab.patch`, código-fonte do servidor/cliente/C++/UI Core e suítes Node.
- **Limite explícito de ferramenta:** este revisor não tem acesso ao **houseCARL** (`AGENTS.md`, 18/11/2026). Todos os dados binários de records do ESP `211F983A…` e dos plugins do modpack (SOUN/SNDR `000845–00085A`, WAVs vencedores do mod `AETHERIUS - Vampire Lord and Werewolf SFX`, magnitudes/duração do Enxame, condições/valores de `00085B–000861`, limpeza de scripts antigos nos MGEFs e os 15 FormIDs de `VAMPIRE_LORD_PERKS`) ficam como **pendência de conferência de records** (fora do alcance de inspeção deste modelo).
- **Nota de ambiente durante a revisão:** outro worker iniciou edições paralelas no working tree de `C:\Code\Aetherius - Servidor Local\source\` e `overlays/sobrenatural/files/server/gamemode/vampire-lord-service.js` durante esta leitura; todas as citações e verificações abaixo foram ancoradas nos commits exatos `58c9db0`, `d757a8b`, `9ce6237` e `307d39c`.

---

## Veredito

**REPROVADO — REQUER CORREÇÕES ANTES DE INSTALAÇÃO/TESTE EM JOGO (1 Bloqueador, 1 Alto, 3 Médios, 1 Baixo).**

A arquitetura geral do lote é sólida e bem integrada nos eixos de P10/F7/keywords, escala sincronizada 1,25×, pipeline de áudio in/loop/out para observadores presentes e tardios, Enxame, isolamento dos favoritos persistidos no PostgreSQL, ausência de crash nativo e regressão limpa nos testes automatizados. Contudo, a inspeção linha a linha identificou **6 defeitos reais verificáveis em código**, sendo **1 bloqueador** que torna o novo leitor `buttonEvent` de `Shout` (Z) completamente inoperante no SkyrimPlatform real e **1 alto de segurança** no motor autoritativo de dano da Força Imensurável:

1. **[Bloqueador — `power-input.js:79,81-82`]** O evento `buttonEvent` do SkyrimPlatform expõe **`event.userEventName`** (`EventHandler.cpp:1884`, `skyrimPlatform.ts:428`), **não** `event.userEvent`. Em jogo real, `event.userEvent !== 'Shout'` é sempre `true` e todo o listener de Z para poderes vampíricos (Morcego humano/Lorde, Névoa, Enxame, Força, Celeridade, Visão, Reverter e Lorde) é código morto; o mock de `teste-poderes.cjs:499,502` mascarou o erro ao passar `userEvent: 'Shout'`. Além disso, ao corrigir o nome do campo, faltam guardas `sp.browser?.isFocused()` e `sp.Utility?.isInMenuMode()` para evitar disparo ao digitar `z` com foco CEF ou dentro de menus não listados.
2. **[Alto — segurança / dano autoritativo — `ActionListener.cpp` (`030-vampire-powers-lord.patch:46`) + `MpActor.cpp:1632`]** `ApplyVampireStrength` aplica multiplicador `2.0×` com base em `hitData.isBashAttack` vindo direto do pacote do cliente sem validação contra `AttackEvidence::Kind::Bash` (já gravado em `AttackEvidence.h:20`) e antes da exclusão de `WeapTypeBow`. Um cliente modificado sob Força Imensurável pode enviar `isBashAttack: true` em qualquer ataque de arma branca ou arco e receber `2.0×` sobre o dano completo (em vez de `1.25×` ou `1.0×`).
3. **[Médio — `vampire-lord-service.js:27-36` + `aetheriusInventoryService.ts:697-700` + `inventory-module.js:244,290`]** No menu Q (`/inventory/favorites`), clique esquerdo (`hand: 'right'`) e tecla `E` (`hand: 'auto'`) equipam magias na **mão direita** (`source = 1`) e aguardam `settle` por `equipment.rightSpell`; porém `vampire-lord-service.js:35` roda a **todo quadro** (`GamemodeUpdateService.onUpdate`) e sobrescreve imediatamente a mão direita de volta para `COTV_VampireLordDrain01`, impedindo o uso primário do menu Q para as magias de mão do Lorde (e atrasando o `settle` em 1,5 s).
4. **[Médio — `MpActor.cpp:1913-1916` (`031-vampire-lord-loadout.patch`) + `DevAttributes.cpp:45,57`]** Os bônus de Ability do Lorde (`mortalStage + lordStage + lordPerks`, +300 a +700) somados em `MpActor::GetMaximumValues()` vazam para `MpActor::SendAttributeMaximumValues()` (`Actor.SetActorValue`) sempre que `SetAttributeBonuses` / `SetDevAttributes` roda durante a forma (ex.: alocação de pontos de classe no TAB ou reidratação `onReady`), dobrando os atributos no cliente (base + Abilities do Skyrim) e deixando o base AV inflado em +300..+700 após reverter à forma mortal até relog.
5. **[Médio — `whitelist.js:216` + `vampire-lord-service.js:154`]** Proteção de relog pós-crash e PD não cobre `DLC1VampireBeastRace` (espelho do defeito já corrigido para `BEAST_RACE` em `036678d`): em crash/reinício durante forma de Lorde, se `whitelist._applyAppearance` rodar antes do tick de 250 ms de `vampire-lord-service.recover`, grava `race = 'DLC1VampireBeast'` no PostgreSQL; e em PD (aparência limpa para `null` com `characterOf(id) === null`), `vampire-lord-service.js:154` reaplica `session.original` sobre o ator limpo.
6. **[Baixo — `supernatural-module.js:108` (`307d39c` / `035-lord-tab.patch`)]** Em `receive()`, a guarda `if (state?.grantId !== next.grantId || next.curse !== 'werewolf')` apaga `beastView = null` para vampiros com `lordGranted === true` sempre que a revisão sobe sem `lordForm` no payload (escolha de Disciplina ou evento push `changed` de XP), deixando o botão `"Transformar em Lorde Vampiro"` desabilitado como `"Indisponível agora"` por até 10 s até o próximo polling.

---

## 1. Achados Reais (Com Evidência)

### Achado 1 (Bloqueador) — `power-input.js:79` usa `event.userEvent` em vez de `event.userEventName` (listener `buttonEvent` morto em jogo) e não bloqueia foco do browser nem `isInMenuMode`

- **Onde:**
  - `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\supernatural\power-input.js:78-86` (commit `d757a8b`; idêntico em `source/server/gamemode/supernatural/power-input.js`)
  - `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\teste-poderes.cjs:499,502`
  - Referências do motor/cliente:
    - `C:\Code\Aetherius - Servidor Local\source\skyrim-platform\src\platform_se\skyrim_platform\EventHandler.cpp:1884`
    - `C:\Code\Aetherius - Servidor Local\source\skyrim-platform\src\platform_se\codegen\convert-files\skyrimPlatform.ts:425-428`
    - `C:\Code\Aetherius - Servidor Local\client-source\client\src\sobrenatural\supernaturalHud.ts:46,59-60`
    - `C:\Code\Aetherius - Servidor Local\client-source\client\src\services\services\helmetToggleService.ts:31`
- **Evidência verificável:**
  - Em `EventHandler.cpp:1884`, `ButtonEventData::ToJavaScriptObject` serializa o campo como **`"userEventName"`** (e `skyrimPlatform.ts:428` declara `userEventName: string`):
    ```cpp
    AddObjProperty(&obj, "userEventName", userEvent.data());
    ```
  - Em `power-input.js:78-86`:
    ```js
    sp.on('buttonEvent', event => {
      if (!live() || !event.isDown || event.userEvent !== 'Shout') return;
      try {
        if (['Main Menu', 'Loading Menu', 'Console', 'Dialogue Menu', 'FavoritesMenu',
          'InventoryMenu', 'MagicMenu', 'MeridianUI_FocusMenu'].some(menu => sp.Ui?.isMenuOpen?.(menu))) return;
        const spell = sp.Game.getPlayer()?.getEquippedSpell(2)?.getFormID();
        if (powers.some(power => power.id === spell && power.vampiric)) activate(spell, 'voice');
      } catch {}
    });
    ```
  - No runtime real do SkyrimPlatform, `event.userEvent` é sempre `undefined`, então `event.userEvent !== 'Shout'` é sempre verdadeiro e o handler nunca chega à linha 80. O teste `teste-poderes.cjs:499,502` não detectou o erro porque simulou `{ isDown: true, userEvent: 'Shout' }`.
  - Com esse handler inoperante, toda a correção T10/Z via controle `Shout` não executa em jogo; apenas Morcego tem o fallback de polling de 100 ms em `hasMagicEffect` (`:98-103`, sujeito a latência e à chegada local do MGEF), enquanto os demais poderes vampíricos no Z (Lorde, Reverter, Névoa, Enxame, Força, Celeridade, Visão) voltam a depender exclusivamente de `actionVoiceFire` (que falhou no teste de 19/11).
  - Adicionalmente, após corrigir para `event.userEventName === 'Shout'`, as linhas 81–82 ainda não verificam `sp.browser?.isFocused()` nem `sp.Utility?.isInMenuMode()`. Em comparação com `supernaturalHud.ts:59-60` e `helmetToggleService.ts:31`, digitar `z` com o browser/CEF focado (ex.: F6 sem `MeridianUI_FocusMenu`) ou pressionar `Z` em menus não listados (`MapMenu`, `ContainerMenu`, `Book Menu`, `Crafting Menu`, `RaceSex Menu`, `Journal Menu`, `TweenMenu`, `BarterMenu`, `Sleep/Wait Menu`, `MessageBoxMenu`, `Lockpicking Menu`, `GiftMenu`) dispararia o poder vampírico equipado na voz.
- **Correção recomendada:**
  - Trocar `event.userEvent !== 'Shout'` por `event.userEventName !== 'Shout'` em `power-input.js:79` (e atualizar o mock em `teste-poderes.cjs:499,502`).
  - Bloquear também `sp.browser?.isFocused?.()` e `sp.Utility?.isInMenuMode?.()` além da checagem de `MeridianUI_FocusMenu`/`Console`/`Loading Menu`/`Main Menu`.

---

### Achado 2 (Alto — segurança / motor de dano autoritativo) — `ApplyVampireStrength` aplica `2.0×` usando `hitData.isBashAttack` não validado pelo servidor (permite 2× em arma branca e fura exclusão de arco)

- **Onde:**
  - `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\030-vampire-powers-lord.patch:45-47` (`ActionListener.cpp` em `OnWeaponHit`)
  - `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\031-vampire-lord-loadout.patch:17-20` (`MpActor.cpp:1627-1660`)
  - `C:\Code\Aetherius - Servidor Local\source\server\cpp\messages\HitMessage.h:24`
  - `C:\Code\Aetherius - Servidor Local\source\server\cpp\server_guest_lib\aetherius_combat\ds5a\AttackEvidence.h:20,25-29`
- **Evidência verificável:**
  - Em `OnWeaponHit`, `hitData.isPowerAttack` é validado por evidência de animação do host (`AttackEvidence::Kind::Power`) e `hitData.isSneakAttack` é zerado por `ignoreClientSneak`. Contudo, `hitData.isBashAttack` chega direto do pacote `HitMessage` do cliente sem qualquer validação em `OnWeaponHit`, sendo entregue diretamente a:
    ```cpp
    float damage = partOne.CalculateDamage(*aggressor, targetActor, hitData) * drawFactor;
    damage = aggressor->ApplyVampireStrength(damage, hitData.source, hitData.isBashAttack);
    ```
  - Em `MpActor::ApplyVampireStrength` (`MpActor.cpp:1632`):
    ```cpp
    if (bash) return damage * 2.f;
    ```
  - O ramo `if (bash)` executa **antes** de verificar `WeapTypeBow` (`:1643`) e **sobre o `damage` inteiro** calculado por `TES5DamageFormula` (que para `isBashAttack = true` ainda aplica o dano integral da arma `CalcWeaponRating()` e `isPowerAttack`, apenas pulando bônus de flecha e `bowShotLedger`).
  - Logo, durante os 40 s de Força Imensurável, um cliente adulterado pode enviar `isBashAttack: true` em qualquer ataque de espada/machado/adaga/martelo (obtendo **`2.0×`** em vez de **`1.25×`**) ou de arco (furando `bowShotLedger` + a exclusão `WeapTypeBow` e obtendo **`2.0×`** em vez de **`1.0×`**).
- **Correção recomendada:**
  - Validar `bash` em `OnWeaponHit` usando `aggressor->GetCombatAttackEvidence().Confirm(aetherius::combat::ds5b::AttackEvidence::Kind::Bash, owner, aggressor->GetNpcGeneration(), currentHitTime)` (já gravado em `AttackEvidence::Record` para `bashStart`/`bashRelease`/`bashPowerStart`) antes de passar `bash` para `ApplyVampireStrength` (ou exigir `bashConfirmed` para o multiplicador `2.0×`), mantendo a exclusão de arco quando aplicável.

---

### Achado 3 (Médio) — Auto-equip contínuo do Dreno na mão direita (`vampire-lord-service.js:35`) sobrescreve qualquer magia do Lorde equipada no menu Q por clique esquerdo ou tecla `E`

- **Onde:**
  - `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\vampire-lord-service.js:27-36` (commit `d757a8b`)
  - `C:\Code\Aetherius - Servidor Local\client-source\client\src\services\services\gamemodeUpdateService.ts:178-220` (`onUpdate` executa `updateOwner` em todo quadro)
  - `C:\Code\Aetherius - Servidor Local\client-source\client\ui-core\modules\inventory\inventory-module.js:188-194,230-237,244,290`
  - `C:\Code\Aetherius - Servidor Local\source\server\ts\systems\aetheriusInventoryService.ts:469-495,693-702`
- **Evidência verificável:**
  - `vampire-lord-service.js:27-36` roda a **todo quadro** no dono durante `phase === 'lord'`:
    ```js
    const drain = sp.Spell.from(sp.Game.getFormEx(value.drain));
    if (drain && rightId !== value.drain && actor.hasSpell(drain)) actor.equipSpell(drain, 1);
    ```
  - As cinco magias de mão colocadas no Q (`VAMPIRE_LORD_FAVORITES`: `DLC1ConjureGargoyleLeftHand`, `COTV_BloodWard`, `COTV_BloodToPower`, `COTV_VampiricGrip`, `COTV_SanguineRune`) recebem `equip: { kind: "spell" }`.
  - No menu Q (`inventory-module.js:244,290`), a ação primária do clique esquerdo envia `equipAbility { hand: 'right' }` e a tecla `E` envia `equipAbility { hand: 'auto' }`.
  - Em `aetheriusInventoryService.ts:697-700`:
    ```ts
    const source = row.equip.kind === "power" ? 2 : h === "left" ? 0 : 1;
    this.papyrus("Actor", "EquipSpell", actorId, [this.espm(row.baseId), source]);
    const slot = (["leftSpell", "rightSpell", "voiceSpell"] as const)[source];
    await this.settle(() => this.host.get(actorId, "equipment")?.[slot] === row.baseId);
    ```
  - Tanto `'right'` quanto `'auto'` despacham `EquipSpell(..., 1)` (**mão direita**) e ficam esperando até `SETTLE_MS` (1.500 ms) por `equipment.rightSpell === row.baseId`.
  - Consequência: ao clicar com o botão esquerdo ou apertar `E` em qualquer magia BothHands do Lorde no menu Q, o motor equipa na mão direita e, no quadro seguinte (≤16 ms), `vampire-lord-service.js:35` imediatamente a remove e reequipa `COTV_VampireLordDrain01` na mão direita — enquanto o servidor trava `busy` no `settle` por 1,5 s. Mesmo para magias `LeftHand`-only (como `DLC1ConjureGargoyleLeftHand`, se o `ETYP` forçar mão esquerda), o servidor ainda envia `source = 1` e sofre timeout de 1,5 s esperando `rightSpell`. Apenas clique direito (`hand: 'left'`) ou o modal do botão `EQUIPAR` → "Mão esquerda" funciona sem conflito.
- **Correção recomendada:**
  - Se o design mantém a mão direita fixa em `COTV_VampireLordDrain01` durante a forma de Lorde (padrão Vampire Lord), rotear em `aetheriusInventoryService.ts:697` as magias de mão presentes em `lordFavorites` para `source = 0` (`leftSpell`) quando `h !== "left"` (ou quando `h === "auto"`/`"right"` em forma de Lorde), para que clique esquerdo e `E` no Q equipem diretamente na mão esquerda e concluam o `settle` em `leftSpell`. Alternativamente, se a mão direita puder ser trocada livremente pelo jogador, equipar `value.drain` em `vampire-lord-service.js:35` apenas uma vez na entrada da forma (`!state.drainEquipped`).

---

### Achado 4 (Médio) — Bônus de Ability do Lorde em `MpActor::GetMaximumValues()` vazam para `SendAttributeMaximumValues()` (`Actor.SetActorValue`), dobrando atributos no cliente e inflando o base AV após sair da forma

- **Onde:**
  - `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\031-vampire-lord-loadout.patch:93-100` (`MpActor.cpp:1908-1917`)
  - `C:\Code\Aetherius - Servidor Local\source\server\cpp\server_guest_lib\aetherius_combat\DevAttributes.cpp:40-68,78-97`
  - `C:\Code\Aetherius - Servidor Local\source\server\gameplay\core\runtime\src\attribute-system.ts:25,91,100-112`
- **Evidência verificável:**
  - `031-vampire-lord-loadout.patch` soma `GetVampireLordBonuses()` (`SN_Vampiro_Estagio* + SN_Lorde_Estagio* + SN_Lorde_PerksSobrenaturais`, totalizando `+300` a `+700` em Vida, Magicka e Vigor) diretamente em `MpActor::GetMaximumValues()`.
  - `MpActor::SendAttributeMaximumValues()` (`DevAttributes.cpp:45,57`) lê `const auto values = GetMaximumValues();` e envia `Actor.SetActorValue("Health"|"Magicka"|"Stamina", values.*)` para o cliente — reescrevendo o **valor base** do ator no motor Skyrim.
  - Em `attribute-system.ts:25,91,100-112`, qualquer alocação de ponto de atributo de classe na UI (ou reidratação `onReady`) durante a forma de Lorde chama `mp.set(actorId, 'aetheriusAttributeBonuses', ...)` → `MpActor::SetAttributeBonuses` → `SendAttributeMaximumValues()`.
  - Consequências verificáveis:
    1. **Durante a forma:** o cliente passa a ter o **base AV** acrescido de `+300..+700` **e** o motor Skyrim ainda aplica os SPELs `Ability` (`SN_Vampiro_Estagio*`, `SN_Lorde_Estagio*`, `SN_Lorde_PerksSobrenaturais`) via `AddSpell` por cima do base AV — dobrando os bônus exibidos no cliente.
    2. **Após sair da forma:** `vampire-lord-service.js:finish` remove as Abilities e restaura a raça, mas não reenvia `SendAttributeMaximumValues()`; o cliente mantém o **base AV** inflado em `+300..+700` em forma mortal até relog ou nova escrita em `aetheriusAttributeBonuses`, dessincronizado do `GetMaximumValues()` do servidor.
- **Correção recomendada:**
  - Em `SendAttributeMaximumValues()` (`DevAttributes.cpp:45`), enviar apenas o máximo base + classe/dev (sem somar `GetVampireLordBonuses()`, cujas magnitudes já são aplicadas no cliente pelas Abilities entregues via `AddSpell`), preservando `GetVampireLordBonuses()` dentro de `GetMaximumValues()` para dano, absorção, regeneração e `aetheriusAttributeBonuses.maximum`.

---

### Achado 5 (Médio) — Relog pós-crash e PD em forma de Lorde não estão protegidos para `DLC1VampireBeastRace` (`whitelist.js:216` e `vampire-lord-service.js:154`)

- **Onde:**
  - `C:\Code\Aetherius - Servidor Local\source\server\gamemode\whitelist.js:34,215-216,233-235,361`
  - `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\vampire-lord-service.js:150-165,200-210` (commit `d757a8b`)
  - Comparação com a correção já aplicada ao Lobisomem em `036678d`: `source/server/gamemode/werewolf-service.js:342-357` e `whitelist.js:216`
- **Evidência verificável:**
  - Em `whitelist.js:216`:
    ```js
    // While the werewolf service restores the actor on relog, wait for the mortal appearance.
    if (!appearance || typeof appearance !== 'object' || appearance.raceId === BEAST_RACE) return false;
    ```
  - Apenas `BEAST_RACE` (`0xCDD84`) adia `_syncRaceGender`. Se o servidor cair/reiniciar abruptamente enquanto o jogador está em forma de Lorde (`appearance.raceId === mp.__aetheriusVampireLord.RACE`, `DLC1VampireBeastRace`), no próximo login `whitelist.checkWhitelist` chama `_watchAppearance` (`:361`) → `_applyAppearance`; se isso concluir antes do tick de 250 ms de `vampire-lord-service.js:205` (`recover(id)`), `_syncRaceGender` grava `race = 'DLC1VampireBeast'` na tabela `characters` do PostgreSQL.
  - Em `vampire-lord-service.js:153-155`:
    ```js
    const current = characterOf(id);
    if ((!current || session.character === current) &&
        !setAppearance(id, session.original, 'devolver a aparência')) return { ok: false, status: 'restore_failed' };
    ```
    Na desconexão normal, `!current` permite restaurar a aparência mortal (o que funciona bem). Porém, em caso de PD (Permadeath) durante a forma de Lorde — quando `discardActiveCharacter` remove o vínculo (`current === null`) e a limpeza zera `appearance` para `null` (`whitelist.js:330`) — `finish(id)` não verifica se `get(id, 'appearance')` ainda existe e reaplica `session.original` (aparência do personagem aposentado) sobre o ator limpo.
- **Correção recomendada:**
  - Em `whitelist.js:216`, adiar também quando `mp.__aetheriusVampireLord?.RACE && appearance.raceId === mp.__aetheriusVampireLord.RACE`.
  - Em `vampire-lord-service.js:154`, quando `!current`, só chamar `setAppearance(id, session.original, ...)` se `get(id, 'appearance')` for um objeto válido (espelhando `werewolf-service.js:346-352`).

---

### Achado 6 (Baixo) — `supernatural-module.js:108` (`307d39c`) apaga `beastView` do botão do Lorde em qualquer atualização de revisão sem `lordForm` (escolha de Disciplina ou push `changed`)

- **Onde:**
  - `C:\Code\Aetherius - Servidor Local\client-source\client\ui-core\modules\supernatural\supernatural-module.js:107-115,229-233,300-304` (commit `307d39c` / `overlays/cliente-local/035-lord-tab.patch`)
  - `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\supernatural-service.js:328`
  - `C:\Code\Aetherius - Servidor Local\source\server\ts\systems\aetheriusUiSystem.ts:171`
- **Evidência verificável:**
  - Em `supernatural-module.js:107-111`:
    ```js
    const next = safeState(raw);
    if (state?.grantId !== next.grantId || next.curse !== 'werewolf') {
      beastView = null;
      beastMessage = '';
    }
    ```
  - O commit `307d39c` habilitou o botão do Lorde para `state.lordGranted` em `:22`, `:50`, `:86` e `:96`, mas deixou `:108` testando apenas `next.curse !== 'werewolf'`.
  - Quando um vampiro com `lordGranted === true` escolhe uma Disciplina (`choose` retorna `{ ok: true, state }` sem `lordForm`) ou recebe um push `action: "changed"` (`aetheriusUiSystem.ts:171` envia `{ state }` sem `lordForm`, ex.: ganho de XP), `receive()` vê revisão maior, avalia `'vampire' !== 'werewolf'` → `true`, zera `beastView = null`, `receiveBeast()` não encontra `lordForm` e `render()` renderiza o botão `"Transformar em Lorde Vampiro"` desabilitado com `"Indisponível agora"` por até 10 s (até o próximo polling de `snapshot` ou clique em `ATUALIZAR`).
- **Correção recomendada:**
  - Em `supernatural-module.js:108`, trocar a condição para limpar `beastView`/`beastMessage` apenas quando mudar `grantId`, mudar o tipo de botão ou perder a concessão:
    ```js
    if (state?.grantId !== next.grantId ||
        (next.curse !== 'werewolf' && !next.lordGranted) ||
        Boolean(state?.lordGranted) !== Boolean(next.lordGranted)) {
      beastView = null;
      beastMessage = '';
    }
    ```

---

## 2. Auditoria por Requisito e Eixo

| Requisito / Eixo | Evidência verificada | Resultado |
|---|---|---|
| **P10 — Sem concessão automática; F7 `lordGrant`/`lordRevoke`; `JogadorLordeVampiro` só no servidor** | `vampire-grants.js:102` removeu concessão automática; `grant-origin.js` desfaz só grants `owned` e preserva origem externa (`teste-lorde.cjs:59-62`). `extension-catalog.cjs:90-91`, `supernatural-admin.cjs:5,26-27`, `audit-details.cjs:29` integram F7 com `characters.class`, `danger: true`, motivo 5–240 chars e auditoria booleana `lordGranted`. `JogadorLordeVampiro` vive exclusivamente em `supernatural-service.js:35,38,219` e disco; nunca vai ao cliente, UI ou auditoria. | **Aprovado** |
| **P10 — Botão no TAB (10 min / recarga 2 h persistida; validação no servidor)** | `supernatural-service.js:239-265,290-295`, `aetheriusUiSystem.ts:153`, `power-input.js:248-253`: validação integral server-side (`actorId` autenticado, `grantId`, `JogadorLordeVampiro`, recarga 2 h gravada em disco com rollback em falha, `gate` completo, `actionPolicy` `move`+`combat`, reserva de 5 de sangue com commit só em `isLord`). Z tem admissão independente. | **Aprovado com ressalva menor** (Achado 6 na UI) |
| **Lorde 1,25× sincronizado (dono, presente, tardio, saída/3D)** | `vampire-lord-service.js:45-64,102,180`: `scale: 1.25` publicado para dono e vizinhos; aguarda 3D e `DLC1VampireBeastRace`; escala relativa em `NPC Root [Root]` via `NetImmerse.setNodeScale` (mesma API de `modelApplyUtils.ts:134`); reaplica em recarga de 3D e restaura valor anterior na saída. | **Aprovado no escopo de código** (validação visual em jogo pendente) |
| **Menu Q automático temporário sem tocar favoritos persistidos** | `aetheriusInventoryService.ts:469,483-484,705`, `supernatural-service.js:377-380`: projeção efêmera só durante `lordGranted && isLord`, remove ação `setAbilityFavorite`, `setHotkey` recusa favorito efêmero (`ITEM_NOT_OWNED`), tabela `gameplay_inventory_favorites` e atalhos persistidos ficam intactos. | **Aprovado com ressalva** (Achado 3 no `equipAbility` das magias de mão) |
| **Sons `AETHERIUS - Vampire Lord and Werewolf SFX` (Swarm in/loop/out, Bat, Mist, Celerity, Strength)** | `powers.js:21-26,48-80,146-190,304-348`: os 11 IDs `000845–00084F` batem com a investigação; intro e outro tocam 1× para dono e vizinhos ≤3.000 u na mesma célula sem replay; loop do Enxame chega a observador tardio e corta em saída de alcance/3D/cancelamento; `monitorIntro` e `checkLoop` param handles na perda de 3D; falha de commit de sangue nunca dispara som/outro. | **Aprovado no escopo de código** (records SOUN/SNDR/WAVs no ESP/MO2 pendentes de houseCARL) |
| **Enxame 20 HP/s por 15 s** | `powers.js:23,239,242-255,426-457,467`: lê `duration` e `dps` dinamicamente do record vencedor (com checagem dos 6 CTDAs + efeito de recarga), drena apenas após `value.committed`, respeita `actionPolicy`, `canDamage`, `onSkySocialDamage`, `onSkyDeathHit`, exclusões e cura confirmada; recarga 60 s. | **Aprovado no escopo de código** (valores 20 HP/s e 15 s no ESP pendentes de houseCARL) |
| **Dash do Morcego humano e do Lorde** | Servidor + guarda nativa 256 u / 1,5 s + despacho `ShoutStart`/`ShoutSprintLongStart` (humano) e `PlayIdle(00BB8B:Dawnguard.esm)` (Lorde) em `powers.js:21,26,166-181,293-299,393-395` e `ActionListener.cpp:213-231` corretos; **bloqueado na entrada Z por `power-input.js:79`** (`event.userEvent` vs `event.userEventName`). | **Bloqueado** (Achado 1) |
| **Força Imensurável no motor de dano autoritativo** | `PropertyBindingFactory.cpp`, `MpActor.cpp:1602-1660`, `PartOne.cpp:171,463`, `powers.js:334-337,353,372-385`: estado nativo bounded 40 s, limpo em morte/desconexão/troca/expiração/revogação, multiplicadores 1,25× melee / 2,5× humano / 1,5× Lorde / arco excluído / bash 2×. **Defeito de autoridade em `hitData.isBashAttack`.** | **Reprovado** (Achado 2) |
| **Vampiric Drain do Lorde (`COTV_VampireLordDrain01`)** | `vampire-grants.js:40,106-108`, `vampire-lord-service.js:27-44,97,102`, `ActionListener.cpp:1345-1379,2084`: substitui dreno humano apenas na forma (preservando origem externa), absorção autoritativa via `ReadSpellHealth`/`ApplySpellHealthAbsorb`. **Conflito com menu Q na mão direita.** | **Aprovado com ressalva** (Achado 3) |
| **Perks do Lorde (15 perks)** | `vampire-grants.js:50-66`, `supernatural-service.js:109`, `perk-origin.js`: 15 perks entregues pelo ledger de autoria com remoção na revogação/remoção da maldição. | **Aprovado no escopo de código** (conferência dos 15 FormIDs e condições em forma mortal pendente de houseCARL) |
| **Bônus do Lorde por estágio só na forma (200..450, 100..350 armadura, 20..45% RM + 50 perks)** | `vampire-grants.js:70-73,108`, `MpActor.cpp:1673-1720,1913-1916`, `MagicResistance.h:106-116`, `TES5DamageFormula.cpp:186,509-513`: condicionados a `IsCreatedAsPlayer() && IsVampireLordRace() && lordStage`, sem dupla contagem em `ResistanceOf`. **Vazamento em `SendAttributeMaximumValues`.** | **Reprovado** (Achado 4; records `00085B–000861` no ESP pendentes de houseCARL) |
| **Relog / desconexão / morte / observadores** | Desconexão e morte encerram forma de Lorde e Força Imensurável; observadores tardios recebem escala 1,25× e loop do Enxame sem replay de intro. **Faltam guardas de relog pós-crash e PD para `DLC1VampireBeastRace`.** | **Reprovado** (Achado 5) |
| **Crash nativo** | Ponteiros, `HasEspm()`, `kInvalidFileIndex`, `std::isfinite`, `Napi::Value` e índices de plugin (regular e ESL) auditados em todos os trechos C++ novos de `030` e `031`. | **Aprovado (sem risco identificado)** |
| **Performance** | `GetVampireLordBonuses` tem fast-path imediato (`!IsCreatedAsPlayer() \|\| !IsVampireLordRace()` com máscara `& 0x00ffffff != 0x283a`); `nearbyListeners` limitado a 128 e chamado só quando necessário; `save()` só em mutações/clique. | **Aprovado** |

---

## 3. Verificações Independentes Executadas Nesta Revisão

- **ESP candidato (`overlays/sobrenatural/plugin/AETHERIUS - SOBRENATURAL.esp`):** SHA-256 conferido `211F983A782EFE553F8DB9E225F50D2BC51EBD2339F8F22A2D6B2462326C74A7` (bate com o relatório).
- **Integridade de espelhos (antes das alterações do worker paralelo):** os 6 arquivos JS de `58c9db0` e `d757a8b` (`supernatural-service.js`, `vampire-lord-service.js`, `powers.js`, `power-input.js`, `vampire-grants.js`, `supernatural-admin.cjs`) estavam byte-idênticos entre `overlays/sobrenatural/files/` e `source/`.
- **Patch de cliente:** `git -C client-source apply --reverse --check overlays/cliente-local/035-lord-tab.patch` → **OK (exit 0)**.
- **Testes Node reexecutados:**
  - Direcionados (`teste-lorde.cjs`, `teste-poderes.cjs`, `teste-formas.cjs`, `teste-transformacoes-18-11.cjs`, `teste-porte-voz.cjs`): **72/72 passaram**.
  - Suíte `overlays/sobrenatural/teste-*.cjs` (`node --test`): **369/369 passaram** com `AETHERIUS_VAMPIRE_FEED_ENABLED=false` e **369/369 passaram** com `AETHERIUS_VAMPIRE_FEED_ENABLED=true`.
  - Scripts independentes: `teste-servico.cjs` **OK**, `teste-painel-f7.cjs` **OK**, `teste-revisao-f7.cjs` **OK**.
  - UI Core: `Sobrenatural/ui-core-module/preview/test-ordering.cjs` → **12/12 PASS**.
- **Testes C++ Catch2:** não reexecutados localmente (o binário de teste não está persistido em `source/build` e o working tree de `source/server/cpp/` recebeu modificações de outro worker em paralelo durante a auditoria).

---

## 4. Pendências Sem houseCARL e Observações Opcionais (Não Bloqueantes)

### Pendências de records (sem acesso ao houseCARL neste modelo)
1. Conferir no ESP `AETHERIUS - SOBRENATURAL.esp` (`211F983A…`) e no perfil LOCAL via houseCARL (ou Luna MAX):
   - Records `SOUN`/`SNDR` `000845–00085A` apontando para os 11 WAVs vencedores de `AETHERIUS - Vampire Lord and Werewolf SFX`, remoção de scripts antigos nos MGEFs e das duas iniciações antigas do dash.
   - Override do Enxame com exatamente `20 HP/s` por `15 s` e preservação dos 6 CTDAs.
   - Records `00085B–000861` (`SN_Lorde_Estagio1..6` com 200..450 Vida/Magicka/Vigor, 100..350 Armadura, 20..45% RM e `SN_Lorde_PerksSobrenaturais` +50 em cada atributo), todos condicionados a `GetIsRace(DLC1VampireBeastRace)`.
   - Os 15 FormIDs de `VAMPIRE_LORD_PERKS` em `vampire-grants.js:50-66` — especialmente confirmar se alguma das 15 perks do COTV produz efeito indesejado em forma mortal, já que `supernatural-service.js:109` as concede sempre que `lordGranted(c)` está ativo (não apenas durante `lordActive`).

### Observações opcionais (não bloqueantes)
1. **Fallback de RM para pergaminhos/cajados (`TES5DamageFormula.cpp:433-436`):** o ramo de `SCRL`/`ENCH` só aplica `ResistanceFactor` quando `aetheriusCombatFixes.magicResist == true` e não inclui o `else if (!ignore)` de `GetVampireLordBonuses().magicResist` adicionado ao ramo `SPEL` em `:511-512`. Com `magicResist: true` (produção) não há diferença.
2. **Exibição de armadura no inventário (`ArmorRatingBinding.h:45-54`):** `aetheriusArmorRating.armorTotal` soma apenas peças vestidas e não soma `GetVampireLordBonuses().armor`; o cálculo real de combate (`TES5DamageFormula::CalcOpponentArmorRating` e `EquippedArmorRating`) já inclui o bônus do Lorde corretamente.
3. **Alinhamento de espelho da UI (`Sobrenatural/ui-core-module/modules/supernatural/supernatural-module.js`):** ainda não recebeu o commit `307d39c` (`FBC783E7…` no `client-source` vs `85E207C3…` no espelho em `Sobrenatural/ui-core-module/`) — atualizar na consolidação junto com a correção do Achado 6.
4. **Duração do botão do Lorde (`vampire-lord-service.js:135`):** `expires = at + durationMs` conta os 10 min a partir do aceite (incluindo os 8 s de `turning`, resultando em 9 min 52 s na raça de Lorde, conforme `teste-lorde.cjs:88`), enquanto o Lobisomem (`werewolf-service.js:244`) conta os 10 min a partir da troca efetiva de raça. Comportamento intencional nos testes; apenas registrar se o dev desejar simetria exata.
5. **Bestas (crossbows) sob Força Imensurável (`MpActor.cpp:1657-1659`):** já documentado em comentário pelo autor que, seguindo a estrutura literal do PERK `3EFE24` (que exclui apenas `WeapTypeBow`), uma besta sem keyword listada recebe `unarmedMultiplier` (`2.5×`). Vale confirmar com o dev se esse comportamento literal do COTV é desejado no Aetherius.
6. **Cache leve em `vampire-lord-service.js:30,51`:** `sp.Game.getFormFromFile(0x283a, 'Dawnguard.esm')` é consultado duas vezes por quadro enquanto em forma de Lorde; trivial de cachear em `state.lordRaceId` quando tocar o arquivo.


## Resposta do autor à revisão de 20/11

1. **Aceito.** O cliente lê `userEventName`, igual ao `ButtonEventData::ToJavaScriptObject` e à declaração de SkyrimPlatform. O controle remapeável Shout é um caminho independente para os poderes gerenciados da voz, incluindo Beast Form e Anel quando equipados. Menus, foco do browser e `Utility.isInMenuMode` bloqueiam esse leitor. O servidor continua conferindo slot, permissão, identidade, posse e política. O teste do dia 19 antecede esse callback, de modo que o nome incorreto explica a falha do fallback de 20/11. O trace original ainda precisa de reteste para identificar por que `actionVoiceFire` não virou comando naquela versão.
2. **Aceito.** `OnWeaponHit` elimina a flag de bash sem a evidência recente `AttackEvidence::Kind::Bash` quando a Força está ativa. O saneamento precede o cálculo, o script e o ledger de arco. O ramo separado de bash legítimo continua em 2× porque `3EFE24:COTV` tem `ModBashingDamage` sem CTDA. O ataque normal de arco continua fora do multiplicador.
3. **Aceito.** A mão direita recebe um Dreno uma vez, depois de raça e concessão confirmadas. Escolhas seguintes persistem. A consulta houseCARL mostrou que Gargoyle, Blood Ward, Blood to Power, Grip e Sanguine Rune têm `ETYP 013F43:Skyrim.esm` (LeftHand). `equipAbility` resolve esse tipo no record vencedor e equipa e aguarda a mão esquerda, inclusive por clique primário ou E. Spell de duas mãos disponíveis continua usando a opção solicitada.
4. **Aceito.** O host continua calculando o máximo efetivo. `SendAttributeMaximumValues` e a projeção inicial de `VisitProperties` passam a usar `GetMaximumValues(false)` para o valor base transmitido ao motor, que aplica as Abilities localmente.
5. **Aceito.** A whitelist adia a escrita de aparência com a raça de Lorde mesmo se o serviço ainda não publicou a raça. O encerramento respeita aparência eliminada na PD, mudança de personagem e desconexão. Se o ator online ainda tem a raça de Lorde sem identidade ativa, aguarda binding para a recuperação. O backup de uma sessão interrompida é usado apenas se a raça ainda é a de Lorde e a identidade está disponível.
6. **Aceito.** O TAB preserva o último estado de forma enquanto o grant e o tipo de botão continuam iguais. O espelho local recebeu o mesmo módulo do cliente.

Os deltas 032 (servidor) e 036 (cliente) são posteriores ao candidato auditado. A revisão independente desses deltas permanece pendente. Não houve instalação no MO2 nem execução do servidor.

---

## Reconfirmação Independente — 20/11/2026 (Gemini 4 Neon)

- **Alvo reconfirmado:**
  - `C:\Code\Aetherius - Servidor Local` — commit `76582000d5d7d0a79c069eb5c5951f0f21571cd9` (`7658200`): `fix(sobrenatural): corrige seis achados da revisão do Lorde` (`overlays/sobrenatural/032-lord-neon-review.patch`, `power-input.js`, `vampire-lord-service.js`, testes)
  - `C:\Code\Aetherius - Servidor Local\client-source` — commit `03a142ebd1312cdf54dfc96ef4057abe7eff5153` (`03a142e`): `fix(sobrenatural): preserva estado do Lorde no TAB` (`overlays/cliente-local/036-lord-tab-push.patch`)
- **Veredito final:** **APROVADO SEM ACHADOS PENDENTES (escopo de código).** Todos os seis achados da revisão inicial foram corrigidos de forma limpa, sem regressão nos testes reexecutados e sem novos defeitos identificados no delta.

### Reconfirmação por achado

1. **Achado 1 — `power-input.js:29-30,78-87` (`buttonEvent`, `userEventName` e bloqueio de menus/foco):** **Resolvido sem regressão.**
   - `event.userEventName !== 'Shout'` agora bate exatamente com `EventHandler.cpp:1884` e `skyrimPlatform.ts:428`.
   - O handler bloqueia `sp.browser?.isFocused?.()`, `sp.Utility?.isInMenuMode?.()` e a lista explícita de menus (incluindo `MeridianUI_FocusMenu` e `Console`).
   - O filtro `power.voice` passa a cobrir todas as potências gerenciadas no slot de voz (Lorde, Reverter, os 7 poderes vampíricos e Beast Form/Anel), mantendo todas as validações autoritativas no servidor (slot, posse, maldição, recarga, sangue, política, nonce único e cadência). A precisão histórica do autor está correta: o callback `buttonEvent` foi introduzido em 20/11 (portanto o campo `userEvent` quebrava o novo fallback de 20/11, não o teste de 19/11).
2. **Achado 2 — `ActionListener.cpp` (`032-lord-neon-review.patch:5-14`, validação de `hitData.isBashAttack`):** **Resolvido sem regressão.**
   - No início de `OnWeaponHit` (antes de `SendPapyrusOnHitEvent`, `IsBowOrCrossbowShot`/`bowShotLedger`, `CalculateDamage` e `ApplyVampireStrength`), quando `hitData.isBashAttack && aggressor->HasVampireStrength()`, o servidor exige `aggressor->GetCombatAttackEvidence().Confirm(AttackEvidence::Kind::Bash, aggressor->GetFormId(), aggressor->GetNpcGeneration(), currentHitTime)`.
   - Conferido em `ActionListener.cpp:394`: `RecordCombatAnimation` registra `hoster = myActor->GetFormId()` para jogadores, coincidindo exatamente com `aggressor->GetFormId()` (e `HasVampireStrength()` já restringe a jogadores vivos online). Um `isBashAttack: true` forjado volta a `false` (dano normal `1.25×` em arma branca e bloqueio por `bowShotLedger` + `WeapTypeBow` em arco), preservando `2.0×` apenas para bash com evidência de animação.
3. **Achado 3 — `vampire-lord-service.js:29-41` + `aetheriusInventoryService.ts:694-700` (Dreno uma vez + `ETYP LeftHand` no menu Q):** **Resolvido sem regressão.**
   - `effects(ctx)` agora só grava `state.drain = value.drain` e chama `actor.equipSpell(drain, 1)` uma única vez após confirmar raça de Lorde e `actor.hasSpell(drain)`, preservando escolhas posteriores do jogador na mão direita e restaurando `previousSpell` na saída.
   - `aetheriusInventoryService.ts:697-702` lê o subrecord `ETYP` do record vencedor de `SPEL`, resolve para o FormID global `0x13f43` (`Skyrim.esm` `LeftHand`) e força `source = 0` (`leftSpell`) com `settle` em `equipment.leftSpell` — eliminando conflito e timeout de 1,5 s no clique esquerdo/`E` das 5 magias de mão do Lorde (confirmadas pelo autor via houseCARL como `ETYP 013F43:Skyrim.esm`), sem alterar magias `EitherHand`.
4. **Achado 4 — `MpActor.h:216` + `MpActor.cpp:380,1908-1938` + `DevAttributes.cpp:45` (`GetMaximumValues(false)` na projeção de base AV):** **Resolvido sem regressão.**
   - `SendAttributeMaximumValues()` e `VisitProperties(CreateActorMessage&)` usam `GetMaximumValues(false)` (e não poluem `CombatProbe`), evitando injetar os bônus de Ability do Lorde no base AV do cliente (`Actor.SetActorValue`) enquanto todas as vias autoritativas do servidor (dano, absorção, regeneração, `Restore/DamageActorValue`, `aetheriusAttributeBonuses.maximum` / Enxame) continuam usando `GetMaximumValues(true)`.
5. **Achado 5 — `whitelist.js:216-221` + `vampire-lord-service.js:157-187` (relog pós-crash e PD em `DLC1VampireBeastRace`):** **Resolvido sem regressão.**
   - `whitelist._applyAppearance` adia a sincronização de raça/gênero para `DLC1VampireBeastRace` (com fallback `mp.getIdFromDesc('283a:Dawnguard.esm')` caso o serviço de Lorde ainda não tenha subido).
   - `finish(id)` protege atores limpos por PD (`appearance == null`) e trocas de personagem, aguarda identidade se online em forma de Lorde sem vínculo (`identity_pending`) e segue restaurando a aparência mortal na desconexão comum. `recover(id)` só restaura se `appearance?.raceId === RACE` e `characterOf(id)` estiver disponível, limpando marcadores obsoletos sem sobrescrever aparência nova.
6. **Achado 6 — `supernatural-module.js:95-122` (`03a142e` / `036-lord-tab-push.patch`):** **Resolvido sem regressão.**
   - `formKey(state) !== formKey(next)` preserva `beastView` e `beastMessage` em atualizações de revisão sem `lordForm` (escolha de Disciplina e push `changed`), limpando corretamente ao conceder/revogar Lorde ou trocar/remover maldição. Espelho em `Sobrenatural/ui-core-module/modules/supernatural/supernatural-module.js` alinhado byte a byte.

### Verificações independentes executadas na reconfirmação

- **Patches (`--reverse --check`):**
  - `git -C source apply --reverse --check overlays/sobrenatural/032-lord-neon-review.patch` → **OK (exit 0)**.
  - `git -C client-source apply --reverse --check overlays/cliente-local/036-lord-tab-push.patch` → **OK (exit 0)**.
- **Consistência de espelhos (SHA-256):**
  - `power-input.js` (`E7BCE454…`): idêntico entre `overlays/sobrenatural/files/` e `source/`.
  - `vampire-lord-service.js` (`95679010…`): idêntico entre `overlays/sobrenatural/files/` e `source/`.
  - `supernatural-module.js` (`D74B6908…`): idêntico entre `client-source/client/ui-core/modules/supernatural/` e `Sobrenatural/ui-core-module/modules/supernatural/`.
- **TypeScript (servidor):** `npx tsc --noEmit` em `source/server` → **OK (exit 0)**.
- **Testes Node reexecutados nos dois modos de alimentação:**
  - `AETHERIUS_VAMPIRE_FEED_ENABLED=false` (overlays `teste-*.cjs` + `werewolf.test.cjs` + `survival-service.test.js` + `skysocial-service.test.js` + `magic.test.cjs`): **514/514 passaram** (`fail 0`).
  - `AETHERIUS_VAMPIRE_FEED_ENABLED=true` (mesmo conjunto): **514/514 passaram** (`fail 0`).
  - Scripts independentes: `teste-servico.cjs` **OK**, `teste-painel-f7.cjs` **OK**, `teste-revisao-f7.cjs` **OK**.
  - UI Core: `Sobrenatural/ui-core-module/preview/test-ordering.cjs` → **13/13 PASS** (incluindo o novo teste de preservação do botão do Lorde em push/escolha).

### Pendências fora do escopo de código desta reconfirmação

- Conferência independente dos records binários do ESP `211F983A…` via houseCARL (fora do alcance deste modelo; item 4 acima permanece listado para fechamento por modelo com houseCARL).
- Compilação do addon nativo Release com `032` + execução dos testes Catch2 (`[VampireStrength],[BatDash],[VampireLordStats]`), instalação em MO2/runtime (`Prepare`) e validação em jogo (dono, observador presente, observador tardio, áudio, Q, dash humano/Lorde e relog).
