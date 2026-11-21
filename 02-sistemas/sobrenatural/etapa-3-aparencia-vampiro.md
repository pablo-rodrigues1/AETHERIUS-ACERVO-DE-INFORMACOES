# Etapa 3 — Aparência vampírica por Reserva de Sangue

Entrega de 21/11/2026, Sol High. Gemini 4 Neon aprovou sem achados em `C:\Code\Aetherius - New Systems\Sobrenatural\docs\REVISAO-APARENCIA-VAMPIRO_neon.md`. A instalação autorizada foi verificada nos perfis LOCAL e GRÁFICO – QUALIDADE. O ensaio em Skyrim segue pendente. [Investigação](investigacoes/INVESTIGACAO_21-11_aparencia-vampiro.md), [instalação](#instalação-2111).

## Regras aprovadas

| Reserva autoritativa | Rosto e olhos de Stage |
|---|---|
| `>= 70` | Stage 1 |
| `>= 50` e `< 70` | Stage 2 |
| `> 30` e `< 50` | Stage 3 |
| `<= 30` | Stage 4 |

Os limiares operam sobre o saldo contínuo. Em zero, o rosto permanece no Stage 4 e Feral vivo usa olhos de combate. Força Imensurável (`COTV_ImmeasurableStrength`) e Celeridade Sobrenatural (`COTV_UnnaturalCelerity`) também ativam os olhos de combate. A prioridade é **combate > Visão Vampírica > Stage**. A Visão mantém o Aura Whisper existente no dono e mostra a malha Glow2 nos olhos para os observadores.

Beber Sangue de um jogador aplica o TXST de boca por **180 segundos reais** após crédito confirmado. `AETHERIUS_VAMPIRE_BLOODY_MOUTH_SECONDS` aceita 1 a 86400 segundos, com padrão de 180 se estiver ausente ou inválida. Nova alimentação confirmada renova o prazo. Morte, abatimento e perda ou troca da concessão o encerram. A saída usa o prazo absoluto e é reconciliada no tick de aproximadamente 1 s ou ao carregar o personagem. O TXST ensanguentado usa o normal de Stage 1 e o detalhe de boca do DVA. Ao sair, o rosto volta ao Stage do saldo vigente. A Poção de Sangue só muda a Reserva.

Há variantes femininas e masculinas para Bretão, Imperial, Nord, Redguard, Dunmer, Altmer, Bosmer e Orc. Na ausência de um visual especial, tenta-se o outro especial ativo e depois o Stage mais próximo dentro do mesmo sexo e família, com preferência pelo Stage menos intenso em empates. Argonianos e Khajiit mantêm o visual vampírico racial. Formas de Lorde e Fera usam suas próprias aparências.

## Commits e patches locais

| Repositório | Commit | Conteúdo |
|---|---|---|
| `C:\Code\Aetherius - Servidor Local\source` | `e8a4535162712ed87f6d713db22c1ebb7aec1d76` | Registra quatro módulos de gamemode que já existiam localmente, pela cópia anterior do overlay `4e071b3` |
| Mesmo repositório | `9f01499a20f52661274891b387085eb4819a8670` | Aparência autoritativa, persistência, Reserva, poderes e alimentação |
| `C:\Code\Aetherius - Servidor Local\client-source` | `3c80c2c79831e4df7b63091e75523362a1c4ac0f` | Rosto e olhos no dono, vizinho e entrada tardia |

Na raiz do laboratório, o patch `overlays\sobrenatural\037-vampire-appearance.patch` aplica sobre a base `e8a4535`. Seu SHA-256 é `0D28149F763634B8D8B8FAC8F5068A99066474F403DA8BCAC4CAB16933AE5FEA`. Os sete módulos em `overlays\sobrenatural\files\server\gamemode` correspondem byte a byte aos do candidato. O patch `overlays\cliente-local\042-vampire-appearance.patch` aplica sobre o cliente `8cfa5534865257fd71345f14aacc2f9d1ab4035e`. Seu SHA-256 é `8DCFA815026082B5E90F3AB6DB855966F8DD0B820BB61A029BA720BB41AC4BFA`. Ambos passaram no check de aplicação sobre índices temporários e no reverse-check. Os commits e patches excluem as alterações preexistentes em `source\server\cpp\addon\property_bindings\`.

## Projeção e restauração

- `source\server\gamemode\supernatural\veil-visuals.js` lê o saldo de `blood-reserve.peek` e os poderes autorizados. `vampire-appearance.js` contém os limiares e o catálogo. Se o saldo está temporariamente indisponível para um vampiro vivo, conserva-se a aparência até a próxima reconciliação.
- `power-input.js` confirma os olhos do poder depois de `blood.commit`. `powers.appearancePowers` verifica prazo, personagem e concessão. `feeding.js` chama `recordFeeding` depois de `BloodReserve.add(...) === true`; o estado `CLOSED` no journal sozinho não sinaliza crédito. Uma falha cosmética é registrada, e o crédito permanece concluído.
- `veil-eyes.js` lê o journal original `data\aetherius-veil-eyes.json` de Bruxa/Lobisomem e guarda também a autoria do TXST e o prazo de boca por concessão. O diário grava os descritores original, aplicado e predecessor antes de publicar uma mudança. A restauração só toca campos ainda atribuíveis ao sistema e aceita TXST original zero. Se houver rollback do mod, restaurar a aparência enquanto o ESP continua carregado.
- `supernatural-service.js` reconcilia no login após projetar a raça. O tick de aparência só escreve o snapshot `appearance` na borda, quando mudam `headpartIds` ou `headTextureSetId`. O snapshot conserva cabelo, tints, morfos, peso e os demais campos. Os observadores tardios usam o mesmo snapshot do ator pelo protocolo já existente; ele não leva a keyword nem a lógica de concessão.
- `client-source\client\src\sync\appearance.ts` reconhece os olhos pelo ESPFE, ID local, sexo e tipo. A `view\formView.ts` e o fluxo do dono aplicam a mudança de olho/TXST na base existente quando raça e estrutura coincidem. Usam um refresh do nó 3D, sem recriar o ator ou realocar o array de headparts. Mudanças estruturais seguem pelo caminho anterior. O dono adia a aplicação enquanto o RaceMenu estiver aberto.

## Pacote visual

Arquivos em `C:\Code\Aetherius - New Systems\Sobrenatural\entregas\AETHERIUS - DVA Visuals`.

| Arquivo | SHA-256 |
|---|---|
| `AETHERIUS - DVA Visuals.esp` | `CE1917E24C42E1E31FB9446C326452EBAAABAC16B290CBA7EBA11EFDC53BDE73` |
| `AETHERIUS - DVA Visuals.bsa` | `3C17595C49DB14EAF8D2DD45651D60B21F483D66DACB6FF249AFCF6DBB85CF15` |

O ESPFE conserva os IDs locais e os masters Skyrim e Dawnguard do DVA 1.4. O descritor usa o novo nome de plugin. O houseCARL leu os 50 registros por caminho absoluto, fora da ordem ativa: 36 TXST, 12 HDPT e duas FLST. A filtragem preserva apenas esses três grupos visuais e verifica ausência de VMAD nos registros. As QUST/GLOB e os dois overrides do Lorde ficam fora. O BSA contém seis NIF e quarenta DDS, sem PEX, fonte Papyrus ou imagem do MCM. A extração por houseCARL confirmou SHA-256 idêntico ao BSA original em 46 de 46 arquivos.

Para reproduzir, na raiz do laboratório execute `python overlays/sobrenatural/gerar-dva-visual.py 'C:\modOrganizer\mods\Dynamic Vampire Appearance' 'C:\Code\Aetherius - New Systems\Sobrenatural\entregas\AETHERIUS - DVA Visuals'`. O gerador verifica os hashes da versão original e preserva os arquivos locais de mídia fora do Git. A autorização de 21/11 cobriu LOCAL e GRÁFICO – QUALIDADE nesta máquina.

## Validação

- **568/568 Node em cada modo** `AETHERIUS_VAMPIRE_FEED_ENABLED=true/false`, com 11 casos novos. Inclui Reserva/Cálice, Lorde, olhos da Bruxa e Lua de Masser. Logs `%TEMP%\aetherius-dva-node-true.log` e `%TEMP%\aetherius-dva-node-false.log`.
- F7 e revisão F7 passaram, 2/2. O standalone do serviço passou, incluindo restauração após throw/no-op/reinício. TypeScript `--noEmit` e webpack passaram. O bundle `client-source\build\dist\client\Data\Platform\Plugins\skymp5-client.js` tem SHA-256 `93FD7C93DC3EA4C96A55B18B22B56A40A9823D43E2B8090984464874C64F4854`.
- A entrada tardia e o receptor cliente foram exercitados em mocks. A composição real do FaceGen e a continuidade de animação/equipamento seguem para o ensaio em jogo.

```powershell
Set-Location 'C:\Code\Aetherius - Servidor Local'
$tests = @(Get-ChildItem 'overlays/sobrenatural/teste-*.cjs' |
    Where-Object Name -NotIn 'teste-servico.cjs','teste-painel-f7.cjs','teste-revisao-f7.cjs' |
    ForEach-Object FullName)
$tests += @(
    'source/server/gamemode/werewolf/tests/werewolf.test.cjs',
    'source/server/gamemode/magic/tests/magic.test.cjs',
    'source/server/gamemode/survival-service.test.js',
    'source/server/gamemode/skysocial-service.test.js',
    'source/server/gamemode/survival-effects.test.js',
    'source/server/gamemode/supernatural/tests/masser.test.cjs',
    'client-source/client/meridian/time-service.test.cjs'
)
foreach ($mode in 'true','false') {
    $env:AETHERIUS_VAMPIRE_FEED_ENABLED = $mode
    node --test --test-timeout=10000 @tests
}
node --test overlays/sobrenatural/teste-painel-f7.cjs overlays/sobrenatural/teste-revisao-f7.cjs
node overlays/sobrenatural/teste-servico.cjs (Resolve-Path 'source/server/gamemode').Path
```

## Roteiro em jogo

1. Usar o LOCAL preparado nesta máquina. Para preparar outro ambiente autorizado, aplicar o delta 037 ou copiar os sete módulos do overlay, aplicar o delta 042 e gerar o bundle com `npm run build` em `C:\Code\Aetherius - Servidor Local\client-source\client`. Instalar o pacote nos perfis de teste do servidor e de todos os clientes. Comparar índice efetivo do ESPFE e FormIDs de exemplo, porque o wire usa IDs numéricos. Verificar `00283C:Dawnguard.esm` e `00CD5B:Dawnguard.esm` e as texturas do BSA no VFS.
2. Preparar um vampiro humano A, vizinho B e observador tardio C. Anotar a aparência original antes do F7. Com a Reserva real e o controle de dados do laboratório, ensaiar 70/69, 50/49, 31/30 e zero. Observar olhos e pele por A, B e C. Usar Poção de Sangue para conferir a subida de Stage sem boca.
3. Usar Visão, depois Força ou Celeridade, e conferir a troca e a volta ao visual anterior quando cada poder expira. Repetir em Feral. Testar movimento, ataque, arma e alvo durante as bordas, observando continuidade no vizinho. Verificar o Aura Whisper no dono.
4. Com a alimentação habilitada, A se alimenta de B. Conferir boca após crédito, entrada de C durante o prazo, renovação com outro alvo, relog e retorno ao Stage ao fim dos 180 s. Repetir com alimentação interrompida e conferir ausência de boca. Testar a limpeza em morte, abatimento ou perda da maldição.
5. Ensaiar humano, elfo e orc de ambos os sexos e o fallback racial de Argoniano e Khajiit. Testar ida e volta do Lorde durante um efeito. Retirar a maldição pelo F7 e verificar os originais após relog. Trocar entre Vampiro, Bruxa e Lobisomem, incluindo Bronze em Lua Cheia. Abrir o RaceMenu pelo F7 e verificar a reaplicação depois da edição e a restauração da nova base.

## Instalação 21/11

O dev autorizou a instalação após o parecer do Gemini 4 Neon, aprovado sem achados. Em `C:\Code\Aetherius - Servidor Local`, foram conferidos o servidor `9f01499`, o cliente `3c80c2c`, o overlay `f6e0092` e o reverse-check dos patches 037 e 042. Skyrim, MO2 e o servidor LOCAL estavam fechados antes e depois da instalação.

| Perfil MO2 em `C:\modOrganizer\profiles` | Aetherius DVA Visuals | Dynamic Vampire Appearance original |
|---|---|---|
| `AETHERIUS - LOCAL` | Mod ativo e ESPFE acrescentado ao fim da ordem. 440 plugins ativos | Ausente de `modlist.txt`, `plugins.txt` e `loadorder.txt`, como antes |
| `AETHERIUS - GRAFICO - QUALIDADE` | Mod ativo e ESPFE no lugar do plugin anterior. 439 plugins ativos | Mod desmarcado em `modlist.txt`; ESP removido de `plugins.txt` e `loadorder.txt` |

Os dois perfis usam o pacote em `C:\modOrganizer\mods\AETHERIUS - DVA Visuals`. Os SHA-256 do ESPFE e do BSA correspondem à tabela do pacote. O bundle `93FD7C93DC3EA4C96A55B18B22B56A40A9823D43E2B8090984464874C64F4854` foi instalado em `C:\modOrganizer\mods\AETHERIUS - SERVIDOR LOCAL\Platform\Plugins\skymp5-client.js`, vencedor no VFS LOCAL. O bundle anterior `24EA076F2BC4BF0D95939D3B7581B8928D555ECA484F75EA8B8005769EE8AFAC` está no arquivo irmão `skymp5-client.js.bak-dva-2111`.

O houseCARL `2.0.0+5e3c744862bc17c85ebe850830603a9a3e6f0779` conferiu os dois perfis. O LOCAL foi lido por uma instância isolada do houseCARL, com um INI temporário que seleciona o LOCAL e resolve os perfis reais via `base_directory`. A seleção do MO2 real permaneceu em GRÁFICO – QUALIDADE. [Implementação da resolução do perfil no houseCARL](https://github.com/Avick3110/houseCARL/blob/5e3c744862bc17c85ebe850830603a9a3e6f0779/src/housecarl-core/Mo2Instance.cs).

- Em ambos, os 46 NIF/DDS do DVA vêm de `AETHERIUS - DVA Visuals.bsa`. O ESPFE visual é o vencedor de `000814` (HDPT `MaleEyesVampireDVA01`, runtime `FE137814`) e `000806` (TXST `SkinHeadFemaleVampireDVA01`, runtime `FE137806`).
- `DLC1VampireChangeEffect` (`00283C:Dawnguard.esm`) e `DLC1RevertEffect` (`00CD5B:Dawnguard.esm`) têm `Dawnguard.esm` como único provedor. No GRÁFICO – QUALIDADE, isso foi conferido comparando o vencedor antes e depois da troca. No LOCAL, Dawnguard já era o vencedor.
- As QUST `DVA_ControlQuest` (`000830`) e `DVA_ConfigQuest` (`000834`) ficaram fora da ordem ativa nos dois perfis. Os três PEX do DVA ficaram fora do VFS pesquisado. Os arquivos originais do mod permanecem no disco com os hashes da investigação.

`scripts\Build.ps1 -SkipInstall` e webpack concluíram. `scripts\Prepare.ps1 -PluginClient 'C:\Code\Aetherius - Servidor Local\backups\dva-2111\profile-snapshot' -PluginProfile 'AETHERIUS - LOCAL'` usou cópias fiéis dos três arquivos do perfil LOCAL. A primeira execução herdou a revisão do repositório pai do snapshot. Uma segunda conferiu a revisão cliente `3c80c2c` no Git e registrou a mesma ordem, com **440 plugins**, 128 regulares e 312 light. O runtime recebeu o ESPFE visual com o SHA-256 aprovado, índice `FE137`, e os sete módulos de gamemode idênticos ao `source`. O addon nativo preparado permaneceu idêntico ao backup, SHA-256 `2A4E677911BE738A8EDE8563E05AB79AE0DF79B2B4341123167E6B4B2BC3DF37`.

Os seis arquivos de perfil têm backups irmãos `modlist.txt.bak-dva-2111`, `plugins.txt.bak-dva-2111` e `loadorder.txt.bak-dva-2111` em cada perfil. O backup do laboratório está em `C:\Code\Aetherius - Servidor Local\backups\dva-2111\replaced` e preserva caminhos relativos com sufixo `.bak-dva-2111`. O inventário `runtime-backup-sha256.json` registra 1814 arquivos. Para reverter antes de iniciar o ensaio, restaurar esses arquivos de perfil e o bundle anterior com o MO2 fechado e preparar novamente o servidor com a ordem restaurada.

Os recibos estão em `C:\Code\Aetherius - Servidor Local\evidence`, nos arquivos `dva-2111-install.json`, `dva-2111-runtime-verified.json`, `dva-2111-housecarl-local.json`, `dva-2111-housecarl-grafico.json` e nos logs `dva-2111-build.log`, `dva-2111-build-client.log`, `dva-2111-prepare.log` e `dva-2111-prepare-revision.log`. O resultado da instalação é estático. A aparência renderizada e a sincronização em jogo ainda precisam do ensaio.
