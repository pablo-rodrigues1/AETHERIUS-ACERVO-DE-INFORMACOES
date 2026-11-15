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

## 2026-11-15, Biblioteca R19 instalada no privado (Melekyy / Codex)

- **Feito:** vários bibliotecários com nomeação e revogação individuais pelo líder; bibliotecário anterior preservado. Somente os nomeados fabricam. Três integrantes fictícios identificados foram adicionados exclusivamente ao painel privado, sem contas ou personagens no mundo.
- **Fabricação:** lote de três segundos, livro animado, progresso e cancelamento na interface; animação nativa de leitura solicitada em atualização segura e distribuída ao dono e aos vizinhos. Sem chamadas de bloqueio de controles ou câmera.
- **Servidor:** revalida sessão, identidade, acesso, bancada e materiais; só entrega e cobra na conclusão pelo diário de inventário. Fechar, cancelar, afastar, desconectar ou perder acesso interrompe sem consumo. Receita preservada: cinco papéis e uma pedra da alma por tomo.
- **Apresentação:** título de classe separado do retorno. Os 104 tomos autorizados receberam nomes e descrições em português; busca pelo português e pelo original, sem alterar os itens nativos, a política de classes ou as magias. Textos com valores ausentes não receberam números inventados.
- **Decisão:** [ADR-0009](../../03-decisoes/ADR-0009-bibliotecarios-fabricacao-e-traducao.md).
- **Verificado:** 88 testes automatizados aprovados, incluindo interface e ausência de sobreposição em três larguras. Servidor nativo isolado com diário real confirmou fabricação simultânea, débito único, cancelamento por revogação e cobertura dos 104 tomos. Esse teste não teve clientes de jogo.
- **Privado:** atualização sem reinício, com backup. Inventários, pertences, grupos, catálogo autorizado, núcleo nativo, conjuração e bundles do cliente preservados. Fonte R19 recebida e reportada pronta pelo cliente conectado.
- **Limites:** usuário interrompeu a inspeção visual com Esc. Animação e disposição final no jogo, visão por outro jogador e homologação oficial permanecem sem confirmação nesta etapa.
- **Próximo:** conferir a fabricação e as nomeações no cliente real; seguir integração oficial. Nenhum bloqueio de publicação.

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
