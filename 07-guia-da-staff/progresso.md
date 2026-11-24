---
titulo: Progresso do desenvolvimento
tipo: guia-staff
status: ativo
responsaveis: []
atualizado: 2026-11-24
tags: [staff, progresso, status]
relacionados: [../02-sistemas/README.md]
---

# Progresso do desenvolvimento

## Resumo
Onde está cada sistema do Aetherius. Atualizado pelas IAs dos devs a cada envio (REGRAS 11.4).
Última revisão: **14/11/2026**, feita a partir do código do servidor (`aetherius-server`). Ela mostra o que existe no código. Se cada sistema está ligado no servidor oficial não foi verificado.
Conjuração revisada em **15/11/2026** no privado; a integração oficial permanece pendente.

**Legenda:** ✅ Concluído · 🟢 Quase pronto · 🟡 Em andamento · 📝 Planejado/estacionado · ⚪ Pendente · ⛔ Travado

**Visão geral:** ✅ 4 · 🟢 4 · 🟡 16 · 📝 4 · ⚪ 1 · ⛔ 0

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
| 1 | [Painel de Admin (F7)](../02-sistemas/painel-adm/README.md) | 🟢 | Conferir a base do F7 e homologar o registro de compras | 14/11/2026 |
| 2 | Classes, Leveling, Inimigos e Dungeons | 🟡 | Alinhar feitiços, XP por abate e conjuração; testar no oficial | 14/11/2026 |
| 3 | [Profissões](../02-sistemas/profissoes/README.md) | 🟡 | 9 profissões e 180 receitas no código; documentação do Core publicada; aguarda nova interface | 18/11/2026 |
| 4 | Loot | ⚪ | Sem código identificado | 14/11/2026 |
| 5 | Durabilidade de equipamentos | 🟡 | Funciona via `/manutencao`; falta aba na interface e teste em jogo | 14/11/2026 |
| 6 | Trade | 📝 | Estacionado: só o convite existe; falta a troca de itens/ouro | 14/11/2026 |
| 7 | [Apoiadores](economia/loja-e-diademas.md) | 🟡 | Loja e benefícios no código; falta ativar e testar | 14/11/2026 |
| 8 | Sobrevivência | ✅ | — | 14/11/2026 |
| 9 | Morte e Respawn | 🟢 | Pronto e testado no código; confirmar no oficial | 14/11/2026 |
| 10 | [Residências e IPTU](../02-sistemas/residencias/README.md) | 🟡 | Homologar acesso, venda e recolhimento do IPTU | 14/11/2026 |
| 11 | [Aluguel de quartos](../02-sistemas/expansao-dos-paineis/comercios-e-quartos.md) | 📝 | Definir o contrato de aluguel e a recuperação dos bens | 14/11/2026 |
| 12 | Painel de informações | 🟡 | Painel do jogador no código; falta homologar | 14/11/2026 |
| 13 | [Loja de Diademas](../02-sistemas/loja/README.md) | 🟡 | Conferir a carteira e o vínculo do personagem na Loja 0.7.0 | 14/11/2026 |

## Em desenvolvimento fora da lista oficial

| Sistema | Status | Próximo passo | Atualizado |
|---|---|---|---|
| Lobisomem sincronizado | 🟢 | Visto em jogo, com grito, coração e tela vermelha; falta teste com 2 jogadores e aplicar no oficial | 14/11/2026 |
| [Olhar dos jogadores (head tracking)](perguntas-frequentes/olhar-dos-jogadores.md) | 🟡 | Feito no servidor de teste: todo mundo vê para onde cada um olha (câmera, pessoas e até o vazio), sem encarar quem chega perto. Corrigido em 17/11: o boneco girava o corpo inteiro em vez de só a cabeça. Falta teste com 2 jogadores | 17/11/2026 |
| [Ocarina (só música)](../02-sistemas/ocarina/README.md) | 🟡 | Item novo: tocar notas e músicas da Zelda, sem poderes; falta testar em jogo | 14/11/2026 |
| [Sons nas telas](../02-sistemas/sons-das-telas/README.md) | 🟡 | Todas as nossas telas fazem barulho, só com sons do próprio Skyrim: clique, fechar, abas, moedas na compra, folhear páginas, pegar e soltar itens no baú e no refino, barras e atalhos de teclado. A criação de personagem precisa da DLL nova; falta ouvir em jogo | 20/11/2026 |
| [Túnica do Herói](../02-sistemas/tunica-do-heroi/README.md) | 🟡 | Roupas para o alfaiate, gorro mostrando o cabelo; falta testar e colar as receitas | 14/11/2026 |
| [Física de cabelos e capas](../02-sistemas/fisica-de-cabelos-e-capas/README.md) | 🟡 | Cabelo comprido com mechas e capas balançando; 910 cabelos prontos. A YuYu viu em jogo: física boa; trança entrando no peitoral já ajustada. Falta ver a versão com mechas | 15/11/2026 |
| NPCs travando (IA quebrada) | 🟡 | Investigando: falta reproduzir com o kit de teste que registra a causa | 14/11/2026 |
| Correções de dano (combate) | 🟢 | Pacote entregue; falta ligar no oficial por fases e testar em jogo | 14/11/2026 |
| [Maldições (vampirismo, licantropia, bruxaria)](../02-sistemas/sobrenatural/formas-e-poderes.md) | 🟡 | Revisar e testar em jogo os poderes e sons da forma de Lorde Vampiro | 20/11/2026 |
| [Governos e castelos](../02-sistemas/governos/README.md) | 🟡 | Validar as portas dos nove castelos | 14/11/2026 |
| [Expansão dos painéis](../02-sistemas/expansao-dos-paineis/README.md) | 📝 | Detalhar os contratos dos projetos, incluindo COMÉRCIO | 15/11/2026 |
| [COMÉRCIO: empresas e profissões](economia/comercio-empresas-e-profissoes.md) | 📝 | Definir aquisição, salários e parâmetros do imposto mensal | 15/11/2026 |
| [Guildas (SkyGuilds) — Melekyy](regras-de-roleplay/guildas.md) | 🟡 | R32 em desenvolvimento: Companions, Skyforge e duas sedes da Irmandade; testes pelo dev | 24/11/2026 |
| [Conjuração — Melekyy](classes/conjuracao.md) | 🟡 | Obter a base completa e testar a integração com dois jogadores no oficial | 15/11/2026 |

## Outros sistemas encontrados no código
Ainda fora da lista oficial. Status a confirmar com os devs: chat de RP, voz por proximidade (VoIP), bancas de mercado, economia regional, refino de equipamentos, sistema de almas, cavalos, marcadores de mapa, zonas seguras.

## Últimas mudanças
- **18/11/2026:** Profissões: documentação técnica completa do `AetheriusGameplayCore` (governador, coleta, artesanato, refino, encantamento fora da Alpha, balanceamento e migrations `0013`/`0014`) e guias simples da Staff publicados no Acervo.
- **15/11/2026:** Cabelos e capas com física: 910 cabelos com mechas (franja, laterais, costas) e capas balançando, sem atravessar o corpo. Pergaminho que não ia para a mão corrigido. Ataque travando depois de trocar a aparência ainda em investigação.
- **15/11/2026:** Baú dos aposentos do Arquimago exclusivo do cargo e nomeado Baú do Arquimago. Depósito, fechar/reabrir e retirada pelo cliente real conferidos, com o septim de teste devolvido. 136 testes completos e rechecagens aprovados; demais compartimentos reais e oficial seguem para homologação.
- **17/11/2026:** Guildas R29: Guildas/Casas observados no Skyrim; recuperação do transporte e retirada de callbacks antigos. Permissões dos quatro perfis e dez quartos aprovados no nativo. Grupo e operação manual do quarto em reteste.
- **17/11/2026:** Guildas: R28 preparada para integração SkyMP, escopo completo e guia publicados. Estante coletiva dos Magos e logs confirmados de depósito/retirada instalados no privado. Preflight/nativo aprovados; oficial e dois clientes reais pendentes.
- **15/11/2026:** Guildas: tag visual retirada, quatro membros de teste adicionados e interferência de uma instância antiga corrigida. O dev confirmou abertura do baú. 135 testes e ensaio nativo aprovados; relato de itens não permanecerem ainda em investigação no cliente real.
- **15/11/2026:** Guildas: troca privada de liderança para professor da escola atual verificada, sem acúmulo de funções, sem reinício e com inventário/insígnia preservados. Nenhuma nova regra; oficial segue pendente.
- **15/11/2026:** Guildas R25 aplicada sem reinício: dez quartos com móveis existentes, escola removida só pelo Arquimago e cronograma visível aos membros. Controle geral de portas só do Arquimago; professores operam portas por proximidade. 134 testes e ensaio nativo aprovados, cliente confirmou vinte nomes. Aceite humano e oficial pendentes.
- **15/11/2026:** Conjuração separada dos arquivos e pacote atual de Guildas, com arquivos antigos preservados e hashes conferidos. Controlador privado segue no SkySync, sem reinício; candidato passou em 13 testes no novo projeto. Documentação publicada; validação global falha em 17 documentos de Sobrenatural. Oficial pendente.
- **15/11/2026:** Guildas R23 instalada sem reinício: professor/bibliotecário sem cargos extras ou liderança, mantendo escola. Três cargos extras de teste retirados com registro; 117 testes e teste nativo isolado aprovados. Cliente confirmou R23; oficial pendente. Documentação publicada; validação global falha em 17 documentos de Sobrenatural.
*Máximo de 10 linhas; as mais antigas saem (o histórico completo fica no git e nos diários).*
