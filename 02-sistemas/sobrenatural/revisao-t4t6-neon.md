# Revisão Independente — T4 / T5 / T6 (Velocidade, Mãos e Uivo da Fera)

- **Revisor:** Gemini 4 Neon (revisão cruzada honesta, somente leitura no código)
- **Data:** 19/11/2026
- **Autor revisado:** Sol High (despacho `task_d2e235f9be35`)
- **Alvo revisado (`C:\Code\Aetherius - Servidor Local`):**
  - `535c284c9059cd2921f0ba338093ffc3aa9ee1b2` — `feat(sobrenatural): aumenta velocidade da fera por habilidade de estágio` (`overlays/sobrenatural/plugin/AETHERIUS - SOBRENATURAL.esp`, SHA-256 `C8B1320710873AD2F8203498990B24305A28D274D8D4D6C4BB2264FC15B4B06A`)
  - `509c0fe655fb792ef5d17f5958434bd75f11f081` — `fix(sobrenatural): limpa e bloqueia equipamento mortal nas mãos da fera` (`overlays/sobrenatural/027-werewolf-hands.patch`, `werewolf/client.js`, `werewolf.test.cjs`)
  - `340cf166455c0af3bd229cbbfef4947bcbe97086` — `fix(sobrenatural): preserva fonte desarmada da fera na limpeza local`
  - `abccba5b7965c5811026a7f3ab38b0ffe08acd6f` — `test(sobrenatural): usa forms reais no caso de combate desarmado`
  - `748be01f18c99a1d72df2129fd7c3aaf8bb65fed` — `docs(sobrenatural): registra decisões e validação de velocidade e mãos da fera`
- **Fontes consultadas:**
  - [INVESTIGACAO_19-11_maos-uivo-fera.md](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/investigacoes/INVESTIGACAO_19-11_maos-uivo-fera.md)
  - [DECISOES.md](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/DECISOES.md) e [ETAPA-2_lobisomem.md](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/ETAPA-2_lobisomem.md)
  - [TESTE-JOGO_19-11.md](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/TESTE-JOGO_19-11.md)
  - Código fonte do laboratório (`source/` e `client-source/`) nos pontos nativos, gamemode e cliente.

---

## Veredito

**APROVADO COM RESSALVAS (2 achados pontuais — 1 Médio, 1 Baixo — e pendência de conferência de records do ESP via houseCARL).**

A arquitetura geral da solução está bem desenhada, segura contra crash nativo, preserva integralmente inventário, encantamentos e armaduras fora das mãos, evita `UnequipAll` no dono, bloqueia reentrada de equipamento mortal apenas durante `WerewolfBeastRace` (`0xCDD84`), mantém a exclusividade mútua das seis abilities `SN_Lobisomem_Estagio1..6` em todas as transições no servidor e tem impacto de performance desprezível.

Foram encontrados **dois defeitos reais e localizados** (correção cirúrgica de poucas linhas, sem redesenho), além da pendência explícita de auditoria dos records do ESP `535c284` pelo **Luna MAX** (Gemini não possui houseCARL):

1. **[Médio] Curto-circuito `||` impede desequipar escudo no dono quando `getEquippedObject(0)` retorna `0x1F4` (Unarmed) ou um `Spell`** (`werewolf/client.js:126-127`). Recomenda-se corrigir antes do teste em jogo (1H + escudo).
2. **[Baixo] `Equipment::ClearWerewolfHands` omite pergaminhos (`espm::SCRL::kType`)** (`Equipment.cpp:31-45`), que no servidor ficam em `Equipment.inv.entries` com `Worn::Left`/`Right`. Recomenda-se incluir na próxima recompilação do addon nativo.

---

## 1. Achados Reais (Com Evidência)

### Achado 1 (Médio) — Curto-circuito `||` em `werewolf/client.js:126` pode impedir a remoção de escudo no cliente dono

- **Onde:**
  - `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\werewolf\client.js:120-129` (byte-idêntico a `source/server/gamemode/werewolf/client.js:120-129`)
  - Cobertura incompleta em `overlays/sobrenatural/files/server/gamemode/werewolf/tests/werewolf.test.cjs:184-195`
- **Código atual:**
  ```js
  for (const slot of [0, 1]) {
    safe(() => {
      const spell = actor.getEquippedSpell(slot);
      if (spell) actor.unequipSpell(spell, slot);
    });
    safe(() => {
      const item = actor.getEquippedObject(slot) || (slot === 0 && actor.getEquippedShield());
      if (item && item.getFormID() !== 0x1f4 && !sp.Spell.from(item)) actor.unequipItem(item, false, true);
    });
  }
  ```
- **Evidência / raciocínio verificável:**
  - O commit `340cf16` introduziu `item.getFormID() !== 0x1f4` justamente porque, após `SetRace(0xCDD84)`, `actor.getEquippedObject(slot)` pode retornar o form desarmado `0x1F4` (`Unarmed`).
  - Um escudo no Skyrim é um `ARMO` no biped slot 39 (não uma arma), de modo que, se `actor.getEquippedObject(0)` retornar `0x1F4` (truthy) enquanto o escudo ainda estiver vestido no slot 39, o operador `||` sofre **curto-circuito** e **`actor.getEquippedShield()` nunca é chamado**. Na linha 127, `item.getFormID() !== 0x1f4` avalia como falso e o escudo permanece equipado no dono durante toda a forma de fera.
  - O mesmo curto-circuito ocorre no frame em que `getEquippedObject(0)` ainda retorna um `Spell` cujo `unequipSpell(spell, 0)` acabou de ser enfileirado na linha 123 (embora, se depois `getEquippedObject(0)` passar a `null`, um tick posterior recupere; se passar a `0x1F4`, nunca recupera).
  - O teste em `werewolf.test.cjs:190` exercitou apenas `{ shield, objects: [null, weapon] }` (`objects[0] === null`), não cobrindo `{ shield, objects: [unarmed, weapon] }`.
- **Correção sugerida:** Checar `actor.getEquippedShield()` separadamente quando `slot === 0`, fora do `||` com `getEquippedObject(slot)` (e evitar `unequipItem` duplicado se `shield.getFormID() === item?.getFormID()`), acrescentando o caso `{ shield, objects: [unarmed, weapon] }` em `werewolf.test.cjs`.

---

### Achado 2 (Baixo) — Pergaminhos (`espm::SCRL`) equipados nas mãos não são limpos no servidor

- **Onde:**
  - `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\027-werewolf-hands.patch` → `source/server/cpp/server_guest_lib/Equipment.cpp:31-45`
  - Comparar com `source/server/cpp/server_guest_lib/MpActor.cpp:2226-2247` (`MpActor::GetEquippedScroll`) e `source/libespm/include/libespm/SCRL.h:11`
- **Evidência / raciocínio verificável:**
  - No SkyMP/Aetherius, pergaminhos (`espm::SCRL`) equipados nas mãos esquerda/direita são armazenados em `Equipment.inv.entries` com `Inventory::Worn::Left`/`Right` — comprovado por `MpActor::GetEquippedScroll()` (`MpActor.cpp:2231-2244`) — e **não** em `leftSpell`/`rightSpell`.
  - Em `Equipment::ClearWerewolfHands` (`Equipment.cpp:32-41`), `isHandItem` trata apenas `espm::WEAP::kType`, `espm::LIGH::kType` (`CanBeCarried`) e `espm::ARMO::kType` (slot 39), omitindo `espm::SCRL::kType`.
  - Consequência: ao transformar segurando um pergaminho na mão, `AppearanceBinding::Set` (`AppearanceBinding.cpp:44`) não limpa o pergaminho antes de publicar a aparência aos observadores, e `ActionListener::OnUpdateEquipment` (`ActionListener.cpp:412-414`) aceita reentrada de um pergaminho `worn` por pacote atrasado ou forjado durante `0xCDD84` (no dono, `werewolf/client.js:126-127` acaba removendo localmente porque `SCRL` não é `Spell`, mas a proteção autoritativa do servidor e a publicação inicial aos observadores ficam furadas para `SCRL`).
- **Correção sugerida:** Incluir `#include "libespm/SCRL.h"` em `Equipment.cpp` e alterar a linha 32 para `bool isHandItem = type == espm::WEAP::kType || type == espm::SCRL::kType;`, adicionando um `SCRL` ao teste `PartOne_UpdateEquipmentTest.cpp`.

---

## 2. Auditoria por Eixo Requisitado

| Eixo | Evidência verificada | Resultado |
|---|---|---|
| **1. T4 — Aplicação/remoção única do bônus de velocidade (entrada, saída, morte, relog, troca de estágio)** | **Lado servidor JS:** `werewolf-grants.js:36-40` coloca em `wanted` exatamente uma `BONUS_BY_STAGE[stageIndex]` (`SN_Lobisomem_Estagio1..6`, `0x827–0x82C`). Na **troca de estágio** (`supernatural-service.js:195-210`) e **remoção** (`:178-194`), `reconcileSpells` (`grant-origin.js:17-35`) remove todas as abilities `owned` fora de `desired` **antes** de adicionar a nova — impedindo coexistência ou empilhamento `+60 + +60`. Na **entrada/saída**, `werewolf-service.js` não duplica abilities; a ativação depende da condição `GetIsRace(0CDD84)` no ESP. Na **morte** (`werewolf-service.js:354-358`), `!alive(id)` (incluindo `skyDeath.phase !== 'ALIVE'`) chama `finish(id, { animate: false })` e devolve a raça mortal em até 200 ms. No **relog/queda** (`werewolf-service.js:269-295,342-348`), `recover(id)` restaura a raça mortal no boot/login, e `onCharacterActive` (`supernatural-service.js:263-267`) é idempotente (`known().has(id)`). **Records do ESP (`535c284`):** ver seção 4 (pendência houseCARL). | **Aprovado no código JS**; **ESP pendente de houseCARL** |
| **2. Inventário e encantamentos preservados** | `Equipment::ClearWerewolfHands` (`Equipment.cpp:10-48`) opera apenas sobre `Equipment` (cópia) e chama apenas `entry.SetWorn(Inventory::Worn::None)` (`Inventory.cpp:54-57`, que zera somente `worn_`/`wornLeft`). Nunca chama `RemoveItem`/`SetInventory` nem altera `count`, `health`, `enchantmentId`, `maxCharge`, `chargePercent`, `name`, `soul`, `poisonId` ou `poisonCount`. Em `MpActor::StoreEquipment` (`BoundWeaponHost.h:258-268`), `form.inv` não é tocado (e com `aetheriusWeaponPoison` ligado, `WeaponPoison.h:115` só zera `Worn` das armas). No cliente dono, `FormViewArray` (`formViewArray.ts:43`) pula `playerCharacterFormIdx`, logo `applyEquipment` (destrutivo) nunca roda no dono. Na morte (`skydeath/src/death-system.js:140,186,259`), `ghostInventory` preserva todos os itens desequipados na bolsa. | **Aprovado** |
| **3. Nada de `UnequipAll`** | `werewolf/client.js:120-129` usa exclusivamente `actor.unequipSpell(spell, slot)` (slots 0 e 1) e `actor.unequipItem(item, false, true)` — sem `unequipAll`, sem `removeAllItems`, sem mexer em armaduras fora do slot 39 nem na voz (`slot 2` / `voiceSpell`). O `applyEquipment` (`equipment.ts:129`, que usa `unequipAll`) continua restrito a réplicas de observadores em `FormView` (`formView.ts:612`). | **Aprovado** |
| **4. Reentrada de equipamento mortal bloqueada só durante a fera** | Em `ActionListener::OnUpdateEquipment` (`ActionListener.cpp:411-414`), `ClearWerewolfHands` só executa quando `appearance && appearance->raceId == Equipment::kWerewolfBeastRace` (`0xCDD84`). Em `AppearanceBinding::Set` (`AppearanceBinding.cpp:41-42`), idem, e ainda restrito a `actor.IsCreatedAsPlayer()` (não afeta NPCs lobisomens). Ao voltar à raça mortal (`werewolf-service.js:238`), o equipamento normal volta a ser aceito imediatamente (coberto por `PartOne_UpdateEquipmentTest.cpp:104-108`). Em `OnSpellCast` (`ActionListener.cpp:1813,1833`), como `leftSpell`/`rightSpell` foram removidos e impedidos de reentrar, casts de magias mortais de mão são recusados no servidor. **Ressalvas:** Achado 1 (escudo no dono se `getEquippedObject(0)` retornar `0x1F4`) e Achado 2 (`SCRL` no servidor). | **Aprovado com ressalvas** (Achados 1 e 2) |
| **5. Dessincronização com observadores** | Em `AppearanceBinding::Set` (`AppearanceBinding.cpp:48-66`), `UpdateEquipmentMessage` limpo e `UpdateAppearanceMessage` são enfileirados **no mesmo canal diferido** `kChannelAppearance = 2` com `overwritePreviousChannelMessages = false` (`PartOne.cpp:725-728`), preservando ordem FIFO em `TickDeferredMessages()` (`PartOne.cpp:1018-1035`): o observador recebe o equipamento limpo antes da aparência de fera. Novos observadores que entram depois recebem `emitterAsActor->GetEquipment()` já limpo em `CreateActorMessage` (`PartOne.cpp:799-801`). Em `ActionListener::OnUpdateEquipment` (`ActionListener.cpp:672-685`), pacotes filtrados (`strippedBeastHands`) replicam `cleaned` aos vizinhos (`userId != rawMsgData.userId`), sem eco ao dono, e funcionam também quando `ReconcileWeaponPoisonEquipment` está ligado (`WeaponPoisonHost.h:141-160`). Ver Obs. 1 sobre `numChanges`. | **Aprovado** (ver Achado 2 e Obs. 1) |
| **6. Crash nativo (C++ / N-API)** | `Equipment::ClearWerewolfHands` (`Equipment.cpp:19,28`) protege `world == nullptr`, `!world->HasEspm()` e `record.rec == nullptr`. `kShieldBipedSlot = 1u << (39 - 30)` (`0x200`) coincide com `MpActor.cpp:2279`. Em `ARMO.h:21-26`, `bodt.bodyPartFlags` é inicializado com `0` (sem UB caso `bod2` e `bodt` estejam ausentes). `LIGH::GetData` é `noexcept`. `ARMO::GetData` só lança se faltar `DNAM` (mesmo padrão pré-existente em `ActionListener.cpp:532,615`), e `ScampServer::Set` (`ScampServer.cpp:1317`) converte `std::exception` em `Napi::Error` sem abortar o processo. `ReconcileBoundWeapons` (`AppearanceBinding.cpp:47`) e `AllowsBoundEquipment` (`ActionListener.cpp:476`) falham fechados com armas vinculadas ativas ou recém-conjuradas. No cliente (`werewolf/client.js:118-129`), todas as chamadas nativas estão isoladas em `safe(() => ...)` sem reter handles `Actor`/`Form` em `state` (zero risco de `indexInPool`). | **Aprovado** |
| **7. Performance** | No cliente dono (`werewolf/client.js:114-131`), o bloco roda apenas quando `isMe && value.phase === 'beast'`, com throttle de `250 ms` (~4 Hz; fora disso é apenas comparação de booleano/timestamp por frame, e para observadores `isMe === false` nem avalia `Date.now()`). No servidor, `ClearWerewolfHands` só executa para `raceId == 0xCDD84` em eventos discretos (`AppearanceBinding::Set` e `OnUpdateEquipment`), iterando apenas sobre itens `worn` em `inv.entries` (tipicamente < 15 itens) com lookups O(1)/cache em `espm`. | **Aprovado** |
| **8. T6 (uivo isolado) e documentação (`748be01`)** | Decisão do dev respeitada: Iniciado sem uivo; Ômega+ Terror (`0CE218:Skyrim.esm`), Alfa Fúria (`00085E:Manbeast.esp`); transporte de Z intocado até resolução de T1/T2. `INVESTIGACAO_19-11_maos-uivo-fera.md` e `ETAPA-2_lobisomem.md` estão byte-idênticos entre o laboratório e `Sobrenatural\docs\`. As decisões de `19/11 — Mãos, velocidade e Z da fera` coincidem entre ambos os `DECISOES.md`. | **Aprovado** |

---

## 3. Observações Opcionais (Não Bloqueantes)

1. **Alinhamento eventual de `numChanges` entre servidor e `SendInputsService` (`AppearanceBinding.cpp:45`, `BoundWeaponHost.h:304`, `sendInputsService.ts:313`, `formView.ts:598`):**
   - `AppearanceBinding::Set` incrementa `++equipment.numChanges` (`N → N+1`) quando limpa as mãos, seguindo o padrão pré-existente de `BoundWeaponHost.h:304`, `skydeath/death-system.js:190,247` e `MpActor.cpp:175`. Como `SendInputsService` mantém `this.numEquipmentChanges` localmente sem sincronizar do servidor (`remoteServer.ts:939-952`), no fluxo normal o `onUnequip` da limpeza local no dono avança o contador para `N+1` e evita colisão na volta à forma mortal (`N+2`).
   - Porém, em cenários pontuais (encerramento/morte antes de `client.js` enviar `UpdateEquipment`, ou presença de arma vinculada onde `AppearanceBinding.cpp:45` + `BoundWeaponHost.h:304` avançam `+2`, ou troca de armadura simultânea que chegue com `numChanges == N+1`), uma atualização subsequente pode coincidir com `this.eqState.lastNumChanges` no observador (`formView.ts:598`) e só ser aplicada na troca de equipamento seguinte. É limitação arquitetural herdada do protocolo `numChanges` do SkyMP e auto-recuperável; não bloqueia este lote.
2. **Recálculo de velocidade no Creation Engine ao transformar com mãos vazias (T4):**
   - Em Skyrim SE, mudanças em `SpeedMult` via Ability condicionada às vezes só atualizam o cache interno de velocidade de movimento quando ocorre atualização de peso/equipamento/estado de movimento. Como `SetRace` + limpeza de mãos costumam atualizar o ator, o problema pode nem ocorrer — mas, se a condição `GetIsRace(0CDD84)` da Ability reavaliar ~1 s após `SetRace` num personagem que já entrou com **mãos vazias**, vale confirmar no teste em jogo (roteiro já previsto em `INVESTIGACAO_19-11_maos-uivo-fera.md:24,46`) a velocidade e a distância real tanto com mãos ocupadas quanto com mãos livres.
3. **Envio duplo inofensivo no canal 2 quando `aetheriusWeaponPoison` estiver ligado (`AppearanceBinding.cpp:46,51-53` + `WeaponPoisonHost.h:158`):**
   - Com `weaponPoison: true`, `actor.SetEquipment(equipment)` chama `ReconcileWeaponPoisonEquipment` → `PublishWeaponPoisonEquipment(false)` no canal 2 e, logo em seguida, `AppearanceBinding.cpp:51-53` enfileira novamente o mesmo `UpdateEquipmentMessage` limpo no canal 2. Não quebra nada (mesmo payload e `numChanges`).
4. **Sincronização menor em `DECISOES.md`:**
   - `C:\Code\Aetherius - New Systems\Sobrenatural\docs\DECISOES.md` possui duas seções anteriores do orquestrador (`18/11 — Sem teste com 2 jogadores` e `19/11 — Recarga do Morcego`) ausentes em `overlays/sobrenatural/docs/DECISOES.md`. A seção nova de `748be01` está idêntica nos dois.

---

## 4. Pendências Explícitas (Fora do Alcance Desta Revisão)

1. **Conferência de records do ESP `535c284` via houseCARL (delegar ao Luna MAX):**
   - Arquivo: `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\plugin\AETHERIUS - SOBRENATURAL.esp` (SHA-256 conferido: `C8B1320710873AD2F8203498990B24305A28D274D8D4D6C4BB2264FC15B4B06A`).
   - Conferir por leitura houseCARL:
     - MGEF `000844` (`SN_Lobisomem_VelocidadeFera`): Constant Effect, Self, `ValueModifier`, `SpeedMult`, flags `Recover`, `NoDuration`, `NoArea`, `HideInUI`, `NoHitEvent`, sem VMAD/scripts.
     - Abilities `000827–00082C` (`SN_Lobisomem_Estagio1..6`): referência ao MGEF `000844` com magnitude `60`, duração `0`, área `0` e CTDA de sujeito `GetIsRace(0CDD84:Skyrim.esm) == 1`.
     - Integridade dos demais 61 records, ordem original de masters e ausência de overrides em `RACE` ou `MOVT`.
2. **Deploy local, Pandora e validação em jogo:**
   - `runtime/server/data/AETHERIUS - SOBRENATURAL.esp` ainda mantém a versão anterior (`65BB3AAE...`) e o addon compilado (`AE4F6342...`) segue em `source/build/dist/server/scam_native.node` (conforme documentado pelo Sol).
   - Regerar o Pandora no perfil `AETHERIUS - LOCAL` (remoção de Precision Creatures + adição de New Creature Animation - Werewolf) e executar o reteste em jogo (medição real de `SpeedMult`/distância, ataques/combos, mãos com feitiços/2H/1H+escudo/tocha/mãos livres, e observadores multiplayer).
3. **T6 — Uivo por estágio no Z:** pendente de integração com a correção de T1/T2.

---

## 5. Verificações Executadas na Revisão

Executadas diretamente em `C:\Code\Aetherius - Servidor Local`, sem editar código nem fazer deploy:

| Verificação | Resultado |
|---|---|
| `git apply --check --reverse overlays/sobrenatural/027-werewolf-hands.patch` sobre `source/` | exit `0` |
| `fc.exe /B` entre `overlays/` e `source/` para `werewolf/client.js` e `werewolf.test.cjs` | Byte-idênticos |
| `node --check overlays/sobrenatural/files/server/gamemode/werewolf/client.js` | exit `0` |
| `node --test` (`teste-formas.cjs`, `teste-porte-voz.cjs`, `teste-transformacao-forcada.cjs`, `teste-aparencia.cjs`, `source/.../werewolf.test.cjs`) | **108/108** (`fail 0`, exit `0`) |
| SHA-256 de `overlays/sobrenatural/plugin/AETHERIUS - SOBRENATURAL.esp` | `C8B1320710873AD2F8203498990B24305A28D274D8D4D6C4BB2264FC15B4B06A` (bate com `748be01`) |
| SHA-256 de `source/build/dist/server/scam_native.node` | `AE4F6342BBB26F005077F6832FD074B737FE339D6897C13487926562677E3B7A` (bate com `748be01`) |
| Paridade de `INVESTIGACAO_19-11_maos-uivo-fera.md` e `ETAPA-2_lobisomem.md` (lab vs `Sobrenatural\docs\`) | Byte-idênticos |

---

## 6. Resposta do autor — 19/11/2026

Candidato `95bec8d` no laboratório `C:\Code\Aetherius - Servidor Local`. Os dois achados foram **aceitos** e corrigidos no mesmo candidato. O veredito original e as observações do revisor acima registram a versão que ele examinou. A reconfirmação independente do candidato está pendente.

| Achado | Evidência e resolução |
| --- | --- |
| Médio — escudo no dono | A regressão adicionada a `source/server/gamemode/werewolf/tests/werewolf.test.cjs:197` falhou antes da correção: o slot esquerdo informou `0x1F4` e o dono produziu zero chamadas a `unequipItem`, embora `getEquippedShield()` tivesse um escudo. `source/server/gamemode/werewolf/client.js:120` agora consulta e desequipa o escudo pelo seu próprio resultado e usa o FormID para evitar repetir a operação se as duas APIs retornarem o mesmo item. O teste cobre punhos, magia ainda reportada pelo slot e o mesmo escudo nas duas APIs. |
| Baixo — pergaminho na sessão de fera | `MpActor::GetEquippedScroll` lê `SCRL` com flag de mão em `Equipment.inv` (`source/server/cpp/server_guest_lib/MpActor.cpp:2226`). Com o pergaminho `096598:Skyrim.esm` na mão esquerda, o teste nativo agora falha na versão anterior à correção com `Worn::Left` (2), em vez de `Worn::None` (0). `source/server/cpp/server_guest_lib/Equipment.cpp:33` passou a incluir `espm::SCRL::kType`. A mesma limpeza retira somente a flag de equipamento, mantém o pergaminho no inventário e filtra sua reentrada para o observador. O teste cobre retorno à forma mortal. |

**Verificações.** Node **506/506** em cada modo de `AETHERIUS_VAMPIRE_FEED_ENABLED`, sem falhas. Filtro nativo `[WerewolfEquipment]`, **13/13 asserções** com os cinco masters base. `tsc --noEmit`, webpack de produção sem deploy/zip, `node --check`, reverse-check do patch 027 e paridade dos dois pares de arquivos source/overlay passaram. No recorte suplementar `UpdateEquipment,[WerewolfEquipment]`, o teste legado `UpdateEquipment` falhou na preparação. O fixture usa `PartOne` sem carregar masters e chama `CreateActor(..., 0x3c)` (`source/unit/PartOne_UpdateEquipmentTest.cpp:5–9`). A construção converte esse worldspace via `pluginIndexMapping` (`source/server/cpp/server_guest_lib/PartOne.cpp:153–165`) e lança `unknown regular runtime slot 0x0` antes do primeiro pacote de equipamento. O caso da fera passou no mesmo recorte.

**Addon.** `cmake --build X:/source/build --config Release --target skymp5-server --parallel 4` passou. SHA-256 do novo `C:\Code\Aetherius - Servidor Local\source\build\dist\server\scam_native.node` é `23669DE235A7A3B480B013326B1638A013C5DAC6D265FA86D51131D7C77F9F2B`. Logs `%TEMP%\aetherius-werewolf-review-native-before.log`, `%TEMP%\aetherius-werewolf-review-native-after.log`, `%TEMP%\aetherius-werewolf-review-false.log` e `%TEMP%\aetherius-werewolf-review-true.log`. Validação em jogo, instalação do addon e demais pendências da seção 4 continuam abertas.

---

## 7. Reconfirmação Independente — 19/11/2026 (Gemini 4 Neon)

- **Alvo reconfirmado (`C:\Code\Aetherius - Servidor Local`):**
  - `95bec8d94d01c0eec2dd06e6d108c4b19d2a1c61` — `fix(sobrenatural): limpa escudo e pergaminho na fera`
  - `33c06186d5428e17e49ab290d2667ecd696968af` — `docs(sobrenatural): registra resposta à revisão da fera`
  - Addon nativo compilado: `source/build/dist/server/scam_native.node`, SHA-256 `23669DE235A7A3B480B013326B1638A013C5DAC6D265FA86D51131D7C77F9F2B` (conferido).

### Veredito da Reconfirmação

**APROVADO NO ESCOPO DE CÓDIGO (sem novos achados).**

Os dois achados da revisão inicial (Médio — escudo no dono; Baixo — pergaminho `SCRL` no servidor) foram corrigidos de forma limpa, cirúrgica e sem regressão. Além disso, comprovou-se que a falha do teste legado `UpdateEquipment` (`PluginIndexMapping: unknown regular runtime slot 0x0`) **já existia antes deste lote** (presente desde a base oficial `aetherius-server-main` / `310cb59`, anterior a `5effbd8`) e não foi introduzida pelos commits T4/T5/T6.

Permanecem abertas apenas as pendências externas já registradas na seção 4: auditoria houseCARL dos records do ESP `535c284` (Luna MAX), distribuição/deploy do addon e ESP, regeneração do Pandora, validação em jogo e integração futura de T6 (uivo no Z) com T1/T2.

### 7.1 Verificação dos Dois Achados Corrigidos (`95bec8d`)

| Achado | Código verificado | Resultado |
|---|---|---|
| **Achado 1 (Médio) — escudo no dono** | `source/server/gamemode/werewolf/client.js:120-141` (espelhado byte-a-byte em `overlays/sobrenatural/files/server/gamemode/werewolf/client.js`): agora consulta `actor.getEquippedShield()` em bloco `safe(...)` próprio **antes** do loop `[0, 1]`, desequipa o escudo e só grava `clearedShieldId = id` após `actor.unequipItem(shield, false, true)` concluir sem exceção. No loop, `id === 0x1f4 \|\| id === clearedShieldId \|\| sp.Spell.from(item)` evita dupla chamada quando `getEquippedObject(0)` devolve o mesmo escudo, sem impedir o desequipamento quando o slot 0 devolve `0x1F4` ou um `Spell`. Cobre todos os casos sem `UnequipAll` e sem reter handles em `state`. Novo caso de regressão em `werewolf.test.cjs:142-151` (`objects: [unarmed]`, `objects: [spell]`, `objects: [shield]`). | **Resolvido sem regressão** |
| **Achado 2 (Baixo) — pergaminho `espm::SCRL` no servidor** | `overlays/sobrenatural/027-werewolf-hands.patch` → `source/server/cpp/server_guest_lib/Equipment.cpp:5,33`: inclui `libespm/SCRL.h` e testa `type == espm::WEAP::kType \|\| type == espm::SCRL::kType`. Remove somente a flag `Worn` (`Worn::Left`/`Right` → `Worn::None`), preservando entrada, contagem e inventário, e bloqueia reentrada por `OnUpdateEquipment` durante `0xCDD84` até o retorno à raça mortal. Coberto em `source/unit/PartOne_UpdateEquipmentTest.cpp:56,73-92,112` com `kFireboltScroll = 0x96598` (`Skyrim.esm`). | **Resolvido sem regressão** |

### 7.2 Auditoria da Falha do Teste Legado `UpdateEquipment`

Confirmou-se que a falha de `TEST_CASE("UpdateEquipment", "[PartOne]")` (`PartOne_UpdateEquipmentTest.cpp:3-42`) **é pré-existente e não foi introduzida por este lote**:

1. **Ponto exato da falha:** ao executar `source/build/unit/Release/unit.exe "UpdateEquipment"` (tanto sem masters quanto com os cinco masters base montados em `OFF\Data`), o teste falha na **linha 9** (`partOne.CreateActor(0xff000ABC, { 1.f, 2.f, 3.f }, 180.f, 0x3c)`) — **antes** da linha 11 (`DoMessage(partOne, 0, jEquipment)`) — com `PluginIndexMapping: unknown regular runtime slot 0x0` (`PartOne.cpp:164` → `PluginIndexMapping.cpp:268`). Nenhuma linha de `027-werewolf-hands.patch` chega a ser executada.
2. **Integridade do código desde a base anterior a `5effbd8`:**
   - `git diff --stat 5effbd8..HEAD -- overlays/` confirma que `027-werewolf-hands.patch` é o único patch nativo adicionado ou alterado após `5effbd8`.
   - `git -C source diff` (contra a base `310cb59`) e `fc.exe /B` contra `C:\Code\Aetherius - OFICIAL\aetherius-server-main` confirmam que `PartOne.cpp`, `PartOne.h`, `WorldState.cpp`, `WorldState.h`, `PluginIndexMapping.cpp`, `PluginIndexMapping.h`, `FormDesc.cpp`, `FormDesc.h` e as linhas `1–42` de `PartOne_UpdateEquipmentTest.cpp` são **byte-idênticos** à base oficial anterior a `5effbd8`.
3. **Mesma falha em outros testes legados `[PartOne]` intocados:** executando o mesmo `unit.exe` nos casos legados `CreateActor/DestroyActor` (`PartOne_ActorTest.cpp:12`), `UpdateMovement` (`PartOne_MovementTest.cpp:116`) e `UpdateAppearance1` (`PartOne_UpdateLookTest.cpp:86`), todos os três falham exatamente com `PluginIndexMapping: unknown regular runtime slot 0x0` no primeiro `CreateActor(..., 0x3c)`.
4. **Precedente anterior ao lote (já em `5effbd8`):** o patch `overlays/sobrenatural/026-bat-dash-movement-guard.patch:174-175` (presente em `5effbd8`) já precisava inicializar manualmente `partOne.worldState.espmFiles = { "Skyrim.esm" }; partOne.worldState.pluginIndexMapping.Build(partOne.worldState.espmFiles);` em seu teste `[BatDash][PartOne]` para contornar essa mesma quebra de fixture herdada da base oficial.

*(Nota de transparência: como `source/` é uma working tree única sobre `310cb59` sem binário `unit.exe` de backup em `5effbd8` e a revisão é estritamente somente leitura no código, a constatação baseia-se na execução direta do `unit.exe` + comprovação byte-a-byte de que todo o código executado até a linha da falha é idêntico ao de `5effbd8` e da base oficial.)*

### 7.3 Verificações Executadas na Reconfirmação

Executadas diretamente em `C:\Code\Aetherius - Servidor Local`, sem editar código nem fazer deploy:

| Verificação | Resultado |
|---|---|
| `git apply --check --reverse overlays/sobrenatural/027-werewolf-hands.patch` sobre `source/` | exit `0` |
| `fc.exe /B` (`overlays/` vs `source/`) para `werewolf/client.js` e `werewolf.test.cjs` | Byte-idênticos |
| `node --check overlays/sobrenatural/files/server/gamemode/werewolf/client.js` | exit `0` |
| `node --test` (`teste-formas.cjs`, `teste-porte-voz.cjs`, `teste-transformacao-forcada.cjs`, `teste-aparencia.cjs`, `source/.../werewolf.test.cjs`) | **109/109** (`fail 0`, exit `0`) |
| `source/build/unit/Release/unit.exe "[WerewolfEquipment]"` (com os 5 masters base via junção temporária `OFF\Data` em `%TEMP%`) | **13/13 asserções em 1 caso** (`exit 0`) |
| `source/build/unit/Release/unit.exe "UpdateEquipment"` (sem e com masters) | Falha na linha 9 com `PluginIndexMapping: unknown regular runtime slot 0x0` (`exit 42`, pré-existente) |
| `source/build/unit/Release/unit.exe "UpdateAppearance1","CreateActor/DestroyActor","UpdateMovement"` | **3/3 falharam** com a mesma exceção `PluginIndexMapping: unknown regular runtime slot 0x0` (`exit 42`, pré-existente) |
| `fc.exe /B` (`aetherius-server-main` oficial vs `source/`) em `PartOne.cpp`, `PluginIndexMapping.cpp`, `FormDesc.cpp` | Byte-idênticos |
| SHA-256 de `source/build/dist/server/scam_native.node` | `23669DE235A7A3B480B013326B1638A013C5DAC6D265FA86D51131D7C77F9F2B` (bate com `33c0618`) |
