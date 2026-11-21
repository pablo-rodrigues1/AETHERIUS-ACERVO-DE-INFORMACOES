# Etapa 2 — Céu sincronizado pelo relógio visual

Candidato do laboratório em 21/11/2026. O servidor publica o dia de Masser e Secunda no `gameTime` v1. O cliente envia a projeção a um fork do MoonMod por evento local SKSE. O relógio visual usa a época de `masserPhaseAt` e conserva os prazos de Papyrus gravados no calendário do jogo. A implementação responde às cinco ressalvas aceitas da revisão de viabilidade. [Investigação](investigacoes/INVESTIGACAO_21-11_ceu-sincronizado.md) · [Revisão Neon](REVISAO-CEU-SINCRONIZADO_neon.md)

## Fontes, patches e artefatos

A raiz do laboratório é `C:\Code\Aetherius - Servidor Local`. Os caminhos abaixo são relativos a ela. O candidato foi construído fora do MO2. A instalação autorizada está registrada em [Instalação 21/11](#instalação-2111).

| Parte | Base → candidato | Caminho de integração |
|---|---|---|
| Servidor | `a3ef672364415021830e9e3934f567390d859c0a` → `79ce921793329bf98ef54776f23efe80e4d716b1` em `source` | `overlays\sobrenatural\036-ceu-sincronizado.patch` depois do 035. O teste Masser atualizado acompanha `overlays\sobrenatural\files\server\gamemode\supernatural\tests\masser.test.cjs` |
| Cliente | `6c78e250c31d3f4c9e7fbc2b7369944105c4800c` → `8cfa5534865257fd71345f14aacc2f9d1ab4035e` em `client-source` | `overlays\cliente-local\041-ceu-sincronizado.patch` após 039/040 |
| DLL | MoonMod v2.0.2 `4732799318176988f177757eebb514c95b76cb3f` → `18ef3eb608693723d6da8d40a3be9de120832fd1` em `tools\moonmod-source`, branch `aetherius-sky-clock` | `overlays\cliente-local\moonmod\001-relogio-visual.patch` aplicado à base upstream, em checkout próprio |
| Overlay | `81966c377a8a18b7e968ae8e840aee7b55d66335` na raiz do laboratório | Contém os três patches e o espelho do teste Masser |

| Arquivo | SHA-256 |
|---|---|
| `tools\native\sky-moons\po3_MoonMod.dll` | `7BAD2A4CFC59259420CE39DC9A2FEE07F42052C70276F714B8724EB4F6AC2E8B` |
| `tools\native\sky-moons\LICENSE` | `51E657EA7520301284BD80D64088F91063A37724DF36DD147CE6DF21CF00943D` |
| `tools\native\sky-moons-client\skymp5-client.js` | `24EA076F2BC4BF0D95939D3B7581B8928D555ECA484F75EA8B8005769EE8AFAC` |
| `036-ceu-sincronizado.patch` | `9817934B4CFE1EB815AB2DF9C5753EBAB0C298126D241A20CD2FCD5BD1BC30D9` |
| `041-ceu-sincronizado.patch` | `A9852CFC6D8E3DB1F070CA5915918227DA6E0015FEECDF8A37390E39BCB93D9C` |
| `001-relogio-visual.patch` | `C442FD4A1905DA36351112EF76D803EF855EBCF98F07EA1592D02BD63469EE50` |

Os três patches passaram `git apply --check --whitespace=error` sobre suas respectivas bases em índices temporários e `git apply --reverse --check --whitespace=error` nas árvores candidatas. O espelho do teste Masser coincide byte a byte com `source`, SHA-256 `5BD636E26D325293446650C671738F4ACC0698E47C1D513A39CA5C941863DE47`. A licença MIT original (Copyright 2022 powerof3) permanece idêntica ao upstream. A DLL carrega os créditos de powerofthree e Aetherius e conserva o nome que seleciona o INI original. [Upstream fixado](https://github.com/powerof3/MoonMod/tree/4732799318176988f177757eebb514c95b76cb3f)

## Contrato e ciclo de vida

- **Servidor.** `source\server\ts\systems\environmentAuthority.ts:241–249` extrai `(gameMinutes - clockEpochMinutes()) / 1440` da mesma amostra usada para a data civil. `skyDaysPassed` é um número de 0 a 3.600.000 ou `null` se o relógio sair desse domínio. O valor usa o mesmo dia que `masserPhaseAt` depois de `floor`. `environmentSystem.ts:148–176` serializa o snapshot existente com a sessão e a sequência. O broadcast continua a cada 30 s, além de pedidos e mudanças da autoridade. O campo legado `gameDaysPassed` continua opt-in via `AETHERIUS_WEATHER_LAB_CALENDAR=1`. A nova projeção funciona com essa flag desligada. O `time.set` mantém a restrição à flag.
- **Cliente.** `client-source\client\src\services\services\timeService.ts:33–52,65–100,106–147` compensa meio RTT na recepção e soma o tempo decorrido até publicar. Usa `AetheriusSkyClockSnapshot` do Form local do jogador, com as formas abaixo. Publica uma vez por sequência aceita depois da prontidão do jogador. `loadGame`, perda de prontidão e `RaceSex Menu` limpam a projeção e permitem republicar uma amostra ainda válida pós-menu. A idade desde a recepção do pacote original acompanha o reenvio. Desconexão, troca de sessão, campo ausente/inválido ou amostra expirada programam invalidação no update. Se o jogador ou a VM ainda não estão disponíveis, a invalidação continua pendente e o nativo tem seu próprio prazo. O campo visual nunca passa pelo writer de `0x39` ou pelo `AetheriusCalendarSnapshot`. Aquele caminho continua reservado a pacotes legados do laboratório de data.
- **Nativo.** `tools\moonmod-source\src\main.cpp:19–67` recebe o evento em `kDataLoaded` e troca a geração de carregamento em `kPreLoadGame`, `kPostLoadGame` e `kNewGame`. `src\SkyClock.cpp:37–96` protege o estado com mutex e agenda no máximo uma tarefa pendente por geração. A amostra válida mais recente substitui a anterior no slot. Invalidação limpa imediatamente o slot, a âncora ativa e a identidade, inclusive enquanto uma tarefa aguarda. Um snapshot mais novo no mesmo frame pode usar a tarefa já pendente. Tarefas de outra geração são ignoradas. O tempo é ancorado em `steady_clock` na chegada do evento. O prazo total é de 90 s desde a recepção JS, somando a idade informada e o tempo nativo decorrido. Reenviar depois de um menu não renova o prazo.
- **Renderização.** `src\Hooks.cpp:7–14,67–70,133–143` mantém o dia em `double`. O hook de fase amostra uma vez e passa o mesmo inteiro às duas luas. O de ângulo conserva a fração até o ângulo final. Sem amostra válida, ambos voltam a ler o calendário local. O fork não escreve `GameDaysPassed`, `midnightsPassed`, `rawDaysPassed` nem dados de save e deixa de salvar o INI ao lê-lo. Sob clima idêntico, as luas convergem a partir da época/escala comuns. Clientes com RTT diferente podem mudar de fase em frames distintos perto da virada. Os hooks são das luas. As estrelas ficam como verificação visual, fora do novo relógio.

```json
{"version":1,"kind":"sample","days":30191.9998,"timeScale":12,"ageMs":300,"serverSessionId":"aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa","sequence":18}
{"version":1,"kind":"invalidate"}
```

**Limite do evento local.** O receptor aceita o nome definido, emissor `0x14` e JSON de até 512 bytes. Valida versão/variante, ID de sessão hexadecimal de 32 caracteres, sequência inteira positiva no intervalo seguro do JavaScript, dias finitos no intervalo permitido, escala de 0 a 100 e idade inferior a 90 s. Uma nova sessão pode substituir a anterior com sequência menor. Papyrus local também pode emitir o evento com o jogador como emissor. Assim, o evento pode ser forjado no próprio jogo e afetar apenas o céu desse processo. O servidor não recebe nem usa esse evento para Maldições, HUD ou progressão.

**Compatibilidade.** A DLL foi compilada apenas para a edição Steam AE `1.6.1170.0`, com Address Library correspondente e SKSE `2.2.6` no laboratório. A versão no manifesto é exata e `SKSEPlugin_Load` rejeita outras versões ou o Creation Kit com log antes de `SKSE::Init`. O loader do SKSE pode barrar a versão pelo manifesto e registrar a recusa no log do SKSE antes de chamar o plugin. `Hooks.cpp:193–225` verifica a entrada de fase para desvios conhecidos, os dois opcodes CALL de textura, a instrução deslocada na posição e o slot virtual de rotação antes de instalar os patches. A Address Library local resolveu os IDs de fase `26158`, posição `26169` e rotação `26168`. As verificações sobre o código carregado precisam ser confirmadas no jogo por log. O conteúdo do arquivo executável no disco nesses RVAs não serviu como prova de assinatura em memória. SE 1.5.97, outros AE, GOG e VR exigem alvos e validação próprios.

## Build e testes

Na raiz do laboratório, Windows x64, CMake 4.3.0-rc3, Visual Studio 18 2026, MSVC `19.51.36256.0`. CommonLibSSE-NG flatrim instalado no prefixo `source\build\vcpkg_installed\x64-windows-sp`, versão `3.7.0-unstable-2024-09-03-b93280e8`, CRT estático `/MT`. Esse port fornece o layout flat SE/AE, e a DLL e os stubs deste fork foram selecionados só para AE 1.6.1170. O target CMake produz arquivos na pasta de build e não tem etapa de deploy.

```powershell
$prefix = (Resolve-Path 'source/build/vcpkg_installed/x64-windows-sp').Path
cmake -S tools/moonmod-source -B tools/native/sky-moons-build -G 'Visual Studio 18 2026' -A x64 "-DCMAKE_PREFIX_PATH=$prefix"
cmake --build tools/native/sky-moons-build --config Release --parallel 4
ctest --test-dir tools/native/sky-moons-build -C Release --output-on-failure
```

- Build CPP Release concluído. `dumpbin /exports /dependents` mostrou `SKSEPlugin_Load`, `SKSEPlugin_Query` e `SKSEPlugin_Version`. Dependências DLL do Windows apenas. CTest `sky-clock` passou 1/1, com validação do JSON, duas atualizações antes do dreno, invalidação pendente e ativa, rejeição de ordem antiga, troca de sessão, descarte de geração anterior, precisão na fronteira e expiração com idade acumulada. O harness testa o estado nativo em processo independente do Skyrim.
- Cliente TypeScript e servidor TypeScript com `--noEmit` passaram. Webpack 5.94.0 compilou o bundle ao diretório `tools\native\sky-moons-client` com `DEPLOY_PLUGIN=false` e `ZIP_PLUGIN=false`.
- Bateria direcionada **112/112**. Lua de Masser **21/21** dentro dela, com época deslocada, modo dentro/fora do laboratório, HUD, botão, F7, três noites e persistência. Scripts de serviço, painel F7, revisão F7 e compulsão incluídos na validação. Os seis testes de `client\meridian\time-service.test.cjs` incluem escala 12, RTT 400 ms, projeção da fronteira 7→0, pausa/retomada, deduplicação, `loadGame`, `RaceSex`, perda de prontidão, expiração, `null`/campo inválido e invalidação durante desconexão com recuperação da VM.
- Bateria ampliada **557/557 por modo** em `AETHERIUS_VAMPIRE_FEED_ENABLED=false/true`. Logs `%TEMP%\aetherius-ceu-node-false.log` e `%TEMP%\aetherius-ceu-node-true.log`.
- Suíte geral adicional do Meridian **127/146**, com as mesmas 19 falhas por nome em uma cópia limpa do cliente base `6c78e250` (**121/140**). São 13 casos de teleporte indexado e seis de foco/PTT. Os seis casos novos passaram. Logs `%TEMP%\aetherius-ceu-meridian.log` e `%TEMP%\aetherius-ceu-meridian-base.log`.

```powershell
$testFiles = @(Get-ChildItem 'overlays/sobrenatural/teste-*.cjs' |
  Where-Object Name -NotIn 'teste-servico.cjs','teste-painel-f7.cjs','teste-revisao-f7.cjs' |
  ForEach-Object FullName)
$testFiles += @(
  'source/server/gamemode/werewolf/tests/werewolf.test.cjs',
  'source/server/gamemode/magic/tests/magic.test.cjs',
  'source/server/gamemode/survival-service.test.js',
  'source/server/gamemode/skysocial-service.test.js',
  'source/server/gamemode/survival-effects.test.js',
  'source/server/gamemode/supernatural/tests/masser.test.cjs',
  'client-source/client/meridian/time-service.test.cjs'
)
foreach ($mode in 'false','true') {
  $env:AETHERIUS_VAMPIRE_FEED_ENABLED = $mode
  node --test --test-timeout=10000 @testFiles
}
node --test overlays/sobrenatural/teste-painel-f7.cjs overlays/sobrenatural/teste-revisao-f7.cjs
node overlays/sobrenatural/teste-servico.cjs (Resolve-Path 'source/server/gamemode').Path
```

## Instalação e ensaio com dois jogadores

1. Após revisão Neon do candidato e autorização do dev, integrar os commits no laboratório, preparar o servidor por `scripts\Build.ps1` e `scripts\Prepare.ps1` e instalar o bundle desta entrega na cópia efetiva de `Data\Platform\Plugins\skymp5-client.js` no perfil LOCAL em **ambas** as máquinas. Verificar pacote `gameTime` v1 com `skyDaysPassed` finito e `gameDaysPassed: null`, com `AETHERIUS_WEATHER_LAB_CALENDAR` desligada. O F7 deve continuar recusando ajuste completo de data nesse modo.
2. Com Skyrim fechado, criar no perfil LOCAL um mod de override com `SKSE\Plugins\po3_MoonMod.dll` e copiar do artefato verificado. Guardar os créditos e a licença junto ao pacote. Priorizar o override acima de `Moons and Stars - Sky Overhaul SKSE`, mantendo seu ESP e suas texturas. Pelo VFS do houseCARL, confirmar um único vencedor para essa DLL e nenhum segundo binário do MoonMod com nome alternativo. Conferir `versionlib-1-6-1170-0.bin` e o INI vencedor do Rudy (SHA-256 `07927BD2D4A697F978D0FA741A71E3D3970DDD6585ADDCB529E677B16330C89B`). O log `Documents\My Games\Skyrim Special Edition\SKSE\po3_MoonMod.log` deve identificar o fork e mostrar os hooks de fase, textura e posição instalados. Se o log indicar instrução ou vtable inesperada, desabilitar o override e guardar o log para diagnóstico. O upstream volta a vencer quando o override é desabilitado.
3. Usar dois saves descartáveis com valores diferentes de `GetGlobalValue GameDaysPassed`. Conectar no mesmo exterior, com céu e clima iguais, um lobisomem e um observador. Registrar `0x39` antes e depois, dia/fase do servidor, HUD e captura da posição e fase de ambos os astros. Repetir pausado na autoridade para eliminar a variação de ângulo por RTT. Os valores locais de `0x39` podem avançar naturalmente. Nenhum deve saltar ao dia do `gameTime` visual.
4. Exercitar Masser 7→0 e 0→1, Secunda na virada do inteiro e uma mudança de ângulo com escala 12. Preparar o estado inicial do relógio em um fixture separado do laboratório ou avançar usando `time.setHour`, que vai à próxima hora pedida sem habilitar ajuste de calendário. Observar os dois clientes na mesma amostra de fase. Medir a divergência imediatamente na virada e sua convergência. Testar entrada tardia, pausa/retomada `12→0→12`, carregar save, `RaceSex`, sair do jogo e reconectar em processo aberto. No ensaio de perda de transporte, reter **todos** os pacotes `gameTime`, inclusive respostas ao pedido de 60 s, por mais de 90 s, observar retorno ao calendário local e recuperar com um pacote novo.
5. Repetir com aluguel e missão Missives em andamento e com efeito COTV ou uma Fera já em forma. Comparar deadlines locais com a execução sem o override e conferir que um salto visual não encerra esses estados. No servidor, exercitar três noites da Lua Cheia, compulsão, F7 e botão e comparar a fase com a HUD. Uma noite continua pertencendo ao dia em que começou mesmo após meia-noite. Anotar visibilidade das estrelas separadamente.

A carga em Skyrim e a validação visual com dois jogadores ainda estão pendentes.

## Instalação 21/11

Instalação local autorizada pelo dev após a reconfirmação do Neon. Revisões conferidas em `source` `79ce921`, `client-source` `8cfa553` e overlay `81966c3`. Os patches 036 e 041 passaram novamente no reverse-check. O servidor LOCAL, o Skyrim e o MO2 permaneceram fechados.

**Build e Prepare.** `scripts\Build.ps1 -SkipInstall` concluiu com as dependências já presentes. O primeiro Prepare detectou sete diferenças de mod/plugin entre o perfil versionado no cliente e o perfil LOCAL do MO2. O Prepare concluído usou `-PluginClient "C:\Code\Aetherius - Servidor Local\backups\ceu-2111\profile-snapshot" -PluginProfile "AETHERIUS - LOCAL"`, com cópias de `modlist.txt`, `plugins.txt` e `loadorder.txt` do MO2 verificadas por hash. Assim o runtime recebeu o perfil de teste efetivo sem alterar o espelho Git. Foram sincronizados 439 plugins, 128 regulares e 311 light. Os scripts rodaram com `-ExecutionPolicy Bypass` apenas no processo filho do PowerShell.

| Arquivo nesta máquina | SHA-256 |
|---|---|
| `C:\Code\Aetherius - Servidor Local\runtime\server\dist_back\skymp5-server.js` | `0F94FC1B205450F9CF0ADAA8E69FE8EF278BFB39E21FA8E67FDE2C30F496D3A0` |
| `C:\Code\Aetherius - Servidor Local\runtime\server\data\aetherius-loadorder.json` | `4D8515E2CFEB98B2EC982790BD2AB9B439131353CEEAE15F0C2F095FF998C0EA` |
| `C:\modOrganizer\mods\AETHERIUS - SERVIDOR LOCAL\Platform\Plugins\skymp5-client.js` | `24EA076F2BC4BF0D95939D3B7581B8928D555ECA484F75EA8B8005769EE8AFAC` |
| `C:\modOrganizer\mods\AETHERIUS - SERVIDOR LOCAL\Platform\Plugins\skymp5-client.js.bak-ceu-2111` | `FD61C198FAA52E1A821106DFBCCC23CCBFCCA95F3B9A63AFFD9F900AD2935709` |
| `C:\modOrganizer\profiles\AETHERIUS - LOCAL\modlist.txt` | `04F7B184C682B996BB55DBBE01A3E1C35F7A3CE098D9160EECE8BC81DB3035AD` |
| `C:\modOrganizer\profiles\AETHERIUS - LOCAL\modlist.txt.bak-ceu-2111` | `31D0F1D2AC605887E462DACCF1C106F7634D7102D2DE08D3DAD0659A34C8E3FA` |
| `C:\modOrganizer\mods\AETHERIUS - Ceu Sincronizado (LOCAL)\SKSE\Plugins\po3_MoonMod.dll` | `7BAD2A4CFC59259420CE39DC9A2FEE07F42052C70276F714B8724EB4F6AC2E8B` |
| `C:\modOrganizer\mods\AETHERIUS - Ceu Sincronizado (LOCAL)\Docs\AetheriusCeu\LICENSE` | `51E657EA7520301284BD80D64088F91063A37724DF36DD147CE6DF21CF00943D` |
| `C:\modOrganizer\mods\AETHERIUS - Ceu Sincronizado (LOCAL)\Docs\AetheriusCeu\CREDITS.md` | `EDF7EB72CD097416E4E180355CD581201ACCEAF3D2C69E075885627DFDA509C6` |
| `C:\modOrganizer\mods\Rudy ENB SE for Obsidian Weathers - LUX - ELFX\SKSE\Plugins\po3_MoonMod.ini` | `07927BD2D4A697F978D0FA741A71E3D3970DDD6585ADDCB529E677B16330C89B` |
| `C:\modOrganizer\mods\Address Library for SKSE Plugins\SKSE\Plugins\versionlib-1-6-1170-0.bin` | `C4093C569A3C83B26587F4B9EA4C55DE9AE6E73B84A2AF9FB3FBD30E2FE0D452` |

**Verificação.** No `modlist.txt`, `+AETHERIUS - Ceu Sincronizado (LOCAL)` entrou imediatamente antes de `+Moons and Stars - Sky Overhaul SKSE`, linhas 391–392. Os outros perfis e os arquivos `plugins.txt` e `loadorder.txt` do LOCAL mantiveram seus bytes. `MoonsAndStars.esp` segue ativo. A DLL original foi preservada no mod upstream (SHA-256 `979DBA0B8AB2ADA76D00F213ADEA3A25631E1F78CAE3D31594B19E5F421B5C24`). O houseCARL `2.0.0+5e3c744862bc17c85ebe850830603a9a3e6f0779`, com leitura explícita do perfil LOCAL, confirmou:

- `Platform\Plugins\skymp5-client.js` vence em `AETHERIUS - SERVIDOR LOCAL`.
- `SKSE\Plugins\po3_MoonMod.dll` tem dois provedores para o mesmo caminho. A única cópia efetiva é o fork 2.0.2.1, em `AETHERIUS - Ceu Sincronizado (LOCAL)`. A inspeção dos 117 caminhos de DLL efetivos encontrou uma única identidade MoonMod. O houseCARL também lista os 15 slots padrão `1.0.0` dos metadados. A leitura do PE confirmou esses slots e a entrada configurada `1.6.1170.0`. A trava em `SKSEPlugin_Load` só aceita `1.6.1170.0`.
- O INI efetivo vem do Rudy, com o SHA-256 aprovado. `versionlib-1-6-1170-0.bin` vence em `Address Library for SKSE Plugins` e tem cabeçalho interno formato 2 para `1.6.1170.0`. O jogo instalado é `1.6.1170.0` com SKSE `2.2.6`.

O bundle de servidor preparado contém `skyDaysPassed`. Fora de um servidor em execução, `EnvironmentSystem.sendTime` foi exercitado com autoridade real e transporte mockado, produzindo `gameTime` v1 com `skyDaysPassed: 3.25`, `gameDaysPassed: null`, `dateChangesEnabled: false` e fase de Masser 1. A suíte Masser passou **21/21**. A flag `AETHERIUS_WEATHER_LAB_CALENDAR` estava desativada na configuração local verificada. Os logs estão em `C:\Code\Aetherius - Servidor Local\evidence` com prefixo `ceu-2111-`, incluindo o inventário, o fixture do pacote, os SHA-256 e a saída do Build/Prepare. A conexão real, o log de carga SKSE e o céu ainda dependem do ensaio em jogo.

### Segunda máquina do dev

1. Transferir a partir de `C:\Code\Aetherius - Servidor Local` os quatro arquivos `tools\native\sky-moons-client\skymp5-client.js`, `tools\native\sky-moons\po3_MoonMod.dll`, `tools\native\sky-moons\LICENSE` e `tools\native\sky-moons\CREDITS.md`. Conferir `Get-FileHash -Algorithm SHA256` de cada um contra a tabela acima antes de instalar.
2. Com Skyrim e MO2 fechados, conferir Steam AE `1.6.1170.0`, SKSE `2.2.6` e a presença de `SKSE\Plugins\versionlib-1-6-1170-0.bin` no VFS LOCAL. Usar a raiz do MO2 dessa máquina. Resolver o vencedor LOCAL de `Platform\Plugins\skymp5-client.js` com houseCARL. Se vencer `AETHERIUS - SERVIDOR LOCAL` como nesta máquina, o destino é `<MO2>\mods\AETHERIUS - SERVIDOR LOCAL\Platform\Plugins\skymp5-client.js`. Copiar o arquivo atual para `skymp5-client.js.bak-ceu-2111` ao lado e substituir o arquivo efetivo pelo bundle aprovado. Manter a configuração de conexão e assinatura já usada pelo LOCAL.
3. Copiar `<MO2>\profiles\AETHERIUS - LOCAL\modlist.txt` para `modlist.txt.bak-ceu-2111` na mesma pasta. Criar `<MO2>\mods\AETHERIUS - Ceu Sincronizado (LOCAL)\SKSE\Plugins` e colocar ali `po3_MoonMod.dll` com esse nome. Colocar `LICENSE` e `CREDITS.md` em `<MO2>\mods\AETHERIUS - Ceu Sincronizado (LOCAL)\Docs\AetheriusCeu`. No `modlist.txt` LOCAL, inserir uma única linha `+AETHERIUS - Ceu Sincronizado (LOCAL)` imediatamente antes de `+Moons and Stars - Sky Overhaul SKSE`. No painel do MO2 com a coluna Prioridade em ordem crescente, isso corresponde a um número de prioridade maior que o do original. Manter o original habilitado para prover o ESP, texturas e demais recursos.
4. Conferir pelo houseCARL os quatro caminhos VFS citados acima. A DLL efetiva deve ser o fork e o INI deve continuar vindo do Rudy. A versão da Address Library deve corresponder ao executável. Verificar os hashes no destino, o backup do bundle e o backup do perfil. Selecionar **AETHERIUS - LOCAL** no MO2 quando for iniciar o ensaio autorizado. O teste em jogo segue os passos 3–5 da seção anterior.
5. Para reverter com o MO2 e jogo fechados, restaurar `skymp5-client.js.bak-ceu-2111` sobre o bundle e retirar só a linha do override no LOCAL. Se o `modlist.txt` ainda estiver no estado instalado, restaurar `modlist.txt.bak-ceu-2111` também reverte a ativação. O fornecedor da DLL original volta a vencer.
