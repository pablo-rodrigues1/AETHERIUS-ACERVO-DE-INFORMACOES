---
titulo: Investigação — inventário e feitiços do SkyUI com autoridade do servidor
tipo: referencia
status: ativo
responsaveis: [mercurius17, pablo-rodrigues1]
atualizado: 2026-11-26
tags: [inventario, skyui, servidor, anti-duplicacao, desempenho]
relacionados: [README.md, diario.md, ../../03-decisoes/ADR-0037-ui-no-cef-do-skyrim-platform-sem-meridian-e-prisma.md]
---

# Investigação — inventário e feitiços do SkyUI com autoridade do servidor

## Resumo
É viável usar o InventoryMenu/MagicMenu/ContainerMenu do SkyUI (Scaleform, sem navegador) como **tela**, mantendo o servidor como **dono** dos itens. O SkyMP já é autoritativo: o servidor guarda o inventário de cada ator e reenvia o estado; o cliente só pede. O trabalho é vetar/reconciliar as mutações locais que os menus vanilla fazem sozinhos. Recomendação: fazer em fases, começando pelo **MagicMenu** (feitiços não duplicam) e pelo **InventoryMenu em modo leitura + equipar**, deixando baú/comércio/crafting na UI Core até a reconciliação estar provada.

## O que já existe (código atual)
- **Servidor dono do inventário:** o `MpActor` (C++, `scam_native.node`) tem o inventário; o cliente recebe a propriedade `inventory` e aplica (`remoteServer`/`worldView`). Toda alteração legítima passa pelo servidor (`inventory-service.js`, `skyinventory`, `container-ownership-service.js`, `durability-service.js`).
- **Cliente já observa mutações locais:** `AetheriusUiService` escuta `containerChanged`, `equip`/`unequip`; `DropItemService`, `CraftService`, `ContainersService` e `LastInvService` já mandam pedidos ao servidor e reverter o local quando recusado.
- **Bloqueios atuais:** a bridge fecha InventoryMenu/FavoritesMenu/MagicMenu/TweenMenu e abre a UI Core. Desligar esse redirecionamento é o que "devolve" o SkyUI.

## Como fica a autoridade com o SkyUI
| Ação no menu vanilla | O que o jogo faz localmente | Como o servidor manda |
|---|---|---|
| Ver lista / filtros / comparação | nada | já sincronizado pelo `inventory` do servidor |
| Equipar / desequipar | `ActorEquipManager` local | `equip` → pedido ao servidor; o servidor confirma ou o cliente desfaz (já existe para a UI) |
| Favoritar (Q) | `ExtraHotkey` local | sem risco; espelhar ao servidor como hoje (`MirrorFavorites` ao contrário) |
| Largar item | cria referência no mundo + tira do inventário | `DropItemService` já pede ao servidor; a referência local é apagada e o servidor cria a oficial |
| Consumir poção/comida | efeito + `RemoveItem` local | interceptar `containerChanged` (−1) e pedir `consume`; servidor aplica efeito via `aetheriusApplyPotion` (já existe) e reenvia o inventário |
| Baú / comércio / roubo | `ContainerMenu`/`BarterMenu` movem itens localmente | **maior risco de duplicação** (cliente cria o item antes do servidor responder). Manter a tela da UI Core (já autoritativa) até haver reconciliação com trava de transação |
| Crafting / alquimia / encantamento | cria item local | já vetado por `CraftService`/profissões; manter |
| Feitiços (MagicMenu) | só equipa feitiço | sem item; seguro. Aprender/esquecer feitiço já é do servidor |

**Regra de reconciliação (anti-dupe):** o inventário local nunca é fonte. A cada `containerChanged` no jogador que não veio de um pacote do servidor, o cliente (1) manda o pedido e (2) agenda o reaplique do último snapshot do servidor em ≤ 300 ms. Item criado localmente sem confirmação some; item removido sem confirmação volta. Duplicar exigiria o servidor aceitar duas vezes o mesmo pedido, e os serviços já usam chaves de idempotência/transação (`core/transaction-service`).

## Ganho de desempenho esperado
O inventário é a tela mais pesada da UI Core (lista grande, ícones, filtros). No SkyUI ela é Scaleform nativo: sem layout/paint do Chromium, sem `executeJavaScript` por pacote e sem projeção de nomes na bridge. Com a migração do ADR-0037 o CEF já é um só; tirar o inventário dele reduz o pico de CPU ao abrir o TAB/I.

## Riscos
- Itens com extras do Aetherius (refino `ExtraHealth`, encantamento do servidor, durabilidade) aparecem no SkyUI sem as informações próprias (tier, durabilidade). Precisaria de um patch de SkyUI (ActionScript) ou aceitar a perda.
- Peso/capacidade, sobrevivência e travas do SkyDeath (ferido/espírito) hoje são aplicados pela UI Core; no vanilla seria preciso fechar o menu pelo cliente nesses estados (já existe `SKYDEATH_MENU_LOCKS`).
- Skyrim Souls RE deixa menus sem pausa: o jogo continua e eventos chegam durante o menu — bom para a reconciliação, mas exige que o reaplique não feche o menu.

## Plano sugerido
1. Flag `aetherius-vanilla-menus` (cliente) para não redirecionar MagicMenu/InventoryMenu na bridge.
2. Reconciliação genérica no cliente (`containerChanged` local → pedido + reaplique do snapshot), com teste de duplicação (largar/pegar/consumir em rajada, perda de pacote, desconexão no meio).
3. Ligar MagicMenu; depois InventoryMenu; baú/comércio só após (2) passar nos testes de rajada.
