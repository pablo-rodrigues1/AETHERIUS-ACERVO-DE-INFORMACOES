# Revisão cruzada técnica — Etapa 1.4

**Data:** 15/11/2026. **Autoria da entrega:** Sol High. **Revisor:** Terra High / Codex, tarefa Orca `task_07317246464b`, dispatch `ctx_67e6288f4f5e`. Estética aprovada pelo dev fora do escopo. Nenhum arquivo da implementação foi alterado por esta revisão.

**Veredito: APROVADO COM RESSALVAS**, condicionado à decisão do orquestrador sobre os apontamentos 1 e 2. O item 1 descumpre literalmente a exigência de escopo em `.aetherius-supernatural`, embora seja uma alteração intencional ativada durante a montagem e removida no unmount normal. O item 2 afeta interação por teclado e exige correção ou aceite explícito. Não há prova de regressão da ordenação dentro dos cenários testados. Homologação em jogo e backend seguem pendentes.

## Escopo e identificação

Raízes: `S = C:\Code\Aetherius - New Systems\Sobrenatural`; `C = C:\Code\Aetherius - OFICIAL\aetherius-client-main`. Foram lidos `C:\Code\Aetherius - New Systems\AGENTS.md`, `S\RETOMADA.md`, `S\docs\ETAPA-1.4_redesenho.md`, `S\docs\REVISAO-1.2_sol.md`, `S\ui-core-module\INTEGRACAO.md`, os arquivos runtime em `S\ui-core-module\modules\supernatural\`, `S\ui-core-module\preview\test-ordering.cjs`, `C\MERIDIAN-COMPATIBILIDADE.md` e os trechos pertinentes do loader/shell oficiais. A cópia local não é repositório Git, portanto commit indisponível. SHA-256 dos arquivos revisados:

| Arquivo em `S\ui-core-module\modules\supernatural\` | SHA-256 |
|---|---|
| `icons.js` | `e8b2f156aa912e6fbb683166110f0e93acb253e8b09495491504a9cb256402fe` |
| `supernatural-data.js` | `ad36ad1b465aaf951144e1e01b121a51b393c076277052b5643f83c514ffa354` |
| `supernatural-module.js` | `d282b5f0d114e7e485b4b792f5b22142d9c40b45b7e8fd87bf15e289f445f3ac` |
| `supernatural-module.css` | `70e120f5ba88e2c481640c114b9abb00abd66234f3cd6041c8315b56c550b664` |

## Apontamentos

### 1. MÉDIA — Estilos modificam o shell fora da raiz do módulo

**Evidência:** `S\ui-core-module\modules\supernatural\supernatural-module.css:2–6`; `supernatural-module.js:46–48,118`; `C\client\ui-core\frontend\index.html:81,103–112` e `shell.js:402–407`. O módulo adiciona `is-supernatural-workspace` ao shell e os seletores alteram diretamente `.world-shade`, `.workspace-frame`, `.workspace-topline`, `.module-content`, `.workspace-footer` e `.workspace-rule`, todos fora do `section.module-mount` que recebe `.aetherius-supernatural`. Há até `!important` no fundo. Isso é vazamento deliberado para o shell enquanto ativo e não atende ao requisito literal de escopo, mesmo com prefixo exclusivo e limpeza normal da classe. Pode colidir com futuras regras ou temas de workspace; não há evidência de que persista após unmount normal.

**Correção sugerida:** manter CSS do módulo sob `.aetherius-supernatural` e, se a mudança do shell for requisito de integração aprovado, formalizá-la como API/tema do shell com diff documentado e testes de restauração; alternativamente obter aceite explícito da exceção e testar navegação para outro módulo e falha de montagem. A remoção da classe depende de `unmount` ser retornado: uma exceção durante `mount` antes do retorno deixa o shell sem o respectivo cleanup, enquanto o catch do shell limpa a área (`shell.js:437–455`); usar tratamento de erro/cleanup garantido ao registrar a classe.

### 2. MÉDIA — Polling destrói foco de teclado e recria a tela sem mudança

**Evidência:** `S\ui-core-module\modules\supernatural\supernatural-module.js:74–76,89–91,93–101,117`. A cada 10 segundos o snapshot chama `render()` no início e no `finally`, que substitui todo o `innerHTML`, incluindo botão Atualizar e escolhas. Portanto um botão focado é removido do DOM mesmo com resposta de mesma revisão; o foco não é restaurado. Push novo também reconstrói o conteúdo. Isso interrompe navegação por teclado e reinicia o halo. O teste `S\ui-core-module\preview\test-ordering.cjs:7–8,29` usa DOM mínimo, sem foco real, e não cobre esse comportamento. É consequência comprovável da substituição de nós, sem ensaio em Meridian nesta revisão.

**Correção sugerida:** atualizar apenas os nós cujo estado mudou e manter estáveis os controles durante snapshots silenciosos; se a reconstrução for necessária, capturar identificador de foco antes da troca e restaurar somente se ainda aplicável, sem mover foco indevidamente. Acrescentar teste em DOM real: focar escolha/Atualizar, executar polling com revisão idêntica e push com revisão nova, conferir foco, rolagem e acionamento. Medir custo de layout/frame time no Meridian com a tela maior.

## Verificações

- **Estado e testes:** executados de `S`: `node ui-core-module\preview\test-ordering.cjs` e `node --check` para `icons.js`, `supernatural-module.js` e `supernatural-data.js`, todos com saída de sucesso. Os sete grupos cobrem ordenação resposta/evento, revisão repetida, remoção, unmount durante resposta, validação de disciplina e polling sem sobreposição. `revision` é validada e comparada em `supernatural-module.js:15–17,52–67`; `grantId` validado e enviado em `:21,29–32,107–108`; backend autoritativo ainda não existe. Os achados 4 e 5 da revisão 1.2 permanecem no código (`:56–62,69,97–100`) e foram aceitos pelo orquestrador para a Etapa 2; não são classificados aqui como novas regressões. As limitações do teste incluem ausência de DOM real e servidor.
- **Loader/manifesto:** `INTEGRACAO.md:10–23` propõe quatro assets locais, na ordem CSS e dados → ícones → adapter. Os quatro existem, prefixo/extensão conferem, e o loader oficial aceita até 16 (`C\client\ui-core\frontend\modules-loader.js:23–33`). Fonte e páginas de preview ficam fora do manifesto. O teste de build/empacotamento não foi executado.
- **Ciclo de vida:** no caminho normal `supernatural-module.js:110–118` remove o listener delegado e subscription, cancela o intervalo e esvazia o conteúdo; `disposed` impede render tardio. A animação CSS existe no pseudo-elemento removido com o DOM, sem timer JavaScript próprio. A exceção de montagem da classe no shell está no item 1.
- **XSS:** textos de estado/erro passam por `esc` (`supernatural-module.js:5,75–76,91`); data é apresentada apenas após regex numérica (`:41`); `curse`, XP e escolhas são validados (`:13–39`), e o markup novo deriva de catálogo local, índices numéricos e símbolos locais (`:77–91`). `icons.js:141–147` restringe classe e resolve desenhos locais; seus IDs nas chamadas do módulo são fixos ou derivados de maldição validada e catálogo local. Não identifiquei caminho de payload do servidor para HTML executável no markup revisado; não foi executado fuzzer ou teste de segurança do ambiente inteiro.
- **Movimento e CEF:** seletores `html[data-motion=reduced]` e `prefers-reduced-motion:reduce` desativam animações e transições em descendentes e pseudo-elementos (`supernatural-module.css:130–131`). O alvo documentado é Meridian 1.5.0 / CEF 152.0.6 (`C\MERIDIAN-COMPATIBILIDADE.md:23`). A inspeção não encontrou API claramente incompatível com esse alvo; não houve execução dentro do jogo nem perfil de compatibilidade do CEF real.
- **Performance:** contagem estática do markup: sem praga 2 SVGs; vampiro 21, lobisomem 22 e bruxa 19 (respectivamente 12, 126, 132 e 114 paths, seis por SVG; `icons.js:6–9,146–147`). As contagens são derivadas do código, não de instrumentação em runtime. Há um halo infinito por tela (`supernatural-module.css:33,124`), com `opacity`/`transform`; hover também usa transições. Não há filtro SVG nessa biblioteca. Polling reconstrói todo o DOM duas vezes mesmo sem alteração (item 2); 10 s não prova, por si só, perda de desempenho. Ainda faltam medição de frame time, custo de pintura e uso em jogo.

## Estado após revisão

Os apontamentos são propostos ao orquestrador; **aceite/rejeição e versão corrigida ainda pendentes**. Esta revisão não fez escrita no acervo, diário ou RETOMADA: consolidação e publicação cabem ao coordenador deste despacho. Não houve alteração em cliente oficial, servidor, painel ou modpack.

## Arbitragem do orquestrador (Opus 5.5, 2026-11-15)
1. **Aceito.** Preferência: levar o efeito visual para dentro de `.aetherius-supernatural`, sem tocar no shell. Se algum ajuste do shell for indispensável ao visual aprovado, ele fica como **exceção documentada** em INTEGRACAO.md, sem `!important`, com cleanup garantido mesmo se o `mount` falhar (try/finally) e com teste de navegação para outro módulo.
2. **Aceito.** O polling silencioso com revisão igual não re-renderiza (sem render de "carregando" no início). Render só quando o estado muda ou há mensagem a mostrar, preservando/restaurando o foco e a rolagem. Teste adicionado.
Correção pelo autor (Sol High), sem mudar a estética aprovada; confirmação pelo Terra High.

## Confirmação

**Data:** 15/11/2026. **Revisor:** Terra High / Codex; tarefa `task_0ec8a10dc64e`, dispatch `ctx_171ec7cc40bb`. Esta seção atualiza o parecer inicial sobre os arquivos corrigidos, sem modificá-los. Referências `S` e `C` mantêm as raízes absolutas definidas acima.

**1 — Escopo e ciclo de vida: CONFIRMADO, com exceções documentadas para o shell.** Em `S\ui-core-module\modules\supernatural\supernatural-module.css:2–7`, as quatro regras externas afetam `world-shade`, `workspace-frame`, `workspace-rule` e `module-content` somente sob `#aetherius-shell.is-supernatural-workspace`, sem `!important`; o padding interno está na raiz. Elas continuam sendo exceções explícitas ao isolamento estrito em `.aetherius-supernatural`, com propriedades e motivos registrados em `S\ui-core-module\INTEGRACAO.md:34–45`, e não uma API nova do shell. A montagem e o rollback utilizam `try/finally`; o cleanup idempotente encadeia `finally`, remove a classe que lhe pertence e preserva classe preexistente (`supernatural-module.js:48–49,151–190`). Executei o teste Node, que passou nos cenários de falha em subscribe/render/timer, navegação simulada para outra raiz e unsubscribe que lança erro. A fixture `preview\test-interaction.js:73–92` também define verificações de estilos computados na transição, e o autor relata sucesso no Chromium Orca; esta confirmação não reexecutou a fixture em navegador nem o shell real do jogo.

**2 — Polling, foco e rolagem: CONFIRMADO no escopo verificado.** `supernatural-module.js:70–87,110–119,122–142,184` preserva os nós durante pedido e não chama `render` em snapshot de revisão igual sem alteração de erro/mensagem; busy mantém a serialização. Em render necessário, captura o controle lógico e a rolagem dos ancestrais, restaura foco apenas se o controle equivalente permanecer habilitado e repõe a rolagem. Executei `node ui-core-module\preview\test-ordering.cjs` a partir de `S`: os 11 grupos passaram, incluindo ausência de render e rolagem preservada em polling igual. O teste de DOM real em `preview\test-interaction.js:35–70` cobre identidade de nós, foco/rolagem no polling e push, foco externo e serialização; os resultados em Chromium Orca constam do relatório do autor e não foram reexecutados nesta confirmação. Homologação no Meridian e medição de desempenho continuam pendentes.

**3 — Valores de Lobisomem: CONFIRMADO.** Comparei `S\docs\ESPECIFICACAO.md:63–76` com `S\ui-core-module\modules\supernatural\supernatural-data.js:42–49` e o cálculo de exibição em `supernatural-module.js:103–105`: humano e fera `[125,150,175,200,225,250]`; adicional exclusivo da fera `[150,200,250,300,350,400]`; total somado `[275,350,425,500,575,650]`; armadura `[150,200,250,300,350,400]`; resistência mágica `[10,15,20,25,30,35]%`. O teste Node passou para os seis estágios. São valores apresentados na UI, sem comprovação de aplicação dos efeitos pelo servidor/jogo.

**Identificação da versão confirmada:** SHA-256 de `supernatural-module.js` `ae3c5188d2fb733c5dc8b9eb50e9030509540045049e114bf4d1aad5d5872640`; de `supernatural-module.css` `1ab610d5f54844b3e023ac2640419f4307864e76eff0867443e0211a0bd3a924`; de `supernatural-data.js` `e32d4ed697a8b5752f641ec2205f45d0ff040b54c38fa74282f09fde2c311d9f`, coincidentes com `S\docs\ETAPA-1.4_redesenho.md`, seção Correções 1.4.

**Veredito final: APROVADO COM RESSALVAS DE INTEGRAÇÃO.** Os dois apontamentos desta revisão foram tratados na versão identificada; aceita-se a exceção de CSS do shell como integração documentada sujeita à arbitragem do orquestrador. A confirmação não substitui teste em Meridian/CEF, backend real ou a resolução dos achados 4–5 da revisão 1.2, já adiados à Etapa 2.

## Arbitragem final (Opus 5.5, 2026-11-15)
- As 4 exceções de CSS do shell foram **aceitas** como integração documentada (INTEGRACAO.md). Elas devem ser reavaliadas na homologação em Meridian; se o UI Core ganhar API de tema de workspace, migrar para ela.
- **Etapa 1 encerrada.**
