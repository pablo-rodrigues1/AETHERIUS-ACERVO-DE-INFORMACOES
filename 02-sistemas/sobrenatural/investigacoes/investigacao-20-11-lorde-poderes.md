# Investigação 20/11/2026 — Poderes vampíricos e Forma de Lorde

Laboratório `C:\Code\Aetherius - Servidor Local`, base `147e84f` e source `310cb59` com overlays. Primeiro candidato de servidor `58c9db0`, UI Core `307d39c`. Consultas houseCARL no perfil LOCAL, epoch `e2-abcf5a7fc38d70f4`. Acervo lido em `6d6136767585b21db2092097e9923f37a160d181`. Revisão cruzada pelo coordenador após o despacho.

## T10 e movimento do Lorde

No teste do dia 19, o efeito chegou sem comando de uso. A entrada agora também lê o evento do controle **Shout** do Skyrim e, para Morcego, tenta a transição do efeito somente se a potência correspondente está equipada na voz. O servidor confere nonce, personagem, posse, forma, política, recarga e sangue. O cliente dono recebe o avanço autorizado. O Morcego humano usa os eventos COTV `ShoutStart` e `ShoutSprintLongStart`. O Lorde usa `BatSprintStart` (`00BB8B:Dawnguard.esm`, via `PlayIdle`). O host rejeita o pacote de posição fora de 256 unidades na janela da guarda. O deslocamento físico dos dois grafos, a distância útil e a colisão precisam de teste em jogo.

## Som, Enxame e Força

A tabela lista os IDs SOUN locais do ESP. Descritores SNDR em `000850–00085A` usam as mesmas saídas radiais do marcador Dawnguard e **um WAV exato por fase**. Em houseCARL, todas as onze cópias vencedoras em LOCAL pertencem ao mod solto `AETHERIUS - Vampire Lord and Werewolf SFX`. O pacote deve garantir o mesmo fornecedor nas instalações de destino. O plugin não distribui duplicatas desses WAVs.

| Poder | SOUN | WAV em `sound/fx/dlc01/mag/` |
|---|---|---|
| Enxame, início | `000845` | `nightcloak/mag_night_cloak_bats_in_01.wav` |
| Enxame, loop | `000846` | `nightcloak/mag_night_cloak_bats_hit_01_lpm.wav` |
| Enxame, fim | `000847` | `nightcloak/mag_night_cloak_bats_out_01.wav` |
| Morcego humano/Lorde, início | `000848` | `powers/mag_powers_bats_01.wav` |
| Morcego humano/Lorde, fim | `000849` | `powers/mag_powers_batsfinish_01.wav` |
| Névoa humana/Lorde, início | `00084A` | `powers/mag_powers_mistform_in.wav` |
| Névoa humana/Lorde, fim | `00084B` | `powers/mag_powers_mistform_out.wav` |
| Celeridade, início | `00084C` | `powers/mag_powers_supernaturalreflexes_in_01.wav` |
| Celeridade, fim | `00084D` | `powers/mag_powers_supernaturalreflexes_out_01.wav` |
| Força, início | `00084E` | `powers/mag_alteration_telekinesis_vampire_throw.wav` |
| Força, fim | `00084F` | `powers/mag_alteration_telekinesis_vampire_grab.wav` |

`powers.js` publica início e fim para o dono e os observadores elegíveis (mesma célula/mundo, até 3.000 unidades). O loop segue a relevância, inclusive para observador tardio. A saída cancela o loop. O ESP remove os scripts de áudio antigos dos MGEFs correspondentes e as duas iniciações antigas do dash. O Enxame passa a 20 HP/s por 15 s e a janela de recarga continua em 60 s. Os CTDAs do dreno e as proteções do servidor são preservados. A fonte dos 40 s antigos era o override posterior da Etapa 2.

O SPEL `33EACA:Curse of the Vampire.esp` tem um modificador de 50 de vida no MGEF `16CD6F`. A força física está no PERK `3EFE24`: arma branca listada 1,25×, golpe sem palavra-chave de arma listada 2,5× em forma humana e 1,5× na forma de Lorde, arco excluído, bash 2×. `ActionListener::OnHit` usa `PartOne::CalculateDamage` e aplica a força no valor entregue a `CalculateCurrentHealthPercentage`. A fórmula carregada em `ScampServer.cpp` usa TES5 mais wrappers. O multiplicador ocorre na saída física comum antes de descontar HP. A janela é nativa, bounded a 40 s. Revogação, expiração, morte e troca de conexão encerram o estado.

## P10 e escala

`supernatural.lordGrant` e `supernatural.lordRevoke` usam `characters.class` e o protocolo F7 com motivo de 5–240 caracteres. `supernatural-service.js` persiste internamente `JogadorLordeVampiro` e reconcilia o poder `00283B:Dawnguard.esm` pelas origens. O sync remove a concessão automática antiga que pertence ao Sobrenatural. Um poder anterior de outra origem é preservado. O TAB recebe `lord.granted` mais estado e prazo. O botão aceita 10 min e inicia 2 h reais de recarga a partir do aceite, persistida. Z tem admissão independente. O painel, o botão, o Reverter Forma, a morte e a desconexão passam pela mesma sessão autoritativa.

O estado de Lorde incorpora escala 1,25 e chega aos dois callbacks de relevância. O callback aguarda a raça de Lorde e escala a raiz do esqueleto a partir da escala anterior. O encerramento restaura o valor capturado. A raça mortal é salva antes da mutação e restaurada com readback.

## Ampliação de 20/11 e limites de validação

O Q do cliente Aetherius abre `/inventory/favorites`. O servidor usa a lista transitória da forma na projeção de `favoritesSnapshot`, preservando tabela `gameplay_inventory_favorites` e atalhos. A API SkyrimPlatform disponível expõe `Game.isObjectFavorited` para consulta; o Q conectado via UI Core permite a projeção sem instalar uma extensão nativa do cliente.

| Slot no Q | SPEL vencedor |
|---|---|
| Conjure Gargoyle | `016909:Dawnguard.esm`, override COTV |
| Blood Ward | `473F48:Curse of the Vampire.esp` |
| Blood to Power | `1C2E83:Curse of the Vampire.esp` |
| Vampiric Grip | `33EAD6:Curse of the Vampire.esp` |
| Sanguine Rune | `55CA9A:Curse of the Vampire.esp` |
| Reverter, Morcego e Névoa | `00CD5C`, `0038B9`, `0038BA:Dawnguard.esm` |
| Força e Celeridade | `33EACA`, `33EADE:Curse of the Vampire.esp` |

O Dreno usa a variante inicial COTV `33EB04`, um míssil. O script de origem escolhe as cinco variantes pelo nível vanilla do jogador, não pelo estágio do Sobrenatural. O candidato usa o mesmo míssil em todos os estágios e projeta a mão direita apenas na forma. Perks projetadas pelo ledger existente conferem identidade e propriedade. O mapa exato de 15 FormIDs está em `supernatural/vampire-grants.js`. Habilidades `00085B–000860` condicionadas à raça aplicam a tabela de estágio e `000861` soma 50 a vida, magicka e vigor pelas três perks sobrenaturais. O host lê as habilidades de estágio e aplica os máximos e defesas na via de dano.

### Pendências técnicas

- Vampiric Grip é `Self` com arquétipo `GrabActor` (`007EBD:Dawnguard.esm`). `client/src/services/services/magicSyncService.ts:97–98` envia o próprio ator como alvo para entrega Self. O host não recebe o alvo agarrado e não replica nem valida o deslocamento de outro ator por essa magia.
- Blood Ward usa `WardPower` e `DamageResist` (`47179D` e `4717A0:COTV`). `ReadSpellHealth` resolve HP e os wrappers de dano não mantêm a barreira carregada por concentração. O evento `wardHit` existe no cliente, mas não é uma confirmação autoritativa de absorção.
- A imunidade de queda do COTV depende de `015FC8:Dawnguard.esm == 2`. O script de transformação original atualiza o global em `LiftoffStart/LevitateStart` e volta a 1 em `LandStart/GroundStart`. A troca autoritativa de raça não inicializa aquela quest. A habilidade de planar altera apenas a gravidade local pelo script COTV durante sprint. O candidato requer validação da transição de voo e implementação dedicada do sinal de levitação se a projeção de raça não atualizar o global.
- Blood to Power, Sanguine Rune e Conjure Gargoyle requerem prova de uso em jogo. A concessão, apresentação e propriedade do SPEL são independentes de provar seus efeitos físicos em sessão real.

## Artefatos

ESP anterior `C8B1320710873AD2F8203498990B24305A28D274D8D4D6C4BB2264FC15B4B06A`, após áudio `03095BEFD5737F60E2A010D9B26ABCBDBB52DBC3EE5EA284D51C1E47460523E8`, após as habilidades de Lorde `211F983A782EFE553F8DB9E225F50D2BC51EBD2339F8F22A2D6B2462326C74A7`. Nenhum runtime, perfil MO2 ou repositório oficial foi alterado.

### Validação executada

- Node 22, `AETHERIUS_VAMPIRE_FEED_ENABLED=false` e `true`, **523/523 em cada modo**. Scripts Serviço, F7 e revisão F7 OK.
- Catch2 Release, filtros `[VampireStrength],[BatDash],[VampireLordStats]`, **38 asserções em três casos**. O teste de status carrega o ESP candidato a partir de `AETHERIUS_LORD_TEST_ESP` com seus mestres e verifica máximos, armadura e resistência no cálculo do host. A biblioteca Catch2 de MSVC 14.51 precisou da CRT correspondente no link do executável de teste.
- `scripts/Build-Native.ps1 -AllowDependencyDownloads` completou e o addon carregou via `require` em Node. SHA-256 `7307F5AA8B29F7F2540950D9F0CF464774C542B841EAF416ACFB42B7C721DDEF`. TypeScript do servidor e do cliente, webpack do cliente OK. Bundle SHA-256 `FD61C198FAA52E1A821106DFBCCC23CCBFCCA95F3B9A63AFFD9F900AD2935709` (a alteração visual do TAB está em UI Core).
- Patches `030-vampire-powers-lord.patch`, `031-vampire-lord-loadout.patch` e `overlays/cliente-local/035-lord-tab.patch` no laboratório. Foram validados o source aplicado e o encadeamento nativo dos overlays.
