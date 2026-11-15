---
titulo: Diário de Guildas (SkyGuilds)
tipo: historico
status: ativo
responsaveis: [Melekyy]
atualizado: 2026-11-15
tags: [diario, guildas, skyguilds]
relacionados: [README.md]
---

# Diário de Guildas (SkyGuilds)

## Resumo

Registro do desenvolvimento de Guildas por Melekyy, do mais recente para o mais antigo.

## 2026-11-15, insígnias R21 ativadas no privado (Melekyy / Codex)

- **Autorização e instalação:** o dev confirmou que fechou o Skyrim privado e o MO2. Fechamento verificado; foi criado backup dos dados do mundo e dos arquivos afetados. Somente o servidor privado foi parado e reiniciado.
- **Plugin:** cópia ESP completa do Elemental Vision II ativada no cliente e servidor, anexada ao fim da ordem sem deslocar os índices anteriores. Verificação de 115 plugins em paridade de ordem e hashes aprovada. Original do mod, bundles do cliente, núcleo nativo, fonte de Morte e configuração de conjuração preservados. Manifesto de terreno atualizado sem alterar a geografia.
- **Entrega real:** o runtime privado carregou os cinco registros configurados e entregou exatamente uma insígnia correspondente à escola já cadastrada do personagem de teste, mesmo com ele desconectado. Presença em unidade única conferida também no inventário persistido em disco; todos os itens anteriores preservados. O personagem permanece membro comum, sem liderança, professor ou bibliotecário.
- **Proteção e acesso:** adaptador de morte conectado no runtime; descarte da insígnia recusado pelo hook do servidor. Bancada continua exclusiva dos bibliotecários, e líder/professores usam os seletores de membro/escola na aba Professores.
- **Evidências:** relatórios locais de instalação, reinício e verificação no runtime, além dos 104 testes e teste nativo isolado de entrega, transferência, morte/recuperação e expulsão. Servidor iniciou com os demais sistemas e voltou a responder ao canal privado.
- **Limites:** esta entrega confirma os dados e a proteção no servidor real; o cliente estava fechado na verificação de entrega. Inspeção visual da insígnia, teste de morte no cliente real e homologação oficial permanecem pendentes. Não houve morte forçada do personagem do dev.
- **Próximo:** reabrir o cliente para conferir visualmente a insígnia e seguir a homologação oficial. Nenhum bloqueio de instalação privada permanece.

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
