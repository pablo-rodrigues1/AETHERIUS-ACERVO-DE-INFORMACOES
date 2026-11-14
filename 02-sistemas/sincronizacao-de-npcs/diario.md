---
titulo: Diário da Sincronização de NPCs
tipo: historico
status: ativo
responsaveis: [kdu]
atualizado: 2026-11-14
tags: [diario, npc]
relacionados: [README.md]
---

# Diário da Sincronização de NPCs

## Resumo
Registro cronológico do desenvolvimento, do mais recente para o mais antigo. Entradas curtas.

## 2026-11-14 (kdu / Claude Opus 5.5)
- **Feito:** análise do teste da sala de dano (13/11): inimigos em laço, arqueiro sem reagir, guerreiro só bloqueando. Numa reprodução do Kdu (13/11), o cliente apagou e recriou o mesmo NPC a cada ~5,6 s pelo caminho `formView.destroy`. Cliente do kit de teste v19 instrumentado: grava em `Data\Platform\Logs\aetherius-cliente-logs.txt` o motivo de cada destruição de NPC, as mensagens de hospedagem (`HOST-START`), animações de ataque bloqueadas e pedidos de hospedagem por NPC parado. O script de coleta do kit junta esse arquivo.
- **Descartado:** o NPC de apoio da sala de teste não é a causa (ele foi tirado da sala durante a luta no v19, só para isolar o teste).
- **Próximo:** reproduzir com o v19, ler os motivos no log e corrigir o ponto certo no cliente.
- **Bloqueios:** aguardando reprodução com o cliente instrumentado.
