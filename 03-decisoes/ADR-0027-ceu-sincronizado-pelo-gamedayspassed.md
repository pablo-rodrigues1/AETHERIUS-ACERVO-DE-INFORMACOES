---
titulo: Céu sincronizado pelo GameDaysPassed do servidor
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [pablo-rodrigues1]
atualizado: 2026-11-21
tags: [sobrenatural, clima, horario, lua, sincronizacao]
relacionados: [02-sistemas/sobrenatural/etapa-2-lua-de-masser.md, 02-sistemas/sobrenatural/investigacoes/INVESTIGACAO_21-11_fases-da-lua.md, 02-sistemas/lobisomem/]
---

# ADR-0027 — Céu sincronizado pelo GameDaysPassed do servidor

## Resumo
O servidor passa a enviar o `gameDaysPassed` a todos os clientes o tempo todo, e não só no laboratório de calendário. Assim a lua (fase e posição) e as estrelas desenhadas pelo Moons and Stars ficam iguais para todos e coincidem com a fase de Masser usada nas mecânicas. Aceito pelo dev em 21/11/2026; a implementação depende do levantamento de impacto descrito abaixo.

## Contexto
- O relógio autoritativo está em `server/ts/systems/environmentAuthority.ts`, derivado do AetheriusWeatherSystem (AndreSoldera, `f00dcdf`, AGPL-3.0-or-later). Ele já calcula `gameDaysPassed` em `clockSnapshot()` (linha 244), mas envia `null` quando `allowDateChanges` não está ligado.
- O cliente (`client/src/services/services/timeService.ts:76-81`) só escreve o global `0x39` (GameDaysPassed) quando recebe valor não nulo. Fora do laboratório, cada jogador fica com o GameDaysPassed local.
- O Moons and Stars (powerof3/MoonMod, ativo nos perfis AETHERIUS, INI vigente do Rudy ENB) calcula fase e posição das luas só a partir do GameDaysPassed (`src/Hooks.cpp`, `src/Settings.cpp:92`). Com valores diferentes, cada jogador vê outra lua.
- O Sobrenatural já transforma lobisomens na Lua Cheia de Masser com fase calculada no servidor (`etapa-2-lua-de-masser.md`). Sem este ADR, a mecânica funciona, mas o céu pode contradizê-la.

## Opções consideradas
| Opção | Prós | Contras |
|---|---|---|
| A. Enviar `gameDaysPassed` sempre | Céu idêntico para todos e igual à mecânica; reaproveita o caminho já existente no cliente | O global é lido por outros scripts (vampirismo vanilla/Curse of the Vampire, aluguéis, prazos de quest, Survival); salto de valor ao conectar |
| B. Manter só no laboratório | Nenhum risco novo | Céu diferente por jogador; Lua Cheia mecânica sem correspondência visual |
| C. Alterar o DLL do Moons and Stars para ler uma fonte própria | Não toca o GameDaysPassed | Fork de DLL nativa por versão do jogo; manutenção alta; contraria a regra de não editar o modpack |

## Decisão
Opção A, por decisão do dev ("precisamos que o céu seja sincronizado para os jogadores"). Condições para entrar em produção:
1. Levantar quem lê o GameDaysPassed no modpack e no cliente (scripts Papyrus, SKSE, mods das maldições) e testar o salto de valor ao conectar.
2. Manter a mesma época de `clockSnapshot()`, para que `floor(gameDaysPassed)` do céu e do servidor coincidam.
3. Mudança feita como patch no laboratório (`C:\Code\Aetherius - Servidor Local`), sem editar os repositórios oficiais, com revisão cruzada e teste em jogo com dois jogadores.
4. Avisar o responsável pelo sistema de clima/horário antes da integração oficial.

## Consequências
- (+) Lua, estrelas e fase de Masser iguais para todos e coerentes com a transformação na Lua Cheia.
- (+) Base para outras mecânicas que dependam da lua ou de dias passados.
- (−) Scripts que reagem ao GameDaysPassed podem disparar com o salto inicial; exige o levantamento da condição 1.
- (−) A fórmula da fase no servidor depende do `po3_MoonMod.ini` do Rudy ENB; trocar o INI exige revisar o servidor.
