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


## 20/11 — Garra, Ala de Sangue, Asas e trilha da transformação

Base auditada `9ce6237` no laboratório e `307d39c` no cliente. A resposta aos seis achados fica em `7658200` e `03a142e`. As mudanças seguintes são os patches `033-lord-concentration.patch` e `overlays/cliente-local/037-lord-concentration-flight.patch`. O servidor trabalha em `source/server/cpp/server_guest_lib/ActionListener.cpp` e `PartOne.cpp`. O cliente usa `client-source/client/src/services/services/{magicSyncService,hitService}.ts` e `src/sobrenatural/lordFlight.ts`. Sons em `overlays/sobrenatural/files/server/gamemode/vampire-lord-service.js`, espelhado no source.

### Garra Vampírica

HouseCARL no perfil LOCAL, epoch `e2-abcf5a7fc38d70f4`: SPEL `33EAD6:COTV` é Concentration/Self e combina o `GrabActor` `007EBD:Dawnguard.esm` com o manto `00E7D7`. O manto carrega `00E7DA:Dawnguard.esm` a 5 HP por segundo sobre o alvo com `Grabbed == 1`. A entrega Self anterior encaminhava o conjurador como alvo da rede. O ESP do overlay conserva o SPEL conhecido e sua arte/sons de conjuração, com o MGEF visual `000862` de arquétipo Script sem VMAD. O arquétipo GrabActor e o manto saem dessa spell para evitar aplicar a física e o dreno nativos em avatares PvP.

O cliente envia a referência da mira quando o próprio Lorde tem LOS. O servidor só aceita a própria sessão de Lorde, referência carregada e visível para o conjurador, na mesma célula/mundo, no hemisfério frontal, até 600 unidades e com desnível de até 96. NPC vivo, único por concentração, elegível para as políticas de dano. Personagem de jogador online ou offline é excluído. A cada 250 ms o host pode aproximar até 80 unidades no plano, parando a 112 unidades horizontais. O host rejeita movimento concorrente sobre o NPC agarrado e envia a posição indexada para os vizinhos. Drena 5 HP/s dentro de 160 unidades e credita somente a vida realmente removida. A referência não muda durante a concentração. A matemática do passo mantém o eixo Z e usa a LOS local de entrada. O host não tem a malha de colisão do Skyrim. Colisão, pontes e reação de IA precisam de teste em jogo.

### Ala de Sangue

HouseCARL: `473F48:COTV` Self/Concentration, `47179D` AccumulateMagnitude em WardPower com magnitude 100, `4717A0` em DamageResist, arte de conjuração `47179F` e impacto `4717A3`. A apresentação original fica no cliente e nos observadores próximos, pelo replay de spell Self. Caso o motor emita WardHit em vez de Hit, o cliente autenticado como dono do agressor ou host do NPC encaminha o impacto pela via normal, com deduplicação de fonte/alvo. O resultado local Friendly/Absorbed/Broken não define a vida no host.

O host reduz até 100 pontos do dano mágico de SPEL, SCRL ou ENCH que chega a OnSpellHit, uma vez mesmo quando chegam dois slots. A aproximação usa a magnitude do record durante toda a concentração. A rampa e o colapso cosmético da barreira no motor não alteram esse valor. O caminho físico e a atualização normal de vida continuam com seus cálculos próprios. O lease das duas magias dura 750 ms e a amostra de concentração do cliente chega a cada 250 ms enquanto a mão continua conjurando a magia. A renovação não abre cast novo. O host confere identidade, fase, equipamento, célula, vida e travas. Soltar cancela, e a sessão desvinculada ou expirada fecha com interrupção para observadores. O cliente continua observando a duração física da concentração, inclusive o custo de magicka do motor. O host protege o dano a partir desse contrato e não emula o consumo de magicka continuamente.

### Asas da Noite e quedas

O `0FFAC0:COTV` condiciona as Asas à perk `025B1A` e `IsSprinting`. A gravidade é `025B1E:COTV = 0,005`, com baseline do mod `025B1D = 1,35`. O ESP do overlay retira o VMAD de `023375:COTV`, deixando um único autor da mudança de gravidade. O SkyrimPlatform do cliente ativa `blockPapyrusEvents(true)`. Em `FridaHooks.cpp:43–68`, os eventos são filtrados, o que inviabiliza depender do OnEffectStart/Finish do COTV para este efeito.

`Skyrim - Animations.bsa` contém `meshes/actors/vampirelord/behaviors/vampirelord.hkx` com o sinal `isLevitating` e eventos `LevitateStart` e `LandStart`. A projeção verifica a fase autoritativa, a raça real e a vida do dono. Em terra escreve 1 em `015FC8:Dawnguard.esm`; em levitação escreve 2. O perk existente `33EB18:COTV` aplica `ModFallingDamage × 0` só com valor 2. A corrida usa a configuração temporária de gravidade independente da imunidade. Ela é restaurada para o valor capturado ao sair do sprint ou da forma. Na saída, morte, carregamento ou desconexão o dono também limpa o global. A queda original é resolvida antes de `ActionListener::OnChangeValues`, que recebe apenas a porcentagem de HP. O host projeta a concessão e recebe a vida resultante. Assim as outras fontes de vida e dano não são confundidas com queda e a proteção tem um único cálculo.

### Sons da animação

HouseCARL confirma vencedores soltos no mod `AETHERIUS - Vampire Lord and Werewolf SFX`. Os três SNDR vanilla do Dawnguard apontam para os caminhos abaixo. A busca de referências em SOUN não encontrou marcadores para eles. O candidato silencia os três descritores de animação pelos respectivos overrides sem ANAM, duplica seus parâmetros de saída nos três SNDR de reprodução com amostra explícita e dá a cada um um marcador SOUN.

| WAV em `sound/fx/dlc01/npc/vampirelord/` | SNDR vanilla | SOUN e SNDR do overlay | Janela |
|---|---|---|---|
| `npc_vampirelord_transformation_01_2d.wav` | `005050:Dawnguard.esm` | `000863` / `000866` | 0–1.761 ms |
| `npc_vampirelord_transformation_02_2d.wav` | `005051:Dawnguard.esm` | `000864` / `000867` | 1.761–7.265 ms |
| `npc_vampirelord_transformation_02_3d.wav` | `005052:Dawnguard.esm` | `000865` / `000868` | 1.761–5.264 ms |

A primeira faixa abre a sequência, com as duas faixas 02 em paralelo a partir do final dela. As janelas são arredondadas para cima a partir dos frames dos WAVs vencedores. Todos os caminhos de `vampire-lord-service.start`, incluindo TAB, voz e confirmação de raça legada, abrem a mesma sequência. O servidor publica por propriedade separada em janela de relevância de até 3.000 unidades, mesma célula/mundo. O cliente toca cada marcador no máximo uma vez por sequência. Quem entra durante um som ouve as faixas ainda ativas pelo tempo restante. `Sound.play` reinicia a faixa em zero para essa entrada tardia e o cliente corta no limite da faixa original. Os WAVs continuam distribuídos pelo mod de sons. A escuta real, percepção de volume e ponto de entrada da pose dependem do teste em jogo.

### Validação do complemento

- Node 22.14.0, **528/528** com alimentação desligada e **528/528** ligada. Serviço, F7 e revisão F7 passaram. A prévia do TAB passou 13 verificações.
- Catch2 Release, **71 asserções em quatro casos**, filtros `[VampireStrength],[VampireLordStats],[VampireLordConcentration],[BatDash]`. Verifica bash sem evidência, máxima base na mensagem inicial, imunidade do jogador à Garra, alcance, remoção de movimento concorrente de NPC, 5 HP ao se aproximar, liberação, absorção de 100 e trilhas não mágicas. O teste com o ESP e seus mestres acusou a primeira comparação incorreta de índice de plugin com byte de runtime. A versão final resolve a origem de FormID por `PluginIndexMapping::GetEspmFileIndex`, inclusive com ESLs intercalados. O link usou a CRT MSVC 14.51 para compatibilidade com Catch2.
- TypeScript do servidor e do cliente e webpack concluídos. `Build-Native.ps1 -AllowDependencyDownloads` e carregamento do addon via Node concluídos. `git apply --reverse --check` validou os patches contra os sources aplicados. Arquivo ESP lido com os mestres pelo teste e verificação estrutural dos 12 records.
- Cliente `8e4b758` (bundle `748348AEA7A90D1DE17B880EDC98E52FCEAAE3D4758A1E47FCB6605C1772C00A`), addon `7934EC9BD9FEA76B3E9DCA655034196586D5CDDAF2BF0AEC7B7FDA8BEDAC57FD`, ESP `7A1397FE2B1DE84016E9CE688C3F06DFC19E9F9143C144806FDE1972DF2CE110`. Sem instalação no MO2, execução do servidor ou integração oficial. Revisão independente e homologação em jogo pendentes.
