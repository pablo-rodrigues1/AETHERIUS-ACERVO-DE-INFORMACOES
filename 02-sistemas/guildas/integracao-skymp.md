---
titulo: Instalação e sincronização de Guildas no SkyMP
tipo: processo
status: ativo
responsaveis: [Melekyy]
atualizado: 2026-11-17
tags: [guildas, skymp, integracao]
relacionados: [README.md, diario.md]
---

# Instalação de SkyGuilds no SkyMP — R28

Responsável: Melekyy. Revisão 17/11/2026. Leia também [escopo](escopo.md) e [validação](validacao-skymp.md).

## O que está preparado

Módulo CommonJS com entrada `bootstrap.cjs`, configuração portátil, verificação de APIs/recursos antes da instalação e testes. Referência de API auditada: [SkyMP, commit 2849e676](https://github.com/skyrim-multiplayer/skymp/blob/2849e676fe008925384b26f34476e382a30bd877/docs/docs_serverside_scripting_reference.md). Isso identifica a referência consultada; não certifica todos os forks/binários nem declara uma versão como mais recente. O código usa o objeto servidor `mp`, não `require('skyrimPlatform')` no Node.

Não há repositório remoto do código configurado; o acervo recebe documentação. O ZIP local contém somente Guildas ativas, testes e ferramentas portáteis. Não inclui estado de jogadores, mods, journal, adaptador de morte, addon SkyMP, pacotes cliente ou controladores de conjuração.

## Dependências

| Recurso | Painel básico | Instalação completa |
|---|---|---|
| SkyMP com `get/set`, propriedades/eventos customizados e consulta de ESPM | Sim | Sim, mais `callPapyrusFunction` |
| Autorização Staff real | Necessária para nomear líderes | Obrigatória na checagem |
| Journal de inventário SkySocial compatível | Sem operações de itens | Obrigatório |
| Recibos nativos `skySocialReceipt-v1` | Sem operações de itens | Obrigatórios e verificados no addon exato |
| Proteção de morte SkyDeath compatível | Sem insígnias | Obrigatória para retenção das insígnias |
| Catálogo/receita, mods e referências verificadas | Opcional/desativados | Obrigatórios |

**SkyMP padrão não basta para o contrato de recibos**: o `InventoryBinding.cpp` da revisão consultada não implementa `skySocialReceipt`. Use a extensão compatível adotada pelo journal do seu servidor, ou integre esse contrato no fork antes de habilitar a versão completa. O pacote não troca nem compila esse binário. `native-receipt-check` confirma leitura/escrita e preservação de recibos; não comprova sozinho recuperação de um processo interrompido.

O journal fornecido precisa ter `commit({ids,before,after,reason,...})`, `locked.has(id)` e `transactions`, respeitar variantes de itens e proteger as transações. O serviço de morte deve expor o contrato `mp.__skyDeathSystem` (`mp`, `becomeGhost`, `finishGhost`, `equipment`) e conservar a proteção aplicada às insígnias. A checagem valida forma/recursos; teste o comportamento do adaptador real.

O JavaScript exige Node >=20. Ferramentas nativas devem usar **o mesmo Node/ABI do addon do servidor**; a validação privada usou Node 22.23.3. Não carregue um addon compilado para outro ABI.

## Passo a passo

1. Em homologação, salve configuração, estado de Guildas e banco/mundo do SkyMP. Extraia para `systems/sky-guilds`. Não sobrescreva cliente carregado nem a instalação de produção.
2. Copie `config/skymp-full.example.json` para sua configuração de operação. Caminhos de estado/catálogo/recibos/addon são relativos a `serverRoot`; `doors.configFile`, `studentRooms.configFile` e `medallionsFile` são relativos ao módulo. `serverRoot` é obrigatório. Use diretório persistente e gravável.
3. Confira os plugins/mods e referências. O exemplo completo corresponde à instalação com JK's College of Winterhold, The Great City of Winterhold e Vision of Skyrim II. Os dez quartos são desse layout; não trate como configuração vanilla universal. Confira também plugins dos tomos, meshes/texturas, dependências e overrides. Clientes e servidor precisam dos mesmos plugins, ordem e versões/hashes. A checagem do servidor não compara os clientes automaticamente.
   Em clientes privados que usam um helper de CRC/tamanho com lista limitada de plugins, atualize também essa lista ao habilitar novos ESP/ESM. O perfil e o servidor podem estar corretos, mas uma lista antiga interrompe a inicialização. Não desative verificação de ordem/arquivos. Teste uma entrada nova no jogo, além de atualização a quente.

4. Gere o catálogo com os plugins reais. A política de Classes continua sendo aplicada no runtime; não altere o filtro para disponibilizar todos os BOOK.

```powershell
node systems/sky-guilds/tools/catalog.cjs C:/SeuServidor
node systems/sky-guilds/tools/preflight.cjs --server C:/SeuServidor --config C:/SeuServidor/systems/sky-guilds/config/server.json --output C:/SeuServidor/data/sky-guilds/preflight.json
```

5. Com portas de teste livres e sem carregar o mundo de jogadores, execute os ensaios isolados usando o Node correspondente ao addon. Eles abrem servidores temporários e bots nas portas 17982 e 17981; não salvam o mundo vivo. Se houver conflito de porta, libere-a antes. O teste da estante requer o plugin/referência da configuração.

```powershell
node systems/sky-guilds/tools/native-receipt-check.cjs --server C:/SeuServidor --output C:/SeuServidor/data/sky-guilds/native-receipts.json
node systems/sky-guilds/tools/native-shared-check.cjs --server C:/SeuServidor --reference "ff172:JK's College of Winterhold.esp" --output C:/SeuServidor/data/sky-guilds/shared-check.json
```

6. Carregue SkySocial/journal e o adaptador de morte antes de Guildas. Integre no ponto de inicialização onde o `mp` real está disponível:

```js
const path = require('node:path');
const bootstrap = require('./systems/sky-guilds/bootstrap.cjs');
const params = {
  serverRoot: __dirname,
  configFile: path.join(__dirname, 'systems/sky-guilds/config/server.json'),
  journal: mp.__skySocial.journal,
  // Implemente no servidor: consulte a sessão e a permissão reais da Staff.
  authorizeStaff: ({ actorId, profile, permission, guild }) =>
    suaAutoridadeStaff.temPermissao(actorId, profile, permission, guild),
};
const { report } = bootstrap.check(mp, params);
if (!report.ready) throw new Error(JSON.stringify(report.errors));
const guilds = bootstrap.install(mp, params);
```

O identificador `suaAutoridadeStaff` é um exemplo de integração, não um serviço incluído. `authorizeStaff` deve retornar exatamente `true` somente para a permissão real. Não substitua por `() => true`. Se recursos faltarem, o bootstrap lança `SKYGUILDS_INCOMPATIBLE` antes de registrar propriedades ou gravar o estado. Não duplique a instalação no mesmo processo nem pule a checagem chamando diretamente `src.install` em produção.

7. Ligue o botão já existente da Staff, somente no seu handler autenticado do servidor, a:

```js
await guilds.adminPanel.assign(staffActorId, { guild: 'mages', profile: targetProfileId });
await guilds.adminPanel.revoke(staffActorId, { guild: 'mages' });
// Outros IDs: 'alchemists' e 'companions'. profile é ID de perfil, não FormID.
```

Guildas não cria outro painel administrativo. O handler revalida conexão, perfil e autorização. `adminProfiles` é uma lista confiável do servidor, vazia nos exemplos; nunca preenchê-la com dados do navegador.

8. No serviço real de Classes, consulte `guilds.classes.hasKeyword(profileId, keyword)` ou `requireKeyword(...)` nos pontos que exigem filiação. São permissões virtuais do servidor, não KYWD automaticamente aplicados ao ator. A ponte `connectLegacy({classSystem})` só atende ao contrato legado `selectClass/setWinterholdStudent` de Winterhold; não liga sozinha os requisitos de Alquimistas/Companions ou outro runtime. Não transfira esses requisitos para o cliente.
9. Reinicie controladamente a homologação e conecte dois clientes reais. Use o checklist abaixo e só então promova ao oficial. O cliente deve executar as propriedades/eventos customizados recebidos pelo SkyMP; não se injeta um bundle privado neste pacote. Se a instalação usa outra UI/bridge, adapte o transporte ao contrato de Guildas e repita os testes de foco, mouse e fechamento.

## Sincronização e persistência

- `skyGuilds` (ou `propertyName` configurado): modelo owner-only, nenhum nonce/dado de gestão enviado aos vizinhos. `_skyGuildsAction`: comandos associados ao ator pelo SkyMP; o servidor revalida sessão e permissões.
- `skyGuildsCraftV1`: estado temporal de fabricação para dono/vizinhos; confirmar o nome exato em `src/crafting.js`. Duração/cobrança são autoritativas. O cliente compensa diferença de relógio apenas para exibição/animação.
- `skyGuildsDoorSealV1`, `skyGuildsStudentRoomV1` e `skyGuildsOfficeChestsV1`: selagem, nomes e streaming. Confira as constantes no código caso personalize o protocolo.
- Estoque do baú coletivo e dos quartos utiliza transporte/inventário nativo SkyMP. Guards de ativação, retirada e depósito verificam acesso no servidor. Não se copia o estoque por cliente. O log coletivo confirma deltas opostos nos dois inventários após a cadeia de hooks e registra somente a quantidade efetiva correspondente ao pacote.
- Preserve o encadeamento dos hooks de outros sistemas. Não instale versões duplicadas nem mantenha timers antigos publicando permissões obsoletas. `dispose()` encerra serviços do módulo; atualizações estruturais em produção exigem manutenção planejada.
- Não basta salvar o JSON de Guildas: persistência dos itens físicos depende também do mundo/banco nativo. Configure esse armazenamento, teste reinício e recuperação de transações no host real.

## Checklist com dois jogadores

1. Staff nomeia os três líderes; jogador comum não consegue forjar nomeação. Convites aceitos e remoção atualizam os painéis e os requisitos de Classes.
2. Professor/bibliotecário não acumulam funções; membro/professor sem permissão não recebe o blip da Biblioteca. Líder e bibliotecários fabricam; materiais entram/saem corretamente, inclusive dois fabricando simultaneamente e cancelamentos.
3. Cada escola entrega uma insígnia; bloquear transferência/descarte/roubo e preservar após morte/reconexão. Retirar escola como Arquimago e expulsar remove o vínculo/item.
4. Dois clientes veem a animação; diferença de relógio, troca de área e perda de função não liberam entrega duplicada. Verificar mouse, seletores, Esc e movimentação após fechar.
5. Atribuir cada um dos dez quartos; dono/faculdade abrem porta só perto; outro aluno é bloqueado. Conferir baú/estante/armário, variantes e septins. Trocar quarto cheio mantém conteúdo para o novo dono.
6. Trancar individual/em lote: tentar ambos os lados das portas, inclusive Arquimago; destrancar e atravessar. Verificar streaming e reconexão.
7. Baú do Arquimago bloqueia demais funções e acompanha sucessão com conteúdo. Dois membros alternam depósito/retirada no coletivo e veem o mesmo estoque. Expulsão bloqueia na hora; Arquimago vê logs exatos e membro comum não.
8. Reiniciar homologação com mundo e JSON persistidos; conferir funções, quartos, trancas, cronograma, auditoria e inventários. Testar recuperação do journal conforme seu contrato; testar inventário cheio, servidor indisponível e desconexão durante fabricação.

## Rollback

Guarde a versão anterior do módulo e sua configuração antes de instalar. Em falha, pare a homologação, preserve o estado novo para diagnóstico e restaure o módulo/configuração anterior compatível. Não reverta só um lado de uma transferência nem apague estoques/recibos. Se for necessário restaurar dados, restaure o conjunto consistente de estado de Guildas, mundo e journal pelo procedimento do servidor. A entrega não altera plugins/ordem automaticamente, não remove arquivos arquivados e não modifica conjuração.

## Testes do pacote

```powershell
npm install
npm test
```

Testes de navegador usam Microsoft Edge headless por padrão. Para Chromium do Playwright: `npx playwright install chromium` e configure `PLAYWRIGHT_CHANNEL=chromium`. Isso exercita a interface HTML e contratos simulados; não equivale a dois Skyrims conectados.
