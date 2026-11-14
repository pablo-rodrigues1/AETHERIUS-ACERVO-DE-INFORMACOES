---
titulo: Aetherius UI Core
tipo: sistema
status: ativo
fase: em-desenvolvimento
responsaveis: [mercurius17]
repositorio: https://github.com/mercurius17/AetheriusUI_Core
depende_de: [../gameplay-core/README.md]
atualizado: 2026-11-14
tags: [interface, meridian, cef, skse, chargen, inventario]
relacionados: [diario.md, ../gameplay-core/README.md, ../../03-decisoes/ADR-0002-sincronizacao-dos-cores-com-os-repositorios-oficiais.md, ../../05-processos/sincronizacao-dos-cores.md]
---

# Aetherius UI Core

## Resumo
Interface modular do Aetherius sobre o MeridianUI (CEF dentro do Skyrim): shell com radial TAB, criação de personagem, inventário/feitiços/mapa e uma bridge SKSE em C++. Roda dentro do cliente oficial como cópia em `client/ui-core`; este repositório é a fonte do framework e recebe de volta as mudanças feitas no cliente.

## Onde está o código
- Repositório: https://github.com/mercurius17/AetheriusUI_Core (branch `main`)
- Cópia em produção: `AetheriusRP/aetherius-client`, pasta `client/ui-core` (o `PROVENANCE.md` de lá registra origem e commit de cada pasta)
- Pontos de entrada:
  - `frontend/Data/MeridianUI/aetheriusui/shell.js`: estados da UI (gameplay, radial, workspace) e navegação
  - `frontend/Data/MeridianUI/aetheriusui/chargen.*`, `chargen-finish.js`: criação de personagem
  - `native/src/main.cpp`, `native/src/chargen.cpp`: bridge SKSE (CommonLibSSE-NG)
  - `ui-inventory/ui/`: módulos de inventário, feitiços e mapa
  - `shared/`, `sdk/`: protocolo de envelopes e router do servidor

## Escopo
- Inclui: shell e radial, HUD, criação de personagem, inventário/feitiços/favoritos, abertura do mapa nativo, tela de carregamento, leitura de descrições e efeitos ativos pela bridge, protocolo cliente↔servidor.
- Não inclui: módulos de outros sistemas que o cliente monta sobre o shell (profissões, loja, casas, roda de gestos, refino, baú, propriedades); `modules/class` e `modules/party`, que vêm do [GameplayCore](../gameplay-core/README.md).

## Funcionamento
| Pasta do cliente (`client/ui-core/`) | Pasta neste repositório |
|---|---|
| `frontend/` | `frontend/Data/MeridianUI/aetheriusui/` |
| `native/` | `native/` |
| `native/tests/native-*.test.cpp` | `tests/` |
| `modules/inventory`, `modules/spells`, `modules/map` | `ui-inventory/ui/` |

- A criação de personagem edita raça e sliders chamando os callbacks do RaceSexMenu vanilla aberto por baixo, só com índices e valores validados contra as listas do próprio SWF. O nome é validado (`IsValidCharacterName`) e gravado só na confirmação (`type: "finish"`); a página recebe `aetherius-chargen-finish` e tem watchdog de 10 s.
- O `shell.js` tem ganchos para telas do cliente (`emotes`, `refine`, `container`) que só agem se a tela existir na página.
- Fonte da UI: Sovngarde Bold (SIL OFL 1.1, `fonts/Sovngarde-OFL.txt`), no lugar da Futura, que não podia ser redistribuída.

## Parâmetros e fórmulas
| Parâmetro | Valor | Descrição |
|---|---|---|
| Watchdog da finalização | 10 000 ms | sem resposta, a página avisa e libera o botão |
| Descrição de feitiço/item | até 2 400 caracteres | truncada na página |
| Chave de feitiço/item | 1–8 dígitos hex | rejeitada fora disso |

## Dependências
| Depende de | Por quê |
|---|---|
| [GameplayCore](../gameplay-core/README.md) | estado autoritativo exibido pelos módulos (classe, atributos, perks) |
| MeridianUI 1.5.0 (`5707877`) | host CEF; mudanças em `meridian/changes.patch` no cliente |
| CommonLibSSE-NG, MSVC, nlohmann_json | build da bridge e dos testes nativos (`docs/DLL_BUILD_GUIDE.md`) |

## Decisões relacionadas
- [ADR-0002](../../03-decisoes/ADR-0002-sincronizacao-dos-cores-com-os-repositorios-oficiais.md): o que volta do cliente para este repositório.

## Diário
Ver [`diario.md`](diario.md).

## Pendências conhecidas
- `tests/native-localized-presentation.test.cpp` e a DLL da bridge não foram compilados na sincronização de 14/11 (faltava nlohmann_json/MSVC na máquina).
- Partes do README do repositório ainda descrevem o criador como "somente consulta" (estado de 04/10).
