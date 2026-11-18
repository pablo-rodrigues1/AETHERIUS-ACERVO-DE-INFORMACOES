# RETOMADA — Sistema Sobrenatural

> **18/11, Opus — Vínculo do Véu (Bruxaria) ESPECIFICADO E CONFIRMADO, NÃO INICIADO** por ordem do dev: ver `docs/ETAPA-4_bruxaria-vinculo-do-veu.md` (Sol: servidor/ritual/runa; Terra: selo, tela do Ritual, terror, áudio). Pendente só confirmar quem escolhe os ingredientes (suposição: servidor). Despachar apenas quando o dev mandar.

> **18/11, Opus — RODADA DE CÓDIGO FECHADA; próximo passo = TESTES EM JOGO.** Todas as revisões cruzadas aprovadas (escopo de código): R1, R2, R3, porte v0.42 (P1/P2), Beber Sangue sem consentimento + regra elmo/capuz (D3), D7 "na dúvida não credita" (`0ec39d2`, `2088225`; flag ligada só em `config/local.env`), HUD vertical (`7c5601a`/`8060a10`), vinheta da Sede Crítica (`f4236da`, `1088d0b`), heartbeat vanilla `UIHealthHeartbeatALP 04DAAF:Skyrim.esm` em loop (`17ae47c`), hot-reload do storage (`93d991a` → `2fffaf9`, aprovado por Sol em `7e62f94`). Bateria: 415/415 em cada modo, Core 39/39, serviço/F7 OK. Sol High agora é **GPT-6.2** (`AGENTS.md`). Abertos: D1, D2, D4, D5, D6; XP de maldição, reset de classe, P10. Testes em jogo: instalar cliente novo no MO2, servidor local Stop→Build→Prepare→Start, cliente iniciado do zero, dois jogadores (roteiro em `docs/PENDENCIAS.md` §4 + áudio/vinheta/HUD/Beber Sangue).

> **17/11, Sol — D7 implementada no candidato local:** poção `0ec39d2`, alimentação `2088225`. Crédito da poção depende do EatItem observado e do resultado certo do ledger. Alimentação interrompida preserva vida e cooldown, registra observações e encerra sem replay. Bateria **410/410** nos dois modos e Core **39/39**. Flag true em `config/local.env`, default false. Runtime ainda precisa receber o gamemode atualizado. Próximos passos: revisão independente D7 e teste humano com cliente iniciado do zero. Ver DECISOES e ETAPA-3.

> **17/11, Sol — D7 decidida pelo dev:** descartar recompensa sob falha ou dúvida, com ativação da alimentação apenas no laboratório depois da implementação. Triagem em `1088d0b` identificou HP possivelmente já alterado e crédito legado sem recibo antes de SETTLED. Arbitragem encaminhada ao coordenador. Ver DECISOES e ETAPA-3. Nesta triagem, o comportamento em código segue o anterior.

> **17/11, Sol — P1 do porte + D3 + revisão HUD:** correções candidatas `b682f61` (sem dispatcher, entrada da fera falha fechada) e `5d3c216` / cliente `33953e1` (autoria racial conservadora autorizada pelo Opus; preservar legados/ambiguidades). Beber Sangue **sem consentimento**, início direto só com ambos sem elmo/capuz permitido, em `b4d161c`; flag permanece default false e journal/quarentena intactos. **381/381 Node + 39/39 Core + serviço/F7 + TypeScript/build passaram**, sem instalação/runtime/jogo. HUD vertical Terra `7c5601a`/`8060a10`: revisão Sol **aprovada no recorte de código/prévias**, CEF/jogo pendentes. Próximo passo: Terra reconfirmar P1/P2 e revisar alimentação; coordenador consolidar Acervo; limpeza de legados raciais exige evidência. Ver COMPARACAO §13 e as seções novas de ETAPA-3.

> **Porte seletivo v0.42 — 17/11, Sol High:** decisão do dev: **não adotar base oficial**. Voz Z integrada ao comando validado (`cb84cad`), habilidades raciais mescladas à fila (`fefdf3b`; cliente `e1fdf9d`) e proteção de headparts no load (`87835ed`; cliente `444bd6a`). **252/252 Node + 39/39 Core + serviço/F7/revisão F7 + TypeScript/webpack passaram**. Scripts source/overlay idênticos; patches cliente 007/008. Bundle compilado **não instalado**, sem MO2/runtime/jogo. Próximo passo deste lote: revisão Terra, consolidação Acervo pelo coordenador e roteiro humano. Detalhes: `docs/COMPARACAO_lobisomem-oficial-v0.42.md`, §12, e `docs/ETAPA-2_lobisomem.md`.

> **Residual R2 #2 — 17/11/2026, Sol High:** callback atrasado limitado a `min(now, offlineAt)` em `a35b0e8`; reaparecimento observado fecha o segmento anterior e reabre sem o marcador antigo. **314/314 testes Node + serviço/F7/revisão-F7 passaram**. Terra já reconfirmou #3/#4/retry; **Terra reconfirmou o residual (17/11): R2 aprovada no escopo dos testes.** R3 (Sol revisa bloqueio de cura) em andamento. D7 permanece: crédito antigo da poção ativo sem flag e não atômico; alimentação default false. Criaturas e demais roteiros em jogo continuam obrigatórios, sem instalação, runtime ou jogo nesta rodada.


> Leia este arquivo **primeiro** ao retomar o trabalho. Atualize-o ao fim de cada sessão/etapa (estado, próximo passo, pendências).

**Última atualização:** 2026-11-16 (noite) — Opus 5.5: agentes parados; pendências detalhadas em `docs/PENDENCIAS.md`.

## Atualização 17/11 (Opus)
- **R1 concluída:** Terra reprovou B (achado B1); Sol corrigiu em `e86c0cb`; Terra reconfirmou: **A e B aprovados** (164/164 gamemode, 39/39 Core). Ver `docs/REVISAO-3AB_CONFIRMACAO_terra.md`.
- **Reserva de Sangue (Sol, em andamento):** dano solar candidato `4c631e1` (sem revisão). Atomicidade da poção **bloqueada** → decisão do dev **D7** (ampliar contrato nativo ou aceitar garantia menor). Sol segue nos itens 2, 3 e 5.

## Estado dos agentes — encerramento de 16/11 (noite)

**Todos os agentes PARADOS pelo dev.** O Sol High foi interrompido com as pendências A/B prontas mas sem commit; o Opus salvou esse trabalho como candidato no commit `88cbbd0` (servidor local), ainda **sem confirmação do Terra High**. Servidor local parado; cliente novo (`client-source 757c571`) **não instalado** no MO2.

**Leia `docs/PENDENCIAS.md`**: lista detalhada de revisões, desenvolvimento, decisões do dev e testes em jogo pendentes, com o passo a passo para retomar.

## Estado atual

- Etapa 0 ✅ fundação documental.
- **Etapa 1 ✅ ENCERRADA (frontend)**: 1.4 redesenho aprovado pelo dev e revisado por Terra High (aprovado com ressalvas de integração: 4 exceções de CSS do shell aceitas). Valores do lobisomem atualizados (armadura 150–400; RM 10–35%%; vida e vigor somados na fera). Histórico: 1.1 coleta; 1.2 telas (Terra); 1.2b correções; 1.3 ícones + Sovngarde (Sol). Revisão final de Sol High: **aprovado com ressalvas** (achados menores 4 e 5 adiados para a Etapa 2).
- Código: `ui-core-module/modules/supernatural/` (data, icons, module .js/.css), `preview/` (index, icons.html, test-ordering.cjs, screens/), `INTEGRACAO.md` (diff do manifest + contrato com o servidor).
- Ainda não testado dentro do jogo (Meridian/CEF). O repositório oficial não foi alterado.
- Base do lobisomem: pacote de kdu em `AETHERIUS - LOBISOMEM 14-11 - KDU/`. Trabalhamos a partir dele e compilamos tudo depois em uma pasta final.
- Acervo: `02-sistemas/sobrenatural/` na `main` (responsáveis: pablo-rodrigues1 e kdu).

## Leitura obrigatória (nesta ordem)

1. `../AGENTS.md` — papéis, regras, fluxo.
1b. `docs/PENDENCIAS.md` — **o que falta, em detalhe (comece por aqui ao retomar)**.
1c. `docs/DECISOES.md` — **todas as decisões de funcionamento das mecânicas** (regras, valores, prevalência).
2. `docs/ESPECIFICACAO.md` — transcrição da planilha, regras fixas e curva de XP.
3. `docs/DIRECAO-DE-ARTE.md` — padrão visual, cores e análise das referências (inclui Disciplinas).
3b. `docs/PROPOSTA-TEXTOS.md` — textos de Lobisomem/Bruxa/tela vazia (aguardando aprovação).
3c. `AETHERIUS - LOBISOMEM 14-11 - KDU/LEIA-ME.md` — sistema base do lobisomem.
4. `docs/PLANO.md` — etapas e pontos de integração mapeados.
5. `docs/diario.md` — histórico.

## Próximo passo

- **16/11, Sol High — continuação:** origem persistida, integração Bruxaria, powers JS, Visão própria e tempos 40/60 (Morcego 1/0), contrato de sangue e flag solar preparados. 122 testes + serviço/F7; revisão Terra e jogo pendentes, por decisão do dev sem teste humano hoje. Ver ETAPA-2_formas-e-poderes.md e ETAPA-2_bruxaria.md. Próxima tarefa despachada: transformação forçada após 72 h reais online, à noite 20h–5h, em lote separado.

**Atualizado em 16/11 (Opus).** Sem testes em jogo nesta sessão, por decisão do dev. Tudo abaixo fica registrado para uma rodada de testes posterior.

### Estado no encerramento (16/11)
Terra confirmou parcialmente o lote anterior e abriu A/B; Sol High preparou a segunda correção e aguarda nova confirmação independente via coordenador. Servidor local PARADO; tasklist confirmou SkyrimSE/VR ausentes. Bundle novo no client-source ainda precisa de instalação no mod LOCAL após revisão; o runtime do servidor foi preparado sem reinício.

| Frente | Situação | Onde |
|---|---|---|
| Correção por origem (magias/perks) + Bruxa ligada ao serviço + poderes do vampiro (40 s/60 s; Morcego sem recarga) + ponto de custo de sangue | **Commitado** por Sol High: `79098ad` (git raiz do servidor local). Patches de cliente em `overlays/cliente-local/002` e `003`; checkpoint do client-source `057f855`. | `docs/ETAPA-2_formas-e-poderes.md`, `docs/ETAPA-2_bruxaria.md` (quadro final da ligação corrigido: concluída em `79098ad`) |
| Transformação forçada do lobisomem | **Candidato implementado:** 72 h reais online, noite 20h–5h, sequência privada de 20 s, animação normal kdu, reset confirmado e F7. 122 testes Node + serviço/F7 passaram; revisão independente e jogo pendentes. | `docs/ETAPA-2_transformacao-forcada.md` |
| Revisão Terra 3 (origem/Bruxa/formas/forçada) | Base confirmada parcialmente; pendências A/B corrigidas pelo autor em lote separado: autoria durável conservada e fila de intenções canceladas. **197 testes; nova confirmação Terra pendente.** | `docs/REVISAO-3AB_CORRECOES_sol.md` |
| Reserva de Sangue | **Parcial** (Terra High): núcleo, drenagem, reserve/commit/cancel, crédito da poção, filtros de cura. Commits `708f018`, `feae242`, `b756602`. | `docs/ETAPA-3_vampirismo-mecanicas.md` |
| Assinatura dos scripts (causa de nada rodar no cliente local) | **Corrigido no local** (`68a6e2b`), não testado em jogo. | `docs/investigacoes/INVESTIGACAO_15-11_scripts-nao-assinados.md` |

### Próximo passo ao retomar
1. **PRIORIDADE — nova confirmação Terra das pendências A/B:** relatório `docs/REVISAO-3AB_CORRECOES_sol.md`; patch 011 do Core após 010 e 005 do cliente após 004. A autoria é mantida duravelmente com `CLAIM_RELEASED` até ausência/remoção; intenções canceladas têm fila de reconciliação em runtime. 158 + 39 = 197 testes passaram. Cliente novo no checkpoint `757c571`, compilado e NÃO instalado no MO2; servidor preparado/parado. Após retorno do coordenador, retomar a Reserva de Sangue; ele consolida o Acervo. Transformação 72 h/noite, F7 e demais regras continuam candidatas sem teste humano.
2. **Restante da Reserva de Sangue: reclassificado alta complexidade por Terra; atribuído a Sol High pelo Opus em 16/11**, após entrega da transformação forçada: sol pelo servidor (`AETHERIUS_VAMPIRE_SERVER_SUN`), ligação do custo aos poderes (contrato reserve/commit/cancel já no dispatcher do Sol), suspensão de fome/sede/frio por personagem + HUD de sangue, "Beber Sangue" no menu X, efeitos (batimentos, visão, aura Feral, nome VAMPIRO FERAL), atomicidade da poção e precisão do relógio de sessão.
3. **Revisões cruzadas** (lista abaixo) antes do teste em jogo.
4. **Rodada de testes em jogo** (lista abaixo), começando pela assinatura dos scripts.
5. Decisões pendentes: restauração dos valores de fome/sede/frio ao deixar de ser vampiro, P6 textos e P10 Lorde só pelo painel. Implementar o literal da Sede Crítica (sair >30) e raio de visão 25 m. Decididos: alimentação só em jogadores (20% vida máxima não letal, 2 m, 60 min/vítima/PvP); custo 5 nos sete poderes gerenciados; Sedução/Reanimação isentas, preservando regras próprias.

### Testes em jogo pendentes (perfil MO2 `AETHERIUS - LOCAL`, setagem pelo F7)
1. **Assinatura dos scripts** (correção do orquestrador, commit `68a6e2b`): confirmar que os scripts do servidor rodam no cliente. É pré-requisito dos itens 2 a 4.
2. **Lobisomem:** transformação forçada (72 h online, à noite, sequência de 20 s; usar o atalho de teste); Forma da Besta (Z) com a animação do kdu; Encerrar forma pelo F7; recarga 0 local; perks e bônus (plugin AETHERIUS - SOBRENATURAL) por estágio.
3. **Vampiro:** raça vampírica (já aprovada em jogo) e olhos após relog; Lorde Vampiro (Z); Morcego (dash), Névoa, Enxame (dreno), Força, Visão (Aura Whisper); duração 40 s e recarga 60 s; Morcego sem recarga.
4. **Bruxa:** vertente pelo F7; feitiços e perks por estágio; upgrades do Shadowbolt (Acólito, Hierofante) e do Atronach (Alto Teurgo); pisos de habilidade 30→60; quests do Shadow Spell Package paradas.
5. **Cura do vampiro:** poção de cura pura recusada; item misto liberado; magia de cura bloqueada; regeneração natural, Magicka e Dreno normais; não vampiro sem mudança.
6. **Reserva de Sangue** (quando entregue): barra no lugar de fome/sede/frio; drenagem; custo de 5 nos sete poderes gerenciados (Sedução/Reanimação isentas); Sede Crítica (batimentos + visão); Feral (aura + nome VAMPIRO FERAL); Poção de Sangue +50; "Beber Sangue" pelo menu X com 2 jogadores (e um terceiro observando).
7. **Dois jogadores:** fera e Lorde vistos pelo outro jogador; alimentação entre jogadores.

### Revisões cruzadas pendentes
- **Nova confirmação Terra 3 A/B:** os cinco itens anteriores foram parcialmente confirmados em 16/11; as duas pendências aceitas foram tratadas pelo autor. Ver `REVISAO-3AB_CORRECOES_sol.md`; não presumir aprovação desta segunda correção.
- Transformação forçada (Sol): revisar contador/relógio, fronteiras da noite, cancelamento durante turning, privacidade e roteiro F7; nenhum teste humano executado.
- Lote menor Fera/Lorde/raça (`4fc0034`): o dev dispensou a confirmação antes do teste; a revisão segue devida.
- Correção por origem + Bruxa + poderes (Sol): revisão do Terra High.
- Mecânicas do vampiro / Reserva de Sangue (Terra): revisão do Sol High.

## Pendências / perguntas abertas

| # | Pendência | Dono | Status |
|---|---|---|---|
| P1 | Publicação no acervo | Usuário | ✅ na main, com kdu como corresponsável |
| P2 | Curva de XP | Usuário | ✅ revisada: 20k + 40k fixos; o restante foi calculado até 2M (ESPECIFICACAO §1). Ajustar se o dev quiser outros números. |
| P3 | XP do estágio 1→2 | Usuário | ✅ 20.000 |
| P4 | Arte das telas | Usuário | ✅ resolvida: DIRECAO-DE-ARTE.md |
| P5 | Remoção/reset | Usuário | ✅ remoção apaga todo o progresso; o servidor reseta a classe ao setar |
| P6 | Textos de Lobisomem/Bruxa | Usuário | revisados com os sobretítulos "Corrupção da Lua/do Véu" e novos lemas; aguardando aprovação final |
| P7 | Trilha | Usuário | ✅ só "Sobrenatural" |
| P9 | Vertente/Disciplina | Usuário | ✅ Vertente no estágio 1; nenhuma troca; 2ª Disciplina no Apoteótico |
| P8 | Ícones de estágio de Vampiro (gota, escudo, morcego, coroa, enxame, sol) e equivalentes de Lobisomem/Bruxa: produzir no estilo de `icones estilizados.png`. | Qualquer modelo (imagem) | aberta |
| P10 | Poder do Lorde Vampiro só por função específica do painel administrativo | Implementação | **decidido, adiado**: para os testes segue liberado no Neófito (`VAMPIRE_LORD_MIN_STAGE`) |
