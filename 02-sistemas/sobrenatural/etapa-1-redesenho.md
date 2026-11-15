# Etapa 1.4 — Redesenho do lobo, eclipse e apresentação

**Data:** 15/11/2026. **Autor:** Sol High, worker Codex. **Resultado:** apresentação implementada e verificada no navegador Orca, com 23 PNGs novos. **Pendente:** revisão cruzada pelo orquestrador e homologação no Meridian/CEF real.

## Pedido e base

O dev solicitou um lobo fiel à referência, um Apoteótico com leitura de eclipse e liberdade para melhorar a composição, preservando textos, regras, Sovngarde e toda a lógica de estado. As edições ficaram exclusivamente em `C:\Code\Aetherius - New Systems\Sobrenatural\ui-core-module\` e `C:\Code\Aetherius - New Systems\Sobrenatural\docs\`.

A base foi a Etapa 1.3, já acrescida das correções de revisão monotônica, `grantId`, validação e polling. Foram relidos `C:\Code\Aetherius - New Systems\Sobrenatural\RETOMADA.md`, `docs\DIRECAO-DE-ARTE.md`, o módulo, a prévia e `preview\test-ordering.cjs`. O contexto de `AGENTS.md` permanece aplicável. Esta pasta não possui `.git`; não existe commit local para identificar a entrega. Os hashes finais estão ao fim deste documento.

Foram abertas e examinadas estas imagens originais:

- `C:\Code\Aetherius - New Systems\referencia\icone lobisomem bruxa e vampiro.png` — perfil do lobo central;
- `C:\Code\Aetherius - New Systems\referencia\icones estilizados.png` — lobo uivando e lobo com runas da primeira linha, além do traço geral;
- `C:\Code\Aetherius - New Systems\referencia\VAMPIRISMO PROGRESSÃO NOMES E TEXTO.png` — eclipse do cartão 06.

O [acervo oficial](https://github.com/pablo-rodrigues1/AETHERIUS-ACERVO-DE-INFORMACOES) também foi consultado na `main`: índice, README e direção de arte do Sobrenatural e ADR-0003. As dependências do sistema foram consultadas na sessão anterior. O redesenho autorizado pelo dev atualiza a composição visual anterior; não redefine a progressão ou qualquer regra de jogo.

## Lobo — silhueta e família

O antigo desenho compacto foi substituído por um perfil redesenhado nas proporções da referência: focinho à esquerda e inclinado para cima, boca alta e aberta, arcadas com dentes separados, orelha longa recuada, nuca desfiada e linha do pescoço alongada. A cabeça permanece vazada, com traço branco e contratraços mais finos. O olho concentra o pigmento âmbar, conforme a identidade da maldição; na referência original ele é vermelho.

Os pontos do desenho foram posicionados considerando o recorte original de aproximadamente 220 × 220 pixels e normalizados para o espaço vetorial da biblioteca. Isso conserva a relação entre focinho, boca, orelha, nuca e pescoço. A comparação visual foi feita com a referência ao lado, seguida de ajuste no peso do contorno. O desenho não é uma reprodução pixel a pixel: o SVG possui linhas mais nítidas que o bitmap de baixa resolução fornecido.

No hero de 1920×1080, o hexágono mede 280 px e a área do lobo ocupa 66% dele, aproximadamente 185 px. Antes, o símbolo ocupava 52% de um hexágono de 180 px, aproximadamente 94 px. A cor fica no olho e nos sinais dos derivados, sem preencher a cabeça. O halo permanece sutil e não utiliza filtro SVG.

Os IDs `werewolf_4` (Gama), `werewolf_6` (Alfa), `howl_hunt`, `howl_terror` e `howl_fury` agora compartilham o mesmo perfil, diferenciados por arcos de uivo, sinais de terror/fúria e sinal lunar do Alfa. A família fica coerente com o símbolo principal. Os IDs de estágio e seus textos permanecem iguais.

## Apoteótico — eclipse de sangue

`vampire_6` deixou de usar um olho oval como motivo principal. O novo desenho usa disco escuro/vazado, crescente carmesim à esquerda, contorno circular duplo parcial e uma estrela de quatro pontas no centro. Oito raios principais curtos e contratraços secundários compõem a coroa externa.

O contraste foi conferido em 48, 96 e 192 px na página comparativa. Em escala pequena, permanecem identificáveis o crescente e a estrela. A referência mostra o cartão bloqueado, com pigmento atenuado; a comparação isolada mostra o desenho na cor ativa. No módulo, o mesmo SVG recebe a atenuação cinza quando o estágio está bloqueado.

## Composição e leitura

A tela passa a ter duas colunas em desktop:

- **Identidade e progresso:** símbolo grande, sobretítulo, nome da maldição, lema, data, estágio atual, trilha com os seis ícones, XP dentro do estágio, barra e XP total/próximo estágio.
- **Marcos e dons:** grade de seis cartões mais leves, seguida por Disciplinas, Vertentes ou Uivos/Instintos.

A trilha usa uma lista ordenada, `aria-current="step"` no estágio atual e títulos com nome/limiar de XP. É informativa; não cria botões, seleção local, ações de rede ou novos pedidos. A barra conserva o mesmo percentual de XP **dentro do estágio**, calculado pela lógica anterior. O watermark do estágio atual é apenas decorativo.

Os cartões mantêm todos os nomes, descrições, frases, estados e limiares. A hierarquia agora distingue número/ícone, estado, nome e descrição; as frases ficam em tom secundário. A borda colorida destaca o estágio atual, enquanto os concluídos usam contorno neutro e detalhe no ícone. Nenhum texto foi trocado por reticências, escondido em acordeão ou removido para caber na tela.

Os dons recebem separação mais clara, ritmo consistente entre cartões e magnitudes no rodapé. Os três uivos e os três instintos conservam os mesmos textos, condições e números. O botão Atualizar, os estados de carregamento/erro e o aviso de ausência de praga continuam disponíveis.

Sovngarde continua herdada do shell. O preview conserva o TTF e a declaração local existentes, sem CDN. As cores foram suavizadas na apresentação: carmesim `#d63a59`, âmbar `#cca05b`, violeta `#ad8aca`; os fundos de pigmento usam baixa opacidade, aproximadamente 6,5%. Texto e contorno principais permanecem brancos sobre preto transparente no módulo.

Em 1920×1080, os sete estados cabem integralmente na captura, incluindo dons e notas. Em 1280×720, identidade/progresso e os seis marcos ficam legíveis; os dons são alcançados pela rolagem vertical normal da área de conteúdo. Há seis capturas complementares dessa parte inferior. Não foi reduzida a fonte a ponto de esconder textos para forçar uma tela inteira em 720p.

O halo tem uma oscilação lenta de opacidade/escala; escolhas disponíveis têm transição discreta no hover. `html[data-motion=reduced]` e `prefers-reduced-motion:reduce` desativam animações e transições. Não há animação de entrada de todos os cartões a cada polling.

## Arquivos

Os caminhos relativos desta tabela partem de `C:\Code\Aetherius - New Systems\Sobrenatural\ui-core-module\`.

| Arquivo | Entrega |
|---|---|
| `modules\supernatural\icons.js` | Lobo, derivados e eclipse redesenhados; atributo estático `data-glyph` para ajustes de traço. |
| `modules\supernatural\supernatural-module.js` | Markup de duas colunas, trilha informativa e composição do painel de progresso. |
| `modules\supernatural\supernatural-module.css` | Hierarquia, ritmo, escalas, cores, responsividade e movimento reduzido. |
| `preview\index.html` | Espaçamento da moldura de prévia, mantendo os sete estados e os recursos de ordenação existentes. |
| `preview\icons.html` | Prancha reorganizada para mostrar 38 desenhos, nas três cores, em 1920×1080. |
| `preview\comparison.html` | Comparações locais de lobo e eclipse, com referência, desenho isolado e aplicação/escala. |
| `preview\references\main-icons.png` | Cópia binária da referência dos três símbolos principais. |
| `preview\references\vampire-progression.png` | Cópia binária da referência de progressão vampírica. |
| `preview\screens\` | 23 PNGs atuais e `validacao-etapa-1.4.json`. |
| `INTEGRACAO.md` | Nota de apresentação, rolagem e separação entre runtime e evidências. |

As cópias de referência tiveram seus hashes comparados aos originais, com igualdade. Os recortes da comparação são feitos na página HTML/SVG; os bitmaps originais não foram editados. Essas imagens e as páginas de evidência não entram no manifesto. A lista de assets de runtime continua a mesma da Etapa 1.3.

O relatório histórico `C:\Code\Aetherius - New Systems\Sobrenatural\docs\ETAPA-1.3_icones-fonte.md` recebeu uma nota sobre a substituição das evidências. As 14 capturas antigas foram substituídas, conforme solicitado; a pasta contém apenas as 23 capturas atuais. `supernatural-data.js`, `test-ordering.cjs`, fontes, cliente oficial, servidor, painel e modpack não foram editados.

## Preservação da lógica

Antes da edição, foram calculados hashes de trechos de código, com quebras de linha normalizadas em LF. Após o redesenho, os mesmos hashes foram conferidos como iguais:

| Trecho | SHA-256 antes e depois |
|---|---|
| De `function stageOf` até antes de `function render` — cálculo/validação, registro, estado, recebimento, revisão e falha | `3588f9f58c77c6ac0c871d3d170cdacca44ab189819d0c082853ee109144694a` |
| De `async function request` até o fim — transporte, payload com `grantId`/`revision`, clique, subscription, polling e unmount | `c643e099a0f236cd332cfcaa79110d6b50384282d151032696661aab04245c33` |
| Conteúdo de `supernatural-data.js`, normalizado em LF | `20936b592a8e64e2700bc004995678482fe0a664eb09162bff29c4a985b21a3a` |

As alterações JavaScript do módulo ficaram no markup de `render`. As condições de escolha `!invalidState`, `canChoose`, limite, seleção e `busy` foram mantidas. A trilha deriva do mesmo `i` e dos mesmos dados já usados pelos cartões, sem alterar o estado. Nenhuma palavra-chave de servidor foi acrescentada à apresentação.

## Testes

Executados com sucesso, a partir de `C:\Code\Aetherius - New Systems\Sobrenatural\`:

```powershell
node --check ui-core-module\modules\supernatural\icons.js
node --check ui-core-module\modules\supernatural\supernatural-module.js
node --check ui-core-module\modules\supernatural\supernatural-data.js
node ui-core-module\preview\test-ordering.cjs
```

O teste de ordenação foi executado antes e depois, sem editar o teste. Seus sete grupos passaram: resposta nova após evento antigo/payload; resposta e evento antigos após estado novo; revisão repetida; remoção durante pedido; resposta após unmount/limpeza do timer; disciplina duplicada/desconhecida e recuperação por snapshot idêntico; polling de 10 segundos sem sobreposição.

Também passaram a análise sintática dos scripts inline dos três HTMLs com `node:vm` e a renderização dos 38 IDs únicos da biblioteca.

As 14 combinações dos sete cenários com as duas resoluções foram verificadas no navegador integrado Orca. `preview\screens\validacao-etapa-1.4.json` registra resolução, altura de conteúdo, cartões, trilha, escolhas, fonte, ausência de textos, overflow e console. Resultados:

- Sovngarde carregada em todos os cenários;
- zero textos ausentes entre todos os nomes, descrições e frases dos estágios/dons mostrados;
- zero transbordamento horizontal da página e dos blocos de texto verificados;
- zero entradas no console em todos os cenários;
- seis cartões e seis posições de trilha por maldição; zero cartões na tela sem praga;
- quatro escolhas disponíveis no Apoteótico, três em Bruxa Neófito e zero nos demais mocks;
- `animationName: none` no halo com movimento reduzido;
- altura de documento igual a 1080 nos sete cenários a 1920×1080, sem rolagem vertical necessária;
- prancha com 38 figuras e 114 SVGs; consoles da prancha e da comparação vazios.

Os 23 PNGs tiveram cabeçalho e dimensões conferidos e foram abertos para inspeção visual. As capturas usam movimento reduzido para registrar uma apresentação estável.

## Capturas

Pasta completa: `C:\Code\Aetherius - New Systems\Sobrenatural\ui-core-module\preview\screens\`.

Cada sufixo abaixo existe com os prefixos `1920x1080-` e `1280x720-`:

| Estado | Sufixo |
|---|---|
| Sem praga | `01-sem-praga.png` |
| Vampiro Neófito | `02-vampiro-neofito.png` |
| Vampiro Ancião | `03-vampiro-anciao.png` |
| Vampiro Apoteótico | `04-vampiro-apoteotico.png` |
| Lobisomem Gama | `05-lobisomem-gama.png` |
| Bruxa Neófito | `06-bruxa-neofito.png` |
| Bruxa Alto Teurgo | `07-bruxa-alto-teurgo.png` |

Os seis estados com maldição têm ainda o complemento `1280x720-<mesmo-nome>-dons.png`, que mostra a parte inferior por rolagem normal. As três evidências adicionais são:

- `1920x1080-08-prancha-icones.png`;
- `1920x1080-09-comparativo-lobo.png`;
- `1920x1080-10-comparativo-apoteotico.png`.

Para reproduzir, abrir `C:\Code\Aetherius - New Systems\Sobrenatural\ui-core-module\preview\index.html`, selecionar o cenário e configurar a viewport. `icons.html` mostra a prancha; `comparison.html` permite alternar lobo/eclipse. Todas as dependências dessas páginas são locais.

## Limitações e revisão

A inspeção ocorreu no Chromium do Orca, com os mocks existentes. Ainda falta homologar dentro do Meridian/CEF, incluindo transparência sobre o jogo, escala da UI, rolagem do workspace e antialiasing real. Não foi feita medição de desempenho em jogo; a apresentação adiciona seis SVGs da trilha e um watermark por tela com maldição, sem filtros SVG ou animações de layout contínuas.

A revisão cruzada desta Etapa 1.4 deve ser feita por outro modelo sob coordenação do orquestrador. Este worker não abriu auxiliares, conforme o despacho; não há parecer independente presumido neste relatório. A versão entregue, os achados futuros e os aceites/rejeições devem ser documentados na consolidação.

A publicação no acervo e a atualização de RETOMADA/diário geral ficam com o orquestrador, pois o despacho restringiu a escrita a `ui-core-module/` e `docs/`. Não foi feita escrita remota. Este relatório está pronto para ser incorporado ao acervo.

## Identificação da entrega

SHA-256 dos arquivos finais:

```text
modules/supernatural/icons.js
e8b2f156aa912e6fbb683166110f0e93acb253e8b09495491504a9cb256402fe
modules/supernatural/supernatural-module.js
d282b5f0d114e7e485b4b792f5b22142d9c40b45b7e8fd87bf15e289f445f3ac
modules/supernatural/supernatural-module.css
1ad9cf3bbccc5094337d81199f6f86a758dfe6b8788d02fa89f7964b5a655000
preview/test-ordering.cjs (não editado)
0ddf06df17a6e6c9523b13b003a0f72419a2944f5ac2a4ac1bb8c716d0b9a5d8
```

## Ajuste final — folga do lobo no medalhão

Em 15/11/2026, após aprovação da interface e dos ícones pelo dev, foi ajustada somente a regra do símbolo principal do Lobisomem para `left:26%; top:27%; width:54%; height:54%`. A proposta 24%/24% ainda deixava o focinho a aproximadamente 0,8 px do contorno interno no medalhão de 280 px. O deslocamento final foi inspecionado em 1920×1080 (280 px), 1280×900 (220 px), 900×800 (200 px), 1440×900 (245 px) e 1280×720 (160 px pela regra específica de altura). O lobo permanece inteiro, com folga visível e centralização óptica; a menor distância entre os contornos vetoriais ficou entre 6,2 e 10,8 px. As duas capturas `1920x1080-05-lobisomem-gama.png` e `1280x720-05-lobisomem-gama.png` foram substituídas. Gama/Alfa nos cartões não exigiram alteração. O SHA-256 vigente do CSS passa a ser `70e120f5ba88e2c481640c114b9abb00abd66234f3cd6041c8315b56c550b664`; o hash acima registra a versão anterior.
