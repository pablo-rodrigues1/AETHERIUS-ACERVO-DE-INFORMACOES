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
