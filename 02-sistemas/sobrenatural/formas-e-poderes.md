---
titulo: Formas e poderes do Sobrenatural no laboratório
tipo: sistema
status: rascunho
fase: em-desenvolvimento
responsaveis: [pablo-rodrigues1, kdu]
repositorio: "projeto local: C:\Code\Aetherius - Servidor Local"
depende_de: [../lobisomem/README.md, ../gameplay-core/README.md, ../ui-core/README.md]
atualizado: 2026-11-15
tags: [sobrenatural, sincronizacao, poderes, investigacao]
relacionados: [README.md, diario.md, ../../03-decisoes/ADR-0014-forma-besta-recarga-real.md]
---

# Formas e poderes no laboratório

## Resumo

O teste humano encontrou transformações inativas, aparência vampírica mortal e poderes parcialmente visuais. O lote local `3299cf1` prepara correções para reteste; revisão cruzada e homologação em jogo continuam pendentes. Névoa e Força encontraram limites do host que exigem decisão do orquestrador antes de integração nativa.

## Onde está o código

- Raiz Git local: `C:\Code\Aetherius - Servidor Local`, commit `3299cf1` sobre `ec7d455`.
- Código ativo: `source\server\gamemode\supernatural-service.js`, `werewolf-service.js`, `vampire-lord-service.js`, `supernatural\power-input.js`; espelhos no overlay `overlays\sobrenatural\files`.
- Cliente recompilado em `client-source`, base `e39163c`; patch completo `overlays\cliente-local\002-formas-raca.patch` substitui o 001.
- Relatório completo: `C:\Code\Aetherius - New Systems\Sobrenatural\docs\ETAPA-2_formas-e-poderes.md`, dez investigações por item em `docs\investigacoes`; espelho versionado em `overlays\sobrenatural\docs`.
- Só perfil MO2 `AETHERIUS - LOCAL`; plugin `AETHERIUS - SOBRENATURAL.esp` e bundle no mod `AETHERIUS - SERVIDOR LOCAL`. Oficial e demais mods preservados.

## Evidências e alterações

| Item | Achado e estado |
|---|---|
| Fera e Lorde | O cliente bloqueia eventos Papyrus. O cast não aplica genericamente efeitos no host; MGEF ativo não prova execução de quest. Leitor adicional detecta cast/borda de efeito e encadeia hook nativo autenticado às sessões autoritativas; logs por entrada. O ponto exato da perda de entrada antiga ainda precisa de reteste. |
| Recarga | WerewolfChange era Power diário. Override LesserPower e recarga persistida pelo servidor: 1800 s reais, local 0. Ver ADR-0014. |
| Raça/olhos | Teste usava Nord vanilla; servidor já guardava a original. Cliente só fazia SetRace em criatura; corrigidos humanoide, login e olhos vampíricos na apresentação de dono/vizinhos, preservando os headparts originais para restauração. |
| Visão | Predator Vision sobrescrevia VampireHuntersSight. Candidato copia efeitos de Aura Whisper III: 30 s, áreas 500 exterior/300 interior. Ainda não comprovado em jogo. |
| Morcegos | O dash depende de ShoutStart/ShoutSprintLongStart em script bloqueado. Falta executor autorizado, replicação pública e teste de colisão. |
| Névoa | O script bloqueado desativava fighting/activate. O host preserva regeneração base por max(baseRate,actorRate), sem hook geral JS de magicka/vigor. Correção integral requer integração adicional no host. |
| Enxame | Cloak → Absorb Health não tem executor autoritativo genérico. Falta resolver alvos/PvP/dano/cura sem duplicação. |
| Força | PerkToApply temporária não está integrada ao cálculo do host; DS5A declara perks compiladas de classe e bash sem fórmula certificada. Ícone ativo não comprova eficácia. |
| Sol | Funcionamento relatado pelo dev; preservado. Não equivale à entrega de política solar autoritativa. |

## Validação e limites

29 testes do kdu, 5 novos testes do lote e testes existentes de serviço/F7 passaram. Webpack, Build/Prepare/Start e boot com 102/102 forms passaram; houseCARL apontou zero referências quebradas, masters ausentes ou records não escaneáveis no plugin. Jogo estava fechado antes do reinício. Esses checks não substituem teste humano nem revisão de outro modelo.

Não se liberou Papyrus globalmente nem se recompilou DLL/addon. Não se implementou a quest inteira de Lorde (menus/levitação/equipamentos/spells). As alternativas de extensão nativa ou entrega parcial explícita serão submetidas a Opus, conforme o despacho.

## Próximo passo

Revisão Terra High e teste humano de Z/retorno/recast, raça/olhos/login e auras; depois teste com segundo observador. Antes de continuar Névoa/Força, decidir a integração ao owner nativo de regeneração/dano. A Etapa 2 permanece aberta.
