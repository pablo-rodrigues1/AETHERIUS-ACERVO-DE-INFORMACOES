# Direção de arte — Módulo Sobrenatural

Definida pelo dev em 2026-11-15.

## Regras

- **Base visual:** seguir o modelo do sistema de Classes do [AetheriusGameplayCore](https://github.com/pablo-rodrigues1/AetheriusGameplayCore). O padrão é **branco sobre fundo preto transparente**: tipografia serifada em caixa alta nos títulos, rótulos pequenos espaçados, linhas finas e losangos como divisores.
- **Cor de destaque por maldição**, usada só em detalhes (bordas do cartão ativo, barra de XP, rótulos, partes do ícone):
  - Vampiro → **Vermelho Carmesim**
  - Lobisomem → **Âmbar**
  - Bruxa → **Violeta**
  - Tela sem maldição → sem cor de destaque (só branco/cinza).
- **Ícones:** sempre estilizados, com traço branco fino à mão e uma cor de detalhe pontual. Nada de ícones sólidos ou genéricos.

## Referências (`C:\Code\Aetherius - New Systems\referencia\`)

| Arquivo | Conteúdo |
|---|---|
| `icone lobisomem bruxa e vampiro.png` | **Os 3 ícones principais**: presas (vampiro, detalhes carmesim), cabeça de lobo uivando (lobisomem, olho vermelho), figura encapuzada com estrela (bruxa, estrela dourada). |
| `icones estilizados.png` | Biblioteca de estilo dos ícones (traço branco, detalhe colorido; escudos, mãos, runas). Usar como guia de traço para os ícones de estágio, poderes, perks e disciplinas. |
| `VAMPIRISMO EXEMPLO INTERFACE.png` | Cabeçalho da tela de Vampiro (ver abaixo). |
| `VAMPIRISMO PROGRESSÃO NOMES E TEXTO.png` | Painel de progressão do Vampiro (ver abaixo). |

## Análise — interface de referência do Vampirismo

### Cabeçalho
- No alto à esquerda: `AETHERIUS · SISTEMAS` (rótulo pequeno) e `SOBRENATURAL` (título). Abaixo, a trilha `Andarilho / SOBRENATURAL`. "Andarilho" parece ser o nome do personagem ou da classe; confirmar.
- **Decisão:** a trilha mostra apenas `Sobrenatural`.
- À esquerda, o ícone principal (presas) dentro de um **duplo hexágono** de linhas carmesim com pequenas pontas/espinhos.
- À direita, centralizado:
  - sobretítulo carmesim `CORRUPÇÃO DO SANGUE`
  - título grande `VAMPIRISMO`
  - lema em itálico: *"A eternidade cobra seu preço em sangue."*
  - divisor de linha com losangos
  - `PORTADOR DESDE 02/11/2026` (data da setagem, que o servidor precisa guardar).

### Painel de estágio atual
- `ESTÁGIO 04 / 06`, nome do estágio (`ANCIÃO`).
- À direita: XP dentro do estágio, `148.387 / 516.129 XP`, com rótulo `XP NESTE ESTÁGIO`.
- Barra de progresso carmesim.
- Rodapé: `XP TOTAL 600.000 / 2.000.000` e `PRÓXIMO · SUSERANO`.
- Fundo do painel com leve gradiente carmesim e marca d'água do ícone.

### Grade de 6 cartões (3×2)
Cada cartão tem: número `0N / 06` e ícone de estágio à esquerda; estado (`CONCLUÍDO` / `ESTÁGIO ATUAL` / `A DESBLOQUEAR`), nome, descrição, frase em itálico e, no rodapé, a XP limiar com um losango.
- Concluído: borda carmesim e ícone carmesim.
- Atual: borda carmesim realçada, fundo levemente carmesim, losango preenchido.
- A desbloquear: borda e ícone cinza.

| # | Estágio | Ícone | Descrição | Frase | Limiar |
|---|---|---|---|---|---|
| 01 | NEÓFITO | gota | Visão vampírica e resistência a doenças marcam o despertar. | *Uma gota basta para que a noite aprenda seu nome.* | 0 XP |
| 02 | VAMPIRO PLENO | escudo com presas | Sedução, força desmedida e escolha da primeira disciplina. | *O primeiro juramento de sangue desenha o destino nas sombras.* | 20.000 XP |
| 03 | ANCILLA | morcego | Forma de névoa e forma de morcegos ampliam o domínio da noite. | *Entre névoa e asas, seu corpo esquece os próprios limites.* | 60.000 XP |
| 04 | ANCIÃO | coroa | Celeridade sobrenatural e drenagem vampírica. | *O tempo se curva diante de passos que já não envelhecem.* | 190.000 XP |
| 05 | SUSERANO | morcegos/enxame | Enxame devorador e reanimação vampírica. | *Sob sua sombra, até a morte aguarda uma ordem.* | 610.000 XP |
| 06 | APOTEÓTICO | sol/olho radiante | O ápice do legado abre a escolha da segunda disciplina. | *Do último sacramento, emerge um ser divino.* | 2.000.000 XP |

- Nota de rodapé: *"Marcos previstos para o vampirismo. Poderes e efeitos dependem de suporte do servidor."*
- A barra de botões no rodapé (`Maldição`, `Vampiro sem XP`, `Neófito` … `Licantropia`) é um **seletor de pré-visualização/debug** da referência. Não faz parte da tela final do jogador.
- Os limiares da tabela acima já seguem a **curva oficial revisada** (ESPECIFICACAO §1). Os números da imagem (64.516…, 148.387 / 516.129) eram da curva anterior e devem ser ignorados.

### Seção de Disciplinas (`VAMPIRISMO - DISCIPLINAS.png`)
Fica abaixo da grade de estágios, separada por uma linha carmesim. Também aparece em `referencia/`.
- Sobretítulo carmesim `DONS DO SANGUE`, título `DISCIPLINAS`; à direita, `UMA EM VAMPIRO PLENO · OUTRA EM APOTEÓTICO`.
- Contador: `N de M disciplina(s) registrada(s) pelo servidor.`
- 5 cartões em linha. Cada um tem um ícone pequeno em moldura quadrada, nome, atributo, frase em itálico e a magnitude no estágio atual (`+30% NO ESTÁGIO`). A escolhida tem borda e fundo carmesim e o selo `ESCOLHIDA`.

| Disciplina | Atributo | Frase | Magnitude (nível 1→5) |
|---|---|---|---|
| CELERIDADE | Velocidade de movimento | *Antes que o mundo desperte, seus passos já cruzaram a noite.* | +10/20/30/40/50% |
| FORTITUDE | Vida máxima | *A eternidade tece uma armadura sob sua pele.* | +25/50/100/150/200 |
| POTÊNCIA | Dano físico, inclusive armas | *Cada golpe traz o peso de incontáveis caçadas.* | +5/10/15/20/25% |
| FEITIÇARIA DO SANGUE | Magicka máxima | *O sangue sussurra palavras que a magia reconhece.* | +25/50/100/150/200 |
| TENACIDADE | Vigor máximo | *Quando o fôlego falha, sua vontade ainda arde.* | +25/50/100/150/200 |

- Nível = estágio − 1. Antes de Vampiro Pleno, os cartões aparecem bloqueados (cinza).
- Quando há escolha disponível, os cartões não escolhidos ficam clicáveis. A escolha é **enviada ao servidor**, que valida e registra. O cliente nunca aplica o efeito por conta própria.
- Nota de rodapé: *"Magnitudes de referência por estágio. A escolha e a aplicação dos efeitos dependem do servidor."*
- **Equivalentes nas outras maldições:** a Bruxa tem a seção **Vertentes** (MORTUI / DAEMONES / TENEBRAE, uma escolha), no mesmo formato. O Lobisomem não tem escolha; a seção equivalente mostra as **perks e uivos** por estágio.

### Aplicação às outras telas
Lobisomem e Bruxa seguem **a mesma estrutura** (cabeçalho + estágio atual + grade 3×2), trocando o carmesim pelo âmbar ou violeta, o ícone principal e os textos. Os sobretítulos, lemas e textos dos cartões dessas duas telas ainda não existem: propor e pedir aprovação do dev. A tela sem maldição usa o mesmo cabeçalho, com um elemento gráfico neutro e o texto "Você não é portador de nenhuma praga."
