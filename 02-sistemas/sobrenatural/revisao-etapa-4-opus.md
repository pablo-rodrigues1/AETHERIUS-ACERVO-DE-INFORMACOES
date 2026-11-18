# Revisão independente: Etapa 4, Bruxaria (Vínculo do Véu + Sobrecarga)

**Revisor:** Opus 5.5, worker de revisão (`task_9384337d717c`). Os autores revisados foram Sol High e Terra High (GPT/Codex). · **Data:** 18/11/2026 · **Modo:** somente leitura. Não foram executados servidor nem jogo, e nada foi instalado.
**Repositórios:** `C:\Code\Aetherius - Servidor Local`, raiz e overlays, e `client-source` com HEAD `c27afd4`. Foram revisados os commits listados no despacho: do Sol, `7dc6c23…f75a57d` e o cliente `8d9fc09`; do Terra, os commits de cliente `850c082…c27afd4` e do laboratório `973ea57…a533441`.

## Veredito

**APROVADO COM RESSALVAS para o laboratório. BLOQUEADO para a compilação final.**
- A lógica do servidor está coerente com o plano e com as decisões do dev. Isso inclui os estágios, a drenagem de 432 s, o teto 300, os blocos `floor((v-100)/50)`, o ritual com escolha validada pelo servidor, a regra D7 e a keyword, que não aparece na projeção: o payload v1 tem só `bond/stage/overload/overloadBlocks` e a propriedade não é visível para vizinhos.
- Há dois bloqueios:
  - **(A1)** Existem alterações no cliente fora do módulo Sobrenatural, o que viola a regra de 18/11.
  - **(B1)** O addon nativo (patch 025) não foi compilado. Sem ele, as penalidades e os bônus de Magicka ficam desligados em silêncio.

## Achados priorizados

### A1 — ALTA (regra de isolamento). Arquivos do cliente fora do módulo `supernatural`

| Arquivo | Commit | O que faz | Viola? |
|---|---|---|---|
| `client/ui-core/frontend/shell.js:434` | `82280b2` | Acrescenta `close()` ao SDK de módulos do shell, usado no fechamento ao aceite do Ritual | **Sim.** O shell é global. Reescrever usando a API existente (`navigate`/rota de gameplay) ou um evento do próprio módulo. Se não houver como, levar ao dev como exceção explícita. |
| `client/src/sync/actorvalues.ts:20` | `8bcc121` | `magicka` vira 0 quando o percentual é NaN (máximo 0 no estágio V) | **Sim.** É sincronização núcleo. A correção é necessária para o estágio V e não deve ser simplesmente removida: precisa ser substituída por proteção do lado do Sobrenatural/servidor (o patch 025 já descarta NaN em `ActionListener.cpp`) ou aprovada como exceção. |
| `client/src/services/services/spSnippetService.ts:68` | `8bcc121` | Novo snippet `AetheriusVeil.ApplyMagicka` | **Sim, formalmente** (serviço núcleo). Isolar num listener próprio do Sobrenatural, se o despacho de snippets permitir, ou documentar como integração mínima. |
| `client/src/services/services/aetheriusUiService.ts:69-71,180,338` | `8bcc121`, `8d9fc09` | Instancia `VeilEffects` e o reset por sessão/loadGame | **Limítrofe.** É o ponto de montagem do serviço. Aceitável apenas se listado como integração mínima. |
| `client/src/services/services/veilEffects.ts` (novo) | `8bcc121` | Terror, sussurros e tela preta no cliente | Não. É exclusivo do Sobrenatural, mas fica no diretório de serviços núcleo. Mover para uma pasta própria do Sobrenatural. |
| `client/index.html:1328,1335-1370` | `8bcc121`, `8d9fc09`, `b007e6e`, `c27afd4` | CSS/JS do selo e do terror, `setVeilBondHud` e ocultação da barra de sangue enquanto o selo está ativo | **Limítrofe.** É o ponto de montagem da HUD própria. O `<style>` inline com `html.aetherius-browser-hidden .veil-seal` deveria ir para `veil-seal.css`. Aceitar se o diff ficar restrito ao bloco. |
| `client/ui-core/modules.json` | `8bcc121` | Registro do módulo `supernatural` (slot 8) | Não. É o registro permitido. |
| `client/ui-core/modules/supernatural/*` | vários | Módulo | Não. |

O critério de aceite da compilação final (um diff contendo apenas o Sobrenatural mais a integração mínima) **não é atendido hoje** por causa de `shell.js`, `actorvalues.ts` e `spSnippetService.ts`.

### B1 — ALTA. Magicka depende de addon não compilado (`overlays/sobrenatural/025-veu-magicka-nativa.patch`)

`veil-magicka.js:21-28` lê `aetheriusVeilMagicka`. Sem a propriedade, ele lança uma exceção, registra **um** aviso e passa a não fazer nada.

Consequência: os estágios III a V (−50% de regeneração, −50% e −100% do máximo) e a sobrecarga (+50% do máximo e +regeneração) **não têm efeito nenhum** até a compilação ficar pronta. Isso é esperado e está documentado, mas o teste em jogo não pode começar sem o addon.

O código C++ foi revisado e está correto no geral:
- valida percentuais (`Validate`);
- preserva a Magicka absoluta ao trocar o máximo;
- usa revisão e perfil no ACK do snippet;
- ignora o relato de Magicka do cliente enquanto há sincronização pendente (`ActionListener.cpp`);
- trata máximo 0 sem dividir por zero (`MpActor.cpp`, damage/restore);
- `CropRegeneration` retorna 0 quando o máximo é 0.

### B2 — MÉDIA. Possível divergência do máximo de Magicka entre cliente e servidor (`spSnippetService.ts:74`)

O snippet faz `setActorValue("Magicka", maximum)` com o máximo **calculado pelo servidor**, que já inclui raça e classe. `setActorValue` altera o valor **base**, e efeitos ativos no cliente (encantamentos ou poções de Fortificar Magicka) somam por cima. O máximo efetivo no cliente pode então ficar maior que o do servidor, e como o servidor sincroniza por percentual, a Magicka do cliente oscila ou é corrigida.

No estágio V (base 0) com equipamento de Fortificar, o cliente ainda terá Magicka.

**Reprodução (jogo):** bruxa no estágio IV usando um anel de Fortificar Magicka. Comparar `player.getActorValueMax("Magicka")` com `GetMaximumValues().magicka`. Validar antes de liberar.

### B3 — MÉDIA. Re-sincronização após recarga do cliente sem desconexão

O ACK limpa `veilMagickaPending`. Se o cliente recarregar o save (`loadGame`) sem passar por `onCharacterInactive`, o valor base volta ao vanilla, e o servidor não reenvia o snippet porque os modificadores não mudaram e a sincronização não está pendente (`DevAttributes.cpp`, `SetVeilMagicka`: `if (!changed && !IsVeilMagickaSyncPending()) return;`).

A desconexão normal está coberta: `veil-bond.js:101` chama `visuals.clear`, que chama `magicka.clear`, o que zera e reaplica na volta.

Sugestão: marcar a sincronização como pendente no `loadGame` do cliente ou no evento nativo de spawn ou carregamento do ator.

### C1 — MÉDIA. Dependências de plugins novos

- O `AETHERIUS - SOBRENATURAL.esp` (`a00899a`) ganhou **`MasterSpellsRunes.esp` como master obrigatório** (SPEL `00083A`). Esse master precisa estar presente e antes do Sobrenatural no **load order do servidor (espm)** e de todos os perfis/cliente. Sem ele, o plugin não carrega, o que derruba **todo** o Sobrenatural, inclusive Vampiro e Lobisomem. Isso precisa ser confirmado na configuração do servidor antes do teste.
- A aura usa `000807:SkyDeath-Ghosts.esp` (`veil-visuals.js:7`), mais uma dependência a listar na compilação final.
- Os records dos sons conferem com o cliente: em `veilEffects.ts:6-9`, `0x83F` a `0x842` correspondem à tabela da ETAPA-4. O heartbeat `04DAAF:Skyrim.esm` foi conferido no houseCARL e é **SOUN** (`UIHealthHeartbeatALP`), o que é correto para `Sound.from`. Há uma inconsistência só no texto da ETAPA-4 §cabeçalho, que o chama de SNDR.

### D1 — BAIXA (performance). Publicação contínua do Vínculo

- `veil-bond.js:58-62,75-77`: a deduplicação compara o `bond` fracionário, que muda a cada segundo pela drenagem. O resultado é um `mp.set` por segundo por bruxa, enviado só ao dono.
- O `updateOwner` (`veil-bond.js:35-44`) roda a cada frame e chama `executeJavaScript` no máximo uma vez por segundo. Esse reenvio periódico é o que mantém o selo visível, porque `index.html` o oculta após 3,5 s, inclusive no estágio V, onde o valor fica parado em 0.

O custo é baixo, mas a atualização de rede poderia ser quantizada, por exemplo em passos de 0,1 ou com envio forçado a cada 5 s.

O `VeilEffects.update` roda a cada frame com chamadas nativas leves. As 25 consultas `isMenuOpen` só ocorrem durante a tela preta. Aceitável.

### D2 — BAIXA. Ritual: perda de material com a conclusão em andamento

Se ocorrer uma interrupção depois que a baixa começou (`veil-ritual.js:98-111`, `stillCurrent`), o ritual fecha como interrompido ou falho sem crédito. Isso está de acordo com D7 e está documentado. Não é um defeito.

### D3 — BAIXA. Janela de modos sobrepostos

Com o padrão `overloadStart=99` (`veil-ritual.js:25,57-61`), os modos normal e sobrecarga ficam elegíveis ao mesmo tempo em 99 < v < 100. O modo normal credita no máximo até 100. Isso está de acordo com a autorização da §8.6. A UI deve deixar claro qual dos dois o jogador escolheu.

### D4 — BAIXA (documentação). `teste-servico.cjs` exige caminho absoluto

Com um caminho relativo (`source/server/gamemode`), o script falha em `teste-servico.cjs:2` com `path` indefinido ou no `require`. Com o caminho absoluto documentado, retorna OK.

## Fidelidade ao plano (resumo)

| Item | Situação |
|---|---|
| Estágios I–V e limiares (`veil-bond.js:15-21`) | ✅ Conforme §1 |
| Drenagem de 432 s só online, persistência, D7 | ✅ Reaproveita `blood-reserve` |
| Sobrecarga até 300 e blocos (`veil-bond.js:23-31`) | ✅ Conforme S1/S2 |
| Modificadores de Magicka (`veil-magicka.js:4-11`) | ✅ Conforme a tabela. ⚠️ Inativos até o addon (B1) |
| Criaturas extras e S3 (patch 022) | ✅ Conforme o documento. R-ANIM está fora do escopo |
| Ritual: 3 categorias escolhidas, validação, receita dobrada, interrupções, 300 s | ✅ `veil-ritual.js:122-156` |
| Fechamento da UI só com `ok && active.id` | ✅ Na apresentação. ⚠️ Usa `shell.close` (A1) |
| Ganho gradual com retarget e drenagem | ✅ Testes de UI 11/11, segundo o documento. Não reexecutei o CEF |
| Terror: chances, cooldown, tela preta só em contexto seguro, restauração | ✅ `veilEffects.ts:87-125,135-149` |
| Sussurros só para a bruxa, 30–90 s e depois 3–6 min, alternados | ✅ `veilEffects.ts:155-162` |
| Olhos, BRUXA e aura | ✅ No código. Dependem de Miggyluv e de jogo |
| Keyword nunca enviada ao cliente | ✅ A propriedade é privada (`isVisibleByNeighbors:false`) e o payload não tem keyword |

## Testes executados pelo revisor

Todos rodaram em `C:\Code\Aetherius - Servidor Local`, com Node v22.14.0, usando o comando da ETAPA-3 (todos os `overlays/sobrenatural/*.cjs` exceto os três scripts de integração, mais os cinco testes Core):

```
AETHERIUS_VAMPIRE_FEED_ENABLED=false → # tests 449 # pass 449 # fail 0 (exit 0)
AETHERIUS_VAMPIRE_FEED_ENABLED=true  → # tests 449 # pass 449 # fail 0 (exit 0)
node overlays/sobrenatural/teste-servico.cjs "<abs>\source\server\gamemode" → OK (exit 0)
node overlays/sobrenatural/teste-painel-f7.cjs   → exit 0
node overlays/sobrenatural/teste-revisao-f7.cjs  → exit 0
client-source/client: npx tsc --noEmit           → exit 0
```

- O total observado, **449**, é maior que os 445 documentados. Os quatro testes a mais vêm dos lotes de apresentação e ritual posteriores, que já entram no glob. Nenhuma falha.
- Os testes de UI (`teste-visual-vinculo`, `teste-ritual-apresentacao`, `teste-efeitos-veu-cliente`) estão incluídos nos 449.
- Pela regra de somente leitura, não rodei o webpack, porque ele grava o bundle em `build/`. O documento registra o SHA do bundle do Sol.
- Não rodei a seleção Core 39 separadamente. Os cinco arquivos Core estão dentro dos 449.

## Pendências para liberar

1. Resolver A1: remover ou reescrever `shell.js`, `actorvalues.ts` e `spSnippetService.ts`, ou obter exceção explícita do dev.
2. Compilar o addon 025 (B1) e testar B2/B3 em jogo.
3. Confirmar `MasterSpellsRunes.esp` e `SkyDeath-Ghosts.esp` no load order do servidor e dos perfis (C1).
4. Fazer a rodada em jogo/CEF com uma bruxa, um observador e um terceiro entrando depois.
