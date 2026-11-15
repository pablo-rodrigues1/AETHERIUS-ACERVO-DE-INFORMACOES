---
titulo: Diário da Sincronização de NPCs
tipo: historico
status: ativo
responsaveis: [kdu]
atualizado: 2026-11-15
tags: [diario, npc]
relacionados: [README.md]
---

# Diário da Sincronização de NPCs

## Resumo
Registro cronológico do desenvolvimento, do mais recente para o mais antigo. Entradas curtas.

## 2026-11-15, impacto do complemento de conjuração (Melekyy / Codex)

- **Impacto:** a [adaptação de conjuração](../gameplay-core/conjuracao.md) limita seus guardas aos modelos autorizados de criaturas conjuradas. Preserva o descarte/estacionamento anterior e a limpeza dos outros NPCs; não declara corrigida a investigação geral de NPCs travando.
- **Evidência:** trecho recuperado do renderer mostrou callback síncrono ignorado antes da atribuição de identidade. O caso foi coberto por teste; a adaptação oficial completa ainda requer a revisão fonte e homologação. Privado com acompanhamento e duas criaturas confirmado pelo usuário.
- **Próximo:** conferir os pontos de criação, host e destruição ao integrar no cliente oficial; manter a investigação de kdu independente.

## 2026-11-14 (kdu / Claude Opus 5.5)
- **Feito:** análise do teste da sala de dano (13/11): inimigos em laço, arqueiro sem reagir, guerreiro só bloqueando. Numa reprodução do Kdu (13/11), o cliente apagou e recriou o mesmo NPC a cada ~5,6 s pelo caminho `formView.destroy`. Cliente do kit de teste v19 instrumentado: grava em `Data\Platform\Logs\aetherius-cliente-logs.txt` o motivo de cada destruição de NPC, as mensagens de hospedagem (`HOST-START`), animações de ataque bloqueadas e pedidos de hospedagem por NPC parado. O script de coleta do kit junta esse arquivo.
- **Descartado:** o NPC de apoio da sala de teste não é a causa (ele foi tirado da sala durante a luta no v19, só para isolar o teste). A sala de teste e o NPC de apoio são só do ambiente de teste local; não vão para o servidor oficial.
- **Próximo:** reproduzir com o v19, ler os motivos no log e corrigir o ponto certo no cliente.
- **Bloqueios:** aguardando reprodução com o cliente instrumentado.
