# Contrato cliente ↔ servidor — Vínculo do Véu v2

Autor: Sol High. Base local `f0bd86e` em `C:\Code\Aetherius - Servidor Local`. Regras de `ETAPA-4_bruxaria-vinculo-do-veu.md`, incluindo as decisões da §8.5. O contrato usa o envelope autenticado do UI Core e a propriedade SkyMP de apresentação ao dono. A keyword, a identidade de persistência e as operações de inventário ficam no servidor.

## Apresentação privada do Vínculo

O servidor publica a propriedade SkyMP `aetheriusVeilPresentation` somente ao dono. A projeção válida é `null` (limpar o selo e parar efeitos privados) ou:

```ts
type VeilPresentation = {
  v: 1;
  bond: number;                 // fracionário, intervalo [0, 300]
  stage: 1 | 2 | 3 | 4 | 5;    // estágio do Vínculo, independente do estágio por XP
  overload: boolean;           // bond > 100
  overloadBlocks: 0 | 1 | 2 | 3 | 4;
};
```

Estágios: I `>70` (inclui sobrecarga), II `>50` até `70`, III `>30` até `50`, IV `>0` até `30` e V em `0`. `overloadBlocks = clamp(floor((bond − 100)/50), 0, 4)`. Para apresentação, usar os valores fracionários e as fronteiras `100` e `200` diretamente. O update do dono grava `sp.storage.aetheriusVeilPresentationV1 = value`. O cliente projeta este estado em `hud.html` na view HUD da UI Core por `AetheriusUI.ToView`, que aciona `window.setVeilBondHud(value)`. A HUD e os efeitos devem reconstruir-se da projeção atual, inclusive após recarga, e limpar em desconexão, troca de personagem ou versão inválida. O servidor volta a publicar a projeção em conexão nova. A UI não calcula drenagem, recompensa, buff ou progresso de XP a partir do relógio local.

## Ritual no UI Core

Usar `moduleId: 'supernatural'` pelo envelope existente (o mesmo de `snapshot` e `choose`). Cada resposta de sucesso tem `{ ok: true, ritual: RitualView }`. Cada erro segue `{ ok: false, error: { code, message } }`, e pode também trazer `ritual` atualizado. O UI Core fornece `sessionId` e `correlationId`. O servidor deriva personagem, autorização e posse do inventário da sessão. Nenhum comando recebe FormIDs, contagem, duração, recompensa ou dados de outro personagem.

| Ação do cliente | Payload | Resposta |
| --- | --- | --- |
| `ritual.status` | `{}` | Disponibilidade dos modos, inventário por categoria, ritual ativo e último resultado da sessão. Pedir na abertura da tela e enquanto houver ritual ativo, até uma vez por segundo. |
| `ritual.start` | `{ mode, categories }` | Três IDs distintos escolhidos pelo jogador; inicia uma única reserva e devolve o mesmo formato de estado. |
| `ritual.cancel` | `{ ritualId, reason?: 'player' \| 'movement' }` | Cancela o ritual indicado e devolve o estado atual. Motivo ausente ou desconhecido equivale a `player`. Um identificador atrasado de outro ritual não cancela o atual. Cancelamento sempre tem crédito zero. |

```ts
type Category = 'daedraHeart' | 'blackSoulGem' | 'rawMeat' | 'hide'
              | 'spellTome' | 'nightshade' | 'nirnroot';
type Mode = 'normal' | 'overload';
type RitualView = {
  v: 1;
  modes: Record<Mode, { eligible: boolean; perCategory: 1 | 2 }>;
  categories: Record<Category, 0 | 1 | 2>;  // quantidades elegíveis, limitadas a 2
  active: null | {
    id: string;
    mode: Mode;
    categories: [Category, Category, Category];
    durationMs: 300000;
    remainingMs: number;
  };
  result: null | {
    id: string;
    outcome: 'completed' | 'interrupted' | 'failed';
    reason: null | 'cancelled' | 'moved' | 'combat' | 'disconnected'
                 | 'died' | 'curseLost' | 'materialsChanged' | 'uncertain';
    endReason: 'completed' | 'player' | 'movement' | 'combat'
             | 'disconnect' | 'death' | 'curseLost' | 'invalid';
    gained: number;  // crédito confirmado; 0 em interrupção/falha
  };
};
```

As categorias são Coração Daedra, Pedra da Alma Negra cheia ou vazia, carne animal crua, pele animal, tomo de feitiço, Nightshade e Nirnroot. A disponibilidade usa o inventário ativo no servidor. A tela acende categorias conforme `count >= perCategory` e habilita o início se o modo estiver elegível, houver três selecionadas válidas e `active` for `null`. A receita exige 1 por categoria no ritual normal e 2 na sobrecarga. O estado de elegibilidade vem do servidor. Sobrecarga pode começar com `99 < bond < 300` no ajuste inicial. A margem vale somente no início. A drenagem continua durante a execução e o teto de 300 se aplica ao saldo projetado mais 50 na conclusão. Códigos do ritual: `NOT_WITCH`, `INVALID_REQUEST`, `MODE_UNAVAILABLE`, `MISSING_MATERIALS`, `RITUAL_ACTIVE`, `RITUAL_NOT_ACTIVE`, `UNSAFE_STATE`, `RITUAL_UNAVAILABLE`; erros de sessão já existentes do UI Core continuam possíveis.

`active.id` identifica a execução, `remainingMs` reflete o relógio autoritativo e `result.id` permite apresentar um resultado uma única vez. Pode-se animar a barra entre respostas, mas a conclusão depende da resposta autoritativa. `result` é um resultado transitório da sessão, preservado para reabertura da tela durante a sessão, até começar outro ritual. Desconectar encerra a execução e limpa o estado da UI. Reconectar começa sem execução. Uma falha de confirmação de consumo resulta em `failed/uncertain` com crédito zero, conforme D7. O servidor replica a runa e o áudio aos observadores. Abrir ou fechar o módulo não altera esses efeitos.

O servidor sempre preenche `endReason` ao encerrar, com `movement` também quando detecta deslocamento ou troca de célula. A HUD monta a mensagem a partir dele. Os campos legados `outcome` e `reason` e o discriminante `v: 1` preservam clientes anteriores. Durante o Ritual, a propriedade privada `aetheriusVeilRitualStatus` replica `{ v, active, result }` no início, a cada segundo e no fim. O cliente usa o temporizador fora do TAB e envia cancelamento com `reason: 'movement'` pelo envelope autenticado. Emotes na tecla K não são comandos de movimento. O cliente deduplica mensagens pelo ID observado. Uma sessão encerrada não recebe a notificação e a seguinte começa com a HUD limpa.

A resposta de aceite de `ritual.start` é `{ ok: true, ritual: { …, active: { id, … } } }`. A interface fecha ao receber esse aceite. Erro, timeout ou resposta sem `active.id` mantém a interface e recupera o estado por `ritual.status`; a presença de um ritual no status permite retomá-lo na apresentação mesmo se a resposta do início se perder. A execução segue no jogo e pode ser interrompida pelas condições habituais. Reabrir a tela consulta a execução atual. O comando explícito de cancelamento permanece disponível no contrato e exige o ID da execução.

O Ritual concluído credita o saldo autoritativo uma vez, após o consumo confirmado. A HUD entrega atualizações normais a `setValue(bond)`. Um alvo pelo menos 2 pontos acima do valor exibido aciona passos de um ponto a cerca de 10 pontos/s. Cada projeção atualiza o alvo. Enquanto o alvo superar o valor exibido, o selo segue subindo. Ao chegar a um alvo igual ou inferior, uma redução usa a interpolação suave. O saldo, os olhos, a aura, os buffs e a capacidade de criaturas usam o valor já confirmado pelo servidor. No login, na reconexão, na recarga ou na restauração da HUD, a apresentação usa `setValue(bond, { immediate: true })`, que cancela a animação e aplica a projeção atual.

## Terror e sussurros

O servidor agenda tentativas privadas no estágio IV a cada 12–20 min (incluindo 35% de tentativas sem envio) e no estágio V a cada 5 min com manifestação. Envio efêmero só ao dono, na propriedade SkyMP `aetheriusVeilTerror`, cuja atualização grava em `sp.storage.aetheriusVeilTerrorV1`. A projeção `aetheriusVeilPresentation` é a condição contínua para apresentar ou interromper o efeito. Formato:

```ts
type VeilTerror = null | {
  v: 1;
  id: string;
  kind: 'audio' | 'thought' | 'heartbeat' | 'blackout';
  variant: 'scream_witch' | 'whisper2_witch' | 'whispering_witch' | number | null;
  durationMs: number;
};
```

`id` permite deduplicar por sessão, `variant` é um ID de áudio, o índice `0–9` da lista de pensamentos da fonte §6.2-B ou `null` para batimento/tela preta. `durationMs` vale `10000` no batimento, `1000–1500` na tela preta e `0` para áudio/pensamento (duração do recurso ou notificação). Limpar a propriedade após a entrega curta e ao sair de terror; `null` na caixa de eventos limpa a pendência, enquanto a saída de estágio IV/V, troca de célula, morte e desconexão encerram a manifestação ativa. O cliente mantém um efeito por vez e pode vetar tela preta pelo contexto local (combate, salto, queda, diálogo, menu ou cena). Se a tela preta for vetada, apresentar um pensamento seguro no estágio V. A tentativa de tela preta já consome o cooldown autoritativo de 90 min. Sussurros de sobrecarga são efeitos locais de `overload: true` (`whisper2_witch` e `whispering_witch`), encerrados ao receber `false` ou limpar a sessão.

## Pontos existentes de integração

- Servidor local: `C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural-service.js:207` (ações do módulo e operação autenticada); `supernatural\blood-presentation.js:2` (propriedade SkyMP privada e ligação com a HUD).
- Cliente local: `C:\Code\Aetherius - Servidor Local\client-source\client\src\services\services\aetheriusUiService.ts:115` (encaminhamento autenticado do envelope); `client\ui-core\modules\supernatural\veil-seal.js` (selo existente).

## Histórico

- 18/11/2026 — Contrato v2 decidido pelo Opus, com `ritual.cancel.reason`, `result.endReason` e projeção privada do Ritual ativo. Mantém `v: 1` no discriminante e aceita chamadas da revisão anterior. `VeilPresentation` e `VeilTerror` conservam o formato original.

- 18/11/2026 — Opus ajustou a elegibilidade de início da Sobrecarga de `bond >= 100` para `bond > 99` com limiar configurável. A condição é autoritativa por `modes.overload.eligible`. O formato das mensagens e a versão v1 permanecem aplicáveis.
- 18/11/2026 — O dev definiu fechamento da interface no aceite de `ritual.start` e ganho gradual apresentado pelo selo. O serviço continua creditando uma vez pela D7. As mesmas mensagens v1 já trazem o aceite e o novo saldo.
