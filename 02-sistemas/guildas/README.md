---
titulo: Guildas (SkyGuilds)
tipo: sistema
status: ativo
fase: em-desenvolvimento
responsaveis: [Melekyy]
repositorio: null
depende_de: [../gameplay-core/README.md]
atualizado: 2026-11-15
tags: [guildas, skyguilds, magos, alquimistas, companions]
relacionados: [diario.md, ../ui-core/README.md, ../painel-adm/README.md, ../expansao-dos-paineis/ritual-de-coleta.md, ../../07-guia-da-staff/regras-de-roleplay/guildas.md]
---

# Guildas (SkyGuilds)

## Resumo

**Melekyy está desenvolvendo o sistema de Guildas**, com painéis e permissões para Magos, Alquimistas e Companions. Há implementação e documentação locais; a integração e a homologação no servidor oficial continuam pendentes.

## Onde está o código

- Projeto local: `Sistema de Guildas 2`, pacote Node.js `sky-guilds`.
- Entrada do servidor: `src/index.js`, função `install(mp, options)`; regras e permissões: `src/system.js`.
- Definições: `config/guilds.json`; configuração de exemplo: `config/server.example.json`.
- Interface: `src/client.js` e `src/ui.js`.
- A URL do repositório do código ainda não foi informada nem está configurada no Git local. Preencher `repositorio` quando houver URL real.

## Escopo

- Inclui membros, convites com aceite, liderança, cargos, professores, funções especiais, vínculo para Classes, biblioteca e acesso a pertences.
- A ponte de autorização da Staff e o vínculo com o runtime oficial de Classes precisam ser conectados aos serviços efetivamente usados pelo servidor.
- Livros autorais/em branco estão desativados na configuração de exemplo atual. O registro local de 14/11 informa que foram arquivados; a bancada de tomos de magia permanece.
- A [conjuração](../gameplay-core/conjuracao.md) é uma frente separada: foi reativada no privado em 15/11, com duas criaturas e acompanhamento; a adaptação oficial continua pendente de homologação. Não faz parte da habilitação dos livros autorais.

## Funcionamento

| Guilda | Líder | Funções específicas |
|---|---|---|
| Magos | Arquimago | Um professor por escola de magia e vários bibliotecários |
| Alquimistas | Mestre Alquimista | Vários professores alquímicos |
| Companions | Chefe Mercenário | Vários instrutores e um Forjador Mestre |

- A Staff nomeia ou remove o líder por API do servidor, após autorização real. Não há ação do navegador para se tornar líder.
- O líder convida, remove membros e gerencia cargos e funções. O jogador precisa aceitar o convite para entrar.
- Membros não podem sair voluntariamente nem remover o próprio perfil. A saída acontece pela remoção feita pelo líder e fica nos Registros. A liderança continua sendo alterada pela Staff.
- Cargos personalizados não concedem gestão. Nos Magos, líder ou professor define uma escola por estudante; o código não permite trocar esse vínculo sem a saída por expulsão.
- A aba de produção na bancada se chama **Biblioteca**; a antiga aba de consulta foi retirada. O bibliotecário pesquisa e fabrica tomos pelo mesmo catálogo autorizado. Cada tomo indica suas classes, categorias (Novato a Mestre) e efeito base disponível nos plugins; a busca reconhece classe e categoria. A liderança mantém a nomeação do bibliotecário na Biblioteca.
- A receita vigente no código cobra **5 papéis e 1 pedra da alma** por tomo, com a mesma validação de acesso e proximidade. A descrição é informativa: não libera magias, não redefine requisitos de aprendizado e não altera seus efeitos.
- Na R17, a Biblioteca apresenta primeiro as classes com suas quantidades de tomos. Escolher uma classe abre somente seus tomos; **Todas as classes** retorna à escolha. A classe é filtrada antes da paginação, e um tomo compartilhado aparece nas classes autorizadas correspondentes.
- A R18 refina os três painéis com referência em Grupo e Reis: molduras, brasões, marca d'água, cartões, abas e botões. Magos mantêm azul; Alquimistas, verde musgo; Companions, marrom escuro. A disposição adapta-se a janelas menores e não altera ações ou permissões.
- A R19 permite nomear e revogar vários bibliotecários individualmente, preservando o anterior na migração. Somente eles fabricam; um Arquimago sem essa função não fabrica. Cada lote leva três segundos, com progresso, cancelamento e solicitação de animação nativa. Os materiais são cobrados apenas na conclusão validada pelo servidor. Fechar a bancada, perder a proximidade ou a função cancela sem débito.
- Os 104 tomos atuais têm título e descrição em português na Biblioteca, com busca também pelo original. A tradução preserva os registros nativos e não renomeia itens do inventário. Valores ausentes nos textos dos mods não são inventados. O título da classe recebeu espaçamento próprio junto ao retorno.
- A R20 detalha a aba **Registros**, exclusiva da liderança: nomes e IDs de perfil de autor e envolvidos, cargo ou função, substituições, revogações e diferenças antes/depois quando aplicáveis. Os dados são salvos no momento da ação e preservados após renomeações ou expulsões. Registros anteriores permanecem intactos, com aviso sobre os detalhes históricos não gravados. O limite existente é de 100 registros recentes exibidos por guilda e 5.000 retidos no estado global.
- O painel dos Magos recusa transferências pelo baú virtual. O acesso aos baús dos quartos passa pelo menu nativo e pelas permissões do servidor; antigos ocupantes preservam a retirada dos próprios pertences.
- A R21 instalada no privado restringe a abertura da bancada aos bibliotecários. Líderes administram essas nomeações em Professores. Nessa mesma aba, líder e professores selecionam membro/escola. O Elemental Vision II está ativo no cliente/servidor privado, com entrega automática e proteção de inventário/morte conectadas; cinco registros reais conferidos. A configuração de exemplo não habilita o mod automaticamente, e a implantação oficial ainda precisa conferir dependências e adaptadores.

## Parâmetros e fórmulas

| Parâmetro | Valor | Fonte local |
|---|---|---|
| Validade padrão do convite | 300.000 ms (5 minutos) | `src/system.js`, `config/server.example.json` |
| Escolas dos Magos | Alteração, Conjuração, Destruição, Ilusão e Restauração | `config/guilds.json` |
| Custo de um tomo de magia | 5 papéis + 1 pedra da alma | `src/spell-recipe.js` |
| Duração de um lote de tomos | 3.000 ms | `src/crafting.js`, `config/server.example.json` |
| Livros autorais na configuração de exemplo | `features.authoredBooks: false` | `config/server.example.json` |

## Dependências

| Dependência ou contrato | Situação |
|---|---|
| SkyMP e perfis persistentes | Host e identidade usados pelo módulo local |
| SkySocial e diário de recibos | Necessários às movimentações de inventário; sem diário, essas movimentações ficam indisponíveis |
| [GameplayCore](../gameplay-core/README.md) | Fonte da política de magias e destino do vínculo de Classes; confirmar o runtime oficial |
| [Painel administrativo](../painel-adm/README.md) | Integração de autoridade da Staff a implementar; não presume API já conectada |
| [UI Core](../ui-core/README.md) | Referência visual local; compatibilidade com o cliente oficial ainda requer validação |
| Plugins e mods de itens | Conferir registros e mesma ordem de carregamento no servidor e nos clientes |

## Impacto em outros sistemas

O [ritual de coleta planejado](../expansao-dos-paineis/ritual-de-coleta.md) exige um participante com vínculo real ao clã Companion. Este cadastro não conecta o ritual ao SkyGuilds nem redefine o identificador oficial do clã; o contrato deve ser combinado com os responsáveis pela integração.

## Decisões relacionadas

- [ADR-0001](../../03-decisoes/ADR-0001-integracao-continua-por-ia.md): documentação contínua.
- [ADR-0003](../../03-decisoes/ADR-0003-codigo-prevalece-sobre-planilhas-de-design.md): documentar o comportamento definido no código.
- [ADR-0006](../../03-decisoes/ADR-0006-biblioteca-e-indicacoes-de-magias.md): apresentação da Biblioteca e fonte das indicações de magias.
- [ADR-0007](../../03-decisoes/ADR-0007-biblioteca-primeiro-a-classe.md): navegação por classe antes dos tomos.
- [ADR-0008](../../03-decisoes/ADR-0008-visual-dos-paineis-de-guildas.md): acabamento visual das três guildas, preservando cores e comportamento.
- [ADR-0009](../../03-decisoes/ADR-0009-bibliotecarios-fabricacao-e-traducao.md): vários bibliotecários, fabricação com duração e tradução da Biblioteca.
- [ADR-0010](../../03-decisoes/ADR-0010-registros-detalhados-de-guildas.md): registros históricos com nomes, IDs e detalhes da alteração.
- [ADR-0011](../../03-decisoes/ADR-0011-bancada-e-insignias-das-escolas.md): bancada exclusiva, seleção de escola e insígnias vinculadas, incluindo morte.
- O registro inicial não introduz nova decisão de design ou arquitetura.

## Diário

Ver [diario.md](diario.md).

## Pendências conhecidas

- Conectar a autorização da Staff e o vínculo de Classes ao servidor oficial.
- Confirmar os registros dos medalhões e as dependências de inventário na implantação oficial; os cinco registros foram ativados e conferidos no privado.
- Homologar com clientes reais: permissões, pertences, itens, conflitos com outros módulos e estabilidade nas transições de área.
- Informar a URL do repositório do código.

## Base da verificação

Levantamento de 14/11/2026 com leitura das fontes locais acima, de `README.md`, `docs/INTEGRACOES.md` e `docs/SISTEMAS-RETIRADOS.md`. A importação de `src/index.js` em Node.js foi verificada; a suíte de testes, o jogo e o servidor oficial não foram executados nesta sessão. Registros locais de implantação privada não comprovam homologação pública.

Atualização de 15/11/2026: 12 testes de interface, catálogo e apresentação aprovados; 104 tomos autorizados e 103 descrições disponíveis no runtime privado. A fonte de interface R16 foi atualizada sem reinício. O personagem de teste estava desconectado; a confirmação visual em jogo permanece pendente. Nenhum teste no oficial foi executado nesta atualização.

Continuação de 15/11/2026: R17 com seleção por classe instalada no privado, 22 testes direcionados aprovados e verificação adicional dos 11 testes de integração após acrescentar a persistência da escolha. O runtime confirma 12 classes e 104 tomos; o cliente conectado reportou renderização R17. A confirmação visual da navegação e a homologação oficial seguem pendentes.

Refinamento R18 de 15/11/2026: sete testes de interface aprovados e oito capturas de prévia com dados fictícios por revisão. As três cores foram preservadas. A atualização foi aplicada sem reinício, e os Magos foram observados diretamente no Skyrim. As outras duas guildas foram conferidas nas prévias; não houve homologação oficial nem aceite explícito do usuário nesta etapa.

Atualização R19 de 15/11/2026: 88 testes automatizados aprovados, incluindo migração, dois bibliotecários fabricando simultaneamente, cancelamento, repetição de pedido, revogação e afastamento. Servidor nativo isolado confirmou os lotes com diário real e os 104 tomos traduzidos; não havia clientes de jogo nesse teste. Instalado no privado sem reinício, com três integrantes fictícios identificados, inventários e arquivos protegidos preservados. O cliente conectado reportou a fonte R19 pronta. A inspeção visual foi interrompida pelo usuário com Esc; aparência final e animação no jogo, incluindo visão de outro jogador, continuam sem confirmação nesta etapa.

Atualização R20 de 15/11/2026: registros detalhados instalados sem reinício; o cliente conectado reportou a fonte R20 pronta. Estado persistente, registros anteriores, inventários e arquivos protegidos preservados. A captura instalada foi exercitada em cópia desconectada do estado no runtime privado, sem gerar ações falsas no histórico real. Testes automatizados cobrem persistência, origem confiável dos nomes, autorização, revogações, substituições, renomeação/exclusão de cargos, histórico antigo, conteúdo literal e disposição em duas larguras. A prévia de interface usa dados fictícios; aceite visual no Skyrim e homologação oficial seguem pendentes.
