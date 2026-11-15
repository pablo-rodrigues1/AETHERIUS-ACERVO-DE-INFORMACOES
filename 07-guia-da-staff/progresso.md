---
titulo: Progresso do desenvolvimento
tipo: guia-staff
status: ativo
responsaveis: []
atualizado: 2026-11-15
tags: [staff, progresso, status]
relacionados: [../02-sistemas/README.md]
---

# Progresso do desenvolvimento

## Resumo
Onde está cada sistema do Aetherius. Atualizado pelas IAs dos devs a cada envio (REGRAS 11.4).
Última revisão: **14/11/2026**, feita a partir do código do servidor (`aetherius-server`). Ela mostra o que existe no código. Se cada sistema está ligado no servidor oficial não foi verificado.
Conjuração revisada em **15/11/2026** no privado; a integração oficial permanece pendente.

**Legenda:** ✅ Concluído · 🟢 Quase pronto · 🟡 Em andamento · 📝 Planejado/estacionado · ⚪ Pendente · ⛔ Travado

**Visão geral:** ✅ 4 · 🟢 4 · 🟡 14 · 📝 5 · ⚪ 1 · ⛔ 0

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
| 3 | Profissões | 🟡 | 9 profissões e 180 receitas no código; bloqueado até a nova interface | 14/11/2026 |
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
| [Olhar dos jogadores (head tracking)](perguntas-frequentes/olhar-dos-jogadores.md) | 🟡 | Feito no servidor de teste: todo mundo vê para onde cada um olha (câmera, pessoas e até o vazio), sem encarar quem chega perto; falta teste com 2 jogadores | 14/11/2026 |
| [Ocarina (só música)](../02-sistemas/ocarina/README.md) | 🟡 | Item novo: tocar notas e músicas da Zelda, sem poderes; falta testar em jogo | 14/11/2026 |
| [Túnica do Herói](../02-sistemas/tunica-do-heroi/README.md) | 🟡 | Roupas para o alfaiate, gorro mostrando o cabelo; falta testar e colar as receitas | 14/11/2026 |
| [Física de cabelos e capas](../02-sistemas/fisica-de-cabelos-e-capas/README.md) | 🟡 | Cabelo comprido e capas balançando; 577 cabelos prontos, falta ver em jogo e medir o desempenho | 15/11/2026 |
| NPCs travando (IA quebrada) | 🟡 | Investigando: falta reproduzir com o kit de teste que registra a causa | 14/11/2026 |
| Correções de dano (combate) | 🟢 | Pacote entregue; falta ligar no oficial por fases e testar em jogo | 14/11/2026 |
| Maldições (vampirismo, licantropia, bruxaria) | 📝 | Progressão em 6 estágios desenhada na planilha; sem código (a transformação do lobisomem está em teste à parte) | 14/11/2026 |
| [Governos e castelos](../02-sistemas/governos/README.md) | 🟡 | Validar as portas dos nove castelos | 14/11/2026 |
| [Expansão dos painéis](../02-sistemas/expansao-dos-paineis/README.md) | 📝 | Detalhar os contratos dos projetos, incluindo COMÉRCIO | 15/11/2026 |
| [COMÉRCIO: empresas e profissões](economia/comercio-empresas-e-profissoes.md) | 📝 | Definir aquisição, salários e parâmetros do imposto mensal | 15/11/2026 |
| [Guildas (SkyGuilds) — Melekyy](regras-de-roleplay/guildas.md) | 🟡 | R22 instalada no privado; confirmar travessia humana e integrar no oficial | 15/11/2026 |
| [Conjuração — Melekyy](classes/conjuracao.md) | 🟡 | Obter a base completa e testar a integração com dois jogadores no oficial | 15/11/2026 |

## Outros sistemas encontrados no código
Ainda fora da lista oficial. Status a confirmar com os devs: chat de RP, voz por proximidade (VoIP), bancas de mercado, economia regional, refino de equipamentos, sistema de almas, cavalos, marcadores de mapa, zonas seguras.

## Últimas mudanças
- **15/11/2026:** Guildas R22 privada: líder/professores controlam 68 passagens nos dois sentidos; professor e bibliotecário não acumulam funções. 112 testes e prova nativa isolada aprovados, cliente recebeu a atualização sem reinício; travessia humana e oficial pendentes.
- **15/11/2026:** Guildas R21 ativa no privado: bancada exclusiva, seleção de escola e insígnias protegidas. Plugin sincronizado nos dois lados; uma insígnia entregue e persistida no personagem de teste, com outros itens preservados. Conferência no cliente e oficial pendentes.
- **15/11/2026:** Guildas R19 no privado com vários bibliotecários, fabricação de três segundos, três integrantes fictícios de teste e 104 tomos traduzidos no painel. 88 testes e servidor nativo isolado aprovados; inspeção visual interrompida por Esc, sem confirmação da animação em jogo nem homologação oficial.
- **15/11/2026:** Guildas R18 com visual inspirado em Grupo e Reis, cores preservadas e Biblioteca por classe; aplicado no privado, com Magos observado no jogo e aceite visual pendente.
*Máximo de 10 linhas; as mais antigas saem (o histórico completo fica no git e nos diários).*

- **15/11/2026:** Aprovado o planejamento COMÉRCIO: empresas, contratação, salários e imposto mensal; texto revisado usa Residência como referência. Sem implementação nesta publicação.

- **15/11/2026:** Conjuração: duas criaturas acompanham no privado, confirmado pelo usuário; somente documentação publicada no acervo, com código/pacote locais e homologação oficial pendente.
- **14/11/2026:** Melekyy inicia a frente de conjuração: corrigir o desaparecimento e adaptar o acompanhamento do privado, preservando as correções anteriores.
- **14/11/2026:** Planilha e fichas completadas com os 164 registros de feitiços de Classes; diferenças do resumo do código documentadas, sem mudança no servidor.

- **14/11/2026:** Melekyy está desenvolvendo Guildas (SkyGuilds); registrados Magos, Alquimistas e Companions, com integração oficial pendente.
- **14/11/2026:** Relatos de 13/11 corrigidos em pacote: ícone girando no loading, `;` sem abrir a constelação, magia que desequipa, L/R legível, nórdico só para nórdicos e controle travando após trocar aparência. Invocação em análise.
