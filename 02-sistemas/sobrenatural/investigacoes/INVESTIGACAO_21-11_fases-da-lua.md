# Investigação 21/11 — Fases da lua e transformação forçada na lua cheia

Status: **análise concluída; implementação não iniciada** (aguarda decisões do dev e ADR). Autor: Opus 5.5 (orquestrador). Revisão cruzada: pendente.

## 1. Pergunta

1. O servidor já tem fases da lua no clima/horário sincronizado?
2. O mod *Moons and Stars - Sky Overhaul SKSE* pode ser usado de forma sincronizada para todos?
3. Dá para forçar a transformação dos lobisomens na lua cheia, reaproveitando o gatilho das 72 h?

## 2. Achados

### 2.1 Servidor oficial: não há fases da lua
- Nenhuma ocorrência de `moon|masser|secunda|lunar` em `C:\Code\Aetherius - OFICIAL\aetherius-server-main\server` (gamemode, `ts/systems`, config).
- Relógio autoritativo: `server/ts/systems/environmentAuthority.ts`. Tempo em minutos de jogo contínuos (`anchorGameMinutes` + `timeScale`, padrão 12); calendário 360 dias/ano, 12 meses × 30 dias (`calendarFromGameMinutes`, linha 27).
- `clockSnapshot()` (linhas 241–246) envia `gameMinutes`, `calendar` e `gameDaysPassed`. Mas:
  ```ts
  const gameDaysPassed=this.dateChangesEnabled?(gameMinutes-initialClock(0,this.options.clockConfig).anchorGameMinutes)/DAY_MINUTES:null;
  ```
  `dateChangesEnabled` só é verdadeiro com `allowDateChanges === true` (linha 69, "laboratório de calendário").
- Envio: `server/ts/systems/environmentSystem.ts:147-175`, pacote `gameTime` a cada 30 s.
- Cliente: `C:\Code\Aetherius - OFICIAL\aetherius-client-main\client\src\services\services\timeService.ts:76-81` escreve o global `0x39` (GameDaysPassed) **somente** quando recebe valor não nulo.
- Consequência: fora do laboratório, cada cliente tem o próprio GameDaysPassed e a lua no céu não é a mesma para todos.

### 2.2 Moons and Stars (powerof3/MoonMod, v2.0.2, Nexus 73336)
- Ativo nos perfis `AETHERIUS - GRAFICO - QUALIDADE`, `AETHERIUS - LOCAL` e `AETHERIUS - PERFORMANCE` (`+` no `modlist.txt`, `*MoonsAndStars.esp` no `plugins.txt`). Desativado em `Default`.
- Fonte (https://github.com/powerof3/MoonMod):
  - `src/Hooks.cpp`, `Moon_UpdatePhase`: fase calculada a partir de `RE::Calendar::GetSingleton()->GetDaysPassed()` (GameDaysPassed), truncado para inteiro.
  - `src/Settings.cpp:92`: `phase = daysPassed % (phases.size() * phaseLength) / phaseLength;`
  - `set_moon_angle`: posição no céu também derivada de GameDaysPassed (parte fracionária), `Speed` e `Days Offset`.
  - Sem aleatoriedade nem estado salvo → **mesmo GameDaysPassed = mesma lua (fase e posição) em todos os clientes**. O DLL não precisa ser alterado.

### 2.3 INI vigente: o do Rudy ENB
- Duas cópias de `po3_MoonMod.ini` no MO2 (busca em `mods/` e `overwrite/`):
  - `C:\modOrganizer\mods\Moons and Stars - Sky Overhaul SKSE\skse\plugins\po3_MoonMod.ini`
  - `C:\modOrganizer\mods\Rudy ENB SE for Obsidian Weathers - LUX - ELFX\SKSE\plugins\po3_MoonMod.ini` ← **vence**
- Prioridade (linha no `modlist.txt`, menor = maior prioridade): Rudy 57/57/44 vs Moons and Stars 393/391/380 (QUALIDADE/LOCAL/PERFORMANCE).
- Diferenças do Rudy: Masser `Speed = 0.25` (orig. 0.23958333333) e `Days Offset = 0.2125` (orig. 0.3125). Secunda idêntica. **Esses campos só afetam a posição; a fase usa `Phase Length in Days` e a lista de `Phase Path`, iguais nos dois.**

| Lua | Ciclo | Ordem das fases (índice) |
|---|---|---|
| Masser | 24 dias (8 × 3) | 0 Lua Cheia, 1 Minguante Gibosa, 2 Quarto Minguante, 3 Lua Minguante, 4 Lua Nova, 5 Lua Crescente, 6 Quarto Crescente, 7 Crescente Gibosa |
| Secunda | 20 dias (20 × 1) | dias 0–2 Cheia, 3–4 Minguante Gibosa, 5–7 Quarto Minguante, 8–9 Minguante, 10–12 Nova, 13–14 Crescente, 15–17 Quarto Crescente, 18–19 Crescente Gibosa |

Fórmula para o servidor (Masser): `fase = Math.floor(floor(gameDaysPassed) % 24 / 3)`; cheia quando `floor(gameDaysPassed) % 24 < 3`.

Nomes PT-BR (8 fases; a lista original do pedido omitia a Nova): Lua Nova, Lua Crescente, Quarto Crescente, Crescente Gibosa, Lua Cheia, Minguante Gibosa, Quarto Minguante, Lua Minguante.

### 2.4 Gatilho das 72 h (compulsão)
Fonte: `C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\forced-werewolf.js` (fonte do servidor `310cb59`), documentado em `docs/ETAPA-2_transformacao-forcada.md`.
- `tick()` a cada 250 ms; a sequência começa quando `row.onlineMs >= config.limitMs` **e** é noite (`isNight(hourOf(id), 20, 5)`) **e** `healthy(id)`.
- `hourOf` já lê a autoridade de ambiente (`globalThis[Symbol.for('aetherius.environment.authority.v1')].clockSnapshot()`), a mesma fonte que daria a fase da lua.
- A sequência de 20 s (mensagens, batimento, tremor, filtro vermelho) termina em `mp.__aetheriusWerewolf.start(id, {animate:true})`; o reset vem pelo stamp `onTransformed`/`lastTransform`.

## 3. Viabilidade da lua cheia como gatilho

**Viável, com pouca mudança.** É um segundo critério de disparo no mesmo `tick()`, reaproveitando sequência, cancelamentos, `healthy`, retry, persistência e reset:

```js
// esboço, não implementado
const due = row.onlineMs >= config.limitMs || fullMoonDue(row);
if (!sequence && due && at >= (retry.get(id) || 0)) { ... beginSequence(id, b, at); }
```

- `moonPhaseAt(gameDaysPassed)` vive no servidor (`ts/systems/` ou em `supernatural/`), calculada a partir de `gameMinutes` da autoridade com a **mesma época** usada em `clockSnapshot()`. O servidor calcula a fase mesmo sem enviar GameDaysPassed ao cliente; a mecânica não depende da lua visível.
- Para não repetir a transformação a cada retry na mesma noite, o registro por personagem guarda a chave da última noite de lua cheia atendida (ex.: `moonNight = floor(gameDaysPassed)` da noite), gravada quando a transformação é confirmada pelo stamp.
- A transformação na lua cheia zera também o contador de 72 h (já acontece, pois o reset é por stamp).
- O cliente nunca decide pela lua que está vendo; a lua no céu é apresentação.

### Pré-requisito para a lua visível bater com a mecânica
Enviar GameDaysPassed sempre (hoje só no laboratório). Mudança pequena no servidor, mas o global é lido por outros sistemas (estágios de vampirismo vanilla/Curse of the Vampire, aluguéis, prazos de quest). **Exige decisão do dev e ADR no acervo**, mais teste do salto de valor ao conectar. Sem isso a mecânica funciona, mas o céu de cada jogador pode mostrar outra fase.

## 4. Decisões pendentes (dev)
1. Lua de referência: Masser (sugerido) ou Secunda, ou ambas.
2. Quais noites: as 3 noites de Masser cheia, ou só a primeira.
3. Janela: só à noite (20h–5h, como as 72 h) ou o dia inteiro.
4. Liberar envio permanente de GameDaysPassed (ADR).
5. O botão Forma da Besta / F7 contam como "atendeu a lua cheia"?

## 5. Riscos e dependências
- A fórmula no servidor copia o INI do **Rudy ENB**; se o Rudy ou o Moons and Stars for atualizado/trocado, revisar.
- Época: os limites de dia caem na hora da época configurada (`gameTimeEpochHour`, padrão 0); manter servidor e cliente na mesma referência.
- Interação com sistema do kdu (`werewolf-service.js`): usa a API `start` já usada pela compulsão; nada novo exigido do kdu.
- Não verificado em jogo: posição relativa sol/lua com `Speed = 0.25` (só visual).

## 6. Classificação
- Função de fase + campo no snapshot: baixa/média.
- Envio permanente de GameDaysPassed: alta (Sol High), após ADR.
- Gatilho de lua cheia no `forced-werewolf.js`: média (Terra High ou Sol, pela proximidade com a compulsão já feita pelo Sol), revisão Gemini 4 Neon.

## 7. Decisões do dev (21/11)
- **Masser governa:** na Lua Cheia de Masser os lobisomens são transformados obrigatoriamente (mesma sequência da compulsão de 72 h).
- **HUD da Lua para lobisomens**, canto inferior direito ao lado de fome/sede/frio, como as HUDs de vampiro e bruxa. Três propostas em `C:\Code\Aetherius - New Systems\Sobrenatural\preview\hud-lua-propostas.html` (A Olho de Masser, B Garras da Lua, C Ciclo de Masser); aguardando escolha.
- **Olhos amarelos** (Miggyluv's Eyes, Nexus 155332): **Bronze Light** como padrão para todo lobisomem; **Bronze Glow** à noite. Headparts encontrados no ESP: `MiggyEyesBronze01/02` (+`F`), `MiggyEyesBronzeGlow`/`MiggyEyesBronzeFGlow`, `MiggyEyesBronze01Glow`/`01FGlow`; texturas por raça (Alt, Arg, Bos, Dun, Kha, Orc). Confirmar pelas texturas qual de `01`/`02` é Light. Mesma regra de fallback da Bruxa para raças sem Glow.

Ainda em aberto: itens 2, 3, 4 e 5 da seção 4.

## 8. Decisões do dev (21/11, 2ª rodada)
- **Olho Glow** só quando o lobisomem está há **≥ 24 h sem se transformar**; abaixo disso, Bronze Light. Implementação proposta: reaproveitar `row.onlineMs` da compulsão (`forced-werewolf.js`), comparando com um segundo limite (`WEREWOLF_EYES_GLOW_HOURS`, padrão 24). A aparência só é enviada na **borda** (cruzou 24 h → Glow; transformação confirmada zera o contador → Light), nunca a cada tick. Custo: uma comparação a mais por lobisomem online no tick de 250 ms já existente; sem timer, arquivo ou pacote novo. O contador é de tempo real **online**, como as 72 h.
- **HUD: proposta A**, posicionada **acima da HUD de sobrevivência**, menor (~56 × 56 px a 1080p), sem fundo/anel externo, traço translúcido. Prévia atualizada no mesmo HTML (seção "A escolhida — versão integrada"). Posição exata a medir na HUD de sobrevivência real (seletor não localizado no cliente oficial nesta coleta).

## 9. Decisões do dev (21/11, 3ª rodada) e premissas do orquestrador
- Transformação obrigatória nas **3 noites** de Masser cheia (`floor(gameDaysPassed) % 24` em 0, 1, 2).
- **Olho Glow durante todo o período de Lua Cheia** (os 3 dias inteiros), além da regra das 24 h sem transformar.
- **HUD A aprovada** na versão integrada (~56 px, acima da sobrevivência).
- Premissas do orquestrador (dev pode reverter): noite = 20h–5h, como a compulsão; a noite pertence ao dia em que começa (horas < 5 contam para o dia anterior); uma transformação por noite; transformação confirmada por qualquer via (compulsão, lua, F7, botão) dentro da noite cheia conta como cumprida; ADR do GameDaysPassed adiado — a mecânica é só servidor e não depende do céu.
- Despacho: Sol High (servidor: fase, gatilho, olhos, propriedade da HUD), Terra High (HUD no cliente). Revisão: Gemini 4 Neon.
