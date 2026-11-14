---
titulo: Levantamento dos castelos por cidade
tipo: referencia
status: ativo
responsaveis: []
atualizado: 2026-11-14
tags: [integracao]
relacionados: [README.md]
fonte: Levantamento de interiores dos castelos de 11/11/2026
---

# Levantamento dos castelos por cidade

## Resumo

Nove interiores e 28 pares de portas transcritos do painel em 11/11. A equipe precisa validar os destinos e as permissões no jogo antes da integração.

## Interiores

| Cidade | Interior mostrado | Célula do interior | Entradas mostradas |
| --- | --- | --- | ---: |
| Whiterun | WhiterunDragonsreach | `165a3:skyrim.esm` | 5 |
| Riften | RiftenMistveilKeep | `16bd2:skyrim.esm` | 3 |
| Solitude | SolitudeBluePalace | `16a04:skyrim.esm` | 2 |
| Falkreath | FalkreathJarlsLonghouse | `13a79:skyrim.esm` | 4 |
| Morthal | MorthalHighmoonHall | `138cc:skyrim.esm` | 1 |
| Winterhold | WinterholdJarlsLonghouse | `13818:skyrim.esm` | 2 |
| Windhelm | WindhelmPalaceoftheKings | `1677:skyrim.esm` | 5 |
| Markarth | MarkarthUnderStoneKeep | `16df2:skyrim.esm` | 5 |
| Dawnstar | DawnstarTheWhiteHall | `13a80:skyrim.esm` | 1 |

## Pares transcritos do painel

### Whiterun

| Tipo | Entrada | Destino |
| --- | --- | --- |
| Selecionada | `1a704:skyrim.esm` | `16707:skyrim.esm` |
| Adicional | `46124:skyrim.esm` | `46b84:skyrim.esm` |
| Adicional | `4a388:skyrim.esm` | `4a37c:skyrim.esm` |
| Adicional | `8113d:skyrim.esm` | `8108a:skyrim.esm` |
| Adicional | `9a0e4:skyrim.esm` | `dcc42:skyrim.esm` |

### Riften

| Tipo | Entrada | Destino |
| --- | --- | --- |
| Selecionada | `42282:skyrim.esm` | `16cfe:skyrim.esm` |
| Adicional | `3bd81:skyrim.esm` | `3be09:skyrim.esm` |
| Adicional | `3c468:skyrim.esm` | `3c46a:skyrim.esm` |

### Solitude

| Tipo | Entrada | Destino |
| --- | --- | --- |
| Selecionada | `37f60:skyrim.esm` | `16b2a:skyrim.esm` |
| Adicional | `9b5c2:skyrim.esm` | `34249:skyrim.esm` |

### Falkreath

| Tipo | Entrada | Destino |
| --- | --- | --- |
| Selecionada | `17764:skyrim.esm` | `14070:skyrim.esm` |
| Adicional | `1e1f06:cotn - falkreath.esp` | `1e1f09:cotn - falkreath.esp` |
| Adicional | `1e1f07:cotn - falkreath.esp` | `1e1f08:cotn - falkreath.esp` |
| Adicional | `1f14fe:cotn - falkreath.esp` | `1e7014:cotn - falkreath.esp` |

### Morthal

| Tipo | Entrada | Destino |
| --- | --- | --- |
| Selecionada | `177c0:skyrim.esm` | `173c6:skyrim.esm` |

### Winterhold

| Tipo | Entrada | Destino |
| --- | --- | --- |
| Selecionada | `17567:the great city of winterhold v4.esp` | `173cb:the great city of winterhold v4.esp` |
| Adicional | `156b6:the great city of winterhold v4.esp` | `1a9138:the great city of winterhold v4.esp` |

### Windhelm

| Tipo | Entrada | Destino |
| --- | --- | --- |
| Selecionada | `c0e2d:skyrim.esm` | `c0e2c:skyrim.esm` |
| Adicional | `c0e0c:skyrim.esm` | `c0e0d:skyrim.esm` |
| Adicional | `c0e10:skyrim.esm` | `c0e0e:skyrim.esm` |
| Adicional | `c0e13:skyrim.esm` | `c0e0f:skyrim.esm` |
| Adicional | `c0e2e:skyrim.esm` | `c0e2b:skyrim.esm` |

### Markarth

| Tipo | Entrada | Destino |
| --- | --- | --- |
| Selecionada | `16e47:skyrim.esm` | `7f65e:skyrim.esm` |
| Adicional | `24013:skyrim.esm` | `7f6af:skyrim.esm` |
| Adicional | `33d2d:skyrim.esm` | `36a82:skyrim.esm` |
| Adicional | `3ef54:skyrim.esm` | `7f681:skyrim.esm` |
| Adicional | `98276:skyrim.esm` | `98272:skyrim.esm` |

### Dawnstar

| Tipo | Entrada | Destino |
| --- | --- | --- |
| Selecionada | `1774c:skyrim.esm` | `13d31:skyrim.esm` |

O tipo “selecionada” identifica a entrada ativa durante o levantamento. As entradas adicionais levam ao mesmo interior segundo a interface consultada. A lista não confere por si só permissões nem confirma um percurso recíproco. [Governo e acesso](README.md).
