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
