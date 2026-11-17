# PENDÊNCIAS — Sistema Sobrenatural (estado em 16/11/2026, fim da sessão)

Documento consolidado pelo Opus 5.5 para a retomada. Todos os agentes estão **parados**; nenhum worker ativo.
Ambiente de testes: `C:\Code\Aetherius - Servidor Local` (git raiz; código ativo em `source\`, patches/arquivos em `overlays\`). Cliente compilado a partir de `client-source\` (git próprio). Perfil MO2: `AETHERIUS - LOCAL`.

## 0. Estado dos repositórios no encerramento

| Repositório | Último commit | Observação |
|---|---|---|
| Servidor local (raiz) | `88cbbd0` | Candidato das pendências A/B, **salvo pelo orquestrador** depois que o Sol High foi parado. Concluído por ele (158 testes Node passaram), mas sem a confirmação do Terra High. |
| Servidor local (raiz), anteriores | `b6a3cf1` / `9b4230a` | Correções da revisão 3 (5 achados), confirmadas pelo Terra com 2 pendências. |
| | `856232a` | Transformação forçada do lobisomem. |
| | `79098ad` | Correção por origem, Bruxa ligada ao serviço, poderes do vampiro, contrato de custo de sangue. |
| | `b756602`, `feae242`, `708f018` | Reserva de Sangue parcial e bloqueio de cura (Terra High). |
| | `68a6e2b` | Assinatura dos scripts do servidor (correção do Opus). |
| client-source | `757c571` | Retenção de autoria de perks (pendência A). Pastas `build/` e backups `*.bak-formas` não versionados. |
| Acervo (main) | ver `git log` | Espelhos de documentos em `02-sistemas/sobrenatural/`. |

- **Servidor local:** PARADO.
- **Cliente compilado novo:** **NÃO instalado** no MO2. Instalar no mod `AETHERIUS - SERVIDOR LOCAL` (`Platform\Plugins\skymp5-client.js`) junto com o servidor novo antes de qualquer teste; o cliente antigo não aceita o formato novo de registro de perks.

## 1. Revisões cruzadas pendentes (prioridade 1)

| # | O que | Quem revisa | Documentos |
|---|---|---|---|
| R1 | **Pendências A/B** da confirmação da revisão 3 (commit `88cbbd0`). A: liberar um registro de perk (`CLAIM_RELEASED`) não descarta a autoria; o autor original continua responsável até observar a remoção. B: intenção de transformação cancelada vai para uma fila independente da sessão, que o servidor resolve enquanto está ligado (não fica órfã até o próximo boot). | Terra High | `docs/REVISAO-3AB_CORRECOES_sol.md` (no overlay: `overlays/sobrenatural/docs/`), `docs/REVISAO-3_origem-bruxa-forcada_terra.md` |
| R2 | Reserva de Sangue (quando o restante for entregue) | Terra High | `docs/ETAPA-3_vampirismo-mecanicas.md` |
| R3 | Bloqueio de cura do vampiro (`708f018`) | Sol High | `docs/ETAPA-3_vampirismo-mecanicas.md` |

## 2. Desenvolvimento pendente

### 2.1 Reserva de Sangue (Sol High; reclassificada como alta complexidade)
Especificação: `referencia/Reserva_de_Sangue_Aetherius.md`. Já feito (Terra): núcleo do sangue, drenagem, contrato `reserve/commit/cancel`, crédito da Poção de Sangue, bloqueio de itens de cura pura. O dispatcher de poderes do Sol já chama `reserve/commit/cancel`.

Falta:
1. **Dano solar pelo servidor:** 5/s, das 7h às 19h; chuva, neve, nevasca e interior protegem; nublado não; nado **não** protege até haver fonte confiável. Atrás de `AETHERIUS_VAMPIRE_SERVER_SUN` (com ela `true`, o `COTV_WeaknessToSunlight` deixa de ser concedido).
2. **Fome, sede e frio desligados POR PERSONAGEM** (lógica, efeitos e HUD) e **barra de sangue** vermelha com ícone no canto inferior direito. Interface: alterar só as cópias `(LOCAL)` dos mods (UI CORE / MERIDIAN - COMPATIBILIDADE); **nunca** sobrepor pastas `MeridianUI` entre mods (o Meridian dá 404).
3. **"Beber Sangue" no menu X** (SkySocial): só em jogadores; +100 de sangue e cura 200; vítima perde 20% da vida máxima sem morrer; alcance 2 m; recarga de 60 min por vítima; regras de PvP/zonas existentes; sessão exclusiva, timeout e limpeza; tela preta de 10 s com as mensagens na vítima; som e tela vermelha no vampiro; animação de feeding para os dois (prova de conceito com espectador).
4. **Sede Crítica** (≤30): batimentos em loop e visão automática (raio 25 m); sai quando >30.
5. **Feral** (0): aura vermelha visível a todos e nome "VAMPIRO FERAL" via SkySocial identity, sem alterar o nome salvo.
6. **Atomicidade da poção** (JSON da reserva × evento nativo de inventário × ledger PostgreSQL) e **precisão do relógio de sessão**.

### 2.2 Outros itens de desenvolvimento
- **Reset de classe na setagem:** a ponte `globalThis.aetheriusClassReset` não existe; hoje o comando avisa "reset de classe PENDENTE".
- **XP de maldição** pelas mesmas ações que dão XP de classe: falta o gancho no GameplayCore (hoje só pelo F7).
- **Névoa e Força Imensurável:** parciais por decisão do dev (zerar regeneração na Névoa e o bônus da Força no dano real exigem nativo).
- **Lorde Vampiro só pelo painel** (P10): decidido, implementação adiada; para os testes continua liberado desde o Neófito (`VAMPIRE_LORD_MIN_STAGE`).
- **Defeito do cliente oficial** em `settingsService.ts:121` (`publicKeys = {}` faz o cliente exigir assinatura): só contornado no ambiente local com chave de teste. Levar ao responsável pelo cliente/servidor oficial e verificar se o oficial tem `serverKey`.
- **Compilação final** em pasta única para o oficial: plugin `AETHERIUS - SOBRENATURAL.esp`, mods do kdu, Pandora rodado no perfil oficial, perfis do cliente oficial, patches de servidor e cliente.

## 3. Decisões que ainda dependem do dev
| # | Decisão |
|---|---|
| D1 | Como fome, sede e frio voltam quando o personagem deixa de ser vampiro (retomar valores congelados ou iniciar padrão). |
| D2 | Histerese da Sede Crítica (hoje: sai quando >30; proposta do documento: sair em ≥35). |
| D3 | Regras finais da mordida em jogador (hoje: proposta do documento) e consentimento/zonas seguras. |
| D4 | P6: aprovação final dos textos de Lobisomem e Bruxa (`docs/PROPOSTA-TEXTOS.md`). |
| D5 | Desarmado do vampiro no Apoteótico (assumido +20). |
| D6 | Itens marcados com `*` na planilha da Bruxaria (significado). |

## 4. Testes em jogo pendentes (rodada única; não houve teste em jogo desde 15/11)
Pré-requisito: instalar o cliente novo e preparar o servidor (`Stop` → `Build` → `Prepare` → `Start`), depois `scripts\Start-Client.ps1 -Mo2Profile 'AETHERIUS - LOCAL' -Profile 1 -SkseExecutableName 'SKSE'`. Setagem pelo F7 (aba Sobrenatural).
1. **Assinatura dos scripts:** confirmar que os scripts do servidor rodam no cliente (pré-requisito dos itens 2 a 4).
2. **Lobisomem:** Forma da Besta (Z) com a animação do kdu; Encerrar forma (F7); transformação forçada (atalho "Forçar transformação" no F7); perks e bônus por estágio.
3. **Vampiro:** Lorde Vampiro (Z); Morcego (avanço), Névoa, Enxame (dreno), Força, Visão; 40 s de efeito e 60 s de recarga; Morcego sem recarga; olhos após relog.
4. **Bruxa:** vertente pelo F7; feitiços e perks por estágio; upgrades do Shadowbolt e do Atronach; pisos de habilidade; quests do Shadow Spell Package paradas.
5. **Cura do vampiro:** poção de cura pura recusada, item misto liberado, magia de cura bloqueada, regeneração e Dreno normais, não vampiro sem mudança.
6. **Remoção da maldição:** some tudo o que o Sobrenatural deu; preexistentes ficam.
7. **Reserva de Sangue** (quando entregue): barra, drenagem, custo de 5 nos sete poderes (Sedução e Reanimação isentas), Sede Crítica, Feral, Poção de Sangue, "Beber Sangue".
8. **Dois jogadores:** fera e Lorde vistos pelo outro; alimentação entre jogadores (e um terceiro observando).

## 5. Como retomar
1. Ler `RETOMADA.md` e este documento.
2. Despachar ao **Terra High** a revisão **R1** (commit `88cbbd0`).
3. Despachar ao **Sol High** o restante da Reserva de Sangue (2.1), retomando a partir de `88cbbd0`.
4. Depois das revisões, organizar a rodada de testes em jogo (seção 4) com o dev.
