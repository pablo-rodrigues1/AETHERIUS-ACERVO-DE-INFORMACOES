# Etapa 1.3 — Ícones estilizados e fonte Sovngarde

**Data:** 15/11/2026. **Autor:** Sol High, worker Codex. **Escopo:** apresentação do módulo Sobrenatural e prévias locais. **Estado:** implementação e verificação visual entregues; revisão cruzada e homologação no cliente real pendentes com o orquestrador.

## Base consultada

Pasta de trabalho: `C:\Code\Aetherius - New Systems`. Não existe `.git` nessa pasta; não há commit local a informar. A base funcional foi a entrega descrita em `C:\Code\Aetherius - New Systems\Sobrenatural\docs\ETAPA-1.2_telas.md`. Foram lidos `AGENTS.md`, `Sobrenatural\RETOMADA.md`, `Sobrenatural\docs\DIRECAO-DE-ARTE.md`, o módulo e a prévia existentes.

As cinco referências abaixo foram abertas como imagens e examinadas visualmente, antes do desenho e durante a comparação final:

- `C:\Code\Aetherius - New Systems\referencia\icone lobisomem bruxa e vampiro.png`;
- `C:\Code\Aetherius - New Systems\referencia\icones estilizados.png`;
- `C:\Code\Aetherius - New Systems\referencia\VAMPIRISMO EXEMPLO INTERFACE.png`;
- `C:\Code\Aetherius - New Systems\referencia\VAMPIRISMO PROGRESSÃO NOMES E TEXTO.png`;
- `C:\Code\Aetherius - New Systems\referencia\VAMPIRISMO - DISCIPLINAS.png`.

Cliente consultado somente para leitura: `C:\Code\Aetherius - OFICIAL\aetherius-client-main\client\ui-core\frontend\styles.css` e `frontend\fonts\`. A cópia oficial não fornece um commit Git nesta sessão. Sua declaração global usa Sovngarde, peso 400–700, `font-display:block`, `font-synthesis:none` e imposição da família em todos os elementos. Nenhuma fonte foi extraída dos SWFs.

Também foram consultados na `main` do [acervo oficial](https://github.com/pablo-rodrigues1/AETHERIUS-ACERVO-DE-INFORMACOES): `INDICE.md`, os READMEs de Sobrenatural, Lobisomem, UI Core, Painel ADM e GameplayCore, o índice de ADRs e o ADR-0003. A entrega não muda design de progressão, mecânicas ou regras de autoridade.

## Arquivos entregues

Todos os caminhos relativos desta seção partem de `C:\Code\Aetherius - New Systems\Sobrenatural\ui-core-module\`.

| Arquivo | Alteração |
|---|---|
| `modules\supernatural\icons.js` | Nova biblioteca com 38 desenhos SVG inline e catálogo com IDs/rótulos. |
| `modules\supernatural\supernatural-module.js` | Consome a biblioteca, usa seis ícones distintos por maldição e mostra os ícones de uivos/instintos junto aos dados existentes. |
| `modules\supernatural\supernatural-module.css` | Define as camadas de traço, pigmento, brilho e métricas para Sovngarde; remove Segoe UI e Georgia do módulo. |
| `preview\index.html` | Carrega `icons.js`, declara a mesma família Sovngarde e conserva os sete cenários existentes. |
| `preview\icons.html` | Prancha de todos os 38 desenhos, cada um em carmesim, âmbar e violeta: 114 SVGs. |
| `preview\fonts\Sovngarde-Bold.ttf` | Cópia binária da fonte existente no cliente, com 70.136 bytes. |
| `preview\fonts\Sovngarde-OFL.txt` | Licença copiada junto da fonte. |
| `preview\screens\*.png` | Sete capturas principais, seis complementares de dons e uma prancha: 14 PNGs. |
| `INTEGRACAO.md` | Lista de scripts atualizada e dependência explícita de `icons.js`. |

`supernatural-data.js` não foi editado por esta tarefa. Os métodos de transporte, normalização de estado, cálculo de estágio, persistência e tratamento de escolhas não foram alterados. A mudança na seção de Lobisomem apenas coloca os mesmos três nomes de uivos em uma faixa com desenhos, preservando as condições e os valores mostrados anteriormente.

## Biblioteca visual

`window.AETHERIUS_SUPERNATURAL_ICONS` expõe `render(id, classeCSS)` e `catalog`. São desenhos próprios em coordenadas vetoriais; não dependem de CDN, fonte de ícones, bitmap remoto ou carregamento de `.svg`. O registro fica congelado; classes aceitam somente letras, números, espaços, hífen e sublinhado. IDs desconhecidos produzem erro explícito, em vez de trocar silenciosamente por um símbolo genérico.

Cada desenho separa seis camadas: halo branco, traço branco, contratraço fino, halo colorido, pigmento e pincel afilado. As linhas têm terminação/arremate arredondados; as curvas e os pequenos traços assimétricos dão variação ao contorno. O brilho é uma repetição de baixa opacidade com linha mais larga, sem filtros SVG, animação ou IDs compartilhados entre instâncias. Preenchimentos ficam restritos a detalhes pequenos, como lâminas dos caninos e o centro de alguns glifos; não há silhueta inteira sólida.

O pigmento usa `currentColor`, herdado de `--sn-accent`. Permanecem as cores existentes no módulo: carmesim `#e5365d`, âmbar `#d7a45b`, violeta `#b18bda`. Os marcos bloqueados ficam cinza e atenuados. O olho do lobo e a estrela da bruxa seguem a cor da respectiva maldição, conforme o despacho; nas imagens originais esses detalhes eram vermelho e dourado, respectivamente.

| Conjunto | IDs / desenho |
|---|---|
| Principais — 3 | `vampire`: arcadas e quatro caninos afilados; `werewolf`: cabeça de perfil com boca aberta, dentes, olho e pelo irregular; `witch`: capuz, figura vazia e estrela sobre a cabeça. |
| Vampiro — 6 | `vampire_1` a `vampire_6`: gota; escudo com presas; morcego e névoa; coroa com gota; enxame e escudo espinhal; olho cercado de raios solares. |
| Lobisomem — 6 | `werewolf_1` a `werewolf_6`: lua; garras; pegada; lobo uivando; três lobos da matilha; lobo coroado. |
| Bruxa — 6 | `witch_1` a `witch_6`: vela; grimório; crânio; estrela ritual em círculo; olho do véu; coroa e véu. |
| Disciplinas — 5 | `celeridade`, `fortitude`, `potencia`, `feiticaria_do_sangue`, `tenacidade`: variações de estrela, losango e cruz de pequenos losangos da referência. |
| Vertentes — 3 | `mortui`: crânio e ossos; `daemones`: portal e fogo; `tenebrae`: olho e sombra. |
| Uivos — 3 | `howl_hunt`, `howl_terror`, `howl_fury`: cabeça em chamada, focinho do terror e fera em fúria. |
| Instintos — 3 | `instinct_life`, `instinct_armor`, `instinct_resist`: vida/vigor, couro/armadura e resistência mágica. |
| Molduras/ornamento — 3 | `frame`: duplo hexágono com espinhos; `frame_empty`: duplo hexágono vazio com vértices interrompidos; `ornament`: divisor com três losangos. |

## Fonte e integração

O módulo mantém apenas `font-family:inherit`; títulos, citações, botões e valores não escolhem outra família. O preview declara:

```css
@font-face {
  font-family: Sovngarde;
  src: url('./fonts/Sovngarde-Bold.ttf') format('truetype');
  font-style: normal;
  font-weight: 400 700;
  font-display: block;
}
* { font-family: Sovngarde, Georgia, serif !important; }
```

A declaração com alternativas acima pertence à prévia e reproduz a regra global do cliente. O módulo herda a família que o shell já fornece. As métricas foram revistas após a primeira captura: título principal entre 34–56 px; nomes dos marcos em 23 px; descrições e citações dos marcos em 17 px; notas em 16 px; magnitudes e valores entre 22–25 px. O espaçamento dos rótulos foi reduzido onde a fonte exigia. Como no cliente, a prévia desativa síntese de fontes: as citações não dependem de um arquivo itálico inexistente.

SHA-256 da fonte local e da fonte oficial, conferidos como iguais:

```text
A4ABB4F2CF49F7E52D87F45D59B1FE2592D464C94F049094F66520072317C2EF
```

O manifesto proposto deve carregar os scripts nesta ordem:

```text
modules/supernatural/supernatural-data.js
modules/supernatural/icons.js
modules/supernatural/supernatural-module.js
```

O CSS continua `modules/supernatural/supernatural-module.css`. Os quatro assets do manifesto têm extensão `.js`/`.css`; a fonte da prévia não entra no manifesto. `INTEGRACAO.md` já contém o diff correto. Se `icons.js` não for carregado antes do módulo, há erro explícito de dependência. Não foi aplicado patch no cliente oficial.

## Comparação visual e iterações

As presas principais seguem as duas arcadas separadas, os dentes curtos e as quatro pontas coloridas da imagem. O lobo mantém perfil aberto, dentes pequenos, olho pontual e contorno quebrado de pelo. A bruxa conserva o capuz vazio, a estrela superior e as linhas laterais de ombro/vestes. O duplo hexágono segue a composição do cabeçalho vampírico; a tela sem praga recebe uma versão própria com pontas partidas e centro desocupado.

Na progressão vampírica, foram preservados os seis motivos identificáveis na referência. O Ancilla recebeu linhas de névoa abaixo do morcego; o Suserano recebeu morcegos pequenos em torno da espinha; o Apoteótico combina olho e disco solar. As Disciplinas usam glifos compactos dentro de molduras quadradas, em lugar dos antigos desenhos de raio, mão e chama. Os conjuntos de Lobisomem e Bruxa foram criados no mesmo vocabulário de linhas e detalhes pontuais, sem repetir o mesmo símbolo entre estágios.

A primeira inspeção com Sovngarde mostrou texto visualmente pequeno; descrições, citações, notas e título foram ampliados. A prancha foi reorganizada em uma linha por conjunto para exibir todos os ícones e suas três cores em uma única captura de 1600 × 1300. Os primeiros ensaios de captura longa do navegador apresentaram repetição de faixas; os PNGs finais foram substituídos por capturas normais de 1600 × 1080, com uma segunda captura de dons para cada estado com maldição. A prancha final não usa montagem ou edição de imagem.

Os SVGs são redesenhos inspirados nas referências, não vetorização exata ou reprodução pixel a pixel. O traço branco foi mantido deliberadamente mais presente que o pigmento, como solicitado. A referência de progressão usa outra fonte; a entrega usa a Sovngarde do cliente, mesmo quando isso muda as quebras de linha.

## Verificação e evidências

Foram executados com sucesso:

```powershell
node --check 'C:\Code\Aetherius - New Systems\Sobrenatural\ui-core-module\modules\supernatural\icons.js'
node --check 'C:\Code\Aetherius - New Systems\Sobrenatural\ui-core-module\modules\supernatural\supernatural-module.js'
node --check 'C:\Code\Aetherius - New Systems\Sobrenatural\ui-core-module\modules\supernatural\supernatural-data.js'
```

Também foram analisados com `node:vm` os scripts inline dos dois HTMLs. Uma execução isolada da biblioteca conferiu 38 IDs únicos, renderização de todos os IDs e ausência de URLs externas, scripts, `foreignObject` ou IDs SVG compartilhados na saída. Não foram adicionados arquivos de teste que apenas espelhariam os desenhos.

As duas prévias foram abertas via `file://` no navegador integrado Orca. Todos os cenários carregaram a fonte (`document.fonts.check('16px Sovngarde') === true`), e o estilo computado mostrou `Sovngarde, Georgia, serif`. Os sete estados apresentaram zero rolagem horizontal a 1600 px. As contagens observadas foram:

| Cenário | Cartões de estágio | SVGs do módulo |
|---|---:|---:|
| Sem praga | 0 | 2 |
| Vampiro Neófito, Ancião e Apoteótico | 6 cada | 14 cada |
| Lobisomem Gama | 6 | 15 |
| Bruxa Neófito e Alto Teurgo | 6 cada | 12 cada |

A prancha mostrou 38 figuras e 114 SVGs. Os consoles das duas páginas ficaram vazios. Um clique em Fortitude no cenário Apoteótico produziu `{"kind":"discipline","id":"fortitude"}` na prévia, mantendo apenas uma disciplina marcada como escolhida. No mesmo cenário, foram verificadas larguras de 850 e 390 px sem rolagem horizontal; isso não substitui homologação em todas as resoluções do cliente.

Todos os PNGs abaixo estão em `C:\Code\Aetherius - New Systems\Sobrenatural\ui-core-module\preview\screens\` e foram abertos para inspeção visual. As capturas `-dons` complementam a parte inferior de cada tela; a barra de seleção pertence somente ao preview.

| Estado | Captura principal | Complemento |
|---|---|---|
| Sem praga | `01-sem-praga.png` | — |
| Vampiro Neófito | `02-vampiro-neofito.png` | `02-vampiro-neofito-dons.png` |
| Vampiro Ancião | `03-vampiro-anciao.png` | `03-vampiro-anciao-dons.png` |
| Vampiro Apoteótico | `04-vampiro-apoteotico.png` | `04-vampiro-apoteotico-dons.png` |
| Lobisomem Gama | `05-lobisomem-gama.png` | `05-lobisomem-gama-dons.png` |
| Bruxa Neófito | `06-bruxa-neofito.png` | `06-bruxa-neofito-dons.png` |
| Bruxa Alto Teurgo | `07-bruxa-alto-teurgo.png` | `07-bruxa-alto-teurgo-dons.png` |
| Prancha completa | `08-prancha-icones.png` | 1600 × 1300, 38 desenhos nas três cores |

## Limitações, revisão e consolidação

- A validação visual foi feita no Chromium do Orca; falta executar dentro do Meridian/CEF e conferir escala, antialiasing e rolagem no cliente real.
- O desenho em camadas adiciona poucos paths por ícone e não usa filtros ou animações; não foi feita medição de desempenho em jogo.
- Não foram alterados servidor, painel, modpack, cliente oficial ou contrato de dados. O preview permanece simulado, sem efeitos de jogo.
- A revisão cruzada desta entrega ainda precisa ser designada pelo orquestrador a outro modelo; não há parecer independente inventado neste relatório. Os achados, aceites/rejeições e versão final devem ser registrados após esse parecer.
- RETOMADA, diário geral e publicação no acervo pertencem à consolidação do orquestrador. Este despacho limitou as edições a `ui-core-module/` e `docs/`, não autorizou outros workers e não realizou escrita remota no acervo. Este relatório está pronto para a consolidação documental.

Hashes SHA-256 para identificar a entrega visual antes da revisão cruzada:

```text
modules/supernatural/icons.js
05B01BDC3D73AFFF5550E89C2C238BCC878A6AB6EEF397C8E75A1DCCE11907B8
modules/supernatural/supernatural-module.js
9A8ED9A741E962E1314CAF459EB627B0BC0A3EA3BF295D673F24307C24D89218
modules/supernatural/supernatural-module.css
133ABA0794812F7459EE4122F6AECC0F5F7D0AE43D860201706B7C8A9A6196CA
```
