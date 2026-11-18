---
titulo: Diário de Guildas (SkyGuilds)
tipo: historico
status: ativo
responsaveis: [Melekyy]
atualizado: 2026-11-18
tags: [diario, guildas, skyguilds]
relacionados: [README.md]
---

# Diário de Guildas (SkyGuilds)

## Resumo

Registro do desenvolvimento de Guildas por Melekyy, do mais recente para o mais antigo.

## 2026-11-18, cenário privado instalado sem alterar Guildas (Melekyy / Codex)

- **Entregue:** Ghostshelf Keep 1.5 e seu patch existente copiados para cliente e servidor privados. Origem somente para leitura, confirmada por hashes de quinze arquivos. Somente a cópia privada do patch perdeu a marca light; registros preservados. [ADR-0026](../../03-decisoes/ADR-0026-cenario-ghostshelf-isolado-no-privado.md).
- **Integridade:** 117 plugins em ordem idêntica cliente/servidor, 115 índices anteriores preservados, dois BSA adicionados ao host. Verificador do cliente ampliado por dois nomes sem desligar CRC/tamanho/caminhos. Configurações anteriores guardadas em backup local; sete arquivos privados protegidos iguais.
- **Prova nativa:** processo isolado carregou 117 plugins e 62 BSA; 212 registros conferidos, incluindo 180 overrides e as três células internas. Referências dos baús mantidos presentes. Mundo dos jogadores não carregado; nenhum Skyrim iniciado/controlado.
- **Limites:** testes visuais, travessia de áreas, Papyrus e dois clientes ficam pendentes para o dev. Guildas R30, dados e conjuração independente preservados; nenhuma entrega oficial nem redistribuição dos arquivos do mod.

## 2026-11-18, preparação de cenário no ambiente privado (Melekyy / Codex)

- **Pedido separado:** instalar Ghostshelf Keep 1.5 no Skyrim/SkyMP privado usando os arquivos já presentes no Mod Organizer do Aetherius, sem alterar a origem. Não muda o escopo nem o código das Guildas.
- **Decisão técnica:** copiar o cenário e o patch existente de desativação de renda/bônus/espelho; remover a marca light apenas da cópia privada do patch, após confirmar que contém somente sobrescritas dos masters. Acrescentar os plugins ao fim da ordem e à lista limitada de verificação do cliente, mantendo CRC/tamanho e os índices anteriores.
- **Estado:** cópias, dependências e preparação em conferência; instalação ainda não concluída neste registro. Código das Guildas, dados e controlador separado de conjuração protegidos. Nada de mods, binários ou dados privados será publicado.
- **Próximo:** verificar instalação, ordem cliente/servidor e preservação por hashes; testes no Skyrim pelo próprio dev. Sem certificação antecipada de scripts Papyrus ou multiplayer.

## 2026-11-17, R30 sem gestão de quartos e com portas do Arquimago (Melekyy / Codex)

- **Pedido final:** retirar quartos/baús por aluno e suas abas; o dev esclareceu que o Arquimago mantém trancar/destrancar portas. Preservar baú exclusivo e comunitário e reposicionar o do Arquimago na própria sala.
- **Retirado:** StudentRooms, fontes/timers de nomes, configuração e testes antigos de quartos; comandos de atribuir/liberar, permissão/modelo/aba e vinculação dos baús de alunos. Limpeza de compatibilidade remove tags/nomes antigos sem criar novos controles de acesso. Código histórico guardado localmente.
- **Preservado:** controle geral das 68 passagens e trancas anteriores exclusivo do Arquimago; demais funções, filiação/escolas/insígnias, fabricação/Biblioteca, cronograma, registros, coletivo e sistemas separados. Três referências de portas indisponíveis foram mantidas desativadas. [ADR-0025](../../03-decisoes/ADR-0025-retirada-dos-quartos-e-preservacao-das-portas.md).
- **Baú:** mesma referência física CONT do cargo, reposicionada na lateral dos aposentos pelo SetPosition do SkyMP, com reaplicação de nome/posição no carregamento do cliente. Mesmo estoque, sem criação ou transferência. Coletivo e seus logs preservados.
- **Instalação:** R30 aplicada no servidor privado sem reiniciá-lo ou sobrescrever bundle carregado. Durante a retirada foi necessário retomar o registro do módulo e usar a API Papyrus de posição própria para referências estáticas; o bootstrap privado original foi preservado. Após o esclarecimento, controle global e sua política anterior foram restaurados. Fonte instalado conferido; 18 inventários e cadastro de membros/cargos/escolas preservados.
- **Validação:** 125 testes locais sem navegador aprovados, zero falhas/pulos; inclui autorização de portas, Biblioteca, cargos, escolas e baús. Nenhum novo teste no Skyrim pelo agente; dev fará abertura, navegação, travessia e conferência visual da nova posição. Não declarar esses testes humanos feitos.
- **Limites:** ZIPs anteriores não incluem esta revisão. Não criar nova publicação de código/ZIP por inferência. Integração oficial, adaptadores reais e dois clientes humanos permanecem pendentes.

## 2026-11-17, quartos sem depósito de septins e testes em jogo pelo dev (Melekyy / Codex)

- **Correção de regra:** ao responder ao teste do quarto, o dev determinou que não deve ser possível depositar septins. Aplicado aos dez compartimentos de quartos, sem estender a restrição a outros baús. Itens comuns permitidos; moedas existentes preservadas e retiradas autorizadas mantidas. [ADR-0024](../../03-decisoes/ADR-0024-quartos-sem-deposito-de-septins.md).
- **Instalação:** guarda do servidor recebe o ID real do item em onPutItem. Fonte atualizada e instância ativa corrigida sem reinício; os hooks antigos que omitiam argumentos receberam encaminhamento externo restrito. Estado/inventários e sete arquivos protegidos preservados. Seis testes unitários de quartos aprovados; nenhum novo ensaio nativo/in-game desta regra executado.
- **Relato atual:** dev informou que não conseguia abrir painel nem porta. A observação anterior de Guildas/Casas abertas não encerra esse novo relato. Retirado somente o diagnóstico antigo anexado ao bundle, com jogo fechado, backup, CRC/caminhos e oito arquivos protegidos preservados. Não alterado o renderer funcional nem o controlador de conjuração.
- **Quarto temporário:** cadastro e localização do teste anterior restaurados antes de a nova sessão ficar ativa, sem enviar comandos de interface. Inventário do personagem e todos os conteúdos atuais do móvel preservados; não apagar conteúdos que apareceram após a ativação do compartimento. Nenhum depósito manual foi certificado.
- **Preferência explícita:** após autorizar inicialmente a retomada do mouse/teclado, o dev definiu “OS TESTES EU MESMO FAÇO”. Nenhum novo controle, abertura de menus, simulação de depósito ou lançamento do jogo pelo agente após essa instrução. Testes no Skyrim ficam com o dev.
- **Pendente:** dev confirmar abertura, navegação, portas, acesso por função e persistência de itens comuns. Oficial/dois clientes continuam não homologados. ZIP R29 anterior é histórico e não inclui esta alteração posterior; não tratá-lo como pacote dessa correção.

## 2026-11-17, provas da R29 e pendências de retomada do cliente (Melekyy / Codex)

- **Verificado:** os 19 testes HTML passaram, incluindo modelos reais de permissão para Arquimago, professor, bibliotecário e membro, rótulo/envio de convite por ID, ausência de saída voluntária, escolas, cronograma, gestão, registros e bancada. Nenhuma ação sem poder é liberada por exibir um painel antigo.
- **Pacote local:** R29, 78 arquivos, 108 testes aprovados da própria distribuição, zero falhas/pulos. Extração e hashes conferidos. SHA-256 4f040e06412d7e894473bf8657b1ce4a5a99766dee34bc3468f095669c5cbcc2. Arquivos ativos instalados das dez áreas conferidas correspondem ao fonte local. Nenhum ZIP/código/binário foi enviado ao acervo.
- **Aguardando dev:** retomar controle do Skyrim ou testar manualmente o depósito; para instalar a retirada do diagnóstico antigo do bundle, fechar somente o cliente. Servidor/MO2 podem continuar abertos. Não sobrescrever cliente carregado. Grupo, operação manual do quarto e aceite visual dos quatro perfis continuam pendentes.
- **Preservação:** restauração do quarto temporário, inventários e localização original preparada com comparação exata; nenhum item será apagado para encerrar teste. Não declarar prova manual do quarto sem depósito real.
- **Publicação:** registro anterior confirmado na main em 6cffbf5; a conferência dos nove documentos alterados aprovou frontmatter/links relativos. Validação global ainda encontra 28 documentos antigos de Sobrenatural fora desta alteração. Integração oficial e dois jogadores humanos não homologados.

## 2026-11-17, recuperação R29 e revisão de todos os perfis em andamento (Melekyy / Codex)

- **Pedido ampliado:** recuperar todos os painéis e conferir o sistema dos Magos como Arquimago, professor, bibliotecário e membro comum. Convite deve ser apresentado por ID do jogador.
- **Diagnóstico/correção:** lista de arquivos corrigida não bastou. Propriedade recebida para ator sem modelo interrompia o lote nativo de rede. Guarda restrita ignora essa atualização e deixa criação/estado inicial para CreateActor; índice zero e recriação verificados. Callback antigo de Guildas podia reaplicar foco/modelo: R29 aposenta somente os nomes legados configurados das Guildas na entrada e na atualização do dono. Outros sistemas preservados.
- **Privado:** duas correções do bundle instaladas com o jogo fechado e backup; fallback físico, estado, inventários, addons e controlador de conjuração preservados. Módulo R29 carregado em inicialização fria. Guildas e Casas observados abertos no processo real; cliente confirmou R29. Nenhum dado privado publicado.
- **ID:** rótulos de convite de Guildas/Casas/Guardas e identificação de membros ajustados. A identidade pública desta instalação foi verificada igual à chave persistente; nenhuma migração de identificador nem novo contrato de Classes/Staff presumido.
- **Verificação:** 150 testes do workspace aprovados antes da inclusão da matriz de interface por função. Addon nativo atual aprovou exclusividade de funções, cinco insígnias/proteção/morte, escola só removida pelo Arquimago, cronograma/registros, 104 tomos e cobrança/cancelamento, 68 passagens em ambos os sentidos, 10 quartos com 60 ativações e troca de dono com conteúdo, baú coletivo e baú exclusivo com troca de liderança. Esses ensaios usam bots, sem jogadores humanos simultâneos.
- **Ainda em andamento:** Grupo no cliente, navegação/ações de todos os perfis e depósito/reabertura manual no quarto. Teste privado temporário restaura quarto/local/itens ao fim; não confundir ativação do servidor com depósito real do cliente. Inspeção do mouse/teclado interrompida pelo Esc físico, aguardando retomada explícita.
- **Erro adicional identificado:** diagnóstico temporário antigo de renderização chamava tradutor de referências dinâmicas com IDs estáticos a cada dois segundos. Remoção somente desse diagnóstico preparada/testada, sem alterar renderer ou conjuração; instalação fria ainda pendente. [ADR-0023](../../03-decisoes/ADR-0023-recuperacao-de-entrada-e-callbacks-das-guildas.md).
- **Limite:** recuperação parcial observada não é declaração de tudo resolvido. Oficial, integração real Staff/Classes e dois clientes humanos continuam pendentes.

## 2026-11-17, regressão geral dos painéis após reentrada (Melekyy / Codex)

- **Relato:** nenhum painel abre após nova entrada no Skyrim privado. Os 149/106 testes anteriores não cobriam esse caminho real de inicialização.
- **Diagnóstico:** log do cliente registra Unlisted plugin basename for private file verification antes de erros de sincronização. Lista limitada do helper privado contém 113 plugins; perfil/servidor usam 115. Faltavam SkyGuilds.esp e Vision of Skyrim II.esp. Não desativar a verificação para contornar.
- **Correção preparada/instalada:** alterar somente a lista limitada no bundle efetivamente usado pelo MO2. Compilação e teste isolado confirmam CRC/tamanho de ambos os nomes e recusa de quatro entradas inválidas. Dev fechou o Skyrim; instalação com backup, sem reiniciar servidor e oito arquivos protegidos preservados. Nenhuma alteração em inventários, cargos ou conjuração.
- **Pendente:** dev reabrir o jogo e retestar Guildas, Casas e Grupo; conferir log do processo novo e investigar falhas adicionais se existirem. Não declarar recuperação completa antes dessa verificação.

## 2026-11-17, R28 preparada para SkyMP e escopo consolidado (Melekyy / Codex)

- **Entrega:** bootstrap com verificação antes de registrar/gravar, presets portáteis, exportador de catálogo explícito, preflight estático e ensaios isolados de recibos/estoque coletivo. Pacote local de Guildas com guia, rollback, checklist multiplayer, hashes e testes executados na própria distribuição. Código/ZIP/relatórios privados não enviados ao acervo.
- **Sincronização:** propriedade de painel configurável; leitura nativa por ID em update, selagem reaplicada após streaming, animação de fabricação tolerante à diferença de relógio, serviços aposentados sem permissões antigas. Domínio dos três grupos preservado.
- **Suítes finais:** 149 testes no workspace e 106 executados na própria pasta distribuída, todos aprovados, sem pulos. ZIP local com 78 arquivos e hashes; nenhum código/binário/ZIP no acervo.
- **Validação:** preflight privado com 115 plugins; host real aprovou 151 referências e 104 tomos. Recibos de inventário aprovados em processo isolado no mesmo addon. R28 recebida pelo cliente conectado, sem reinício; estado/inventários e nove arquivos protegidos preservados. Autorização Staff checada quanto ao formato, não como botão oficial conectado.
- **Documentação:** [escopo completo](escopo.md), [instalação](integracao-skymp.md), [matriz](validacao-skymp.md) e guia Staff atualizados. Responsável Melekyy; fase em-teste no privado. Livros autorais continuam guardados e conjuração permanece independente.
- **Validação documental:** os 13 Markdown alterados passaram no formato e links relativos. O verificador global encontra 28 documentos preexistentes de Sobrenatural sem o frontmatter exigido; nenhum deles faz parte desta alteração. Esse problema do acervo permanece separado da aprovação das Guildas.
- **Limites:** SkyMP padrão não fornece os recibos adicionais do journal. Homologação no fork oficial, adaptadores reais, dois clientes humanos, recuperação após queda e conferência dos mods/clientes continuam pendentes. [ADR-0022](../../03-decisoes/ADR-0022-preparacao-skymp-das-guildas.md).

## 2026-11-17, estante coletiva e auditoria do Arquimago (Melekyy / Codex)

- **Pedido:** transformar o Bookcase indicado no Arcanaeum em baú coletivo dos Magos e acrescentar suas movimentações nos Registros do Arquimago.
- **Implementação:** referência existente, estoque nativo único, acesso a todos os membros atuais e revogação imediata na expulsão; proximidade/instância e locks mantidos. Nome Baú Coletivo do Colégio confirmado no cliente carregado, sem criar/limpar/migrar pertences.
- **Registros:** depósitos/retiradas confirmados após os hooks, com nome/ID do jogador, item/variante, quantidade, horário e antes/depois. Recusas e pedidos sem efeito não são registrados como movimentações. Exclusivo da liderança e sem reconstrução retroativa.
- **Evidência:** três testes novos de auditoria e ensaio nativo isolado com três bots, duas transferências e dois registros corretos. Estoque compartilhado, reabertura, outsider e expulso verificados. Instalado no privado sem reinício, preservando estado e inventários; dois clientes humanos pendentes. [ADR-0021](../../03-decisoes/ADR-0021-bau-coletivo-do-colegio.md).

## 2026-11-15, preparação de integração SkyMP e escopo completo iniciados (Melekyy / Codex)

- **Pedido:** preparar o sistema completo de Guildas para integração/sincronização SkyMP antes de consolidar seu escopo no Acervo Aetherius, conforme esclarecimento do dev.
- **Plano em execução:** conferir o contrato do host e cliente, separar dependências do privado, acrescentar entrada/configuração verificável e preparar pacote local de Guildas com guia, reversão e checklist multiplayer. Corrigir referências nativas entre quadros e identificação configurável da propriedade de interface.
- **Limite de publicação:** apenas Markdown no acervo; código, pacote e backups permanecem locais. Nenhuma alteração autorizada de conjuração ou sistemas alheios. Não presumir homologação oficial ou dois clientes reais.
- **Próximo:** concluir implementação e verificações antes de atualizar o escopo final e os quadros.

## 2026-11-15, Baú do Arquimago e depósito pelo cliente verificados (Melekyy / Codex)

- **Identificação esclarecida:** o dev estava diante do Chest dos aposentos do Arquimago, não do compartimento do Quarto 01. A referência foi confirmada pelo cliente e servidor na célula correta.
- **Entregue:** ponto físico com acesso exclusivo ao Arquimago atual para abrir, depositar e retirar. Não depende de reivindicar outro baú pessoal; mantém proximidade e bloqueios de inventário. Antigo líder perde acesso e novo líder recebe pela função, com conteúdo mantido no mesmo móvel.
- **Nome:** Baú do Arquimago confirmado nativamente no cliente. Propriedade de apresentação e conexão na instalação garantem reaplicação em novas conexões/streaming, sem mexer em plugins ou controles.
- **Persistência real:** transferência de um septim pelo cliente chegou ao servidor; o septim permaneceu após fechar/reabrir, e a retirada da mesma unidade foi confirmada. Saldos restaurados ao início do teste. Conteúdo, dados pessoais e backups permanecem locais; não houve reposição arbitrária de itens.
- **Verificado:** 136 testes completos de autorização, quatro rechecagens após a conexão do nome, fonte instalada, configuração persistida e apresentação real. Sem reinício e com pertences/cadastro pessoais preservados.
- **Decisão:** [ADR-0018](../../03-decisoes/ADR-0018-bau-exclusivo-do-arquimago.md). Homologação oficial, troca de liderança entre dois clientes e outros compartimentos reais permanecem pendentes.

## 2026-11-15, membros de teste e abertura dos quartos R26/R27 (Melekyy / Codex)

- **Membros:** tag visual de fictício retirada por pedido do dev. Quatro novos cadastros comuns de teste adicionados no privado, totalizando oito membros; preservada identificação interna `testOnly`, sem criar contas/atores ou dar funções/escolas automaticamente. Cada inclusão tem registro administrativo; dados individuais permanecem locais.
- **Papéis privados:** após testar professor e membro comum, o dev pediu retorno a Arquimago. Operações verificadas sem reinício, com inventário, escola e quarto preservados. Não alteram as regras de exclusividade.
- **Falha de abertura:** autorização do quarto aceitava o dono, mas a cadeia antiga de baús recusava. Inspeção das closures identificou uma instância anterior de Guildas ainda embutida sob outros sistemas. Seus callbacks foram desativados e as guardas antigas passam a não interferir; o cadastro atual decide o acesso. Mantidos hooks dos demais sistemas, pertences e arquivos protegidos.
- **Portas:** reafirmada restrição por proximidade a dono, Arquimago e professores atuais; outros alunos e pessoas externas não abrem quartos atribuídos. Controle geral segue exclusivo do Arquimago.
- **Evidência:** 135 testes completos, incluindo recarga com hook antigo retido e tag órfã; 60 ativações nativas isoladas e movimentos autorizados de septins aprovados. Cliente confirmou R27; o dev confirmou que o baú abriu normalmente. Treze arquivos protegidos e inventários preservados.
- **Investigação ativa:** dev relatou que os itens não ficam no baú. No rastreamento posterior, houve ativações recusadas de estante sem dono, sem pacote de depósito observado. Persistência pelo cliente real ainda não confirmada; não afirmar correção nem repor/apagar itens sem identificar a transferência.
- **Próximo:** reproduzir depósito no compartimento correto com transporte real e verificar fechar/reabrir. Homologação oficial permanece pendente.

## 2026-11-15, troca administrativa de função no privado (Melekyy / Codex)

- **Pedido atendido:** personagem conectado definido como professor da própria escola. Liderança retirada na mesma gravação, respeitando exclusividade. A nomeação anterior daquela escola era um registro fictício de teste e foi substituída com auditoria; nenhum outro jogador real foi removido.
- **Verificado:** permissão de professor/quartos ativa, sem gestão de líder, controle geral de portas ou fabricação. Vínculo de escola, inventário/insígnia, quartos, pertences e demais guildas preservados; estado vivo igual ao disco. Backup local, sem reinício e sem alteração de código ou regras do sistema.
- **Limite:** operação administrativa privada; não representa homologação oficial ou novo aceite visual. Dados de jogadores e backups permanecem locais.

## 2026-11-15, entrega privada R25 e compartimentos existentes (Melekyy / Codex)

- **Entregue:** dez quartos, atribuição por professores/Arquimago e acesso de porta por proximidade. Controle geral das portas exclusivo do Arquimago, conforme confirmação do dev. Troca/liberação é permitida com pertences, que permanecem no móvel para o novo dono.
- **Nova regra de ocupação:** por pedido posterior do dev, permitir troca/liberação mesmo com itens e septins, mantendo o conteúdo no móvel e passando seu acesso ao novo dono. A proposta de exigir esvaziamento foi retirada.
+- **Revisão pedida:** aproveitar móveis existentes nos quartos sem baú. Catálogo final: seis baús, três estantes e um armário. Quatro baús recém-criados estavam vazios e foram desativados, com referências e backup preservados localmente. Nenhum conteúdo original foi apagado ou transferido.
- **Escolas:** ação de retirada exclusiva do Arquimago, auditada, preservando membro/funções; revogação da insígnia pelo journal. Professores não podem retirar vínculo.
- **Visão geral:** contador de bibliotecários no lugar de cargos; Cronograma de aulas no lugar dos nomes dos bibliotecários. Só Arquimago adiciona/remove escola, dia semanal/data e horário de Brasília; membros apenas leem. A aba Cargos e suas regras continuam.
- **Interface:** removida somente a legenda indicada; deduplicação da R24 mantida. Inicialização do estado da animação corrigida para chaves ausentes do runtime; nomes de móveis resolvem os handles nativos em update e são propagados pela porta correspondente.
- **Verificado:** 134 testes completos; ensaio nativo isolado com quatro bots/zero clientes humanos, 60 ativações e depósitos/retiradas autorizadas de septins em dez compartimentos. Guardas contra acesso alheio e troca com conteúdo preservado aprovadas. Um pacote malformado de retirada travou somente o ensaio isolado e não faz parte da certificação.
- **Privado:** R25 recebida pelo cliente, 20 nomes aplicados sem erro na checagem final, estado vivo/disco e treze arquivos protegidos conferidos; inventários, grupos, Casas, escolas e histórico preservados. Sem reinício. Dados de teste reais permanecem locais.
- **Decisão:** [ADR-0016](../../03-decisoes/ADR-0016-quartos-escolas-e-cronograma-dos-magos.md). A conjuração permanece independente e não foi alterada.
- **Documentação:** dez Markdown desta entrega passaram na checagem de frontmatter e links locais. A validação global segue falhando em vinte documentos de Sobrenatural já presentes no remoto, fora desta alteração; não confundir isso com os testes do sistema privado.
- **Próximo:** aceite humano do cronograma, portas/compartimentos e cliques; teste com dois clientes e integração/homologação oficial. Código e backups não publicados no acervo.


## 2026-11-15, cronograma e contagem de bibliotecários solicitados (Melekyy / Codex)

- **Visão geral:** substituir o contador de cargos dos Magos pelo total de bibliotecários nomeados. No espaço que mostrava os nomes dos bibliotecários, apresentar o cronograma de aulas.
- **Autoridade:** só o Arquimago cadastra ou remove aulas. Os demais membros consultam escola, dia e horário. Oferecer dia semanal ou data específica, com horário de Brasília explícito e validação no servidor.
- **Escopo acumulado:** continua a implementação dos dez quartos, baús nominais, controle geral de portas exclusivo do Arquimago e retirada de vínculo de escola exclusiva dele. A legenda indicada na tela de membros já foi retirada, sem alterar as restrições dos cargos.
- **Próximo:** concluir testes nativos dos baús/quartos e de permissões, instalar a entrega conjunta com backup e atualizar o guia da Staff.

## 2026-11-15, dez quartos de alunos e retirada da escola iniciados (Melekyy / Codex)

- **Pedido:** cadastrar os dez quartos com cama da instância atual, atribuir um aluno e um baú nominal por quarto, com acesso de porta por proximidade ao dono e à administração. Professores e líder atribuem os quartos.
- **Esclarecimento confirmado:** apenas Arquimago conserva o controle geral remoto das portas. Professores abrem as portas dos quartos por proximidade. A referência à direção é tratada como liderança atual.
- **Levantamento:** personagem localizado no Hall of Attainment; dez camas reais nos registros vencedores, seis no andar de entrada e quatro no inferior. Conferir porta/compartimento de cada quarto; excluir estoques de comerciantes e ambientes sem cama. Não há baús físicos já vinculados no cadastro privado consultado.
- **Escola:** acrescentar retirada de vínculo exclusiva do Arquimago, com auditoria e revogação da insígnia pelo diário de inventário. Professores continuam podendo definir vínculo, sem poder retirá-lo.
- **Apresentação:** retirar somente a legenda de função exclusiva indicada na imagem, preservando todas as regras de cargos. Alteração já enviada à interface privada.
- **Próximo:** concluir catálogo dos quartos, implementar persistência/permissões, testar e instalar com backup. Código e dados privados permanecem locais.

## 2026-11-15, R24 aplicada e reconstrução repetida corrigida (Melekyy / Codex)

- **Entrega:** componente de opções dentro do painel usado por todos os seletores, com contexto, opções e escolhas preservados; líder dos Magos recebe marcador e fabricação sem função de bibliotecário. Custo, duração e proximidade mantidos; membro comum/professor sem autoridade continua recusado.
- **Causa observada dos cliques:** uma repetição idêntica do estado de fabricação forçava reconstrução do conteúdo. Foram medidas 31 reconstruções em 1,2 s sem mudança desse estado; pressionar/soltar o mouse perdia o controle original. A interface agora ignora repetições idênticas e não força invalidação desnecessária. Na inspeção posterior houve zero reconstruções no mesmo intervalo.
- **Verificação:** 119 testes completos e 16 rechecagens de interface aprovados, incluindo repetição de 60 callbacks durante clique. Teste nativo isolado confirmou fabricação pelo líder com receita real e cancelamento por revogação. Cliente confirmou R24 e marcador no local. Lista aberta por chamada direta permaneceu estável; evento de mouse sintetizado não equivale ao clique físico. Imagem posterior do dev mostra a aba Membros, sem comprovar todos os seletores.
- **Recuperação:** o rastreamento temporário interferiu no encadeamento durante uma atualização e foi removido. O módulo de Guildas foi recarregado sem reiniciar jogo/servidor; estado, inventários, Casas e grupos preservados. A rota voltou a aceitar abertura e troca de aba. Nenhum controle de conjuração alterado.

## 2026-11-15, seletores estáveis e bancada para o líder iniciados (Melekyy / Codex)

- **Relato:** listas de opções não permanecem abertas ao escolher valores; o dev esclareceu que afeta todos os seletores, não apenas aluno/escola.
- **Direção:** corrigir o componente comum para apresentar opções dentro da interface, preservar a lista durante atualizações e impedir escolhas que perderam validade. Testar clique, atualização, teclado e permissão.
- **Pedido adicional:** o líder dos Magos deve receber o blip e acesso à fabricação sem acumular a função de bibliotecário. Membros comuns e professores sem essa autoridade continuam sem bancada.
- **Próximo:** implementar, testar, aplicar atualização suportada e conferir a fonte no cliente privado. Responsável Melekyy, implantação oficial pendente.

## 2026-11-15, conjuração retirada da estrutura atual de Guildas (Melekyy / Codex)

- **Constatação:** os painéis instalados já não carregavam o controlador. O complemento candidato e quatro ferramentas antigas de arquivamento/desativação conjunta ainda estavam junto do projeto; a separação física foi solicitada pelo dev.
- **Entrega:** pasta candidata, dois ZIPs candidatos e backup privado anterior movidos ao projeto independente `SIstema de Summons`. As quatro ferramentas e o ZIP anterior de Guildas foram preservados no histórico desse projeto. Os 21 arquivos movidos passaram na conferência SHA-256. Diagnósticos e uma pré-condição antigos do operador de livros que consultavam conjuração foram retirados.
- **Pacote:** nova distribuição local de Guildas gerada e inspecionada: 151 entradas, nenhum script/controlador de conjuração nem ferramenta conjunta removida. Os backups conjuntos antigos em `archive` continuam intactos e excluídos da distribuição. Nomes da escola de Conjuração e tomos seguem como conteúdo legítimo do painel.
- **Privado:** inspeção em leitura de 31 dependências dos módulos de Guildas retidos no runtime, junto do entrypoint instalado, confirmou ausência de controlador de conjuração. A instância ativa pertence ao SkySync e continua habilitada em V5, com duas criaturas registradas no momento da consulta. Fontes ativos dos painéis R23 e 14 arquivos protegidos da implantação anterior conferidos por hash. Nenhum reinício, reinstalação, mudança de configuração ou bundle ativo.
- **Testes e limites:** 13 testes do candidato aprovados na nova pasta. A suíte de 117 testes de Guildas é da entrega R23 anterior; não foi repetida para a movimentação de arquivos. Esta inspeção não é novo teste visual de acompanhamento nem homologação oficial. Só documentação publicada; código, ZIPs e dados privados continuam locais. Aplica a separação prevista no [ADR-0004](../../03-decisoes/ADR-0004-adaptacao-isolada-da-conjuracao.md).
- **Publicação:** entrega enviada à `main` em `40a940d`, com nove documentos alterados conferidos quanto a cabeçalhos e links; atualizações concorrentes de Sobrenatural preservadas no rebase. A execução [Validar acervo](https://github.com/pablo-rodrigues1/AETHERIUS-ACERVO-DE-INFORMACOES/actions/runs/43762867970) falhou nas 17 ausências de frontmatter de Sobrenatural, fora desta alteração. Essa falha global não impediu a separação local nem a conferência do privado.

## 2026-11-15, separação dos arquivos de conjuração iniciada (Melekyy / Codex)

- **Pedido:** conferir se os scripts de conjuração estão juntos aos painéis de Guildas e retirá-los desse projeto.
- **Constatação:** o runtime dos painéis já não instala conjuração; o controlador privado está no SkySync. Contudo, a pasta candidata de adaptação e quatro ferramentas antigas de arquivamento/desativação conjunta ainda estavam no projeto de Guildas.
- **Direção:** mover o complemento para o projeto local separado de Summons, preservar os arquivos e pacotes antigos com hashes e retirar as ferramentas conjuntas da distribuição de Guildas. Backups históricos não são módulos ativos. Não desativar nem reinstalar conjuração no privado.
- **Próximo:** conferir dependências carregadas, testar o complemento no destino e inspecionar o novo pacote de Guildas. Melekyy continua responsável; homologação oficial pendente.

## 2026-11-15, R23 instalada: professor/bibliotecário sem cargos extras (Melekyy / Codex)

- **Entrega:** especialistas dos Magos não acumulam cargos personalizados, liderança ou outra função; professor ocupa somente um posto de escola. O vínculo pessoal de escola permanece. Regra nos dois sentidos das ações, na validação persistente e nos seletores da interface. [ADR-0013](../../03-decisoes/ADR-0013-exclusividade-dos-cargos-dos-magos.md).
- **Migração:** projeção validada e backup antes de mudar dados. Três cargos personalizados foram retirados de dois integrantes fictícios pela ação normal do líder, cada retirada com nomes, IDs, cargo e autor nos Registros privados. Funções, escolas, liderança, inventários, portas e demais dados preservados. Nenhuma troca automática de escola ou insígnia; nenhum dado de jogador publicado.
- **Verificação:** 117 testes completos aprovados; teste nativo isolado com quatro bots e nenhum cliente real confirmou recusas pelo transporte, persistência, escola preservada e inventários intactos. A prévia usa dados fictícios. Atualização aplicada sem reiniciar jogo/servidor nem sobrescrever bundles ativos; estado vivo corresponde ao disco e cliente conectado confirmou R23.
- **Limites e próximo:** aceite visual humano e homologação oficial pendentes. Antes de instalar em outra base, conferir acúmulos antigos: a carga rejeita dados incompatíveis e não apaga cargos silenciosamente. Outras guildas, receita, insígnias, conjuração e portas não receberam novas regras.
- **Publicação:** entrega enviada à `main` em `1bdbfce`, após rebase que preservou a atualização concorrente de Sobrenatural. Nove documentos de Guildas alterados passaram na conferência de cabeçalhos e links. A execução [Validar acervo](https://github.com/pablo-rodrigues1/AETHERIUS-ACERVO-DE-INFORMACOES/actions/runs/43761697161) falhou por 17 documentos sem frontmatter, todos de Sobrenatural; os três novos vieram da atualização concorrente. Falha documental global registrada, sem alterar o outro sistema ou impedir a implantação privada conferida.

## 2026-11-15, exclusividade dos cargos de professor e bibliotecário iniciada (Melekyy / Codex)

- **Pedido:** quem for professor ou bibliotecário dos Magos não pode ter outros cargos; preservar apenas o vínculo com a escola.
- **Implementação prevista:** recusar cargos personalizados e liderança acumulados com essas funções, impedir professor em várias escolas e manter a incompatibilidade entre professor e bibliotecário. Validar ações, estado persistido e candidatos da interface.
- **Migração privada:** preservar função e escola existentes; retirar somente cargos personalizados incompatíveis com registros detalhados. Não renomear cargos para tratá-los como escola, alterar inventário ou conceder outra escola automaticamente.
- **Próximo:** testes dos dois sentidos de nomeação, persistência e interface, atualização suportada e verificação privada. Responsável Melekyy, sistema em desenvolvimento; homologação oficial pendente.

## 2026-11-15, R22 instalada: portas do Colégio e funções incompatíveis (Melekyy / Codex)

- **Entrega:** aba Portas do Colégio exclusiva do líder/professores, com busca, áreas, estado e trancar/destrancar individualmente ou em lote. Trancas valem para entrada e saída, inclusive líder, professor e NPC. Sessão, nonce, revisão e autoridade revalidados no servidor; política e registros detalhados persistidos juntos. [ADR-0012](../../03-decisoes/ADR-0012-portas-do-colegio-e-funcoes-incompativeis.md).
- **Catálogo final:** 68 passagens/86 referências, incluindo 16 pares de teleporte e quatro grupos de portão. Foram excluídos 12 candidatos sem função de passagem do Colégio, como vitrines, algemas e porta do estábulo da cidade. O levantamento de limite encontrou ainda o portão principal da ponte fora da localização College modificada pelo mod da cidade; foi incluído explicitamente. Três referências originalmente desativadas permanecem assim.
- **Funções e liderança:** nomeações incompatíveis recusadas nos dois sentidos e filtradas na interface; revogar a função anterior antes de nomear a outra. Um acúmulo de teste foi corrigido preservando professor e revogando bibliotecário com registro. A pedido do dev, a ponte administrativa autorizada nomeou o personagem de teste Arquimago, preservando escola e inventário; não concedeu as outras funções automaticamente.
- **Testes:** 112 testes completos aprovados; após ajuste final do catálogo/reconfiguração, seis testes direcionados aprovados. O servidor nativo isolado, sem clientes reais, validou o catálogo e recusou 166 pacotes de ativação das 83 referências ativas; ao destrancar, a travessia Salão dos Elementos–Arcanaeum funcionou nos dois sentidos. As três referências desativadas não foram criadas/habilitadas.
- **Implantação:** atualização suportada sem reiniciar jogo/servidor, com backup. Cliente conectado confirmou versão R22 e as APIs nativas necessárias; zero referências pendentes. Inventários, escola, catálogo/receita/traduções, insígnias, núcleo, Morte, conjuração e bundles ativos preservados. A instalação não trancou nenhuma passagem nem produziu ações de teste no histórico real.
- **Limites:** prévia com dados fictícios e prova nativa isolada são distintas de travessia humana. Conferência no jogo, teste com dois clientes reais e homologação oficial continuam pendentes. O controle vale para as portas catalogadas, sem barreira global de teleporte e sem reverter transições já aceitas. Documentação publicada separadamente; código, relatórios privados e assets permanecem locais.
- **Próximo:** confirmar uso da aba e travessia no cliente privado, depois adaptar catálogo/transporte ao oficial. Receita, baús, insígnias, Classes e ritual Companion não mudaram.
- **Publicação:** entrega enviada à `main` em `5439813`, somente Markdown. Os dez documentos alterados passaram na conferência de cabeçalhos e links. A execução [Validar acervo](https://github.com/pablo-rodrigues1/AETHERIUS-ACERVO-DE-INFORMACOES/actions/runs/43760665924) falhou nas 14 ausências de frontmatter preexistentes de Sobrenatural; falha global conferida, fora dos documentos de Guildas. Não afeta a instalação privada verificada e não foi corrigida alterando o outro sistema.

## 2026-11-15, portas do Colégio e funções incompatíveis iniciadas (Melekyy / Codex)

- **Pedido:** aba exclusiva de líder/professores para trancar/destrancar todas as portas do Colégio ou uma passagem por vez, bloqueando ambos os lados para todos até a liberação. Levantar as referências reais da instalação e os dois lados das portas com teleporte.
- **Regra adicional:** professor e bibliotecário não podem acumular funções. Nomeações incompatíveis exigem revogação prévia; filtrar candidatos na interface e validar no servidor. Na migração privada, preservar professor e revogar bibliotecário quando houver conflito, com registro.
- **Privado:** a pedido do dev, o personagem de teste foi nomeado líder pela ponte administrativa temporariamente autorizada; inventário e escola preservados. Um acúmulo incompatível de função de teste foi corrigido e registrado, preservando professor.
- **Levantamento inicial:** registros vencedores da base instalada incluem o Colégio de JK. Foram localizadas 97 referências de tipo DOOR em 13 células e conexões, além de dois ativadores relacionados. Vitrines e algemas usam esse tipo internamente, mas não são passagens; classificá-las separadamente. Nenhuma referência irresolúvel encontrada no escopo do Colégio.
- **Direção:** controle persistente por passagem, par de referências fechado em conjunto, validação autoritativa antes da ativação, estado sincronizado e registros detalhados. Não conceder passagem automática ao operador enquanto a porta estiver trancada. Preservar portas desativadas por missões e não trancar prédios vizinhos fora do Colégio.
- **Próximo:** concluir catálogo, implementar, testar comandos e travessia nativa nos dois sentidos e aplicar por atualização suportada. Homologação oficial pendente.

## 2026-11-15, insígnias R21 ativadas no privado (Melekyy / Codex)

- **Autorização e instalação:** o dev confirmou que fechou o Skyrim privado e o MO2. Fechamento verificado; foi criado backup dos dados do mundo e dos arquivos afetados. Somente o servidor privado foi parado e reiniciado.
- **Plugin:** cópia ESP completa do Elemental Vision II ativada no cliente e servidor, anexada ao fim da ordem sem deslocar os índices anteriores. Verificação de 115 plugins em paridade de ordem e hashes aprovada. Original do mod, bundles do cliente, núcleo nativo, fonte de Morte e configuração de conjuração preservados. Manifesto de terreno atualizado sem alterar a geografia.
- **Entrega real:** o runtime privado carregou os cinco registros configurados e entregou exatamente uma insígnia correspondente à escola já cadastrada do personagem de teste, mesmo com ele desconectado. Presença em unidade única conferida também no inventário persistido em disco; todos os itens anteriores preservados. O personagem permanece membro comum, sem liderança, professor ou bibliotecário.
- **Proteção e acesso:** adaptador de morte conectado no runtime; descarte da insígnia recusado pelo hook do servidor. Bancada continua exclusiva dos bibliotecários, e líder/professores usam os seletores de membro/escola na aba Professores.
- **Evidências:** relatórios locais de instalação, reinício e verificação no runtime, além dos 104 testes e teste nativo isolado de entrega, transferência, morte/recuperação e expulsão. Servidor iniciou com os demais sistemas e voltou a responder ao canal privado.
- **Limites:** esta entrega confirma os dados e a proteção no servidor real; o cliente estava fechado na verificação de entrega. Inspeção visual da insígnia, teste de morte no cliente real e homologação oficial permanecem pendentes. Não houve morte forçada do personagem do dev.
- **Próximo:** reabrir o cliente para conferir visualmente a insígnia e seguir a homologação oficial. Nenhum bloqueio de instalação privada permanece.
- **Publicação:** entrega enviada à `main` em `163c385`. A execução [Validar acervo](https://github.com/pablo-rodrigues1/AETHERIUS-ACERVO-DE-INFORMACOES/actions/runs/43755302609) continua falhando por ausência de frontmatter em documentos preexistentes de Sobrenatural; conferidos 14 arquivos, todos fora de Guildas. Essa falha documental global não impediu a instalação e a verificação privadas descritas acima.

## 2026-11-15, painel R21 aplicado e ativação das insígnias pendente (Melekyy / Codex)

- **Aplicado sem reinício:** bloqueio da bancada para membros sem a função de bibliotecário e seletores de membro/escola em Professores. Nomeação de bibliotecários disponível ao líder nessa aba. O cliente conectado confirmou a fonte R21; a tentativa de abrir a bancada pelo personagem comum foi recusada pelo servidor e a interação deixou de constar no seu modelo.
- **Preservado:** escola, participação como membro comum, inventário, histórico e demais dados. Nenhuma insígnia genérica foi concedida ou permissão de liderança restabelecida.
- **Pendente:** ativar a cópia privada do plugin Elemental Vision II e a proteção de inventário/morte preparada para ele. Jogo e MO2 continuam abertos; solicitou-se ao dev fechá-los. A instalação sincronizada está pronta, mas ainda não foi executada. O servidor continua com o plugin desativado nesta etapa.
- **Evidências locais:** relatório de atualização da interface, status R21 do cliente, 104 testes aprovados e teste nativo isolado dos cinco itens, com morte/recuperação e bloqueios. A conferência visual da tela de escola usa dados fictícios; não equivale a teste em cliente real.
- **Próximo:** após o fechamento, ativar o plugin no cliente/servidor, reiniciar o privado e verificar a entrega correspondente à escola já cadastrada. Homologação oficial continua pendente.

## 2026-11-15, R21 preparada para ativação privada (Melekyy / Codex)

- **Decisão:** [ADR-0011](../../03-decisoes/ADR-0011-bancada-e-insignias-das-escolas.md). Membro comum, professor e líder sem função de bibliotecário não recebem interação nem abrem a bancada. Nomeação de bibliotecários e seleção de membro/escola ficam acessíveis em Professores conforme as respectivas permissões.
- **Insígnias:** entrega e reparo idempotentes com journal; bloqueios nativos de descarte, depósito e roubo e proteção no journal contra transferência/alteração. Adaptador do SkyGuilds mantém a insígnia no inventário durante espírito/recuperação e fora da penalidade dos demais equipamentos. Expulsão continua revogando o item.
- **Verificado:** 104 testes automatizados aprovados. Servidor nativo isolado carregou os cinco itens reais do Elemental Vision II, confirmou entrega sem duplicatas, acesso da bancada, bloqueios, permanência na morte/recuperação e revogação por expulsão. Esses testes não tiveram clientes reais de jogo. A prévia de seleção de escola usa dados fictícios.
- **Preparado:** instalador privado com backup, cópia ESP completa em override isolado, plugin ao fim da ordem nos dois lados, atualização do manifesto de terreno sem alterar geografia e verificação de cliente/servidor. Os arquivos originais do mod e os bundles devem permanecer intactos.
- **Aguardando:** o dev fechar Skyrim privado e MO2 para aplicar a ativação e reiniciar somente o servidor privado. A implantação desta revisão ainda não foi realizada nesta entrada.
- **Próximo:** instalar, verificar o recebimento da insígnia do personagem de teste e conferir as permissões após a reabertura. Homologação oficial pendente.

## 2026-11-15, bancada restrita e insígnias de escola iniciadas (Melekyy / Codex)

- **Pedido confirmado:** membros comuns não abrem a bancada nem para consulta. Somente bibliotecários têm acesso. Líderes e professores selecionam um membro e sua escola no painel; o membro recebe automaticamente a insígnia correspondente do Elemental Vision no inventário.
- **Proteção solicitada:** impedir descarte, armazenamento, troca, doação, roubo e perda na morte. Preservar o vínculo de uma escola por membro e a remoção da insígnia ao encerrar a filiação por expulsão.
- **Diagnóstico:** Elemental Vision II está disponível na instalação privada, mas o plugin está desativado no perfil e ausente da ordem de carregamento do servidor; configuração de medalhões está vazia. Os IDs de cinco variantes Mondstadt já foram catalogados localmente e serão conferidos nos registros reais.
- **Direção:** bloqueio da bancada no servidor e no cliente, seleção de membro/escola no painel e proteção autoritativa de inventário integrada à morte. Ativação do plugin deve preservar índices existentes, arquivos protegidos e dados, com backup e instalação segura fora da sessão ativa do jogo.
- **Próximo:** implementar e testar as proteções, preparar a instalação e coordenar a reabertura do cliente privado. Integração e homologação oficial continuam pendentes.

## 2026-11-15, regra do membro comum e teste privado (Melekyy / Codex)

- **Regra confirmada:** o membro não sai voluntariamente; só o líder pode removê-lo. Não criar botão nem ação de saída voluntária. A proposta de acrescentar essa ação foi cancelada pelo dev antes de ser implementada.
- **Registros:** remoção pelo líder já registra autor, membro removido, nomes e IDs de perfil. Preservar esse histórico após a remoção. Uma tentativa de saída recusada não cria uma remoção fictícia.
- **Privado:** a pedido do dev, o personagem de teste passou de líder/bibliotecário para membro comum. Usadas as operações de revogação existentes, com registro e backup; inventário, escola e outras guildas preservados, sem reinício. Os outros ocupantes mantêm suas funções. Nenhum jogador real foi removido da guilda nesta verificação.
- **Verificado:** 11 testes direcionados de auditoria e política de membros aprovados. Incluem as três guildas, tentativa de saída e remoção do próprio perfil, rejeição pelo evento do cliente e remoção autorizada com registro. As fontes de regras e auditoria instaladas no privado têm os mesmos hashes das verificadas.
- **Biblioteca:** a fabricação continua exclusiva dos bibliotecários. O dev relatou que o membro comum não abre o craft; solicitada a distinção entre permitir consulta sem produção e restringir a bancada inteira. Nenhuma ampliação de acesso foi aplicada enquanto essa preferência não foi esclarecida.
- **Próximo:** esclarecer o acesso de consulta à Biblioteca; integração e homologação oficial continuam pendentes.

## 2026-11-15, registros detalhados R20 instalados (Melekyy / Codex)

- **Feito:** a liderança vê nomes e IDs de perfil de quem realizou a ação e dos envolvidos, com cargo, função, escola, substituição ou revogação. Mudanças de mensagens mostram antes/depois; exclusão de cargos identifica os membros afetados; expulsão informa vínculos removidos.
- **Persistência:** nomes e detalhes são capturados pelo servidor junto da alteração; renomear cargos ou remover membros não altera os registros anteriores. Nomes de autor enviados pelo navegador são ignorados. Falha ao salvar reverte alteração e registro. Pedidos sem mudança efetiva são identificados.
- **Histórico anterior:** preservado sem reconstrução de dados ausentes. A interface identifica registros antigos e mantém seus IDs e datas. Limites e permissão da aba foram preservados.
- **Decisão:** [ADR-0010](../../03-decisoes/ADR-0010-registros-detalhados-de-guildas.md).
- **Verificado:** 98 testes automatizados aprovados, incluindo persistência, permissões, nomes confiáveis, substituições, revogações, cargos, escolas e mensagens. Prévia com dados fictícios confirma texto literal seguro, quebra de linhas e restrição da tela; captura de interface em 1353 × 906 e conferência também em largura de 520.
- **Privado:** aplicado com backup, sem reinício. Estado e registros existentes, inventários, catálogo, tradução, núcleo nativo, conjuração e bundles do cliente preservados. A captura instalada foi exercitada em cópia desconectada do estado; nenhum evento de teste foi inserido no histórico real. O cliente conectado confirmou a fonte R20 pronta, com inventário desbloqueado.
- **Publicação:** entrega enviada à `main` em `ca722e7`. A execução [Validar acervo](https://github.com/pablo-rodrigues1/AETHERIUS-ACERVO-DE-INFORMACOES/actions/runs/43744662890) falhou por ausência de frontmatter em 11 documentos preexistentes de Sobrenatural; a conferência dos arquivos versionados confirmou que todas essas falhas estão fora de Guildas. Os arquivos daquele sistema não foram alterados nesta sessão.
- **Limites:** não houve inspeção visual desta revisão no Skyrim nem homologação oficial. A conferência de interface foi uma prévia automatizada. O envio documental foi publicado; a validação global continua dependendo da correção dos documentos de Sobrenatural.
- **Próximo:** conferir novas ações na aba Registros no jogo e seguir as integrações oficiais pendentes.

## 2026-11-15, registros detalhados iniciados (Melekyy / Codex)

- **Pedido:** continuidade de Guildas por Melekyy, em desenvolvimento, para detalhar quem alterou o quê e quem foi afetado: nomes, IDs de perfil, cargos e funções.
- **Direção:** gravar os dados no momento da ação pelo servidor, preservando nomes e cargos mesmo após remoção ou renomeação. Incluir diferenças antes/depois quando aplicáveis; manter a aba restrita à liderança.
- **Histórico anterior:** não reconstruir nomes de funções nem alterações que os registros antigos não armazenaram; indicar essa limitação.
- **Próximo:** implementar, testar persistência e autorização e atualizar o privado sem reinício. A homologação oficial continua pendente.

## 2026-11-15, Biblioteca R19 instalada no privado (Melekyy / Codex)

- **Feito:** vários bibliotecários com nomeação e revogação individuais pelo líder; bibliotecário anterior preservado. Somente os nomeados fabricam. Três integrantes fictícios identificados foram adicionados exclusivamente ao painel privado, sem contas ou personagens no mundo.
- **Fabricação:** lote de três segundos, livro animado, progresso e cancelamento na interface; animação nativa de leitura solicitada em atualização segura e distribuída ao dono e aos vizinhos. Sem chamadas de bloqueio de controles ou câmera.
- **Servidor:** revalida sessão, identidade, acesso, bancada e materiais; só entrega e cobra na conclusão pelo diário de inventário. Fechar, cancelar, afastar, desconectar ou perder acesso interrompe sem consumo. Receita preservada: cinco papéis e uma pedra da alma por tomo.
- **Apresentação:** título de classe separado do retorno. Os 104 tomos autorizados receberam nomes e descrições em português; busca pelo português e pelo original, sem alterar os itens nativos, a política de classes ou as magias. Textos com valores ausentes não receberam números inventados.
- **Decisão:** [ADR-0009](../../03-decisoes/ADR-0009-bibliotecarios-fabricacao-e-traducao.md).
- **Verificado:** 88 testes automatizados aprovados, incluindo interface e ausência de sobreposição em três larguras. Servidor nativo isolado com diário real confirmou fabricação simultânea, débito único, cancelamento por revogação e cobertura dos 104 tomos. Esse teste não teve clientes de jogo.
- **Privado:** atualização sem reinício, com backup. Inventários, pertences, grupos, catálogo autorizado, núcleo nativo, conjuração e bundles do cliente preservados. Fonte R19 recebida e reportada pronta pelo cliente conectado.
- **Limites:** usuário interrompeu a inspeção visual com Esc. Animação e disposição final no jogo, visão por outro jogador e homologação oficial permanecem sem confirmação nesta etapa.
- **Publicação:** atualização enviada à `main` em `9081546`. A execução [Validar acervo](https://github.com/pablo-rodrigues1/AETHERIUS-ACERVO-DE-INFORMACOES/actions/runs/43730031323) falhou por ausência de frontmatter em oito documentos preexistentes de Sobrenatural. A conferência dos arquivos versionados confirmou que os documentos de Guildas têm o cabeçalho; não foram alterados os documentos do outro sistema nesta sessão.
- **Próximo:** conferir a fabricação e as nomeações no cliente real; seguir integração oficial. Envio publicado; validação global do acervo depende da correção documental de Sobrenatural.

## 2026-11-15, fabricação animada e vários bibliotecários iniciados (Melekyy / Codex)

- **Pedido:** continuidade autorizada de Guildas: animação ao fabricar tomos, três membros fictícios no painel privado, vários bibliotecários e correção do título de classe próximo ao retorno.
- **Regra aprovada:** somente bibliotecários nomeados podem fabricar; liderança continua exclusiva na nomeação e revogação. Preservar o bibliotecário atual na migração.
- **Direção técnica:** fabricação com conclusão validada pelo servidor, cancelamento sem consumo antes da entrega e animação executada em atualização segura do cliente. Identificar os membros fictícios como teste e restringir sua criação ao privado.
- **Próximo:** conferir animações disponíveis no jogo, implementar, testar permissões e entrega e aplicar sem reinício quando suportado.
- **Bloqueios:** nenhum nesta etapa; homologação oficial permanece pendente.

## 2026-11-15, refinamento visual R18 entregue (Melekyy / Codex)

- **Feito:** Magos, Alquimistas e Companions receberam molduras, brasões, marca d'água, cartões de informação e liderança, abas, botões e cartões de classes refinados. Azul, verde musgo e marrom escuro preservados, com referência nos painéis locais de Grupo e Reis.
- **Decisão:** [ADR-0008](../../03-decisoes/ADR-0008-visual-dos-paineis-de-guildas.md). Biblioteca por classe, permissões, receitas, inventários e catálogo preservados.
- **Verificado:** sete testes de interface aprovados, incluindo estabilidade das abas e retirada do estilo antigo durante a atualização. Oito capturas de prévia por revisão, com dados fictícios, cobrindo as três guildas, membros, professores, Biblioteca e janelas de 1366 × 768 e 390 × 844. Cores conferidas pelos valores computados.
- **Privado:** atualização sem reinício, com backup, preservação dos arquivos protegidos e recepção da R18 no cliente. Painel dos Magos e navegação entre telas observados diretamente no Skyrim; Alquimistas e Companions conferidos nas prévias. Corrigidos a sobreposição do estilo antigo, o aviso de versão da apresentação e a barra de rolagem do cliente.
- **Limites:** observação visual local não equivale a aceite explícito do usuário nem homologação oficial; a observação no jogo cobriu os Magos.
- **Impacto:** alteração de apresentação, sem contrato novo para Core, Grupo, Reis ou ritual de coleta.
- **Próximo:** colher o aceite visual do dev e seguir as integrações oficiais pendentes.
- **Bloqueios:** nenhum de publicação.

## 2026-11-15, refinamento visual das três guildas iniciado (Melekyy / Codex)

- **Pedido:** continuidade do desenvolvimento de Guildas: estilizar Magos, Alquimistas e Companions usando os painéis locais de Grupo e Reis como referência e conservando suas cores.
- **Direção:** fundo escuro, molduras e brasões, cartões e títulos mais legíveis; azul, verde musgo e marrom escuro mantidos.
- **Escopo:** apresentação e organização visual; preservar navegação por classe da Biblioteca, funções, permissões, receitas e dados.
- **Próximo:** comparar as três guildas em prévias renderizadas, verificar a navegação e aplicar a atualização privada sem reinício.
- **Bloqueios:** nenhum nesta etapa; aceitação visual em jogo e integração oficial continuam pendentes.

## 2026-11-15, catálogo primeiro por classe entregue (Melekyy / Codex)

- **Feito:** a Biblioteca R17 abre com as classes e suas quantidades de tomos. Ao escolher uma classe, mostra apenas os tomos associados; Todas as classes retorna ao início. Categorias e efeitos permanecem nos tomos.
- **Decisão:** [ADR-0007](../../03-decisoes/ADR-0007-biblioteca-primeiro-a-classe.md), complementando a apresentação já autorizada, sem mudar regras de magia.
- **Verificado:** 22 testes direcionados aprovados; a persistência da escolha na sessão foi acrescentada e os 11 testes de integração correspondentes passaram. Filtragem antes da paginação verificada com uma classe de 35 tomos e contagem sem duplicação por categoria.
- **Privado:** R17 aplicada sem reinício, com backup e arquivos protegidos preservados. Runtime confirma 12 classes e 104 tomos autorizados; o cliente conectado informou renderização da R17. A navegação visual pelo jogador e a homologação oficial ainda não foram confirmadas.
- **Impacto:** receita, inventários, permissões, efeitos e conjuração preservados. Nenhum contrato do Core ou do ritual de coleta foi alterado.
- **Próximo:** conferir a navegação no jogo e seguir a integração oficial.
- **Bloqueios:** nenhum de publicação; homologação oficial segue pendente.

## 2026-11-15, ajuste da ordem do catálogo iniciado (Melekyy / Codex)

- **Pedido:** continuidade da alteração de Biblioteca já autorizada: apresentar primeiro as classes e, dentro da classe escolhida, seus tomos.
- **Decisão de apresentação:** separar escolha de classe e lista de tomos, com retorno à lista de classes; preservar categorias, efeitos, receita e autorização.
- **Próximo:** implementar a navegação e validar filtragem e atualização privada sem reinício.
- **Bloqueios:** nenhum nesta etapa; homologação oficial continua pendente.

## 2026-11-15, Biblioteca e indicações de magias entregues (Melekyy / Codex)

- **Feito:** aba Biblioteca antiga retirada da interface; aba da produção renomeada para Biblioteca. Cada tomo mostra classes e categorias autorizadas e a descrição do efeito base, quando disponível. A liderança conserva a nomeação do bibliotecário na Biblioteca.
- **Decisão:** [ADR-0006](../../03-decisoes/ADR-0006-biblioteca-e-indicacoes-de-magias.md), usando a política vigente e os registros dos plugins; sem alterar o aprendizado, a receita ou os feitiços.
- **Verificado:** 12 testes de interface, catálogo e apresentação aprovados. Atualização privada R16 aplicada sem reiniciar e com backup; 104 tomos autorizados, todos com associação de classe, e 103 com descrição de efeito disponível. Dados, catálogo de autorização e arquivos protegidos preservados.
- **Limite:** o personagem de teste não estava conectado ao privado durante a aplicação; a nova fonte de interface está registrada para a próxima conexão. A confirmação visual em jogo e a homologação oficial continuam pendentes.
- **Impacto:** nenhum ajuste de contrato no Core ou no ritual de coleta; a busca da Biblioteca também reconhece classe e categoria. A conjuração reativada permanece separada.
- **Próximo:** confirmar a apresentação no jogo e seguir as integrações oficiais de Staff e Classes.
- **Bloqueios:** nenhum de publicação; integração oficial segue pendente.

## 2026-11-15, início do ajuste da Biblioteca (Melekyy / Codex)

- **Autorização:** o dev confirmou a continuidade de Guildas, já sob responsabilidade de Melekyy e em desenvolvimento, para esta alteração de interface.
- **Escopo aprovado:** retirar a Biblioteca antiga; chamar a aba de produção de tomos de Biblioteca, mantendo o funcionamento; indicar classes, categorias e efeitos das magias.
- **Fonte:** a associação de classe/categoria vem da política autorizada existente; efeitos serão consultados nos registros dos plugins carregados. A planilha não amplia o catálogo nem redefine o feitiço.
- **Próximo:** implementar e verificar a apresentação no privado, preservar permissões e publicar o resultado.
- **Bloqueios:** nenhum nesta etapa; integração e homologação oficiais continuam pendentes.

## 2026-11-15, impacto da conjuração (Melekyy / Codex)

- **Feito:** [conjuração](../gameplay-core/conjuracao.md) reativada no privado, com acompanhamento e duas criaturas confirmados pelo usuário; complemento v0.38 separado e ainda candidato.
- **Impacto:** biblioteca, tomos, permissões, catálogo de Classes, custos de magicka e livros autorais não foram alterados. Não interpretar o limite privado de duas como mudança automática da capacidade oficial do Core.
- **Próximo:** homologar o complemento sobre a revisão oficial e manter as integrações de Guildas alinhadas ao runtime real.
- **Bloqueios:** fontes completos/homologação oficial pendentes; parte privada confirmada.

## 2026-11-14, referência de feitiços das classes (mercurius17 / Codex)

- **Feito:** disponibilizado o [catálogo documental de feitiços](../gameplay-core/feiticos-por-classe.md), com 164 registros da planilha e diferenças dos JSONs do GameplayCore.
- **Impacto:** biblioteca, tomos, permissões e vínculo de Classes do SkyGuilds não foram alterados. A lista documental não substitui o catálogo autorizado nem comprova uma ponte de integração.
- **Próximo:** consultar a comparação ao integrar a política de magias ao GameplayCore; alinhar os contratos com Melekyy.
- **Bloqueios:** nenhum para o registro documental; continuam as pendências de integração já descritas abaixo.

## 2026-11-14 (Melekyy / IA)

- **Feito:** Melekyy confirmou que está desenvolvendo Guildas e autorizou o cadastro no acervo. Registrados o documento técnico, o guia da Staff e a atualização dos quadros e do índice. Instalado o bloco padrão do acervo no `AGENTS.md` do projeto local, que ainda não tinha esse arquivo.
- **Verificado:** leitura do acervo e das fontes locais; importação do módulo em Node.js. Sem execução do jogo, do servidor oficial ou da suíte de testes nesta sessão.
- **Próximo:** conectar e homologar os contratos de Staff, Classes e inventário na implantação oficial; informar a URL real do código.
- **Acesso ao acervo:** tentativas iniciais bloqueadas por falha do conector e ausência de credencial local. Resolvido com a instalação do GitHub CLI e a autorização de Melekyy; autenticação e permissão de escrita confirmadas. A atualização simultânea do GameplayCore foi incorporada antes do envio.
- **Bloqueios:** nenhum bloqueio de acesso ao acervo. A integração e a homologação no servidor oficial continuam pendentes.
- **Dependências:** o ritual de coleta planejado menciona vínculo real ao clã Companion; ainda não foi conectado ao SkyGuilds. Não houve alteração de código dos sistemas dependentes.
- **Decisões:** nenhuma nova decisão de design; aplicação dos ADRs de documentação e fonte da verdade já vigentes.
