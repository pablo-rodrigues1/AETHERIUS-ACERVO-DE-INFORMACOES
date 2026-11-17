---
titulo: Changelog
tipo: historico
status: ativo
responsaveis: []
atualizado: 2026-11-17
tags: [changelog]
relacionados: []
---

# Changelog

## Resumo
Mudanças relevantes por versão, da mais recente para a mais antiga. Formato baseado em [Keep a Changelog](https://keepachangelog.com/pt-BR/).

## [Não lançado]
### Adicionado
- Guildas R28: preparação SkyMP com bootstrap verificável, configuração portátil, correções de sincronização e pacote local testado. Escopo completo, guia de integração/rollback e matriz de evidências publicados em Markdown. Estante coletiva para membros atuais dos Magos e log confirmado de depósitos/retiradas exclusivo do Arquimago, instalados sem reinício. Oficial/dois clientes humanos pendentes. [Escopo](../02-sistemas/guildas/escopo.md), [ADR-0021](../03-decisoes/ADR-0021-bau-coletivo-do-colegio.md), [ADR-0022](../03-decisoes/ADR-0022-preparacao-skymp-das-guildas.md).
- Física de cabelos e capas (kdu): 910 cabelos com mechas por região e 6 capas do Cloaks and Capes balançando via FSMP 4.1.1, com barreiras contra o corpo e a armadura; ossos e física gerados por ferramenta própria, sem arquivos de física de terceiros. Dois mods sem plugin; em teste. [Sistema](../02-sistemas/fisica-de-cabelos-e-capas/README.md).
- Guildas: Baú do Arquimago exclusivo do cargo, nome nativo persistente e conteúdo preservado. Depósito, fechar/reabrir e retirada de um septim pelo cliente real confirmados, sem reinício. 136 testes e rechecagens aprovados; oficial pendente. [Decisão](../03-decisoes/ADR-0018-bau-exclusivo-do-arquimago.md).
- Guildas R26/R27: retirada a tag visual de fictício, quatro cadastros comuns privados acrescentados e interferência de instância antiga corrigida na abertura dos quartos. Abertura confirmada pelo dev; 135 testes aprovados. Relato posterior de persistência dos itens segue em investigação. [Decisão](../03-decisoes/ADR-0017-identificacao-interna-dos-membros-de-teste.md), [diário](../02-sistemas/guildas/diario.md).
- Guildas R25 privada: dez quartos com compartimentos existentes e propriedade protegida, controle geral das portas exclusivo do Arquimago, retirada de escola por ele e cronograma de aulas na Visão geral. 134 testes, ensaio nativo e vinte nomes no cliente conferidos; aceite humano/oficial pendentes. [Decisão](../03-decisoes/ADR-0016-quartos-escolas-e-cronograma-dos-magos.md), [diário](../02-sistemas/guildas/diario.md).
- Sobrenatural: candidato local 4fc0034 reduzido a Fera/Lorde/raça/recarga após correções da revisão Terra; 50 testes, builds e boot sem falhas, jogo/revisão de retorno pendentes. Demais poderes adiados, sem nativo ([relatório](../02-sistemas/sobrenatural/formas-e-poderes.md), [ADR-0015](../03-decisoes/ADR-0015-sobrenatural-lotes-js.md)).
- Conjuração: complemento candidato e pacotes separados fisicamente do projeto de Guildas, preservando arquivos anteriores e hashes. Ferramentas conjuntas retiradas da distribuição atual dos painéis; dependências e ZIP conferidos sem controlador de conjuração. Privado permanece no SkySync sem reinício, e o candidato passou em 13 testes no destino. [Estado e limites](../02-sistemas/gameplay-core/conjuracao.md), [diário de Guildas](../02-sistemas/guildas/diario.md).
- Guildas R23 privada: exclusividade de professor/bibliotecário ampliada a cargos personalizados e liderança; um posto de professor por pessoa, com vínculo pessoal de escola preservado. Migração auditada retirou três cargos extras de teste. 117 testes, teste nativo isolado e confirmação R23 do cliente aprovados; oficial pendente. [Decisão](../03-decisoes/ADR-0013-exclusividade-dos-cargos-dos-magos.md), [diário](../02-sistemas/guildas/diario.md).
- Guildas R22 instalada no privado: aba Portas do Colégio exclusiva de líder/professores, 68 passagens/86 referências, tranca nos dois sentidos inclusive para operador, persistência e auditoria. Professor e bibliotecário não acumulam funções. 112 testes, rechecagens e teste nativo isolado aprovados; cliente confirmou a atualização, sem reinício. Travessia humana e homologação oficial pendentes. [Decisão](../03-decisoes/ADR-0012-portas-do-colegio-e-funcoes-incompativeis.md), [diário](../02-sistemas/guildas/diario.md).
- Guildas R21 ativa no privado: bancada exclusiva dos bibliotecários, escolha de membro/escola por líder e professores e insígnias protegidas do Elemental Vision II. Instalação sincronizada, 104 testes e teste nativo isolado aprovados; servidor real confirmou entrega e persistência de uma unidade, com os outros itens preservados. Conferência visual e homologação oficial pendentes. [Decisão](../03-decisoes/ADR-0011-bancada-e-insignias-das-escolas.md), [diário](../02-sistemas/guildas/diario.md).
- Guildas R20: registros de gestão com nomes, IDs de perfil, cargos/funções e antes/depois; persistência histórica, acesso exclusivo da liderança e aviso para registros antigos. Instalado no privado sem reinício, com estado preservado e fonte R20 confirmada no cliente. Testes e prévia automatizada aprovados; conferência no jogo e homologação oficial pendentes. [Decisão](../03-decisoes/ADR-0010-registros-detalhados-de-guildas.md), [diário](../02-sistemas/guildas/diario.md).
- Guildas: visual R18 inspirado em Grupo e Reis, preservando azul, verde musgo, marrom escuro e a navegação por classe. Sete testes de interface aprovados; aplicado no privado sem reinício, com Magos observado em jogo e prévias das três guildas. [Decisão](../03-decisoes/ADR-0008-visual-dos-paineis-de-guildas.md), [diário](../02-sistemas/guildas/diario.md).
- Guildas: Biblioteca R17 apresenta primeiro as classes e depois seus tomos, com retorno à escolha e filtragem antes da paginação; aplicada no privado sem reinício, com testes e renderização R17 reportada pelo cliente. [Decisão](../03-decisoes/ADR-0007-biblioteca-primeiro-a-classe.md), [diário](../02-sistemas/guildas/diario.md).
- Guildas: aba de produção renomeada para Biblioteca, com classes, categorias e efeitos base dos tomos; antiga Biblioteca retirada. R16 instalada no privado, 12 testes aprovados e confirmação visual em jogo pendente. [Decisão](../03-decisoes/ADR-0006-biblioteca-e-indicacoes-de-magias.md), [diário](../02-sistemas/guildas/diario.md).
- Planejamento COMÉRCIO aprovado: estabelecimentos adquiríveis, contratação de profissionais, salários e Imposto de Comércio mensal definido pelo Rei; referência estrutural em Residência, consequências econômicas e políticas pendentes. Incluídos [texto completo](../02-sistemas/expansao-dos-paineis/comercio-empresas-e-profissoes.md), [ADR-0005](../03-decisoes/ADR-0005-comercio-empresas-e-profissoes.md) e guia da Staff; sem implementação ou cobrança nova nesta publicação.
- Conjuração: acompanhamento e duas criaturas instalados e confirmados no privado; complemento separado para v0.38, com backup e reversão, ainda pendente de integração/homologação oficial. [Estado e evidências](../02-sistemas/gameplay-core/conjuracao.md), [ADR-0004](../03-decisoes/ADR-0004-adaptacao-isolada-da-conjuracao.md).
- Conciliação de feitiços: Classes - Progressão B23:F40 e fichas da Staff com os 164 registros de Classes, por tier; catálogo no Acervo e no GameplayCore, com divergências de authorizedSpells/allSpellsList documentadas. Apenas planilha e documentação, sem alteração de runtime. [Catálogo](../02-sistemas/gameplay-core/feiticos-por-classe.md).
- Documentadas as entregas Loja 0.7.0, Residências 0.5.3, Painel ADM 2026.11.13.1 e governos ADM1/REIS1/TABFIX1, com as cinco expansões planejadas em 13/11. [Inventário das entregas](../06-referencias/inventario-aetherius.md).
- Criação do acervo de informações ([ADR-0000](../03-decisoes/ADR-0000-adocao-do-acervo.md)).
- Regras de integração contínua por IA, diário por sistema, quadro de status ([ADR-0001](../03-decisoes/ADR-0001-integracao-continua-por-ia.md)).
- GameplayCore SYNC-1114 (`05f5a0f`): skills de classe, capacidade de invocação, `class.level`, Anti-Mago com Maestria em Conjuração, chaves `character:<uuid>` ([diário](../02-sistemas/gameplay-core/diario.md)).
- UI Core SYNC-1114 (`e9075d0`): criador de personagem que finaliza, descrições pela bridge, tela de carregamento, fonte Sovngarde ([diário](../02-sistemas/ui-core/diario.md)).
- Guia da Staff: classes, progressão, masmorras, manutenção, fichas por classe, raças e pedras; design de poções, encantamentos e maldições ([ADR-0003](../03-decisoes/ADR-0003-codigo-prevalece-sobre-planilhas-de-design.md)).
- Processo de sincronização dos Cores ([ADR-0002](../03-decisoes/ADR-0002-sincronizacao-dos-cores-com-os-repositorios-oficiais.md)).
- Verificação de sistema já existente com confirmação do dev (REGRAS 10.5.1).
- Pasta `07-guia-da-staff/` com guias didáticos para toda a Staff (REGRAS 11).
- Painel de progresso `07-guia-da-staff/progresso.md` (REGRAS 11.4).

### Corrigido
- Pergaminho que não ia para a mão: o cliente não reconhecia o tipo "scrollitem" enviado pelo servidor (no SkyrimPlatform é "Scroll"). Correção no patch de cliente do pacotão v0.38; teste em jogo pendente. [Diário](../02-sistemas/gameplay-core/diario.md).

- **2026-11-16 — Sobrenatural (candidato local):** origem de grants, Bruxaria, poderes JS, tempos 40/60 e contrato de sangue, 122 testes; revisão e homologação pendentes.

- 2026-11-16 — Sobrenatural: transformação forçada 72 h reais online/noite, sequência privada de 20 s e atalho F7; candidato local 856232a, 122 testes Node, revisão e jogo pendentes. Corrigido status da integração Bruxa (79098ad).
