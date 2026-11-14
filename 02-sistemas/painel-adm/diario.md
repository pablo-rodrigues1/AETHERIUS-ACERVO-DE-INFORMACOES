---
titulo: Diário de Painel administrativo
tipo: historico
status: ativo
responsaveis: []
atualizado: 2026-11-14
tags: [aetherius]
relacionados: []
---

# Diário de Painel administrativo

## Resumo

Histórico documental de Painel administrativo, com a base de integração e o próximo passo.

## 2026-11-14, controle "dá e tira" após trocar aparência (kdu / Claude Opus 5.5)
- **Causa:** depois da criação de personagem o cliente segura os controles até a sessão nova da UI. Ele os desligava de novo a cada quadro e brigava com quem os religava, por até 90 s: o jogador perdia e ganhava o controle sem parar e a bússola piscava.
- **Feito:** os controles são desligados uma vez, e de novo só após cada loading. Soltam 8 s depois de o personagem recarregar. Patch do cliente em `missoes/2026-11-14-bugs-akatosh` do kdu.
- **Próximo:** testar em jogo trocando a aparência pelo painel.

## 2026-11-14

Registrada a entrega **2026.11.13.1** no acervo. Operações de staff pelo F7, auditoria, inventário, PD e registro de compras. A revisão instalada continua a confirmar pela equipe oficial.

Próximo passo: Conferir as versões do F7 e testar a consulta a compras históricas.

[Documentação do sistema](README.md)
