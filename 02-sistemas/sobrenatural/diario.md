---
titulo: Diário de Sobrenatural
tipo: historico
status: ativo
responsaveis: [pablo-rodrigues1, kdu]
atualizado: 2026-11-15
tags: [diario]
relacionados: [README.md]
---

# Diário de Sobrenatural

## Resumo
Registro cronológico do desenvolvimento, do mais recente para o mais antigo. Entradas curtas.

## 2026-11-16 (pablo-rodrigues1 / Claude Opus 5.5) — revisão 3 e decisões
- **Feito:** revisão do Terra High rejeitou origem/Bruxa/forçada com 5 achados (revisao-3-origem-bruxa-forcada-terra.md); Sol High corrigiu (revisao-3-correcoes-sol.md; 180 testes). Decisões do dev: Sedução e Reanimação sem custo de sangue; na remoção da maldição, remover tudo o que o Sobrenatural concedeu (limitação: magia ganha por outra fonte depois da concessão pode sair junto).
- **Próximo:** confirmação do Terra High; Sol High segue a Reserva de Sangue.
- **Bloqueios:** nenhum; sem testes em jogo nesta sessão.

## 2026-11-16 (pablo-rodrigues1 / Claude Opus 5.5) — encerramento da sessão
- **Feito:** correção por origem de magias/perks, Bruxa ligada ao serviço e poderes do vampiro (Sol High, commit local 79098ad); Reserva de Sangue parcial (Terra High); decisões do dev sobre a Reserva de Sangue e a transformação forçada do lobisomem. Estado completo e próximos passos em retomada.md.
- **Próximo:** transformação forçada (Sol High), restante da Reserva de Sangue (Terra High), revisões cruzadas e rodada de testes em jogo.
- **Bloqueios:** nenhum; nada testado em jogo nesta etapa.

## 2026-11-15 (pablo-rodrigues1 / Claude Opus 5.5) — início das mecânicas de vampirismo
- **Feito:** levantamento (Terra High) do que é viável para cura/comida, alimentação, sol e passivas (etapa-3-vampirismo-mecanicas.md). Valores provisórios de teste aprovados pelo dev: sol 5/s das 7h às 19h; mordida em NPC 25/25/60 s/+25 sangue; Poção de Sangue cura 100 e +50 sangue; mordida em jogador cura 200 e +100 sangue; só itens de cura pura são recusados ao vampiro.
- **Próximo:** implementação (Terra High) e teste em jogo.
- **Bloqueios:** recusar só o efeito de cura de itens mistos exigiria código nativo (fora do escopo).

## 2026-11-15 (pablo-rodrigues1 / Claude Opus 5.5) — Bruxaria preparada; scripts não assinados
- **Feito:** concessões da Bruxa por estágio e vertente, upgrades da TENEBRAE pelo servidor, parada do upgrade automático do Shadow Spell Package, pisos de habilidade (maior valor entre classe e maldição) no GameplayCore local, tela da Bruxa atualizada (etapa-2-bruxaria.md). Achada a causa de nenhum poder funcionar no servidor local: o cliente recusava os scripts do servidor por falta de assinatura (investigacoes/investigacao-15-11-scripts-nao-assinados.md); corrigido com chave de teste local.
- **Próximo:** correção por origem (magias/perks) e ligar a Bruxa ao serviço; teste em jogo de Fera, Lorde e Bruxa.
- **Bloqueios:** limite do Codex; correção por origem sem responsável definido.

## 2026-11-15 (pablo-rodrigues1 / Claude Opus 5.5) — painel F7
- **Feito:** aba Sobrenatural no painel da staff (F7) com 7 ações (consultar, setar, remover, XP, Disciplina, Vertente, encerrar forma), com auditoria e revalidação de sessão. Revisado por Terra High e aprovado em jogo. Detalhes: etapa-2-painel-f7.md, revisao-etapa-2-painel-f7.md; investigação do F8 abandonada em investigacoes/.
- **Próximo:** corrigir formas e poderes que falharam no teste em jogo (fera, Lorde, raça vampírica, morcego, névoa, enxame, visão; recarga de 30 min).
- **Bloqueios:** nenhum.

## 2026-11-15 (pablo-rodrigues1 / Claude Opus 5.5) — plugin e animação
- **Feito:** plugin AETHERIUS - SOBRENATURAL.esp com os bônus numéricos (vampiro, disciplinas, lobisomem); animação do lobisomem do kdu ativada via Pandora no perfil LOCAL (saída isolada, mods compartilhados restaurados). Detalhes: etapa-2-plugin-e-animacao.md.
- **Próximo:** teste em jogo; revisão cruzada.
- **Bloqueios:** nenhum.

## 2026-11-15 (pablo-rodrigues1 / Claude Opus 5.5) — Etapa 2: vampirismo e Lorde Vampiro
- **Feito:** poderes e inatos do COTV por estágio; raça vampírica projetada; Lorde Vampiro sincronizado para todos (mesmo modelo da fera do kdu); cliente local com o patch de cliente do kdu; painel de teste F8. Detalhes: etapa-2-vampirismo.md.
- **Próximo:** teste em jogo com 2 jogadores; estágio mínimo do Lorde; bônus numéricos via GameplayCore.
- **Bloqueios:** bônus numéricos dependem do sistema de atributos do GameplayCore.

## 2026-11-15 (pablo-rodrigues1 / Claude Opus 5.5) — Etapa 2: lobisomem no servidor
- **Feito:** base do kdu aplicada no servidor local; serviço Sobrenatural (estado autoritativo, keyword só no servidor, revision/grantId, UI snapshot/choose + push, comando /maldicao); perks e magias do Manbeast por estágio. Detalhes: etapa-2-lobisomem.md.
- **Próximo:** teste em jogo; lado cliente do kdu; bônus que exigem plugin; revisão cruzada.
- **Bloqueios:** bônus sem record (exigem plugin); rebuild do cliente para o kdu.

## 2026-11-15 (pablo-rodrigues1 / Claude Opus 5.5) — servidor local de testes
- **Feito:** ambiente local isolado (só loopback, PostgreSQL próprio, 16 módulos, bots autenticados, scripts start/stop/reset, overlays para kdu e Sobrenatural). Revisado e corrigido. Detalhes: ambiente-servidor-local.md e revisao-ambiente-servidor-local.md.
- **Próximo:** Etapa 2 (servidor do Sobrenatural) sobre este ambiente.
- **Bloqueios:** Alma/market_stalls sem migrations; capacidades UNSUPPORTED; addon v19 sem commit verificável (homologação parcial aceita).

## 2026-11-15 (pablo-rodrigues1 / Claude Opus 5.5) — Etapa 1 encerrada
- **Feito:** correções da revisão do redesenho (escopo de CSS, polling sem redesenho, foco e rolagem preservados); valores do lobisomem na tela (armadura 150–400, RM 10–35%, vida e vigor somados na fera). Revisão de Terra High: aprovado com ressalvas de integração (revisao-etapa-1-redesenho.md).
- **Próximo:** Etapa 2 (servidor).
- **Bloqueios:** nenhum. Homologação em Meridian/CEF pendente.

## 2026-11-15 (pablo-rodrigues1 / Claude Opus 5.5) — redesenho aprovado
- **Feito:** ícone do lobisomem refeito fiel à referência, Apoteótico como eclipse de sangue, layout em duas colunas com trilha de 6 marcos. Aprovado pelo dev. Relatório: etapa-1-redesenho.md.
- **Próximo:** revisão técnica da 1.4; Etapa 2 (servidor).
- **Bloqueios:** nenhum

## 2026-11-15 (pablo-rodrigues1 / Claude Opus 5.5) — Etapa 1 concluída
- **Feito:** módulo `supernatural` do UI Core com 4 telas, 38 ícones estilizados, fonte Sovngarde Bold, versão de estado (`revision`), identificador de setagem (`grantId`), atualização a cada 10 s + botão Atualizar e validação dos dados. Revisão cruzada de Sol High: reprovado → corrigido → aprovado com ressalvas. Documentos: etapa-1-telas.md, etapa-1-correcoes.md, etapa-1-icones-fonte.md, revisao-etapa-1.md, integracao-ui-core.md.
- **Próximo:** Etapa 2, servidor (handlers snapshot/choose, persistência, setagem pelo painel, push).
- **Bloqueios:** nenhum. Ainda não testado dentro do jogo.

## 2026-11-15 (pablo-rodrigues1 / Claude Opus 5.5) — decisões de texto e escolhas
- **Feito:** sobretítulos Corrupção do Sangue/da Lua/do Véu e lemas refeitos; nomes em português; Vertente da Bruxa no estágio 1; Disciplina e Vertente não podem ser trocadas (2ª Disciplina no Apoteótico).
- **Próximo:** Etapa 1, as telas do UI Core.
- **Bloqueios:** nenhum

## 2026-11-15 (pablo-rodrigues1 / Claude Opus 5.5)
- **Feito:** início do sistema. Abas de maldições da planilha transcritas. Definidos: curva de XP (20k/40k fixos pelo dev; restante calculado até 2M), direção de arte (padrão do Class System; detalhes em Carmesim/Âmbar/Violeta), regras de setagem/remoção (o servidor reseta a classe; a remoção apaga o progresso), keyword só no servidor. Referências de UI do vampirismo analisadas (estágios e Disciplinas). Proposta de textos para Lobisomem e Bruxa. kdu entra como corresponsável: o lobisomem parte do pacote dele.
- **Próximo:** Etapa 1, as 4 telas do módulo Sobrenatural no UI Core, com dados mock.
- **Bloqueios:** nenhum

## 2026-11-15 (pablo-rodrigues1 / Sol High) — formas e poderes, lote parcial
- **Feito:** investigação dos dez itens relatados em jogo; lote local 3299cf1 prepara entrada das formas, recarga real, raça/olhos/login e candidato Aura Whisper. 29 testes kdu + 5 novos, serviço/F7, builds, boot e check estrutural passaram. Ver formas-e-poderes.md e ADR-0014.
- **Próximo:** revisão Terra e reteste humano, depois observador; Etapa 2 aberta.
- **Bloqueios:** regen magicka/vigor da Névoa e perk temporária da Força exigem decisão sobre integração ao host; nada nativo alterado. Papyrus global segue bloqueado.


## 2026-11-15 (pablo-rodrigues1 / Sol High) — lote menor pronto para reteste
- **Feito:** commit local 4fc0034 fecha somente Fera, Lorde, raça/olhos/login/reversão e recarga; resolve achados Terra 1/2/3/5 com comando validado, preservação durável da original, callbacks canceláveis e fase após aparência confirmada. 50/50 Node e scripts de serviço/F7 passaram, builds sem addon e boot com 435 plugins/16 ativos/0 falhas; jogo fechado durante reinício.
- **Decisão:** poderes restantes adiados, patch Visão desativado, Predator Vision continua vencedor; Névoa/regen e Força/dano parciais sem nativo, baseline COTV só provisório local (ADR-0015). Bruxaria é de outro worker; Sol fará depois a correção transversal de origem para lobisomem/vampiro e contrato de extensão.
- **Próximo:** coordenador encaminha roteiro de jogo; confirmação Terra e reteste humano ainda não ocorreram. Diário local do orquestrador registra exceção do dev para testar o lote menor antes da confirmação Terra. Ver [relatório](formas-e-poderes.md).
- **Bloqueios:** nenhum para iniciar o teste menor; resultados do motor, observador e origem dos grants pendentes, sem homologação oficial.


## 2026-11-15 (Sol High, despacho Opus) — continuação por origem
- **Iniciado:** registro estável por personagem das magias efetivamente concedidas, arbitragem das perks com GameplayCore e ligação do catálogo Bruxaria; revisão crítica do lote Argon e poderes restantes do vampiro.
- **Contexto:** scripts de servidor sem assinatura foram recusados no cliente local; correção Ed25519 já preparada no laboratório pelo orquestrador, ainda sem teste em jogo. Testes anteriores não provam falha do código das formas.
- **Próximo:** testes Node, builds e roteiro humano; revisão cruzada Terra após entrega. Oficial e mods compartilhados preservados.


## 2026-11-16 (Sol High, despacho Opus) — origem, Bruxaria e poderes
- **Feito:** registro estável de spells por personagem, claims/ACK de perks com Core, catálogo Argon ligado e upgrades TENEBRAE preservando magias próprias. Revisão crítica documentada. Poderes JS e Visão própria; overrides 40/60 nos quatro temporários, Morcego 1/0, custo pelo contrato de sangue Terra e flag solar preparada/desligada.
- **Validação:** 122 testes, scripts serviço/F7, builds sem addon e estrutura houseCARL passaram; boot 435 plugins/162 forms/16 ativos/0 falhas. Ver formas-e-poderes.md, etapa-2-bruxaria.md e ADR-0019.
- **Próximo:** revisão independente Terra e teste humano posterior (dev decidiu não testar hoje); nova transformação forçada 72 h online/noite em lote separado. Sem homologação oficial.


## 2026-11-16 (Sol High) — início da transformação forçada
- **Decisão do dev:** substituir os antigos 7 dias por 72 h reais online por personagem, zeradas só na transformação confirmada. Ao vencer, aguardar noite do relógio autoritativo (20h–5h) e executar avisos/efeitos privados de 20 s antes da animação normal do kdu.
- **Em implementação:** contador monotônico persistido, cancelamento/limpeza, comprovação de raça e reaproveitamento dos assets kdu sem editar modpack. Testes Node e roteiro posterior; sem teste em jogo hoje.


## 2026-11-16 — retomada da transformação forçada (Sol High)
Continuação autorizada pelo dev/orquestrador: 72 h reais online por personagem, janela noturna 20h–5h e aviso de 20 s antes do fluxo normal do kdu. Teste inicial e fontes ignoradas recuperados; integração ao boot/F7, persistência e limpeza em andamento, sem teste em jogo. Revisão independente será feita pelo coordenador com outro modelo após a entrega.


## 2026-11-16 — entrega da transformação forçada (Sol High)
Candidato no laboratório `856232aa0cc7474a50017e3382a86fe2d5c7f6aa`: 72 h reais online por personagem/concessão, noite 20h–5h, pré-aviso privado de 20 s e fluxo normal kdu. F7 antecipa só o contador no alvo; stamp privado zera apenas após readback da raça e protege o reset após queda; cancelamentos e IMAD finito tratados. 122/122 testes Node (29 novos), serviço/F7/revisão F7, parse/reverse-check passaram; runtime preparado, servidor permanece parado, nenhum teste humano/modpack/OFICIAL. [Relatório e roteiro](etapa-2-transformacao-forcada.md); [decisão](../../03-decisoes/ADR-0020-sobrenatural-transformacao-forcada.md); [investigação](investigacao-16-11-transformacao-forcada.md). Corrigido o quadro Bruxa: ligação por origem entregue em 79098ad, revisão independente e jogo pendentes. Próxima tarefa Sol: restante da Reserva de Sangue, reclassificada alta complexidade por Terra.
