# Índice do Acervo

> Mantenha esta lista atualizada a cada commit. Formato: `caminho` — resumo de uma linha.

## 00 — Guia
- `00-guia/como-usar.md` — Como navegar, consultar e contribuir com o acervo.
- `00-guia/glossario.md` — Termos do projeto e do SkyMP.

## 01 — Visão geral
- `01-visao-geral/README.md` — Visão do servidor Aetherius e mapa da arquitetura.
- `01-visao-geral/repositorios.md` — Repositórios do projeto e responsabilidade de cada um.

## 02 — Sistemas
- `02-sistemas/README.md` — Quadro de status, estrutura de pastas e mapa de dependências dos sistemas.
- `02-sistemas/gameplay-core/README.md` — AetheriusGameplayCore: lógica autoritativa de gameplay, módulos, parâmetros e pendências.
- `02-sistemas/gameplay-core/diario.md` — Diário do GameplayCore (sincronizações SYNC-1102 e SYNC-1114).
- `02-sistemas/gameplay-core/feiticos-por-classe.md` — 164 registros de feitiços por classe, escola e tier; conciliação da planilha e divergências dos JSONs.
- `02-sistemas/gameplay-core/correcoes-de-dano.md` — Correções de dano no servidor: chaves por fase, armadura, resistências, pendências.
- `02-sistemas/gameplay-core/conjuracao.md` — Conjuração separada dos painéis de Guildas, com acompanhamento e duas criaturas confirmada no privado; adaptação v0.38 e homologação oficial pendentes.
- `02-sistemas/ui-core/README.md` — Aetherius UI Core: interface Meridian/CEF, bridge SKSE, mapeamento de pastas cliente↔repo.
- `02-sistemas/ui-core/diario.md` — Diário do UI Core (sincronização SYNC-1114).
- `02-sistemas/sobrenatural/README.md` — Sobrenatural: maldições (vampiro, lobisomem, bruxa), setagem pelo painel, keyword no servidor, 6 estágios até 2M XP, UI.
- `02-sistemas/sobrenatural/especificacao.md` — Transcrição da planilha de maldições e regras do dev.
- `02-sistemas/sobrenatural/direcao-de-arte.md` — Padrão visual e análise das referências de UI (estágios, Disciplinas).
- `02-sistemas/sobrenatural/proposta-textos.md` — Proposta de textos de Lobisomem, Bruxa e tela vazia.
- `02-sistemas/sobrenatural/integracao-ui-core.md` — Manifesto do módulo e contrato snapshot/choose com o servidor.
- `02-sistemas/sobrenatural/revisao-etapa-1.md` — Revisão cruzada da Etapa 1 e arbitragens.
- `02-sistemas/sobrenatural/etapa-1-telas.md`, `etapa-1-correcoes.md`, `etapa-1-icones-fonte.md` — Relatórios da Etapa 1.
- `02-sistemas/sobrenatural/diario.md` — Diário do Sobrenatural.
- `02-sistemas/lobisomem/README.md` — Lobisomem sincronizado: forma controlada pelo servidor, animação própria, dano de prata.
- `02-sistemas/lobisomem/diario.md` — Diário do Lobisomem sincronizado.
- `02-sistemas/olhar-sincronizado/README.md` — Olhar sincronizado: o boneco de cada jogador olha para quem ele mira (head tracking no multiplayer).
- `02-sistemas/olhar-sincronizado/diario.md` — Diário do Olhar sincronizado.
- `02-sistemas/ocarina/README.md` — Ocarina só com música: notas, músicas, modo automático, sem poderes.
- `02-sistemas/ocarina/diario.md` — Diário da Ocarina.
- `02-sistemas/tunica-do-heroi/README.md` — Túnica do Herói: 16 peças para o alfaiate, gorro mostrando o cabelo.
- `02-sistemas/tunica-do-heroi/diario.md` — Diário da Túnica do Herói.
- `02-sistemas/fisica-de-cabelos-e-capas/README.md` — Física de cabelos e capas: cabelo comprido e capas balançando (FSMP), gerado por ferramenta própria.
- `02-sistemas/fisica-de-cabelos-e-capas/diario.md` — Diário da Física de cabelos e capas.
- `02-sistemas/sincronizacao-de-npcs/README.md` — Investigação da IA de NPCs travada (NPC recriado em laço no cliente).
- `02-sistemas/sincronizacao-de-npcs/diario.md` — Diário da Sincronização de NPCs.
- `02-sistemas/guildas/README.md` — Guildas de Melekyy: R25 privada com dez quartos, compartimentos existentes, retirada de escola e cronograma; integrações oficiais pendentes.
- `02-sistemas/guildas/diario.md` — Diário de Guildas, responsável Melekyy, registro inicial e pendências de publicação e integração.
- `02-sistemas/loja/README.md` — Loja do TAB, carteira de Diademas e benefícios de apoiador.
- `02-sistemas/loja/diario.md` — Histórico documental de Loja e apoiadores, com a base de integração e o próximo passo.
- `02-sistemas/residencias/README.md` — Compra, acesso, revenda e imposto das propriedades residenciais.
- `02-sistemas/residencias/diario.md` — Histórico documental de Residências e IPTU, com a base de integração e o próximo passo.
- `02-sistemas/painel-adm/README.md` — Operações de staff pelo F7, auditoria, inventário, PD e registro de compras.
- `02-sistemas/painel-adm/diario.md` — Histórico documental de Painel administrativo, com a base de integração e o próximo passo.
- `02-sistemas/governos/README.md` — Governos municipais, nomeação de Reis, portas dos castelos e tesouro.
- `02-sistemas/governos/diario.md` — Histórico documental de Governos, Jarl e castelos, com a base de integração e o próximo passo.
- `02-sistemas/expansao-dos-paineis/README.md` — Cinco projetos para bancos, comércios, quartos, portões e ritual de coleta.
- `02-sistemas/expansao-dos-paineis/diario.md` — Histórico documental de Expansão dos painéis, com a base de integração e o próximo passo.
- `02-sistemas/expansao-dos-paineis/bancos-e-adm.md` — Empréstimos com juros para o Rei autorizado, com cobrança na conta institucional.
- `02-sistemas/expansao-dos-paineis/comercio-empresas-e-profissoes.md` — Modelo aprovado do COMÉRCIO: empresas, profissões, salários, imposto mensal e consequências possíveis de RP; implementação planejada.
- `02-sistemas/expansao-dos-paineis/alocar-comercio.md` — Cadastro de estabelecimentos, portas e responsável comercial.
- `02-sistemas/expansao-dos-paineis/portoes-da-cidade.md` — Controle de portões municipais por jurisdição de Rei e Lorde.
- `02-sistemas/expansao-dos-paineis/comercios-e-quartos.md` — Aluguel de quartos com direitos de entrada e uso do baú durante o contrato.
- `02-sistemas/expansao-dos-paineis/ritual-de-coleta.md` — Ritual territorial de coleta iniciado pelo Rei com mago, alquimista e Companion.

- `02-sistemas/governos/levantamento-dos-castelos.md` — Nove interiores e os 28 pares de portas por cidade.
- `02-sistemas/painel-adm/continuidade-da-sessao.md` — Proposta de continuidade do F7 e contrato de renovação pendente.

## 03 — Decisões
- `03-decisoes/README.md` — Registro de ADRs e seus status.
- `03-decisoes/ADR-0000-adocao-do-acervo.md` — Adoção deste repositório e de suas regras.
- `03-decisoes/ADR-0001-integracao-continua-por-ia.md` — IAs documentam o desenvolvimento continuamente via commit na main.
- `03-decisoes/ADR-0002-sincronizacao-dos-cores-com-os-repositorios-oficiais.md` — O que volta do servidor/cliente para os repos dos Cores e o que fica de fora.
- `03-decisoes/ADR-0003-codigo-prevalece-sobre-planilhas-de-design.md` — Quando planilha de design e código divergem, vale o código.
- `03-decisoes/ADR-0004-adaptacao-isolada-da-conjuracao.md` — Complemento restrito à conjuração, preservação da versão anterior e separação entre privado aprovado e oficial pendente.

- `03-decisoes/ADR-0005-comercio-empresas-e-profissoes.md` — Aprovação do modelo COMÉRCIO, com Residência como referência e políticas ainda pendentes.
- `03-decisoes/ADR-0006-biblioteca-e-indicacoes-de-magias.md` — Biblioteca na bancada dos Magos com classes, categorias e efeitos das fontes existentes.
- `03-decisoes/ADR-0007-biblioteca-primeiro-a-classe.md` — Escolha da classe antes da lista de tomos na Biblioteca.
- `03-decisoes/ADR-0008-visual-dos-paineis-de-guildas.md` — Acabamento dos três painéis inspirado em Grupo e Reis, com as cores preservadas.
- `03-decisoes/ADR-0009-bibliotecarios-fabricacao-e-traducao.md` — Vários bibliotecários, fabricação com duração e tradução da Biblioteca.
- `03-decisoes/ADR-0010-registros-detalhados-de-guildas.md` — Registros com nomes, IDs e detalhes preservados no momento da ação.
- `03-decisoes/ADR-0016-quartos-escolas-e-cronograma-dos-magos.md` — Quartos existentes, controle geral e retirada de escola do Arquimago, cronograma de aulas.
- `03-decisoes/ADR-0017-identificacao-interna-dos-membros-de-teste.md` — Tag visual retirada e identificação interna preservada, com quatro novos cadastros privados de teste.
- `03-decisoes/ADR-0018-bau-exclusivo-do-arquimago.md` — Baú existente dos aposentos exclusivo do cargo, com nome e depósito/reabertura verificados no cliente privado.
- `03-decisoes/ADR-0011-bancada-e-insignias-das-escolas.md` — Bancada só para bibliotecários, escolha de escola por líder/professores e insígnias protegidas.
- `03-decisoes/ADR-0012-portas-do-colegio-e-funcoes-incompativeis.md` — Controle das passagens nos dois sentidos por líder/professores e exclusão entre professor e bibliotecário.
- `03-decisoes/ADR-0013-exclusividade-dos-cargos-dos-magos.md` — Professor/bibliotecário dos Magos sem cargos extras ou liderança, preservando o vínculo pessoal de escola.
- `03-decisoes/ADR-0015-seletores-e-bancada-do-lider.md` — Listas de opções estáveis para todos os seletores e fabricação de tomos autorizada ao líder sem acumular funções.

## 04 — Histórico
- `04-historico/CHANGELOG.md` — Mudanças consolidadas por versão.

## 05 — Processos
- `05-processos/README.md` — Fluxos de trabalho do time.
- `05-processos/integracao-ia.md` — Como a IA se integra ao acervo; bloco padrão para o AGENTS.md dos projetos.
- `05-processos/sincronizacao-dos-cores.md` — Passo a passo para sincronizar GameplayCore e UI Core com as cópias oficiais.

## 06 — Referências
- `06-referencias/README.md` — Resumos de material externo.
- `06-referencias/planilha-classes-efeitos-encantamentos.md` — Planilha de design de classes, leveling, raças, poções, encantamentos e maldições, conferida contra o código.
- `06-referencias/documentacao-das-entregas.md` — Catálogo por revisão dos 123 documentos de origem em cinco repositórios.
- `06-referencias/modulos-sociais.md` — A referência de integração do painel Jarl inclui murais, grupos, negociação, apresentação e acampamentos.
- `06-referencias/estudos-de-integracao.md` — Histórico dos estudos de combate, login, interface e clima e de seus limites de validação.
- `06-referencias/inventario-aetherius.md` — Mapa das entregas preparadas para integração ao servidor oficial e dos estudos e planejamentos que as acompanham.

## 07 — Guia da Staff
- `07-guia-da-staff/README.md` — Explicações simples de mecânicas do jogo para toda a Staff.
- `07-guia-da-staff/progresso.md` — Painel com a situação atual de cada sistema.
- `07-guia-da-staff/glossario.md` — Termos do jogo em linguagem leiga.
- `07-guia-da-staff/classes/como-funcionam-as-classes.md` — As 18 classes, marcos, troca de classe, Winterhold e ações da Staff.
- `07-guia-da-staff/classes/conjuracao.md` — Criaturas que acompanham o dono, limite de duas no privado e integração oficial ainda em teste.
- `07-guia-da-staff/progressao/nivel-xp-e-atributos.md` — XP, XP em grupo, fadiga diária e pontos de atributo.
- `07-guia-da-staff/dungeons/inimigos-e-masmorras.md` — Respawn no mundo aberto e gerações/reset das masmorras.
- `07-guia-da-staff/itens/manutencao-de-equipamentos.md` — Kits de manutenção, cargas, penalidade e /manutencao.
- `07-guia-da-staff/perguntas-frequentes/olhar-dos-jogadores.md` — Para onde os jogadores olham (em teste): a cabeça vira para quem o jogador mira.
- `07-guia-da-staff/perguntas-frequentes/tecla-ponto-e-virgula-constelacao.md` — A tecla ; abre a constelação: causa e o que fazer se continuar.
- `07-guia-da-staff/sobrenatural/lobisomem.md` — Como funciona o lobisomem (em teste): transformação, tamanho, prata.
- `07-guia-da-staff/classes/ficha-conjuradores.md` — Skills e perks por marco, com feitiços por tier da planilha, das 6 classes conjuradoras (do código).
- `07-guia-da-staff/classes/ficha-guerreiros.md` — Skills e perks por marco, com feitiços por tier da planilha, das 6 classes guerreiras (do código).
- `07-guia-da-staff/classes/ficha-especialistas.md` — Skills e perks por marco, com feitiços por tier da planilha, das 6 classes especialistas (do código).
- `07-guia-da-staff/racas-e-pedras/README.md` — Tema: raças e pedras guardiãs.
- `07-guia-da-staff/racas-e-pedras/racas-e-pedras-guardias.md` — Habilidade de cada raça e de cada pedra guardiã.
- `07-guia-da-staff/itens/pocoes-e-encantamentos.md` — Design: poções e encantamentos do Alquimista e Encantador Mestre (não implementado).
- `07-guia-da-staff/sobrenatural/maldicoes.md` — Design: vampirismo, licantropia e bruxaria em 6 estágios (não implementado).
- `07-guia-da-staff/economia/loja-e-diademas.md` — O jogador usa Diademas na Loja do TAB e o Apoiador Beta pertence à conta.
- `07-guia-da-staff/economia/casas-e-iptu.md` — O dono paga o imposto da casa em Septims a cada 15 dias e gerencia o acesso pelo TAB.
- `07-guia-da-staff/economia/governos-e-castelos.md` — O Rei nomeado pode controlar as portas do castelo no painel de Casas.
- `07-guia-da-staff/regras-de-roleplay/guildas.md` — Explicação simples das Guildas em desenvolvimento por Melekyy e dos limites da atuação da Staff.
- `07-guia-da-staff/economia/comercio-empresas-e-profissoes.md` — Guia da Staff sobre o planejamento comercial e os limites do que já foi decidido.

- `02-sistemas/sobrenatural/formas-e-poderes.md` — Lote menor Fera/Lorde/raça/recarga pronto para reteste; poderes e origem de grants adiados.
- `03-decisoes/ADR-0014-forma-besta-recarga-real.md` — Forma da Besta LesserPower com recarga autoritativa de 30 minutos reais e zero no laboratório.


- `03-decisoes/ADR-0015-sobrenatural-lotes-js.md` — Lotes locais e Névoa/Força parciais sem extensão nativa.
