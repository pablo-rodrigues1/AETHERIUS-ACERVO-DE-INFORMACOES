---
titulo: Progresso do desenvolvimento
tipo: guia-staff
status: ativo
responsaveis: []
atualizado: 2026-11-14
tags: [staff, progresso, status]
relacionados: [../02-sistemas/README.md]
---

# Progresso do desenvolvimento

## Resumo
Onde está cada sistema do Aetherius. Atualizado pelas IAs dos devs a cada envio (REGRAS 11.4).
Última revisão: **14/11/2026**, feita a partir do código do servidor (`aetherius-server`). Ela mostra o que existe no código. Se cada sistema está ligado no servidor oficial não foi verificado.

**Legenda:** ✅ Concluído · 🟢 Quase pronto · 🟡 Em andamento · 📝 Planejado/estacionado · ⚪ Pendente · ⛔ Travado

**Visão geral:** ✅ 4 · 🟢 3 · 🟡 9 · 📝 2 · ⚪ 2 · ⛔ 0

## 🎯 Em foco agora
- **Classes e Leveling:** ligar o XP ganho ao matar inimigos e testar no servidor oficial.
- **Profissões:** nova interface; depois, testes dentro do jogo.

## Infraestrutura

| Sistema | Status | Próximo passo | Atualizado |
|---|---|---|---|
| Launcher do servidor | ✅ | — | 11/09/2026 |
| Integração Launcher + Mod Organizer 2 | ✅ | — | 11/09/2026 |
| Sincronização de mods cliente × servidor | ✅ | — | 11/09/2026 |

## Sistemas do jogo
*Na ordem de implementação.*

| # | Sistema | Status | Próximo passo | Atualizado |
|---|---|---|---|---|
| 1 | Painel de Admin (F7, com comandos) | 🟢 | Pronto no código; ligar por configuração no oficial | 14/11/2026 |
| 2 | Classes, Leveling, Inimigos e Dungeons | 🟡 | 18 classes e 9 marcos prontos; falta XP por abate e teste no oficial | 14/11/2026 |
| 3 | Profissões | 🟡 | 9 profissões e 180 receitas no código; bloqueado até a nova interface | 14/11/2026 |
| 4 | Loot | ⚪ | Sem código identificado | 14/11/2026 |
| 5 | Durabilidade de equipamentos | 🟡 | Funciona via `/manutencao`; falta aba na interface e teste em jogo | 14/11/2026 |
| 6 | Trade | 📝 | Estacionado: só o convite existe; falta a troca de itens/ouro | 14/11/2026 |
| 7 | Apoiadores | 🟡 | Loja e benefícios no código; falta ativar e testar | 14/11/2026 |
| 8 | Sobrevivência | ✅ | — | 14/11/2026 |
| 9 | Morte e Respawn | 🟢 | Pronto e testado no código; confirmar no oficial | 14/11/2026 |
| 10 | Aluguel de casas | 🟡 | Residências e impostos por cidade no código; falta ativar e testar | 14/11/2026 |
| 11 | Aluguel de quartos | ⚪ | Sem código separado identificado | 14/11/2026 |
| 12 | Painel de informações | 🟡 | Painel do jogador no código; falta homologar | 14/11/2026 |
| 13 | Loja de Diademas | 🟡 | Carteira de Diademas e loja no código; falta ativar e testar | 14/11/2026 |

## Em desenvolvimento fora da lista oficial

| Sistema | Status | Próximo passo | Atualizado |
|---|---|---|---|
| Lobisomem sincronizado | 🟡 | Pronto no kit de teste; falta ver a versão final em jogo e testar com 2 jogadores | 14/11/2026 |
| NPCs travando (IA quebrada) | 🟡 | Investigando: falta reproduzir com o kit de teste que registra a causa | 14/11/2026 |
| Correções de dano (combate) | 🟢 | Pacote entregue; falta ligar no oficial por fases e testar em jogo | 14/11/2026 |
| Maldições (vampirismo, licantropia, bruxaria) | 📝 | Progressão em 6 estágios desenhada na planilha; sem código (a transformação do lobisomem está em teste à parte) | 14/11/2026 |

## Outros sistemas encontrados no código
Ainda fora da lista oficial. Status a confirmar com os devs: chat de RP, voz por proximidade (VoIP), governança/Jarl, bancas de mercado, economia regional, refino de equipamentos, sistema de almas, cavalos, marcadores de mapa, zonas seguras.

## Últimas mudanças
*Máximo de 10 linhas; as mais antigas saem (o histórico completo fica no git e nos diários).*

- **14/11/2026:** Planilha de classes, leveling, raças, poções, encantamentos e maldições conferida com o código e levada ao guia; onde diverge, vale o código.
- **14/11/2026:** Correções de dano do servidor documentadas (kdu entra no GameplayCore pela frente de dano).
- **14/11/2026:** Lobisomem sincronizado e investigação dos NPCs travando entram no acervo; guia da Staff do lobisomem publicado.
- **14/11/2026:** Classes: skills definidas pelo servidor, limite de invocações, ajuste de nível pelo painel da Staff e Anti-Mago com Maestria em Conjuração. Guias da Staff de classes, progressão, masmorras e manutenção publicados.
- **14/11/2026:** Painel revisado a partir do código do servidor.
- **08/11/2026:** Profissões: execução no servidor implementada; ativação bloqueada até a nova interface.
- **25/10/2026:** Classes: pacotes de integração do servidor e do cliente preparados; build e teste em jogo pendentes.
- **12/10/2026:** Classes: entrega de perks validada em jogo no servidor local (162/162 marcos).
- **01/10/2026:** Classes, Dano, Inimigos, Leveling e Durabilidade unificados no GameplayCore.
- **11/09/2026:** Classes: efeitos de raças, pedras guardiãs e distribuição de classes prontos no código.
