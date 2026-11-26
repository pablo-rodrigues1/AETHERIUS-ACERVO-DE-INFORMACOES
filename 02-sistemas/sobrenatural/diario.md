# Diário — Sistema Sobrenatural

## 2026-11-15 — Opus 5.5 (orquestrador)
- Início do sistema. Lidas as abas "Maldições - Informações" e "Maldições - Progressão" da planilha; transcrição em `ESPECIFICACAO.md`.
- Criados `AGENTS.md` (raiz), `RETOMADA.md`, `PLANO.md`.
- Mapeado: módulo `supernatural` já existe no catálogo do UI Core (rota `/supernatural`, slot 8), mas sem entrada em `modules.json`.
- Encontrado no acervo o sistema `lobisomem` (kdu, em-teste) — cobre a forma da fera, não a setagem/progressão. Publicação no acervo aguardando confirmação do dev (REGRAS 10.5.1).
- Achado: conflito na curva de XP da planilha (46.500 vs 2 milhões). Registrado como pendência P2.

## 2026-11-15 (2) — Opus 5.5
- Dev decidiu: vale o total de **2.000.000 XP**. Curva derivada (31x = 2M, x = 64.516). Confere exatamente com a referência de UI do vampirismo.
- Dev definiu a direção de arte: padrão do Class System (GameplayCore), branco sobre preto transparente, ícones estilizados; detalhes em Carmesim (vampiro), Âmbar (lobisomem) e Violeta (bruxa).
- Analisadas as 4 imagens de `referencia/`; resultado em `DIRECAO-DE-ARTE.md` (layout, textos dos 6 estágios do vampiro, estados dos cartões). Novo requisito: o servidor guarda a data da setagem.

## 2026-11-15 (3) — Opus 5.5
- Curva de XP revisada pelo dev: 1→2 = 20.000, 2→3 = 40.000; o orquestrador calculou o restante até 2M: 130.000 / 420.000 / 1.390.000 (acumulados 190.000 / 610.000 / 2.000.000; razão ≈3,26). Os limiares da imagem de referência ficam obsoletos.
- Decisões: trilha mostra só "Sobrenatural"; a remoção apaga todo o progresso; o servidor reseta a classe na setagem.
- Base do lobisomem: pacote de kdu (`AETHERIUS - LOBISOMEM 14-11 - KDU/`); kdu adicionado como corresponsável.
- Analisada `VAMPIRISMO - DISCIPLINAS.png`: nível da disciplina = estágio − 1; seção documentada em DIRECAO-DE-ARTE.
- Proposta de textos de Lobisomem/Bruxa/tela vazia em PROPOSTA-TEXTOS.md.
- Sistema publicado no acervo (`02-sistemas/sobrenatural/`).

## 2026-11-15 (4) — Opus 5.5
- Sobretítulos definidos: Corrupção do Sangue / da Lua / do Véu. Lemas novos: Lobisomem *"Sob a lua cheia, a carne esquece que um dia foi humana."*, Bruxa *"Quem rasga o véu jamais retorna inteiro do outro lado."*
- Nomes de feitiços, perks e poderes traduzidos para português.
- Vertente escolhida no estágio 1. Disciplina e Vertente não podem ser trocadas; a 2ª Disciplina vem no Apoteótico.

## 2026-11-15 (5) — Opus 5.5
- Coletas 1A (UI Core, Hydrogen) e 1B (estilo do Class System, Argon) concluídas.
- Etapa 1.2: telas implementadas por Terra High (`ui-core-module/`). Revisão de Sol High: **reprovada** por ordenação de estado, mais 2 apontamentos. Todos aceitos; decisões (revision, grantId, push + polling de 10 s, validação) registradas em REVISAO-1.2_sol.md.
- Dev definiu a fonte Sovngarde Bold (já em `ui-core/frontend/fonts/`) e pediu que todos os ícones sejam refeitos com prioridade. Etapa 1.3 em curso com Sol High.

## 2026-11-15 (6) — Opus 5.5
- 1.3: Sol High refez os 38 ícones (traço branco com pigmento por maldição) e aplicou a Sovngarde Bold herdada do shell; 14 capturas em preview/screens.
- 1.2b: Terra High aplicou revision, grantId, polling de 10 s + Atualizar e validação. Confirmação de Sol High: aprovado com ressalvas; achados 4 e 5 adiados para a Etapa 2.
- **Etapa 1 encerrada.** Próxima: Etapa 2 (servidor).

## 2026-11-15 (7) — Opus 5.5
- Etapa 1.4 (Sol High), pedida pelo dev: lobo principal redesenhado fiel à referência (perfil, mandíbula aberta, pelagem desfiada); Apoteótico virou "eclipse de sangue"; layout novo em duas colunas (hero + progresso com trilha de 6 marcos à esquerda; marcos e dons à direita). Lógica de estado inalterada (hashes iguais; test-ordering passa). 23 capturas em 1920×1080 e 1280×720. Relatório: ETAPA-1.4_redesenho.md.
- Aguardando aprovação visual do dev. A revisão cruzada da 1.4 ainda não foi feita.

## 2026-11-15 (8) — Opus 5.5
- O dev aprovou a interface e os ícones da 1.4. Ajuste final: lobo do hero em 54% (left 26%, top 27%), dentro do hexágono em todos os tamanhos (Sol High).
- Revisão técnica da 1.4 enviada a Terra High.

## 2026-11-15 (9) — Opus 5.5
- Buff do dev na armadura da forma de fera: 100/150/200/250/350/400 (antes 50/100/150/200/250/300). Resistência mágica inalterada (10/15/20/25/25/25%). Especificação atualizada; aplicação no módulo enviada ao Sol High junto com as correções 1.4.
- Segundo buff do dev na armadura da forma de fera: **150/200/250/300/350/400** (substitui o anterior). Resistência mágica inalterada.
- Terceiro buff do dev na forma de fera: resistência mágica **10/15/20/25/30/35%** (Delta e Alfa subiram). Armadura mantida em 150–400.
- Regra do dev: na forma de fera, os bônus de vida/vigor "humano e besta" e "só besta" se SOMAM (totais 275/350/425/500/575/650).
- Correções 1.4 + adendos do lobisomem aplicados (Sol High) e confirmados (Terra High). Exceções de CSS do shell aceitas. **Etapa 1 encerrada.** Etapa 2 aguarda o "pode iniciar" do dev.

## 2026-11-15 (10) — Opus 5.5
- Servidor local de testes em `C:\Code\Aetherius - Servidor Local` (homologação parcial aceita pelo dev). Revisões: Terra High → correções (Opus Low) → confirmação (Opus Low, escalação temporária). Codex fora por limite: a escala passou a ser Opus 5.5 Low + Gemini.

## 2026-11-15 (11) — Opus 5.5 (implementação própria, a pedido do dev)
- Etapa 2 iniciada pelo lobisomem: overlay do kdu aplicado; supernatural-service (estado, keyword só no servidor, revision/grantId, snapshot/choose, push, /maldicao); perks e magias do Manbeast por estágio (houseCARL, 27/27 resolvidos no boot); UI instalada no perfil MO2 LOCAL. Teste simulado ok; servidor sobe sem erro. Pendências em ETAPA-2_lobisomem.md.
- Painel de teste in-game (F8, só staff e só com AETHERIUS_SUPERNATURAL_TEST_PANEL=true no servidor local): botões para setar, remover e alterar XP; ação `test` no servidor. Script `supernatural-test.js` só no mod MO2 local. Commit local `525b2ab`.

## 2026-11-15 (12) — Opus 5.5 (implementação própria)
- Vampirismo: inatos e poderes do COTV por estágio, raça vampírica projetada, Lorde Vampiro sincronizado (vampire-lord-service, mesmos preceitos do kdu), cliente local recompilado com o patch do kdu (build base idêntico ao MO2), painel F8 com disciplinas e vertentes. 65/65 records no boot. Ver ETAPA-2_vampirismo.md. O .md antigo do vampirismo foi analisado; as decisões desta pasta prevalecem.

## 2026-11-15 (13) — Opus 5.5 (autorizado a escrever no modpack)
- Plugin AETHERIUS - SOBRENATURAL.esp (45 records: bônus de estágio, disciplinas e lobisomem humano/fera). Mods do kdu (animação + plugin) só no perfil LOCAL; Pandora rodado pelo MO2; saída isolada em AETHERIUS - LOCAL - PANDORA, com os mods compartilhados restaurados. Servidor local com 435 plugins, animação aeth, 102/102 records. Ver ETAPA-2_plugin-e-animacao.md.

## 2026-11-15 (14) — Sol High, despachado por Opus 5.5

- F8 investigado: registro do evento confirmado no boot, mas nenhum ack ready/keyError nem F8 chegou no teste do dev; TAB e F7 funcionam. Prefixo, ordem de registro e padrão do kdu analisados; logger de mouse não cobre F8. Causa não confirmada; investigação abandonada em favor do F7 por decisão do dev. Relatório: `investigacoes/INVESTIGACAO_15-11_f8-painel-teste.md`.
- F7 ganhou categoria Sobrenatural e sete ações no alvo selecionado: conceder/remover, XP assinado, disciplina, vertente, encerrar forma e consultar estado. Mesmo fluxo de autorização A+, sessão, requestId e auditoria do painel. API real preserva vagas/estágios; projeção não inclui keyword. F8 removido e flag local desligada.
- Frontend isolado em `AETHERIUS - SERVIDOR LOCAL\Platform\UI`, sem editar o Data original. Overlays `003-admin-panel.patch`, `004-admin-panel-client.patch`, executor e teste versionáveis no servidor local. Testes Node e sintaxe passaram; servidor reiniciado com jogo fechado. Revisão cruzada e teste em jogo solicitados ao orquestrador, que consolida o acervo. Ver `ETAPA-2_painel-f7.md`.

## 2026-11-15 (14) — Opus 5.5
- Decisão do dev: o poder do Lorde Vampiro será dado só por uma função específica do painel administrativo. Implementação adiada; para os testes continua liberado desde o Neófito.

## 2026-11-15 (15) — Sol High, correções da revisão e implantação F7

- Terra High não aprovou a primeira versão; Opus aceitou os quatro achados. Corrigidos: reautorização/identidade na fila e após reset, persistência só no personagem com efeitos adiados quando a sessão muda, restauração de Lorde/Fera verificável com recuperação preservada, parâmetros na reserva/auditoria e XP antes/depois, remoção da rota/flag/listener/projeção do teste antigo. Testes de concorrência/falha/auditoria passaram; novo build completo passou.
- Primeiro teste F7: aba ausente, pois o frontend havia sido copiado para Platform/UI (árvore legada). Segunda tentativa em MeridianUI/aetherius-client, por sobreposição no mod SERVIDOR LOCAL, causou 404 e prendeu o dev na tela até fechar o jogo. Opus corrigiu com cópia COMPLETA COMPATIBILIDADE (LOCAL), original desabilitado, e removeu as árvores sobrepostas de SERVIDOR LOCAL. Perfil versionado no cliente `e39163c`. Instalador e documentação agora exigem raiz única na cópia completa; houseCARL confirma um único provedor ativo. Novo teste e confirmação Terra pendentes.

## 2026-11-15 (16) — Sol High, encerramento do painel F7

- Terra High confirmou as quatro correções para teste local com ressalvas, preservadas na revisão. O dev confirmou a interface F7 funcionando em jogo na instalação final COMPATIBILIDADE (LOCAL); Opus autorizou o commit e o encerramento. ETAPA-2_painel-f7.md e a investigação F8 finalizadas, com limites explícitos e espelhos versionados no overlay local. Acervo a consolidar pelo orquestrador.
- O F8 permanece abandonado, causa não confirmada; seu caminho de teste foi removido. Formas/poderes e homologação ampliada de operações/PG/multiplayer são trabalho separado, a ser despachado depois.

- Commit final no servidor local: `ec7d45529f40bcac5e2845262eaea9fda644af85`, árvore raiz limpa; documentação final espelhada em `overlays/sobrenatural/docs/`.

## 2026-11-15 (15) — Opus 5.5
- Painel F7 com a aba Sobrenatural (7 ações) concluído por Sol High; revisão de Terra High (reprovado → corrigido → confirmado); interface aprovada em jogo pelo dev. Commit local ec7d455. F8 abandonado (causa não confirmada). Lição: no Meridian, uma pasta MeridianUI nunca pode vir de dois mods (404 por raiz canônica): usar cópias (LOCAL) completas.
- Teste do dev revelou falhas nas formas e poderes (fera, Lorde, raça vampírica, morcego, névoa, enxame, visão); tarefa enviada a Sol High (ETAPA-2_formas-e-poderes.md).

## 15/11/2026 — Sol High: formas e poderes, lote parcial
- Transporte redundante para fera/Lorde, recarga real padrão 30 min (local 0), SetRace/olhos/login e candidato Aura Whisper preparados. Testes Node/build/boot passaram; revisão cruzada e jogo pendentes.
- Limites concretos do host em regen da Névoa e perk temporária de Força; implementação desses trechos pausada para consulta ao orquestrador conforme despacho. Relatório ETAPA-2_formas-e-poderes.md e dez investigações por item.


## 2026-11-15 (16) — Opus 5.5
- Planilha revisada pelo dev (Bruxaria): Shadow Spell Package entra nos mods; a TENEBRAE foi refeita (Shadowbolt, Crushing Darkness, Shadow Stride, Curses, Shadowform, Shadow Atronach, Shadow Rend), com perks e upgrades por estágio. A seção 5 da especificação foi reescrita a partir das coordenadas das células, o que também corrige a distribuição por estágio de MORTUI, DAEMONES e dos perks globais (a versão anterior deduzia pela ordem das colunas).
- Decisão do dev: o upgrade dos feitiços da TENEBRAE é feito pelo servidor (troca da versão anterior pela melhorada no estágio); o upgrade automático do mod não será usado.
- Terra High investigou a Bruxaria e reclassificou para alta complexidade (investigacoes/INVESTIGACAO_15-11_bruxaria-concorrencia-grants.md): o reconciliador remove magias sem saber a origem e o cliente pode tomar perks de classe. A correção por origem (todas as maldições) e a Bruxaria foram para a fila do Sol High. Decisão do dev: pisos de habilidade via GameplayCore local (maior valor entre classe e maldição), sem nativo.

## 15/11/2026 — Sol High: retorno da revisão de formas
- Cinco achados Terra aceitos e implementados; Morcego, Enxame (baseline COTV LOCAL) e projeção pública da Névoa em JS. Névoa/regen e Força/dano permanecem parciais por decisão do dev; nenhum nativo alterado.
- 50/50 testes e serviço/F7 passaram. Boot inicial revelou colisão de hook com SkyDeath; corrigido com instalação após bootAll e documentado. Boot final 16 ativos/0 falhas; revisão Terra e jogo pendentes.
- Próxima tarefa recebida: Bruxaria e reconciliador por origem para todas as maldições; prioridade atual continua fechar revisão/teste das formas.

- Decisão do dev: a Bruxaria vai para os modelos Gemini (exceção ao AGENTS.md, para poupar o limite do Codex). Gemini 4 Argon despachado (fase 1: levantamento de records; fase 2: implementação depois do mecanismo por origem do Sol High). O Sol High faz só a correção transversal por origem.
- Decisão do dev: o lote menor (Fera, Lorde, raça e recarga) vai direto ao teste em jogo, SEM a confirmação do Terra High. Exceção registrada ao fluxo de revisão cruzada do AGENTS.md; a revisão desses itens continua pendente.

## 15/11/2026 — Sol High: candidato menor para teste
- Decisão posterior do dev: somente Fera/Lorde/raça/recarga agora; poderes e origem de grants depois, sem Bruxaria. Achados Terra 1/2/3/5 implementados e 4 adiado junto da Visão; nenhum novo hook mecânico/visual desses poderes instalado.
- 50/50 Node + serviço/F7/revisão F7 passaram; builds completos sem addon; servidor reiniciado com Skyrim fechado. Boot server-20261115-191225.stdout.log, PID 21092, 435 plugins, 16 ativos/0 falhas. ESP 46 records e perfil anterior intacto.
- Relatório/roteiro em ETAPA-2_formas-e-poderes.md; confirmação Terra e teste humano não executados pelo autor. Coordenador avisado por ask após commit. Material experimental preservado em backups/formas-proximo-lote-20261115 no laboratório.

- Fechamento do candidato menor: commit raiz `4fc00349cbe8e75d62be962e687f98d544e9a182`; Acervo publicado na main em `3f711373b4b5be231682974a5cbbd54aedf991c2` (relatório, diário/status, ADR-0015 e índices). Nenhum teste humano ou confirmação Terra foi presumido.
- Decisões da Bruxaria (dev): Necromancy = VKR_Con_030_Necromancer_Perk_WasNecromancy [PERK 000581DD]; Conjure Keeper = só Arqueiro e Guerreiro; Necromaster aparece como "Necromestre" na tela; Atronach entra direto na versão Expert no Alto Teurgo; o upgrade automático do SSP é parado no cliente para todos (servidor faz os upgrades); pisos de habilidade no GameplayCore local autorizados.
- Dev: Sol High parado (limite do Codex não resetou). Pendências dele que ficam abertas: correção por origem (magias/perks) e o restante dos poderes (morcego, névoa, enxame, força, visão). O último trabalho dele está no commit 4fc0034; nada dele ficou sem commit.
- Bruxaria preparada pelo Gemini 4 Argon (records do Luna MAX): witch-grants.js, parada das quests do SSP, pisos de habilidade no GameplayCore local, tela da Bruxa e testes; commit local 603dea7. Falta ligar ao serviço (depende da correção por origem). Investigação do orquestrador: scripts do servidor não assinados eram recusados pelo cliente (chave de teste aplicada no ambiente local, commit 68a6e2b; não testado em jogo).
- Vampirismo (mecânicas do .md), Terra High: valores PROVISÓRIOS aprovados pelo dev — sol 5/s (07–19; chuva/neve/nevasca/interior/nado protegem), mordida em NPC 25 dano/25 cura/60 s/+25 sangue, sangue 0–100 começando em 100; Poção de Sangue cura 100 e +50 sangue; morder jogadores PERMITIDO (cura 200, +100 sangue; dano na vítima e regras pendentes). Cura/comida: só itens de cura pura são recusados ao vampiro (sem nativo).
- Decisão do dev: poderes do COTV duram 40 s (+20 s) com recarga de 60 s a partir do uso (20 s de espera após o efeito); Forma de Morcego sem recarga. Dano solar confirmado em 5/s. Enviado ao Sol High.
- 16/11 — Reserva de Sangue (referencia/Reserva_de_Sangue_Aetherius.md) adotada como mecânica principal do vampirismo e enviada ao Terra High. Decisões do dev: tempo real online (13h30 de 100 a 0), alimentação só em jogadores pelo menu X (+100 sangue, cura 200; vítima perde 20% da vida máxima sem morrer, 2 m, 60 min por vítima, regras de PvP existentes), alimentação em NPC cancelada, Poção de Sangue +50 sangue (cura 100), morte mantém a reserva. Poderes custam 5 de sangue (gate no dispatcher do Sol). Duração 40 s/recarga 60 s só para Força, Névoa, Celeridade e Enxame; Morcego mantém o dash de 1 s sem recarga; Sedução e Reanimação mantêm regras próprias (decisão do orquestrador, a confirmar).
- 16/11: o dev decidiu não testar em jogo nesta sessão. RETOMADA ganhou a lista consolidada de testes em jogo e revisões pendentes.
- Terra High encerrou parcialmente a Reserva de Sangue (núcleo + reserve/commit/cancel + crédito da poção + filtros de cura; commits 708f018, feae242, b756602). Restante registrado no RETOMADA.
- Transformação forçada do lobisomem (dev): 72 h de tempo real online sem transformar → transformação forçada à noite (20h–5h do jogo), com sequência de 20 s (mensagens, batimento, tremor, filtro vermelho). Enfileirada para o Sol High.


## 2026-11-16 — Sol High, continuação origem/Bruxaria/poderes
- Implementados ledger estável de spells por personagem, claims compartilhados de perks com Core e ACK, integração do catálogo Argon/pisos 005, upgrades TENEBRAE conservando magias pessoais. Revisão crítica do Argon registrada; revisão independente Terra pendente.
- Poderes JS ligados após boot, Visão própria Aura Whisper imune ao conflito com Predator, tempos medidos e override 40/60 apenas para Força/Névoa/Celeridade/Enxame; Morcego 1 s sem recarga. Névoa/Força continuam parciais sem nativo. Reserva de Sangue integrada ao contrato Terra reserve/commit/cancel; Lorde só cobra na fase confirmada. Flag solar acordada permanece desligada, sem tick solar neste lote.
- Corrigidos condição de cooldown do Enxame e conflito da Força com cura genérica. 122 testes + scripts serviço/F7 passaram, builds e estrutura do ESP conferidos. Documentos detalhados e investigação atualizados. Dev adiou testes em jogo; roteiro preparado, sem solicitar execução. Próximo lote: transformação forçada 72 h online/noite, em commit separado.

- Fechamento do lote origem/poderes: boot final PID 7980, log server-20261116-001610.stdout.log; acervo main be29f35. Commit raiz local separado antes de iniciar transformação forçada.
- 16/11 — Sessão encerrada pelo dev. Sol High parado após o commit 79098ad (origem + Bruxa + poderes); transformação forçada não implementada (só um teste sem commit). Servidor local parado. RETOMADA atualizado com estado, próximos passos e decisões pendentes.
- 16/11 (retomada): Terra High reclassificou o restante da Reserva de Sangue como alta complexidade (sem alterações). Enfileirado para o Sol High depois da transformação forçada; o Terra High fará a revisão.


## 2026-11-16 — Sol High: transformação forçada do lobisomem
- Recuperadas as fontes ignoradas e teste inicial; versão final no overlay: 72 h online por personagem/concessão, noite [20h,5h), sequência privada 0/5/5,25/10/15/20 s, animação kdu normal. Checkpoint monotônico, stamp privado após raça confirmada e limpeza por cancelamento/watchdog.
- F7 ganhou Forçar transformação sobre o alvo, antecipando só as horas e mantendo noite/bloqueios, permissão, reautorização, sessão, requestId e auditoria. Fechados cancelamentos durante turning e troca de personagem sem sobrescrever o rosto substituto; filtro de 3 s renovado conforme leitura houseCARL.
- 121/122 testes Node (29 novos), serviço/F7/revisão F7, parse e reverse-check passaram; runtime preparado sem sync de plugins e servidor permanece parado. Nenhum teste em jogo, modpack ou OFICIAL alterado. Revisão independente pendente do orquestrador; roteiro em docs/ETAPA-2_transformacao-forcada.md. Corrigida a pendência antiga de ligação Bruxa: concluída em 79098ad.
- 16/11: transformação forçada entregue pelo Sol High (856232a, 122 testes; acervo bcdbd9e). Despachados: Sol High → restante da Reserva de Sangue; Terra High → revisão cruzada de 79098ad, 856232a e 4fc0034.
- Decisões do dev (16/11): Sedução e Reanimação sem custo de sangue; na remoção da maldição, remover tudo o que o Sobrenatural concedeu (limitação: magia ganha por outra fonte depois da concessão pode sair junto).


## 2026-11-16 — Sol High: correções prioritárias da revisão Terra 3

- Reserva de Sangue pausada antes de alterar seu núcleo. Cinco achados sobre 79098ad/856232a/4fc0034 aceitos pelo orquestrador e tratados pelo autor; confirmação Terra ainda pendente.
- Dev decidiu Sedução/Reanimação **isentas** e normais; sete poderes gerenciados cobram 5 e falham fechados sem API/commit válido. Na origem, retirar grants próprios e proteger preexistentes; aquisição externa posterior pode ser removida junto, limitação aceita sem nativo.
- Barreira de snapshots completos GameplayCore/Sobrenatural antes de retirar perks; diário com fsync antes da raça, recuperação conservadora de intenção interrompida e vínculo personagem/concessão; reached vanilla novamente ligado à sessão autorizada por seq/UUID/janela. Prova do reset não depende da propriedade dinâmica.
- 144 Node + 36 receptor/Core = **180/180**; serviço/F7/revisão F7, builds e reverse-check passaram. Dois testes PG recusaram execução sem AETHERIUS_TEST_DATABASE_URL; guard preservado. Runtime preparado sem restart; cliente compilado, **não instalado no MO2**. Nenhum addon/OFICIAL/modpack/jogo alterado.
- Relatório detalhado, limites e roteiro: `REVISAO-3_CORRECOES_sol.md`; investigação `investigacoes/INVESTIGACAO_16-11_revisao-3-origem-e-transformacao.md`. Orquestrador recebe commit para confirmação Terra e consolidação no Acervo; depois retomar a Reserva de Sangue.

- Fechamento da prioridade: código no commit raiz `9b4230a0b9e22d7631aae312da9977c53a062f83`; checkpoint cliente `79303d57cc0a75246f45ca2ff2c83ba95dceb824`. Árvore raiz limpa após commit; encaminhar confirmação Terra antes de presumir aprovação.
- 16/11 (noite): estado verificado e registrado no RETOMADA. Sol High ativo nas pendências a/b da confirmação do Terra (alterações ainda sem commit no servidor local); Terra/Gemini/Luna encerrados; servidor local parado; MO2 aberto pelo dev; cliente novo não instalado.


## 2026-11-16 — Sol High: retorno A/B da confirmação Terra

- Terra confirmou parcialmente os cinco itens e abriu duas pendências aceitas; Reserva de Sangue novamente pausada antes de mudar seu núcleo. Autoria de perk deixa de ser descartada quando outro provider só publicou desejo: novo status `CLAIM_RELEASED`, prova durável conservada em ambos os backends até ausência/remoção e retirada ainda pendente no Core.
- Intenção cancelada agora permanece sob fila própria até confirmar raça aplicada ou abortar no-op comprovado; tick recupera sem restart, e `start` recusa antes de turning enquanto há pending. Morte/saída/remoção/F7/troca de concessão/personagem cobertas.
- **158 + 39 = 197/197** testes Node; serviço/F7/revisão F7, builds e patches incrementais 011/005 verificados. Teste novo do ACK Core usa executor SQL simulado com arquivo, não PG real. Runtime preparado e parado; bundle cliente `24284D9C...C3EDCC5`, checkpoint `757c571`, **não instalado no MO2** por instrução do coordenador.
- Documentação: `REVISAO-3AB_CORRECOES_sol.md` e investigação atualizada; nova confirmação Terra e consolidação no Acervo pelo coordenador. Nenhum teste humano/nativo/OFICIAL/modpack alterado.
- 16/11 (noite): dev pediu para parar os agentes. Sol High parado; o trabalho das pendências A/B (158 testes) foi salvo pelo Opus como candidato no commit 88cbbd0, sem confirmação. Pendências detalhadas em docs/PENDENCIAS.md.
- 16/11: criado docs/DECISOES.md com todas as decisões de funcionamento (regras gerais, progressão, interface, vampirismo, Reserva de Sangue, licantropia, transformação forçada, bruxaria, ambiente local).

- 17/11: correção candidata B1 da confirmação Terra: evidência de raça e ambiguidade de readback são conservadas entre retentativas; aborto somente sem evidência anterior e com no-op comprovado. Seis injeções novas, regressão 164/164; relatório REVISAO-3AB_CORRECOES_sol.md. Sem jogo; nova confirmação Terra e publicação do Acervo pelo coordenador.

- **17/11 — Sol High (candidato):** corrigido B1 da confirmação Terra em `e86c0cb` (164/164 na rodada dedicada; nova confirmação pendente). Reserva: sol `4c631e1`; suspensão individual de necessidades/HUD `1f84be4`, cliente `f57ec7d`; Sede Crítica/Feral `6fb08d1`; relógio `f5a4593`; alimentação desativada `e4133ac` + guardas `3246967`. D1–D3 configuráveis, ainda abertas. D7 bloqueia atomicidade da poção e também delimita a alimentação não atômica: Opus autorizou somente candidato default false com journal, quarentena/manual e protótipo PlayIdleWithTarget, sem ativar flag. Validação final 298 testes Node + serviço/F7/revisão-F7, patches e preview de navegador; sem runtime, instalação MO2 ou jogo. Relatório autossuficiente ETAPA-3_vampirismo-mecanicas.md; acervo e revisão independente pelo coordenador.


## 2026-11-17 — Sol High: correções arbitradas da revisão R2

- Corrigida preservação da entrada/cauda até fechamento seguro (`3015b91`), persistência única por tick com rollback e fsync/rename (`34fac30`), projeção de HUD/efeitos nos limiares sem esperar 10 s (`7889be4`) e retry após dispose (`3a4b70e`). Uma injeção revelou iteração indefinida ao reconstruir Map durante dispose; teste local interrompido, iteração por snapshot corrigida e regressão passou com timeout. Sem execução de servidor/jogo.
- 311/311 Node (13 casos R2 adicionais), serviço/F7/revisão-F7 exit 0. Benchmark real de arquivo temporário: N=50.000/V=2.000, uma escrita, tick 22,801 ms e projeção de 2.000 leituras 3,433 ms na rodada com projeção; sem promessa de SLA ou simulação de PG/nativo. Fonte/overlay idênticos; cliente continua f57ec7d.
- Criaturas podem ser MpActor/neighbors no código do host; políticas de materialização/relevância impedem prometer toda criatura visível sem jogo. Matriz obrigatória documentada. D7 esclarecida: poção antiga +50 ativa sem flag; alimentação default false não desliga a poção nem resolve atomicidade. ETAPA-3/DECISOES/PENDENCIAS/RETOMADA e investigação atualizadas; confirmação Terra e publicação no Acervo pelo coordenador. Nenhuma mudança nativa, oficial, runtime, instalação MO2 ou jogo nesta R2.


- **17/11 — Sol High, residual R2 #2:** reprodução com relógio injetado comprovou sangue 79,9944 após callback 1 h atrasado; arbitragem Opus aplicada em `a35b0e8` limita close a min(now, offlineAt), resultando em 99,9889 para apenas 2 s observados. Reaparecimento via tick/get fecha/reabre e limpa o limite antigo sem cobrar gap. Três regressões novas, 24/24 dedicados e **314/314 bateria completa**, serviço/F7/revisão-F7 todos exit 0. Terra já reconfirmou #3/#4/retry; confirmação residual e publicação Acervo pelo coordenador. ETAPA-3, investigação, DECISOES, RETOMADA e PENDENCIAS atualizados; sem jogo/runtime/nativo/MO2/cliente.

## 2026-11-17 — Opus 5.5: consolidação das revisões R1/R2/R3

- **R1 (Terra):** A confirmada; B reprovada (B1) → Sol `e86c0cb` → Terra reconfirmou **aprovado** (164/164 gamemode, 39/39 Core). `docs/REVISAO-3AB_CONFIRMACAO_terra.md`.
- **R2 Reserva de Sangue (Terra):** 4 achados; arbitragem Opus aceitou #2/#3/#4 e tratou #1 (criaturas) como teste obrigatório em jogo. Sol corrigiu (`3015b91`, `34fac30`, `7889be4`, `3a4b70e`, residual `a35b0e8` com close em min(now, offlineAt)); Terra reconfirmou **aprovado no escopo dos testes** (314/314). `docs/REVISAO-R2_reserva-de-sangue_terra.md`.
- **R3 bloqueio de cura (Sol revisando Terra):** reprovado (P1 Fortificar Vida tratado como cura pura; P2 roteamento de Magicka em área). Terra corrigiu (`66d42d5`, `190d4e7`, docs `0e4dd84`); Sol reconfirmou **aprovado** (324 + 14 suplementares). `docs/REVISAO-R3_bloqueio-cura_sol.md`.
- **Decisão Opus:** alimentação implementada só como candidato atrás de flag default false, com journal e quarentena manual, sem declarar atomicidade.
- **Aberto:** D7 (dev): ampliar contrato nativo ou aceitar garantia menor para poção/alimentação; poção antiga ativa sem flag. Rodada de testes em jogo inteira pendente; nada instalado no MO2.

## 2026-11-17 — Comparação com o lobisomem oficial v0.42.0
- Sol High comparou oficial v0.42.0 × base kdu 14/11 × nossa versão: o `werewolf-service.js` oficial é idêntico ao kdu; nossa versão é extensão endurecida. Oficial acrescenta só `actionVoiceFire` no client.js; fonte TS oficial sem `SetRace`. Recomendação: **híbrido** (base v0.42.0, serviço de fera único = nosso, porte seletivo, mescla manual do cliente).
- Terra High revisou e **confirmou** (29 testes oficiais, 130 locais), com ressalvas: rebase revisado, validar entrada Z e testes em jogo; estimativa de horas não é garantia.
- Integração aguarda autorização do dev. Relatório: `docs/COMPARACAO_lobisomem-oficial-v0.42.md`.

## 2026-11-17 — Porte autorizado sem adotar base oficial

- Decisão posterior do dev: manter **nossa solução** e portar apenas correções oficiais confirmadas; substitui a recomendação histórica de base híbrida. Sol High implementou voz Z no comando validado (`cb84cad`), habilidades raciais mescladas com fila/SetRace (`fefdf3b`, cliente `e1fdf9d`) e liberação de headparts antes do load com invalidação de callbacks (`87835ed`, cliente `444bd6a`).
- Mantidos marker UUID/seq, nonce/geração/gates, serviço de 403 linhas, journal/B1, compulsão e F7. Prata/SkySocial/seis chaves comuns já coincidiam com a lógica oficial; nenhuma segunda aplicação nem mudança de `.env`.
- Validação: **252/252 Node**, incluindo 29 kdu adaptados e 18 casos novos; **39/39 Core**; serviço/F7/revisão F7; TypeScript e webpack sem deploy; reverse-check 007/008 e igualdade dos scripts source/overlay. Bundle `faa4d24900f2490ec5d9c979499ddfc2c5b6cfb3480f2e7413e809be5e4307cd`, compilado e **não instalado**; sem servidor/jogo/MO2/runtime.
- Revisão independente do porte e testes humanos permanecem pendentes. Comandos, hashes, caminhos e limites em `C:\Code\Aetherius - New Systems\Sobrenatural\docs\COMPARACAO_lobisomem-oficial-v0.42.md`, §12; etapa/retomada atualizadas. Consolidação no Acervo pelo coordenador.


## 2026-11-17 — Correções do porte, D3 e revisão independente da HUD

- Sol aceitou os dois P1 da Terra. `b682f613d92f48d4d9d5775bbaef0f5574281ca4` remove fallback de eventos sem dispatcher e invalida referências antigas descartadas. `5d3c2164e5b3a8ac8b5f52f74f215d7c49c1627e` / cliente `33953e1355d213ee119f7c27d96eb5a571f944d0` introduz autoria racial conservadora, por arbitragem explícita do Opus: legados/ambiguidades preservados, só adições próprias comprovadas são removíveis; AddSpell pelo caminho já existente do servidor protege origem externa. Sem contrato nativo novo. Investigação em `docs/investigacoes/INVESTIGACAO_17-11_porte-voz-origem-racial.md`; relatório COMPARACAO §13.
- Decisão do dev para D3: Beber Sangue sem consentimento, direto pelo menu X autenticado, ambos sem elmo; capuz permitido. `b4d161c8a63553f0dfeca9a3defd0d1ac7f0aae8` retira reply/UI de consentimento, cancela convites legados no boot e classifica equipamento server-side por records/keywords/EditorID, incluindo capuzes blindados conferidos via houseCARL somente leitura. Preserva flag false, 2 m, 60 min/vítima, PvP/zonas, exclusividade e journal/quarentena. Casos ambíguos e tabela de amostras documentados em ETAPA-3/DECISOES.
- Sol revisou independentemente a HUD vertical Terra (`7c5601a`, raiz `8060a10`) somente em leitura: **aprovada em código/prévias**, sem bloqueante; contrato e pastas MeridianUI preservados, 0/30/100 em 1280×720 e 60 em 1920×1080 inspecionados, CEF/motor/colisões reais pendentes. Parecer próprio em ETAPA-3.
- Bateria final **381/381**, Core **39/39**, serviço/F7/revisão F7, TypeScript e webpack sem deploy passaram. Bundle final `955b534355c38694860a123c45d721c15661987205636aaa0a8aff42309658c4`, compilado e não instalado. Oficial/MO2/runtime/jogo intactos. Restam reconfirmação Terra dos P1, revisão da alimentação, legados dependentes de evidência, Acervo pelo coordenador e testes humanos.

## 2026-11-17 — D7, decisão do dev e arbitragem de saúde

- O dev autorizou perder item/efeito para evitar crédito de sangue em qualquer dúvida. A flag de alimentação poderá ser habilitada só no laboratório após implementar a regra.
- Triagem Sol High em `1088d0b`: hook da poção ocorre antes da baixa nativa e o ledger PG não comprova remoção daquele evento. Beber Sangue pode alterar a vida antes de o journal conseguir registrar o resultado. O legado também pode ter gravado sangue antes de SETTLED.
- Em arbitragem com o coordenador: destino de dano/cura e crédito legado possivelmente aplicados. Ver `ETAPA-3_vampirismo-mecanicas.md`. Apenas documentação nesta triagem.

## 2026-11-17 — Sol High, D7 candidata

- Poção `0ec39d2`: recibo privado de EatItem por unidade, débito idempotente quando rastreada e descarte em falha PG/JSON. Consumo fora do ledger conserva +50 quando a consulta PG confirma a condição. Caiu durante o processamento, o boot não reaplica.
- Alimentação `2088225`: HP e cooldown preservados, observações por ator, encerramento automático e crédito de sangue passando por último. Flag true só em `config/local.env` ignorado, default false. Runtime ainda precisa receber o gamemode candidato. Bateria 410/410 nos dois modos, Core 39/39 e scripts serviço/F7 OK. Revisão externa da D7 e teste em jogo pendentes.

## 2026-11-17/18 — Opus 5.5: fechamento da rodada de código
- Dev: não adotar o lobisomem oficial; portar só correções (feito e aprovado após 2 P1). Beber Sangue sem consentimento, exige ambos sem elmo (capuz ok). D7 decidida: "na dúvida não credita" (item pode sumir sem sangue; nunca sangue sem consumo). Barra de sangue vertical estilizada; vinheta vermelha em Sede Crítica (≤30 até >30); batimento = heartbeat vanilla `UIHealthHeartbeatALP` em loop contínuo.
- Revisões: Terra aprovou D7 com ressalvas (PG/áudio reais em jogo); Sol reprovou o hot-reload `93d991a` (dois sons) → Terra `2fffaf9` → Sol aprovou. 415/415 por modo.
- AGENTS.md: Sol High → `gpt-6.2-sol`.
- Próximo: testes em jogo.

## 2026-11-18 — Opus 5.5: Vínculo do Véu especificado (não iniciado)
- Especificação `referencia/Aetherius_Sistema_Bruxaria_Vinculo_do_Veu.md` lida; entendimento confirmado pelo dev com ajustes (estágio V −100% Magicka máx. + terror a cada 5 min; tela do Ritual nova com entidade; ingredientes; selo centro inferior com degradê; escrita no plugin autorizada; runa do MasterSpellsRunes.esp). Registrado em `docs/ETAPA-4_bruxaria-vinculo-do-veu.md` e DECISOES. Por ordem do dev, nada despachado.


## 18/11 — Terra High: selo da Bruxa aumentado e abaixado

Ajustado apenas o visual conforme pedido do dev: 90 px em 1920×1080, 60 px em 1280×720 e posição 7vh acima da borda inferior. Cliente `e82ac03`, patch e doze capturas `f0bd86e`; teste visual 2/2. Revisão cruzada e verificação de sobreposição no jogo pendentes; não houve instalação no MO2. Ver `ETAPA-4_bruxaria-vinculo-do-veu.md`.

## 2026-11-18 — Opus 5.5: Sobrecarga do Vínculo adicionada ao plano
- Pedido do dev: incluir no plano da Etapa 4 a especificação `Aetherius_Implementacao_Sobrecarga_Bruxas.md`. Registrada em ETAPA-4 §8 com pendências S1–S7; nada despachado.

## 18/11 — Terra: prévia candidata da sobrecarga
Cliente `d6ae059` e laboratório `973ea57`: camadas da arte original, capturas e sequência de quadros; teste visual 3/3. Não está integrado ao contrato v1 nem revisado, CEF/jogo pendentes. Cadência dos sussurros foi decidida por Opus e registrada na ETAPA-4; FFmpeg não tem muxer xWMA e WAV experimental não constitui áudio pronto. Terror, Ritual, sons e records permanecem pendentes.

## 18/11 — apresentação Terra entregue
WAV PCM (sem XWM), tela do Ritual em mock v1, API visual de terror e selo sem anel concluídos no escopo de apresentação; integração com servidor e records transferida a Sol. Cliente `ce281d6`; laboratório `8738b14`, `1561f68`, `0289e58`; teste Node 7/7. Previews em 1280×720, revisão cruzada e CEF/jogo pendentes; detalhes e APIs na ETAPA-4.

## 18/11/2026 — Etapa 4 em execução (Sol High)

Contrato cliente-servidor v1 entregue ao coordenador. Servidor local: relógio e projeção `7dc6c23`, agenda de terror `944b0c3`, Ritual e materiais `4b16eb5`. O modo Sobrecarga pode iniciar acima de 99, com a drenagem preservada. O perfil ativo Qualidade impediu a criação in-place no ESP pelo houseCARL. A área do Ritual permanece bloqueada para início até ter a runa. A integração nativa, a revisão cruzada e os testes em jogo permanecem pendentes.

## 18/11 — Terra: sobrecarga visual intensificada
Selo sem anel ganhou halo, raios longos e abertura mais evidente por pedido do dev; cliente `46d86b3`, laboratório `cfb896b`, 5/5 testes. Cinco capturas reais e cinco versões renderizadas a 4× em 1280×720 e 17 quadros; a tentativa 1920 saiu corrompida. Revisão, CEF e jogo pendentes; detalhes em ETAPA-4.

## 18/11 — transições do selo
Animações com easing e primeira projeção imediata, 15 quadros e slider; cliente `8bcc121` contém autoria mista Terra/Sol, laboratório `94f4313`. Teste 5/5; integração precisa chamar `immediate:true` na restauração de HUD reutilizada, revisão/CEF/jogo pendentes. Ver ETAPA-4.

## 18/11/2026 — Etapa 4 candidata para revisão (Sol High)

O lote concluiu a integração do contrato v1, runa e sons no ESP (`a00899a`), invocações e blocos (`3831f69`), runa/pose replicadas (`1537e23`), aura, olhos e Magicka (`42d5832`) e integração empacotada (`f69c438`). O cliente usa o selo do Terra em `8bcc121`, com correções Sol em `480f19d` e `8d9fc09`. Foram 445/445 testes Node por modo e 39/39 do recorte Core. CMake offline parou por ausência de CMakeRC. Miggyluv está instalado, mas fora da ordem ativa do perfil LOCAL. R-ANIM ficou em despacho próprio. Revisão independente, recompilação do addon e teste humano estão pendentes.

## 2026-11-18 — Decisão do dev: entrega isolada do Aetherius UI
- Registrada em DECISOES: a compilação final não leva nenhuma alteração do Aetherius UI fora do módulo Sobrenatural (HUD geral, menu radial, outros módulos). Auditoria das alterações fora de escopo (incl. 4 exceções de CSS da Etapa 1.4) fica como pré-requisito da compilação final.

## 18/11 — Ritual e ganho gradual (Terra)
Tela segue modelo da Bruxa, fecha após confirmação e permanece aberta em recusa; ganho ≥2 sobe gradualmente, retarget tolera dreno enquanto alvo fica acima do mostrado. Cliente `b007e6e`, `82280b2`, `5589c91`, `c27afd4`; laboratório `ca52c0b`, `e081b52`; testes 11/11 + 4/4 e TypeScript OK. Revisão cruzada, CEF e jogo pendentes; ver ETAPA-4.

## 2026-11-18 — Opus 5.5: Etapa 4 com código revisado e addon compilado
- Revisão Opus Low (REVISAO-ETAPA-4_opus.md) → correções A1/B2/B3 + R1–R6 (Opus Low; cliente 7fb11d8, lab f4b9303, patches 021/022) → Gemini Neon aprovou no escopo de código (459/459 por modo, Core 151/151). Política: Fortificar nunca anulado; multiplicadores sobre o máximo total. Premissa: não existe save game.
- Addon nativo com patch 025 compilado por Opus Low (Build-Native, 0 erros/0 warnings), `scam_native.node` SHA-256 77f28d98….
- Dev: Codex/GPT bloqueado até nova autorização; Sol encerrado. Alterações não commitadas do Sol preservadas em phase0-basic.js e magic.test.cjs (decisão pendente).
- Pendente: teste em jogo/CEF, reativar Miggyluv no perfil LOCAL, auditoria .shell.is-supernatural-workspace, R-ANIM.

## 18/11 — Poderes vampíricos após o teste em jogo (Sol High)

Candidato local `415acde` estende o uso por Z aos seis poderes próprios e replica os cinco sons de entrada que têm marcador SOUN. O Enxame exige leitura válida de HP para creditar cura. Node 39/39 no recorte, revisão cruzada pendente. V2 precisa de reteste do movimento real; V7 de Visão/Lorde requer caminho para os descritores de som. Ver [Etapa 3](ETAPA-3_vampirismo-mecanicas.md) e roteiro V2–V5 com dois jogadores.


## 18/11 — Sincronização de poderes sem teste multiplayer agora (Sol High)

`827afe4` prepara dash autoritativo de 128 unidades, visual público de Morcego/Névoa/Enxame, entrada de áudio apenas para presentes próximos e Visão privada de NPCs e jogadores. Node 46/46 no recorte e 486/486 em cada modo da flag de alimentação. O dash usa teleporte sem consulta de colisão. Os SNDR da Visão e do Lorde ainda precisam de caminho de reprodução. Revisão e roteiro V2–V5/V7 no oficial em [Etapa 3](ETAPA-3_vampirismo-mecanicas.md).

## 18/11/2026 — correções de integração pós-jogo (Sol High)

Laboratório 42315d1 e cliente 61d7547, com patch de cliente 030. HUD Meridian para sangue, selo e timer. Ritual v2 cancela movimento e apresenta motivo autoritativo. Runa e um trecho de som vanilla são replicados aos vizinhos, inclusive quem chega depois. A restauração dos olhos aceita UUID. A serialização protege argsJsonDumps e a barreira de spawn aceitou a aparência mais recente nos testes. HouseCARL confirmou SOUN 000843 e quatro WAVs do mod LOCAL. UI CORE (LOCAL) sincronizada em 107 arquivos verificados. Overlay 335/335 em cada modo, Core 13/13, serviço/F7, TypeScript e webpack passaram. Bundle e Prepare pendentes. Próxima ação é revisão cruzada, reteste local com um jogador e, depois, validação dos observadores no oficial pelo roteiro da ETAPA-4. Ver INVESTIGACAO_18-11_excecoes-hud-ritual-pisos.md para as causas ainda em validação.

## 19/11 — Revisão Neon dos poderes (Sol High)

Candidato `09c7767` + `01056e2` remove o teleporte do Morcego, usa os dois eventos COTV no dono e limita a posição no host antes de replicar. Alvos e agressor protegidos ficam fora do Enxame. A arte do Morcego encerra sem cortar o áudio. Node 490/490 nos dois modos e teste nativo com 11 asserções. Ver [Etapa 3](ETAPA-3_vampirismo-mecanicas.md). Próximo passo é reconfirmação Neon e verificação em jogo após atualizar addon/gamemode.

## 19/11/2026 — R1 e R2 da revisão Neon da Bruxa (Sol High)

Cliente 295f02e, laboratório 1887fd0 e patch incremental 031. Blackout preserva duração e fade com a entrega atrasada. O Ritual descarta movimento capturado com chat ou menu focado. O cooldown de terror reabre o arquivo com UUID ou chave numérica legada. Testes 27/27 direcionados e 343/343 por modo de alimentação. TypeScript e webpack passaram. Reconfirmação Neon e distribuição em par de bundle e frontend da HUD pendentes. B4 aguarda decisão registrada em ETAPA-4.

## 19/11/2026 — Som do Ritual B4 (Sol High)

Decisão do dev aplicada em 65edf8c. O Charge toca no início autorizado e na conclusão com crédito confirmado, com janela para entrada tardia. O cancelamento corta o trecho inicial em curso. A apresentação da runa segue independente do som. Passaram 13 testes direcionados e 345 por modo no overlay. Revisão Neon e validação oficial pendentes. Ver ETAPA-4 e DECISOES.

## 2026-11-19 — Teste solo e encerramento da sessão (Opus)
- Lotes do dia: Opção A (Z sem SpellCast nativo; 7435282/77ea350/850211c), poderes do vampiro (dash por movimento no dono + guarda nativa 026, Enxame com zonas seguras, sons COTV), Bruxa/HUD/ritual (HUDs, timer, cancelamento por movimento, contrato v2, som no início/conclusão, sussurros, pisos). Todos revisados e aprovados pelo Gemini Neon (Opus Medium como 2º revisor só em L1/V6). Decisões: Morcego com recarga de 3 s; som do ritual no início e na conclusão; sem teste com 2 jogadores (validar no oficial).
- Teste solo do dev: HUDs e sussurros OK; 9 defeitos registrados em `docs/TESTE-JOGO_19-11.md` (T1–T9). Dev removeu Precision Creatures, adicionou New Creature Animation - Werewolf (regerar Pandora) e definiu as auras pelo Colored Magic Auras (Red vampiro, Purple Bruxa). Correção adiada para nova sessão.

## 19/11/2026 — Diagnóstico da voz Z e do avanço do Morcego (T1/T2/T10)

O teste solo registrou os efeitos de Fera, Lorde e Morcego, sem resolução de comando para esses três usos. `bcb0c02` acrescenta evidência dos callbacks de ação e do permit, além das recusas da guarda e da política do Morcego. Passaram 52 testes focados, 498 por modo de alimentação na cópia isolada e 503 por modo no laboratório integrado até `3e60b7f`. TypeScript e webpack passaram. Revisão Neon e reteste pendentes. [Investigação e roteiro](investigacoes/INVESTIGACAO_19-11_voz-z-e-dash.md).

## 19/11/2026 — T9, Runa de Conjuração

Laboratório `47835dc` restaura o acionamento da malha de Conjuração e limpa a Ability, o efeito e a pose com referências do frame atual. Baterias integradas 503/503 por modo de alimentação. Revisão Neon e validação de T9/B7 em jogo pendentes. [Investigação T9](investigacoes/INVESTIGACAO_19-11_runa-ritual.md)

## 19/11/2026 (tarde) — Correções do teste solo T1–T10 consolidadas (Opus)
- **T3** `fx.cleanup` em loop: storage versionado + migração (Terra, `632869b`/`e71771d`). Neon: **aprovado sem achados**.
- **T9** runa do ritual: evento da malha restaurado, B5/B7 preservados (Sol, `47835dc`/`7a22c8e`). Neon: **aprovado sem achados**.
- **T7/T8** auras: Colored Magic Auras Red `000D62` (Feral) e Purple `000801` (Sobrecarga), dono + observadores (Sol, `89d4b20`). Neon: **aprovado sem achados**. Luna (houseCARL): ESP light, 88 records novos (tomos/magias/anéis) sem listas/vendedores/overrides; desabilitado no LOCAL e fora do manifesto.
- **T4/T5/T6** fera: +50% velocidade via MGEF `000844` (+60 SpeedMult, GetIsRace fera) nas seis abilities de estágio (`535c284`); limpeza dirigida das mãos + proteção autoritativa do equipamento (`509c0fe`, `340cf16`, `abccba5`). Neon: aprovado com ressalvas (escudo no dono, pergaminhos no host) → corrigidos em `95bec8d` e **reconfirmados**. Falha do teste legado UpdateEquipment é pré-existente na base oficial `310cb59`. Addon novo SHA-256 `23669DE2…`. ESP do overlay `C8B13207…` ≠ cópia ativa MO2/runtime `65BB3AAE…` (sem 000844).
- **Decisões do dev:** sem uivo no Iniciado (Terror a partir do Ômega, Fúria a partir do Alfa; uivo isolado até corrigir o Z); velocidade +50% direto.
- **T1/T2/T10** Z e dash do Morcego: **não corrigidos** — só instrumentação diagnóstica (`bcb0c02`; Neon aprovado). Log mostra efeitos locais sem comando resolvido nem envio COTV; hipótese: captura tardia do ponteiro ActionEvent no Skyrim Platform. Usar MagicEffect como gatilho exige arbitragem da Opção A.
- Laboratório HEAD `876e0a8`.

## 19/11/2026 — Forma da Besta no TAB

Botão no topo da tela do Lobisomem. O servidor reaproveita o gatilho do F7, dá 10 min na raça da fera e persiste 2 h de recarga sem interferir nas 72 h. Candidato `19a6b0f` (laboratório) e `5c1bb42` (cliente). Passaram 513 testes por modo de alimentação, serviço/F7, TypeScript, webpack e prévia. Runtime preparado com 439 plugins e MO2 LOCAL atualizado com backup. Servidor parado. Revisão Neon e teste em jogo pendentes. [Etapa 2, hashes e roteiro](ETAPA-2_transformacao-forcada.md#1911--forma-da-besta-pelo-tab)

## 19/11/2026 — Resposta ao Neon sobre a Forma da Besta

Neon aprovou o candidato original com duas ressalvas. Sol confirmou as duas e corrigiu a restauração da fera na desconexão, a sincronização da raça no relog e a limpeza da recusa antiga na UI. Laboratório `036678d`, cliente `5e8b491`. Passaram 517 testes por modo de alimentação e o LOCAL foi preparado e instalado com o servidor parado. Reconfirmação e jogo pendentes. [Relatório e hashes deste deploy](REVISAO-BOTAO-FERA_neon.md#5-resposta-do-autor--19112026)

## 19/11/2026 (noite) — Botão Forma da Besta + deploy LOCAL (Opus)
- Pedido do dev: botão **Forma da Besta** na 1ª tela do Lobisomem (TAB), mesmo gatilho da forçada 72 h (F7) sem mexer no contador; forma de 10 min; recarga 2 h persistente no servidor. Sol: `19a6b0f`, `5c1bb42`, `a7abb03`. Neon: aprovado com ressalvas (desconexão gravando raça de fera — pré-existente; mensagem antiga na UI) → corrigidas em `036678d`/`5e8b491` e **reconfirmadas sem achados**.
- Dev confirmou: botão a qualquer hora; forçada pode ocorrer durante a recarga do botão; Z mantido (recargas independentes, sessão de fera única).
- ESP novo `C8B13207…` instalado no MO2 pelo Opus (backup `backups\esp-mo2-20261119`). Dev ativou Colored Magic Auras e regerou o Pandora. Sol fez Build/Prepare (439 plugins) e instalou cliente + UI CORE no LOCAL; **servidor NÃO iniciado** (pedido do dev). Laboratório `147e84f docs(sobrenatural): registra resposta ao Neon e hashes do deploy`.

## 19/11/2026 (noite) — Pacote de entrega para o servidor oficial (Opus)
Compilado em `C:\Code\Aetherius - New Systems\Entrega_Sobrenatural_19_11` (882 arquivos, 460 MB): patch cumulativo do servidor (81 arquivos sobre `310cb59`, sem `settings.ts`/`yarn.lock`) e do cliente (31 arquivos sobre `bc305e5`, sem perfis LOCAL), ambos verificados com `git apply --check` nas bases; mesclagem em 3 vias com o oficial atual deixa 4 conflitos no servidor (werewolf-service, werewolf/client, werewolf test, skysocial identity) e 2 no cliente (loadGameService, appearance), entregues com marcadores. Mods: SOBRENATURAL (+VISOES, sons da Bruxa), LOBISOMEM TRANSFORMACAO, Colored Magic Auras, New Creature Animation - Werewolf, Miggyluv's Eyes, Pandora regerado (perfil LOCAL). Precision Creatures removido. Guia: `LEIA-ME_INTEGRACAO.md`.

## 19/11/2026 (noite) — Pacote rebaseado para o oficial novo de 19/11 (Opus)
Dev atualizou `C:\Code\Aetherius - OFICIAL`. Mesclagem 3 vias (base 310cb59/bc305e5 + pacote kdu; oficial novo × Sobrenatural): 7 conflitos resolvidos à mão (magic-service + teste, werewolf/client, loadGameService, remoteServer, appearance com import Keyword restaurado, app.js do F7 com aba Compras + Sobrenatural). Patches regerados sobre o oficial e validados com `git apply --check`. Testes na árvore mesclada: gamemode 142/142; Sobrenatural 319/319 por modo + 44 de cliente; tsc/webpack do cliente e tsc do backend OK; falhas de Meridian (7) e GameplayCore idênticas/ambientais ao oficial puro. Addon C++ não recompilado. Pontos para o dev: raciais conservadoras vs. remoção ampla do oficial; restore-over-time do MpActor × bloqueio de cura do vampiro. Ver `Entrega_Sobrenatural_19_11\REBASE_OFICIAL_19-11.md`.

## 19/11/2026 (noite) — Fangs and Eyes (olhos vermelhos) no pacote (Opus)
Nova versão do Fangs and Eyes (Nexus 6842, `Fangs and Eyes Red-6842-1-0.7z`) incluída em `03-mods-jogo` como substituição direta. `BVFE.esp` idêntico ao do runtime do servidor (`43205137…`); só os BSA mudaram, então o manifesto de plugins não muda.


## 19/11/2026 (noite) — Repositório privado do sistema (Opus)
Criado https://github.com/pablo-rodrigues1/Aetherius_Sobrenatural (privado) com README, `docs/FUNCIONAMENTO.md`, pacote de integração rebaseado, mods próprios, módulo UI, testes e todo o histórico de desenvolvimento. Mods de terceiros não incluídos (licença/tamanho), listados em `mods/TERCEIROS.md`. Acervo aponta `repositorio:` para ele.


## 20/11/2026 — Lorde e poderes vampíricos

Candidato no laboratório desde `58c9db0`, cliente `307d39c`. Duas formas de Morcego com autorização e guarda nativa, novo áudio, Enxame 20 HP/s por 15 s, Força aplicada no host e concessão P10. Ampliação inclui as 15 perks, Q automático temporário, míssil COTV na mão direita e habilidades de estágio. Passaram 523 por modo e 38 asserções nativas. A evidência das lacunas de Grip, Blood Ward e levitação e o roteiro em jogo estão na [investigação](investigacoes/INVESTIGACAO_20-11_lorde-poderes.md). Revisão independente e teste em sessão pendentes.


## 20/11/2026 — Resposta aos seis achados Neon no lote do Lorde

Corrigidos entrada pelo controle Shout, evidência de bash, equipar da mão esquerda pelo Q, projeção dos atributos base, guarda de aparência no relog/PD e atualização do botão do TAB. O erro no campo do botão pertence ao candidato de 20/11. A causa exata de ActionEvent não chegar ao dispatcher na versão de 19/11 precisa do reteste. [Evidência e arbitragens](REVISAO-LORDE-20-11_neon.md#resposta-do-autor-à-revisão-de-2011).


## 20/11/2026 — Candidato de mecânicas e áudio do Lorde

Garra de NPC, Ala de Sangue autoritativa, Asas e áudio dos três WAVs na sequência comum. Áudio e estado da forma usam propriedades separadas. ESP candidato só no overlay. [Investigação e roteiro](investigacoes/INVESTIGACAO_20-11_lorde-poderes.md).


## 20/11/2026 — Correções da revisão do complemento do Lorde

O teleporte indexado acompanha o movimento do modelo dos observadores. A concentração usa renovação ordenada com margem e sinal de fim para o dono. Cliente `ae7c172`. Passaram 530 testes por modo, 81 asserções nativas, TypeScript, webpack e Build-Native. ESP conserva o hash do candidato. [Resposta ao Neon](REVISAO-LORDE-COMPLEMENTO_neon.md#resposta-do-autor--2011).

## 20/11/2026 — Lote do Lorde Vampiro, HUD Cálice e pacote 20_11 (Opus)
- **HUD Cálice da Noite** (Terra; aprovada pelo dev com brilho crescente ao esvaziar): cliente `afd96a0`/`9a96871`, laboratório `4c69023`/`e854259`/`8e87732` (patches 034/035). Neon: aprovada após 2 ajustes (movimento reduzido; quantização de 0,5 ponto).
- **Lorde Vampiro** (Sol): P10 (`lordGrant`/`lordRevoke`, keyword `JogadorLordeVampiro` só no servidor, botão no TAB, 10 min/2 h); escala 1,25×; menu Q automático temporário; todas as 15 perks; Vampiric Drain do Lorde; bônus por estágio só na forma (200–450 / 100–350 / 20–45%); sons do mod AETHERIUS - Vampire Lord and Werewolf SFX (poderes + transformação para todos); Enxame 20/s por 15 s; Força no dano autoritativo; Grip só em NPCs; Blood Ward no host; Wings of the Night. Commits `58c9db0`, `d757a8b`, `7658200`, `bb2510a`, `d830ca7`; cliente `307d39c`, `03a142e`, `8e4b758`, `ae7c172`. Neon reprovou o 1º candidato (6 achados, incl. `userEvent`→`userEventName` no Z, introduzido em 20/11) e o complemento (2 achados); todos corrigidos e reconfirmados sem achados. Luna (houseCARL) conferiu o ESP `7A1397FE…`: sem erros.
- ESP final instalado no MO2 LOCAL (backup `backups\esp-mo2-20261120`).
- Pacote renomeado para `Entrega_Sobrenatural_20_11` e remesclado com o oficial de 19/11 (novo conflito: `magicSyncService.ts`, resolvido com os dois lados). Testes na árvore mesclada: 376/376 por modo, gamemode 142/142, tsc/webpack OK. Mod de SFX incluído e na §4.4 (releases).
- Pendente: teste em jogo de todo o lote (Z/dash, Grip, levitação, áudio para observadores, perks em forma mortal via quest `306FBE`).

## 21/11/2026 — Fases da lua e lua cheia como gatilho da compulsão (análise)

O servidor oficial não tem fases da lua. O Moons and Stars (powerof3) calcula fase e posição só a partir do GameDaysPassed; o INI que vale é o do Rudy ENB, cuja fase é igual à original (Masser 24 dias, Secunda 20). A lua cheia pode entrar como segundo critério no mesmo `tick()` das 72 h. Para o céu bater com a mecânica, o GameDaysPassed teria que ser enviado sempre (hoje só no laboratório), o que pede ADR. Nada implementado; decisões pendentes com o dev. [Investigação](investigacoes/INVESTIGACAO_21-11_fases-da-lua.md).

## 21/11/2026 — Lua de Masser: compulsão na Lua Cheia, olhos Bronze e HUD da Lua

- **Dev:** Masser governa; transformação obrigatória nas 3 noites de Lua Cheia (`floor(dias) % 24` ∈ {0,1,2}, noite 20h–5h, uma vez por noite, qualquer via cumpre). Olhos Bronze Light para todo lobisomem; Bronze Glow após 24 h online sem transformar ou nos 3 dias de cheia. HUD proposta A, acima da sobrevivência, ~56 px.
- **Sol** (servidor): `48fb5fe`, `7dab4e2`, `a3ef672` sobre `9e313e4`, espelho `8b233e3`. Botão Forma da Besta continua sem zerar as 72 h (decisão de 19/11), mas cumpre a noite cheia por recibo separado. Argoniana feminina mantém olhos originais (sem HDPT Bronze feminino no mod; pendência do dev). [Detalhes](etapa-2-lua-de-masser.md).
- **Terra** (cliente): `7e175bb` + correção `6c78e250`, laboratório `3f3b9d8`, patches 039/040. [Detalhes](etapa-2-hud-lua.md).
- **Neon:** aprovado com 1 P3 (fixtures sem mock + chamada sem guarda), corrigido e reconfirmado: 453/453 por modo. [Revisão](revisao-lua-masser-neon.md). 33 HDPT Bronze conferidos por coleta houseCARL (Luna).
- Pendentes: teste em jogo/CEF; ADR do envio permanente do GameDaysPassed (lua visível sincronizada); Miggyluv's Eyes ativo só no perfil LOCAL (desativado em GRAFICO e PERFORMANCE).

## 21/11/2026 — Decisões do dev: céu sincronizado e perfis

- ADR-0027 aceito: o servidor enviará o GameDaysPassed sempre, para a lua ser a mesma para todos. Implementação condicionada ao levantamento de quem lê esse global.
- Miggyluv's Eyes ativado também nos perfis GRAFICO - QUALIDADE e PERFORMANCE (backups `*.bak-miggy-2111`).
- Argoniana feminina fica sem olho Bronze (dev: não precisa fornecer).

## 21/11/2026 — Céu sincronizado (ADR-0027, relógio visual isolado das luas)

Fork mínimo do MoonMod v2.0.2 (powerof3, MIT, `4732799`) para AE 1.6.1170: fase e ângulo de Masser e Secunda leem `skyDaysPassed` do servidor; o GameDaysPassed (`0x39`) do jogo nunca é escrito. Servidor `79ce921`, cliente `8cfa553`, fork `18ef3eb`, overlay `81966c3`. Neon: viabilidade aprovada com 3 P2/2 P3, implementação aprovada sem achados (557/557 por modo, 112 direcionados, CTest; 19 falhas do Meridian confirmadas preexistentes na base). Pendente: autorização do dev para instalar a DLL no perfil LOCAL e teste com dois jogadores. [Etapa](etapa-2-ceu-sincronizado.md) · [Revisão](revisao-ceu-sincronizado-neon.md).

## 21/11/2026 — Céu sincronizado instalado no LOCAL e auditoria de clima/horário

- Dev autorizou a instalação. Sol instalou no perfil AETHERIUS - LOCAL: bundle do cliente e mod `AETHERIUS - Ceu Sincronizado (LOCAL)` acima do Moons and Stars (backups `*.bak-ceu-2111`). houseCARL: um único MoonMod efetivo, INI do Rudy com SHA correto, Address Library 1.6.1170. Passos da 2ª máquina na [etapa](etapa-2-ceu-sincronizado.md).
- Terra auditou clima, horário e ações de ambiente do painel (antes × depois): nenhuma regressão comprovada; limite de evidência em F7/banco/jogo. [Auditoria](auditoria-clima-horario-21-11-terra.md).
- Pendente: ensaio com dois jogadores, incluindo F7 de hora, pausa/retomada e clima regional.

## 21/11/2026 — HUD da Lua e Cálice implantados no UI Core LOCAL

Faltavam os ativos do UI Core no mod `AETHERIUS - UI CORE (LOCAL)`. Sol implantou os cinco arquivos de `8cfa553` (HUD da Lua e, por dependência do CSS, o Cálice da Noite aprovado em 20/11), com backups `*.bak-lua-2111`; diff/hashes/VFS conferidos. Servidor preparado confere byte a byte com o HEAD (Masser, olhos Bronze, compulsão). Lua de Masser completa no LOCAL; falta ensaio em jogo.

## 21/11/2026 — Aparência do Vampiro por estágio da Reserva (Dynamic Vampire Appearance)

- **Dev:** Stage 1 (100–70), 2 (69–50), 3 (49–31), 4 (30–0) mudam rosto (TXST) e olhos; sangue na boca 3 min reais após alimentação validada; olhos de combate (Feral, Immensurable Strength, Unnatural Celerity) > visão noturna (Vampiric Vision) > estágio; Argoniano/Khajiit com aparência racial normal (DVA não cobre). Pacote só visual com nome próprio (alternativa A): o DVA original inicia 2 quests e sobrescreve `DLC1VampireChangeEffect`/`DLC1RevertEffect` usados pelo Lorde.
- **Sol:** servidor `9f01499`, cliente `3c80c2c` (troca de olho/rosto in-place, sem recriar o ator remoto), overlay `f6e0092` (patches 037/042); ESPFE+BSA `AETHERIUS - DVA Visuals` (50 records, 46 assets idênticos ao original). 568/568 por modo.
- **Neon:** aprovado sem achados. [Etapa](etapa-3-aparencia-vampiro.md) · [Revisão](revisao-aparencia-vampiro-neon.md).
- Pendente: autorização para alterar perfis do MO2 e ensaio com dois jogadores.

## 21/11/2026 — DVA Visuals instalado (LOCAL e GRAFICO)

Pacote `AETHERIUS - DVA Visuals` ativo nos perfis LOCAL e GRAFICO - QUALIDADE; DVA original inativo nos dois (quests fora da ordem; Dawnguard volta a ser o único provedor de `DLC1VampireChangeEffect`/`DLC1RevertEffect`). Bundle do LOCAL trocado, Build/Prepare com 440 plugins, backups `*.bak-dva-2111`. Falta ensaio em jogo. Dev pediu unificar os plugins e pastas nossos do Sobrenatural (incl. Entrega_Sobrenatural_20_11) em um ESP e uma pasta, sem o Lobisomem do kdu e sem terceiros: análise em andamento.

## 21/11/2026 — Unificação do ESP e da pasta, Fase 1 (Sol High)

- Escopo confirmado pelo dev: uma pasta e um ESP próprios para o Sobrenatural, incluindo o derivado DVA Visuals, o fork do céu, o áudio e o módulo de UI. Lobisomem do kdu e mods de terceiros seguem separados.
- Inventário do LOCAL, GRÁFICO e PERFORMANCE e comparação dos 18 overrides concluídos. Visões vem do mod SERVIDOR LOCAL. O ESPFE candidato comporta 149 records próprios. A proposta preserva os IDs locais do principal, move os 50 do DVA Visuals e desloca 34 outros slots regulares no LOCAL. A aprovação do corte depende de ensaiar o remapeamento dos dados e o rollback.
- Relatório em `Sobrenatural/docs/investigacoes/INVESTIGACAO_21-11_unificar-esp.md` na pasta de desenvolvimento. Pendente revisão Neon e decisão do dev sobre o corte. Análise em leitura, sem alterações de MO2, código ou saves.

## 21/11/2026 — Unificação do ESP e da pasta, Fase 2 (Sol High)

- Neon aprovou a análise com três ressalvas, incorporadas pelo autor. O UI Core mantém MeridianUI completo e os 16 assets de runtime. O LOCAL tem 34 deslocamentos regulares e a primeira publicação oficial preserva os 437 slots travados.
- Candidato fora do MO2 com 167 registros, BSA SSE, 65 WAV soltos, DLL do céu e UI Core de teste completo. Passaram 569/569 Node por modo, 81 asserções nativas e CTest Lua 1/1. Decisões do dev em [ADR-0028](../../03-decisoes/ADR-0028-sobrenatural-esp-unificado.md).
- Relatório e roteiro em Sobrenatural/docs/ETAPA-4_unificacao-esp.md na pasta de desenvolvimento. Pendente revisão Neon do candidato, autorização para instalar e backup/início limpo no LOCAL. Sem instalação ou reset nesta etapa.

## 21/11/2026 — Unificação instalada (Opus, contingência do Sol)

Neon aprovou o candidato sem achados. Instalado nos perfis LOCAL, GRAFICO e PERFORMANCE: pasta única `AETHERIUS - SOBRENATURAL` (ESPFE com 167 records, BSA, 65 WAV, DLL do céu). LOCAL com load order idêntico à proposta (438) e dados de teste arquivados (início limpo). Backup integral verificado. [Etapa 4](etapa-4-unificacao-esp.md).

## 21/11/2026 — Entrega_Sobrenatural_21_11 (versão definitiva para integração oficial)

Montada pelo Opus sobre a 20_11 com merge de 3 vias do delta de 21/11 (sem conflitos; oficial de 19/11 inalterado). Patches: servidor 92 arquivos, cliente 39. Na árvore oficial mesclada: Sobrenatural 391/391 por modo, gamemode 164/164, cliente 8/8, tsc e webpack OK. Pasta única `AETHERIUS - SOBRENATURAL`, perfis e manifestos oficiais propostos (437 slots preservados). Neon: 1 P1 (PNG corrompido no patch), 1 P2 (perfis) e 1 P3 corrigidos e reconfirmados; aprovado sem achados. [Guia](entrega-21-11-integracao-oficial.md) · [Revisão](revisao-entrega-21-11-neon.md).

## 21/11/2026 — Repositório e documentação finais

- Repositório `Aetherius_Sobrenatural` atualizado a partir da `Entrega_Sobrenatural_21_11` (commit `2c829d4`): patches, painel, load order e manifestos, mod próprio (ESP leve, DLL do céu, sons da Bruxa), `FUNCIONAMENTO.md` (só o validado em jogo), Códex das Pragas em PDF e histórico de desenvolvimento. O BSA com assets do DVA e os 61 WAV de SFX ficam fora do Git (estão na entrega e no `aetherius-mods`).
- Documentação da entrega simplificada a pedido do dev: um documento técnico e conceitual ([funcionamento.md](funcionamento.md)) cobrindo a base e o que foi validado em jogo; o restante do histórico continua neste Acervo e em `docs/desenvolvimento/` do repositório.
- Códex das Pragas refeito para jogadores (sem dados de administração nem números de bônus), com capa a partir das referências de arte, em PDF.
- Status: pronto para integração pelo dev oficial; testes em jogo das funcionalidades de 20 e 21/11 pendentes.

## 25/11/2026 — Revisão 2 das correções de 24/11 (Opus, madrugada)

Revisão das correções da 1ª rodada (pacote `INVESTIGACAO_E_SOLUCOES_24_11`). Bug sério no M4: o plano racial tirava o monitor racial de todo vampiro e todas as raciais durante a forma de fera. Provado com os records reais e corrigido. Também foram reforçados o N2 (garantia do Papyrus de volta), o M1 (desvio só no caso do bug, compilado) e o V3 (tentativas limitadas + ESP proposto, não instalado). Nada visto em jogo. [Revisão 2](investigacoes/revisao-2-correcoes-24-11-opus.md).
