# Investigação de 21/11/2026 — Aparência vampírica por Reserva de Sangue

**Autor:** Sol High. **Entrega:** Fase 1, análise estática. A decisão do dev para a implementação de Fase 2 consta ao fim. Revisão independente pelo Gemini 4 Neon e validação em jogo pendentes.

## Bases e perfis

- DVA 1.4, Nexus 96817, Aipex8 e TaikoDragon. Instalação lida em `C:\modOrganizer\mods\Dynamic Vampire Appearance`. O `meta.ini` identifica o arquivo de instalação como a versão 1.4. [Página do mod](https://www.nexusmods.com/skyrimspecialedition/mods/96817).
- ESP `DVA - Dynamic Vampire Appearance.esp`, SHA-256 `C973ADEA4884CAA55C3EB9683F04D0B356A5B57B92F2E095EA033A34E3E5C715`. BSA homônimo, SHA-256 `2206993A595ED2BF059725C5B78EFA7975F9728126DBFBA967E8F864A07B271B`. Fonte `Source\Scripts\dva1_controller.psc`, SHA-256 `23334253DFD43DEBA099AF2C3BB7F227D24B8B992B1DD0D54830621E537562E8`.
- houseCARL `2.0.0+5e3c744862bc17c85ebe850830603a9a3e6f0779`, epoch `e2-e74e107473c7aecd`, perfil ativo **AETHERIUS - GRAFICO - QUALIDADE**. Consultas `load_order_status`, `records` (inventário, campos, referências e delta), `bsa_list` e `asset_status`.
- Laboratório `C:\Code\Aetherius - Servidor Local`, overlay na base `81966c377a8a18b7e968ae8e840aee7b55d66335`, checkout `source` em `79ce921793329bf98ef54776f23efe80e4d716b1` e `client-source` em `8cfa5534865257fd71345f14aacc2f9d1ab4035e`. Os arquivos JS de serviço, alimentação, poderes, Reserva e olhos lidos em `source` coincidem byte a byte com `overlays\sobrenatural\files`. `AppearanceBinding.cpp` e `PartOne.cpp` já continham alterações de terceiros. A serialização e a replicação de aparência foram conferidas na base e no checkout.
- Acervo em `f94a1a570fadae35695dab6fcd92d49d268e6e63`, com leitura do índice, Sobrenatural, Lobisomem, UI Core, Painel Administrativo, Gameplay Core e ADRs relacionados. Consulta em leitura.

| Perfil em `C:\modOrganizer\profiles` | Pasta do mod em `modlist.txt` | ESP em `plugins.txt` e `loadorder.txt` |
|---|---|---|
| `AETHERIUS - GRAFICO - QUALIDADE` | `+Dynamic Vampire Appearance`, linha 3 | Marcado na linha 428 e ordenado na linha 439. No houseCARL, namespace ESL `FE137` desta ordem |
| `AETHERIUS - LOCAL` | Entrada ausente | Entrada ausente nos dois arquivos |
| `AETHERIUS - PERFORMANCE` | Entrada ausente | Entrada ausente nos dois arquivos |

O resultado de `load_order_status(profile=..., filter=...)` nesta versão repetiu o resultado do filtro da ordem ativa em todos os perfis. O cabeçalho informou os totais do perfil solicitado. A tabela usa a leitura direta dos três arquivos de cada perfil para o estado específico do DVA.

## Catálogo dos recursos

O ESP tem **54 records próprios** (36 TXST, 12 HDPT, duas FLST, duas QUST e dois GLOB) e dois overrides de MGEF. Os IDs abaixo têm seis dígitos locais e pertencem a `DVA - Dynamic Vampire Appearance.esp`. O servidor deve resolver pelo arquivo de origem, ID local, tipo e EDID. O índice `FE137` é específico do perfil consultado.

### Olhos

`DVA_Eyes_Female` (`000831`) e `DVA_Eyes_Male` (`000832`) são as FLST vinculadas ao controlador. Todos os HDPT são do tipo `Eyes`, `Playable`, têm flag do próprio sexo, zero `ExtraParts` e `ValidRaces = 107B99:Skyrim.esm` (`HeadPartsHumanoidVampire`). Os EDIDs têm o padrão `FemaleEyesVampireDVA0N`, `MaleEyesVampireDVA0N` e `EyesVampireDVA0N` para o TXST, com `N = índice + 1`.

| Uso | Índice | HDPT feminino | HDPT masculino | TXST | Malha por sexo |
|---|---:|---|---|---|---|
| Stage 1 | 0 | `00080E` | `000814` | `000800`, Day1 | `eyes{female,male}vampire1.nif` |
| Stage 2 | 1 | `00080F` | `000815` | `000801`, Day2 | mesma malha |
| Stage 3 | 2 | `000810` | `000816` | `000802`, Day3 | mesma malha |
| Stage 4 | 3 | `000811` | `000817` | `000803`, Day4 | mesma malha |
| Combate | 4 | `000812` | `000818` | `000804`, Glow1 | `eyes{female,male}vampire2.nif` |
| Visão noturna | 5 | `000813` | `000819` | `000805`, Glow2 | `eyes{female,male}vampire3.nif` |

As malhas ficam em `meshes\actors\character\DVA\`, e as difusas em `textures\actors\character\DVA\Eyes\EyeVampire_01{Day1..4,Glow1..2}.dds`. Os seis TXST também referenciam `EyeBrown_n.dds` e `EyeEnvironmentMask_M.dds` sob `textures\actors\character\Eyes\`.

### Rosto e sangue na boca

Os seis arrays `CX_*` estão em `DVA_ControlQuest`, `VirtualMachineAdapter.Aliases[0].Scripts[0].Properties`. A leitura dos links retornou a ordem abaixo. O último elemento é um **TextureSet de rosto completo** com sangue, sem HDPT de boca dedicado, shader, RFCT ou efeito mágico para a boca.

| Propriedade | Stage 1 | Stage 2 | Stage 3 | Stage 4 | Boca |
|---|---|---|---|---|---|
| `CX_Female_Human` | `000806` | `000807` | `000808` | `000809` | `00082C` |
| `CX_Male_Human` | `00080A` | `00080B` | `00080C` | `00080D` | `00082F` |
| `CX_Female_Elf` | `00081A` | `00081B` | `00081C` | `00081D` | `00082A` |
| `CX_Male_Elf` | `000822` | `000823` | `000824` | `000825` | `00082D` |
| `CX_Female_Orc` | `00081E` | `00081F` | `000820` | `000821` | `00082B` |
| `CX_Male_Orc` | `000826` | `000827` | `000828` | `000829` | `00082E` |

Os EDIDs são `SkinHead{Female,Male}{Elf,Orc ou vazio}VampireDVA0{1..5}`. Todos os TXST de rosto têm `FaceGenTextures` e `HasModelSpaceNormalMap`. As difusas `{Female,Male}HeadVampire.dds` e os mapas `Head_sk.dds` / `Head_S.dds` mantêm os caminhos de rosto do jogo. O normal usa `DVA\Normal\VampireNormal{F,M}{Elf,Orc ou vazio}{1..4}.dds`. O campo `Height` do Mutagen referencia `DVA\Detail\VampireDetail{F,M}{1..5}.dds`. O TXST de boca usa o normal de **Stage 1** de sua família e o detalhe `5` do sexo. Reutilizar o recurso fielmente significa que, durante o sangue, o rosto mostra essa variante e os olhos continuam a seguir suas próprias prioridades.

O BSA é v105 e tem 51 entradas. São seis NIF, seis DDS de olho, 24 DDS normais de rosto, dez DDS de detalhe, uma imagem de MCM e quatro PEX/PSC do controlador e do MCM. Os **46 arquivos visuais de cabeça** sob `DVA\` são fornecidos pelo BSA no perfil ativo, sem concorrentes na consulta VFS. As seis referências comuns de rosto são fornecidas pelos dois mods **Tempered Skins** no perfil gráfico. Para os dois mapas comuns dos olhos, a varredura avisou que a lista de BSAs base de `Skyrim.ini` não foi descoberta. A ausência indicada na varredura não confirma falta desses mapas no jogo.

### Raças e limites

| Família | Raças em `Skyrim.esm` (base / vampírica) |
|---|---|
| Humana | Bretão `013741/08883C`, Imperial `013744/088844`, Nord `013746/088794`, Redguard `013748/088846` |
| Elfa | Dunmer `013742/08883D`, Altmer `013743/088840`, Bosmer `013749/088884` |
| Orc | `013747/0A82B9` |

O controlador cobre essas oito raças jogáveis em ambos os sexos. A FLST válida para olhos inclui também `0A82BA` (Elder vampiro), que fica fora dos arrays de rosto e do mapa racial jogável do Sobrenatural. Argonianos (`013740/08883A`) e Khajiit (`013745/088845`) têm raça vampírica no Sobrenatural e ficam fora do catálogo do DVA. Não há variante verificada para encaixar nos olhos, UV e focinho dessas raças. A forma de Lorde tampouco usa cabeça humanoide do DVA.

Proposta de fallback seguindo a Bruxa: buscar a alternativa mais próxima **dentro da família e sexo compatíveis**. Se um olho especial faltar, usar o outro modo especial somente se também estiver ativo, depois o olho de Stage. Se um Stage faltar, escolher o Stage mais próximo da mesma família, com preferência pelo menos intenso no empate. Se a boca faltar, usar o rosto do Stage. Sem candidato compatível, preservar a aparência existente da raça. Para Khajiit/Argoniano, isso conserva o visual vampírico racial atual. Cobertura DVA nessas raças requer recursos próprios e ensaio em jogo.

## Comportamento original do DVA

A fonte `C:\modOrganizer\mods\Dynamic Vampire Appearance\Source\Scripts\dva1_controller.psc` estende `ReferenceAlias`. Em `OnUpdate` (linhas 396–525), lê `PlayerVampireQuest.VampireStatus` e `LastFeedTime`, `IsInCombat` e efeitos de night vision. Ao entrar no vampirismo, guarda o olho e o TextureSet existentes (452–457). Usa índices de Stage 0–3 e os especiais 4/5 (23–44, 730–747). `UpdatePlayerEyes` recusa raças fora dos arrays (599–606) e usa `ChangeHeadPart` (645–650). `UpdatePlayerComplexion` aplica `ActorBase.SetFaceTextureSet` (661–688), seguido de `QueueNiNodeUpdate` (510–513). Esses métodos, `FormList.GetAt` e `Game.GetRealHoursPassed` foram conferidos na referência Papyrus.

O DVA dá prioridade a **visão noturna > combate > Stage** (613–636). O pedido propõe **combate > visão > Stage** e troca o combate vanilla pelos sinais autoritativos Feral, Força e Celeridade. A ordem proposta é a opção recomendada para Aetherius.

Ao detectar mudança de `LastFeedTime`, o DVA guarda `mouthFxStart = Game.GetRealHoursPassed()` (459–466). Encerra quando `(Game.GetRealHoursPassed() - mouthFxStart) * 60 * 60 > 31` (468–475), observado no próximo update. O comentário ao lado diz “about 20 mins”. A referência Papyrus tem uma descrição de unidade inconsistente com o nome da função. O valor verificável é a fórmula do script. A proposta para o servidor é **31 segundos reais** após a alimentação concluída, com limpeza ao atingir o prazo, morrer ou perder a maldição. Nova alimentação concluída reinicia o prazo. Um observador tardio recebe a boca pelo saldo de tempo vigente. Essa duração segue a leitura em horas do cálculo, com prazo explícito do servidor.

## Transporte e integração no SkyMP

1. **O protocolo já tem os dois campos.** `C:\Code\Aetherius - Servidor Local\source\server\cpp\server_guest_lib\Appearance.h:43–76` serializa `headpartIds` e `headTextureSetId` como FormIDs de runtime. `MpActor.cpp:247–254` grava o snapshot na change form. `server\cpp\addon\property_bindings\AppearanceBinding.cpp` converte `mp.set(id, 'appearance', value)` em `UpdateAppearanceMessage` para os listeners. `server\cpp\server_guest_lib\PartOne.cpp:792–803` copia o snapshot para `CreateActorMessage` ao entrar no alcance. Mudança de wire format ou addon dispensada pela implementação conhecida.
2. **O cliente aplica o TXST em ambos os caminhos.** `C:\Code\Aetherius - Servidor Local\client-source\client\src\sync\appearance.ts:127–173,251–283` resolve `TextureSet.from(Game.getFormEx(headTextureSetId))`, chama `npc.setFaceTextureSet(...)` e atualiza o nó no dono. O remoto usa `applyAppearance` na base gerada. `remoteServer.ts:805–837,915–945` encaminha o snapshot no load inicial e recebe mudanças. Os tints, morfos e presets do personagem podem ser mantidos. O fallback `headTextureSetId = 0` já é aceito por esse caminho.
3. **Há uma correção TypeScript obrigatória para os olhos.** `appearance.ts:133–144` hoje remove qualquer HDPT de olhos em uma das dez raças vampíricas e injeta Dawnguard/Skyrim, tanto no dono quanto nos observadores. A correção proposta reconhece o olho visual autorizado pelo record conhecido do pacote (plugin + ID local e sexo compatível) e preserva os outros headparts. Conserva a seleção racial atual quando o snapshot não trouxer um olho DVA verificável. O cliente resolve assets para desenhar o snapshot do servidor. O gameplay e a elegibilidade continuam no servidor.
4. **Validar estabilidade das cópias remotas.** `client\src\view\formView.ts:83–116,148–169,267–289,696–700` hoje invalida e recria a base e o ator remoto em mudanças de aparência além do nome. A primeira implementação deve exercitar esse caminho com dois jogadores e entrada tardia. Se uma troca em combate ou alimentação reiniciar animação, FX ou alvo, tratar o delta só de olho/TXST no cliente usando a base dinâmica exclusiva do ator, com a mesma contagem de partes e refresh do 3D. Preservar a recriação existente para mudança de raça, sexo ou estrutura. A inspeção prova o transporte e a chamada para aplicar o TXST, com composição da malha/FaceGen em jogo ainda por validar.
5. **IDs leves e distribuição.** O DVA é ESPFE. `source\libespm\src\Combiner.cpp:86–89` lê a flag e `server\cpp\addon\ScampServer.cpp:1633–1651` converte entre descritor e FormID pelo mapeamento de plugins. O resolvedor de `supernatural\form-resolver.js` já confere EDIDs. Preferir resolução por descritor e checagem de tipo/EDID; guardar descritores no registro de autoria, incluindo zero como estado original válido de TXST. As duas aparências carregam números de 32 bits e `Game.getFormEx` os interpreta no load order local. Antes de ativar o recurso é preciso confirmar a mesma identidade e índice de runtime do plugin em host e clientes de cada perfil suportado.

### Projeção autoritativa proposta

- Em `source\server\gamemode\supernatural\blood-reserve.js:11,165–170`, `peek` devolve saldo contínuo e o estado existente `FERAL` (zero). Para cobrir os valores fracionários, propõe-se Stage 1 em saldo `>=70`, Stage 2 em `>=50` e `<70`, Stage 3 em `>30` e `<50`, Stage 4 em `<=30`. Usar a Reserva e seus limiares, independentemente do rank por XP e do arredondamento visual do Cálice.
- A fonte de poder é o estado em `supernatural\powers.js:20–25,351–400,458–479`, com `vision`, `strength`, `celerity` e respectivos prazos. Reconciliar depois do commit do custo em `power-input.js:118–136,262–269` e ao concluir/cancelar. A Visão atual segue com seu Aura Whisper privado. A mudança do HDPT é pública e segue o mesmo tempo ativo. O combate visual entra em Feral, Força ou Celeridade e sai quando todos os três terminam.
- Na árvore atual, `supernatural\feeding.js:217–235` grava `CLOSED` antes do commit de sangue. Usar somente o sucesso do crédito autoritativo depois de validar personagem e concessão. `SETTLED` é terminal legado e `CLOSED` isolado não prova sucesso. Um cancelamento, recuperação de journal ou Poção de Sangue não inicia a boca.
- Unificar a precedência de olhos com `supernatural\veil-visuals.js:25–33` e `veil-eyes.js:76–129`. Guardar a base original e a autoria por personagem antes da primeira escrita, restaurar o que o sistema substituiu e manter a base ao mudar entre Vampiro, Bruxa e Lobisomem. Registrar também a variante anterior durante uma atualização para reconciliar interrupção entre journal e snapshot. Guardar o prazo de boca por concessão enquanto o snapshot persistido carrega o TXST de boca. Reconciliar prazo, raça, sexo e concessão ao ativar personagem, após edição autorizada e na volta da Fera/Lorde. Enquanto o editor ou uma forma de criatura estiver ativo, transferir a restauração para a projeção humanoide e preservar os slots das formas.
- O fluxo de `supernatural-service.js:115–164` muda e restaura a raça vampírica e em seguida publica o estado. A projeção visual deve usar esse fluxo e os estados existentes de Reserva/poder. Amostrar a Reserva em sua cadência existente para detectar limites fracionários. Só escrever `appearance` quando o par de olho/TXST mudar ou houver restauração/reconciliação necessária. `headpartIds` e `headTextureSetId` bastam no tráfego, sem expor as keywords de servidor.
- Na edição de rosto, o servidor só aceita `UpdateAppearance` enquanto o RaceMenu está aberto e emite `onUpdateAppearanceAttempt(actorId, appearance, isAllowed)` depois de persistir a alteração aceita (`source\server\cpp\server_guest_lib\ActionListener.cpp:416–453`, `gamemode_events\UpdateAppearanceAttemptEvent.cpp:14–39`). Usar a edição autorizada para reconciliar a base. No cliente, adiar reaplicação cosmética com o menu aberto e testar a proteção da lista de headparts de `appearance.ts:176–194` durante a reedição F7. O sinal de menu aberto aparece hoje no snapshot inicial e no controle de menu. A camada JS não tem um getter de propriedade nativo para ele nessa base.

## Isolar os recursos do mod

`000830` (`DVA_ControlQuest`) e `000834` (`DVA_ConfigQuest`) têm `StartGameEnabled, RunOnce`. A primeira ancora `DVA1_Controller` no alias de `PlayerRef`. A segunda tem `DVA_ConfigMenu` e `SKI_PlayerLoadGameAlias`. `DVA_Enabled` (`000835`) vale 1 no ESP, e `OnInit` pode reabilitar o controlador.

O DVA também é o vencedor no perfil gráfico para `00283C:Dawnguard.esm` (`DLC1VampireChangeEffect`) e `00CD5B:Dawnguard.esm` (`DLC1RevertEffect`). O delta contra o master mostrou `dva_debug` anexado nos dois efeitos, além de diferenças em condições e dados do revert. A fonte solta `dva_debug.psc` alterna opções de aparência durante essas mudanças de forma. Os dois PEX no BSA têm versões soltas vencedoras, e o PEX de debug está só solto. Colocar `DVA_Enabled = 0` ou desligar só a quest de controle deixa outros pontos de execução e overrides do Lorde.

| Opção | Impacto |
|---|---|
| **A. Pacote visual dedicado, recomendado** | Gerar fora do MO2 um ESPFE com 36 TXST, 12 HDPT, as duas FLST se forem consultadas por configuração e somente os links de assets necessários. Gerar BSA homônimo com os 46 arquivos visuais a partir da instalação do DVA. O original fica inativo no perfil em que o pacote visual entra. A troca/autorização de instalação vale para o LOCAL e para os perfis clientes usados no teste. Se o ESP mudar de nome, registrar o mapa de novos descritores. Conferir regras de distribuição antes de qualquer publicação de assets. |
| **B. Cópia filtrada com o nome e IDs originais** | Gerar fora do MO2 um ESPFE de substituição com os mesmos records visuais e arquivar só os assets. Remover, na cópia, os dois overrides de Dawnguard, os dois QUST e os dois GLOB. Preserva descritores e depende de garantir que a cópia filtrada é a vencedora e que nunca coexistam duas distribuições com o mesmo nome. A limpeza de sessões antigas que já usaram as quests DVA precisaria ser verificada. |
| **C. Adiar a integração visual** | Preservar o visual vampírico atual até o pacote de recursos ser autorizado. O perfil LOCAL atual não consegue resolver os FormIDs ou carregar os recursos. |

Para A ou B, auditar vínculos e ausência de QUST/VMAD de execução, de MGEF sobrescrito, PEX e MCM no pacote efetivo. Confirmar wins no VFS e que os MGEF do Lorde voltaram ao provider esperado na ordem de cada perfil. O teste deve cobrir jogador macho/fêmea humano, elfo e orc; fallback Khajiit/Argoniano; dono, vizinho e terceiro que entra no alcance; perda da maldição e retorno do Lorde. Repetir os limites 70/69, 50/49, 31/30 e zero; cruzar Visão com Feral/Força/Celeridade; testar alimentação confirmada e interrompida, 31 s de boca e relog antes/depois desse prazo. Antes do jogo, rodar Node por modo de alimentação, a regressão Reserva/Cálice, Lorde, olhos da Bruxa e Lua de Masser, TypeScript e o reverse-check dos patches de overlay.

## Decisão do dev em 21/11

- **Alternativa A.** Gerar fora do MO2 `AETHERIUS - DVA Visuals.esp` (ESPFE) e BSA homônimo, exclusivamente visuais. A instalação nos perfis ou desativação do DVA original requer nova autorização expressa.
- **Boca por 180 segundos reais**, configuráveis em `AETHERIUS_VAMPIRE_BLOODY_MOUTH_SECONDS`, depois de alimentação de jogador com crédito confirmado. Nova alimentação renova o prazo. Esse valor substitui a proposta de 31 s da investigação.
- **Combate (Feral, Força ou Celeridade) > Visão Noturna > Stage.** Argonianos e Khajiit conservam a aparência racial vampírica, sem variante DVA.

O candidato, os commits, o pacote e o roteiro atualizado constam na [Etapa 3 de aparência vampírica](../ETAPA-3_aparencia-vampiro.md).
