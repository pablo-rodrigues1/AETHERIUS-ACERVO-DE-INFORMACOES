# Etapa 2 (parcial) — Vampirismo e Lorde Vampiro no servidor local

Data: 2026-11-15. Autor: Opus 5.5 (orquestrador), a pedido do dev. **Ainda sem revisão cruzada e sem teste em jogo.**
Ambiente: `C:\Code\Aetherius - Servidor Local`, commit raiz `e1ecfe0`. Complementa `ETAPA-2_lobisomem.md`.

## Fonte das regras

`referencia/Aetherius_Sistema_Vampirismo_Mecanicas.md` foi lido por inteiro. **O que decidimos nesta pasta prevalece sobre ele** (ordem do dev):

| Tema | O .md antigo | Vale (decisão atual) |
|---|---|---|
| Curva de XP | Conflito 46.500 × 2.000.000 | 20k / 40k / 130k / 420k / 1,39M (total 2.000.000) |
| Grau das disciplinas | "proposto, não ratificado" | Grau = estágio − 1; 2ª disciplina no Apoteótico, entra no grau 5; sem troca |
| Lorde Vampiro | "não aprovado como funcional" | O dev pediu que funcione e sincronize, como a fera do kdu |
| Inatos | "referência" | A planilha manda: inatos vêm do COTV e não dependem de estágio |

Pontos do .md que seguem **pendentes** e não foram inventados: números de sol, fome e sangue; bloqueio de curas humanas por origem; Potência no dano real; bônus de vida, magicka e vigor por rank e disciplina; dano desarmado; linhagem e Sangue-Fraco.

## O que foi feito

1. **Serviço Sobrenatural** (já existente) passou a aplicar efeitos ao vampiro (`supernatural/vampire-grants.js`, records conferidos pelo houseCARL):
   - **Inatos (desde o Neófito):** VampireVampirism, VampirePoisonResist, COTV_ResistFrost, COTV_WeaknessToFire, COTV_SupernaturalAwareness, COTV_EnhancedStamina01, COTV_WeaknessToSunlight, VampireHuntersSight (Visão), VampireCharm (Sedução), COTV_VampireBatForm, COTV_VampireDrain, COTV_VampireRaiseDead.
   - **Por estágio (acumulam):** Vampiro Pleno → COTV_ImmeasurableStrength; Ancilla → DLC1VampireMistform; Ancião → COTV_UnnaturalCelerity; Suserano → COTV_DevouringSwarm.
   - **Lorde Vampiro:** poder `DLC1VampireChange` (Dawnguard 0x283B). **O estágio mínimo ainda não foi decidido**; está liberado desde o Neófito para permitir o teste (`VAMPIRE_LORD_MIN_STAGE`).
   - **Raça:** na setagem, a aparência troca a raça-base pela variante vampírica (Nord → NordRaceVampire etc., 10 raças vanilla). A raça original fica no registro e volta na remoção. Raças fora da tabela não mudam.
   - Remoção: tira todos esses spells, encerra a forma de Lorde e devolve a raça.
2. **`vampire-lord-service.js`**: Lorde Vampiro sincronizado com os mesmos preceitos do `werewolf-service` do kdu.
   - O cliente avisa o uso do poder e a mudança da própria raça (o script do Dawnguard/COTV transforma o PC localmente).
   - O servidor confere que o jogador é vampiro com o poder, guarda a aparência original e publica a aparência com `DLC1VampireBeastRace` para todos. Os outros recriam o boneco como Lorde.
   - Reverter (a raça volta a mortal), morrer, sair ou perder a maldição devolve a aparência. Se o servidor cair, a restauração acontece no login (`data/aetherius-vampire-lord.json`).
3. **Cliente local recompilado com o patch de cliente do kdu** (`overlays/cliente-local/`). `switchPlayerCreatureRace` troca a raça do próprio ator para qualquer raça sem `ActorTypeNPC`, o que cobre fera e Lorde. O build sem o patch é byte-idêntico ao `skymp5-client.js` do MO2, então a única diferença é o patch. Instalado só no mod `AETHERIUS - SERVIDOR LOCAL`.
4. **Animação do lobisomem em `vanilla`** no servidor local (`WEREWOLF_ANIMATION=vanilla`): o mod de animação e o plugin do kdu não estão no perfil LOCAL.
5. **Painel F8:** ganhou a seção do vampiro (5 disciplinas), a da bruxa (3 vertentes) e o botão "Encerrar forma (Lorde/Fera)".

## Evidências

- `overlays/sobrenatural/teste-servico.cjs` passou. Além dos casos do lobisomem: setagem de vampiro troca a raça para NordRaceVampire e dá o poder do Lorde; Força Imensurável só aparece a partir do estágio 2; a raça do Lorde vinda do PC publica a aparência de Lorde e a volta restaura a vampírica; a remoção restaura a raça original; a keyword some do disco na remoção e a revisão continua subindo.
- Boot: `[vampire-lord] Lorde Vampiro sincronizado ativo (raça 0x200283a, poder 0x200283b)` e `[supernatural] … forms 65/65`.

## Pendências

- Teste em jogo com 2 jogadores (fera e Lorde vistos pelo outro).
- Estágio mínimo do Lorde Vampiro.
- Bônus numéricos (rank, disciplinas, dano desarmado, Potência): o único canal nativo é `aetheriusAttributeBonuses`, que pertence ao sistema de atributos do GameplayCore (pontos de classe). Precisam ser integrados lá ou ganhar habilidades num plugin.
- Política do .md ainda em aberto: bloquear cura humana, sol pelo servidor (hoje vale o COTV_WeaknessToSunlight), fome e sangue.
- Lorde Vampiro sem animação de transformação replicada para os outros (eles veem a troca de modelo). Com o plugin e a animação do kdu, a fera também ganharia os efeitos dele.
