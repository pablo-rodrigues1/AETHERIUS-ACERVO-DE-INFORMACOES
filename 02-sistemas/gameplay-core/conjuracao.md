---
titulo: Conjuração - privado e adaptação v0.38
tipo: sistema
status: ativo
fase: em-teste
responsaveis: [Melekyy]
repositorio: null
depende_de: [README.md, ../sincronizacao-de-npcs/README.md]
atualizado: 2026-11-15
tags: [conjuracao, seguidores, instancias, v038]
relacionados: [diario.md, ../guildas/README.md, ../../03-decisoes/ADR-0004-adaptacao-isolada-da-conjuracao.md, ../../07-guia-da-staff/classes/conjuracao.md]
---

# Conjuração: privado e adaptação v0.38

## Resumo

Melekyy atua apenas na conjuração, com confirmação explícita do dev para complementar a frente existente. O privado recebeu acompanhamento normal/entre áreas e limite de duas, aprovados pelo usuário; a adaptação separada para a v0.38 continua candidata, sem homologação no oficial.

## Onde está o código

- Fontes privados: projeto `Sistema de Sync Geral`, `src/summons/`, `src/retire-summons.js`, configuração `config/summons.json` e `native/SkySummonAI.cpp`.
- Complemento v0.38: projeto local independente `SIstema de Summons`, diretório `conjuracao/`, com adaptador de companheiro, instalador, reversão e testes. Transferido de Guildas em 15/11/2026 com hashes conferidos; não integra o pacote atual dos painéis.
- Por escolha explícita de Melekyy em 15/11/2026, somente a documentação é publicada no acervo. Código, pacote de distribuição e backup anterior permanecem locais; não foi criado repositório de código nem enviada uma release. O campo `repositorio` permanece `null`.
- A base completa da v0.38 pura não foi encontrada na máquina nem entre os repositórios acessíveis; não confundir os fontes locais privados com essa base.

## Escopo e preservação

Inclui desaparecimento/renderização de conjuração, acompanhamento, mudanças de área/instância e duas criaturas quando escolhido. O ZIP acumulado de 14/11 foi preservado, com SHA-256 `177ea81172f696e9ad0c70055bcc6520f77918a02f2f0f9ad76bef850431b392`.

Antes da alteração foram salvos fontes, configuração, dados de conjuração, arquivos instalados e bundles privados, com manifesto de hashes. Esse backup contém dados privados e fica separado do pacote de distribuição. Não restaurar dados antigos sobre inventários/progresso novos.

Veneno, reanimação, lobisomem, custos de magicka, catálogo de classes e livros autorais não foram alterados. As correções de amizade e o descarte/estacionamento do pacote anterior são preservados.

## Privado instalado

Separação conferida em 15/11/2026: o controlador permanece no SkySync, habilitado em V5. Os módulos de Guildas retidos no runtime e seu entrypoint não carregam conjuração; o novo pacote dos painéis foi inspecionado sem esses scripts. Nenhum controlador, configuração ou bundle ativo foi movido ou reinstalado nesta organização. O complemento, seus ZIPs e o backup privado anterior ficam no projeto separado; quatro ferramentas antigas conjuntas e o pacote anterior de Guildas ficam no histórico, sem uso como instaladores atuais. Backups conjuntos de 14/11 continuam preservados fora da distribuição atual de Guildas.

| Item | Estado |
|---|---|
| Controlador | Reativado por solicitação do dev |
| Propriedade visível | `skySummonActorV5` |
| Definição anterior | V4 preservada; não reutilizada como renderer vazio |
| Limite | Duas criaturas por dono |
| Terceira conjuração válida | Substitui a mais antiga, preservando as duas mais novas |
| Duração | Mantida a política anterior do privado, até a morte |
| Acompanhamento | Pacote nativo V7, executado pelo host real |
| Áreas/instâncias | Mesma referência no servidor; o cliente libera/recupera acompanhamento conforme a área e o host |

A configuração anterior desativada não oferecia um controlador ativo; a nova propriedade permite reativá-lo em execução sem usar a definição V4 arquivada com callbacks vazios. Foram atualizados apenas os arquivos de conjuração do módulo privado. Nenhum bundle, addon do servidor ou binário de cliente foi substituído nessa ativação, nem houve reinício do jogo/servidor.

## Adaptação v0.38 candidata

Foi recuperado código JavaScript pós-loader dos mapas embutidos no cliente instalado em 13/11. Isso permite reproduzir trechos do renderer, mas não certifica os fontes TypeScript nem o hash da v0.38 pura.

O teste demonstrou um defeito de ordem: um callback síncrono de spawn era rejeitado antes da atribuição de `refrId`, mantendo `ready` falso. O complemento registra a identidade antes do callback e usa a criação normal para modelos de conjuração autorizados pelo servidor. Isso corrige o cenário reproduzido; ainda não comprova a causa única do relato no oficial.

O adaptador preserva amizade do dono/grupo, usa a lista nativa de host e evita que as duas limpezas de movimento cancelem o acompanhamento ativo do host. Não adota cópias locais criadas pela magia como criaturas autorizadas. Troca de host, combate ou área libera o pacote; o acompanhamento retoma quando a nova cópia e o dono estiverem disponíveis na mesma área.

### Limite de duas no oficial

O instalador oferece `--two`, que altera apenas `trim(owner)` do gerenciador, chama a remoção `end` já existente e preserva as durações do serviço original. Essa é uma escolha explícita de limite fixo, inclusive para classes que teriam outro limite. **Sem a opção, o limite original permanece**, incluindo a composição do Core. Não é uma mudança automática das regras oficiais.

### Instalação e reversão

O instalador confere os pontos conhecidos do código e começa em modo de simulação. Com `--apply`, salva os bytes e hashes anteriores antes de escrever; os corpos anteriores das funções são mantidos. A reversão recusa sobrescrever arquivos modificados depois da instalação. Não reaplicar o pacotão inteiro sobre uma base já corrigida.

## Dependências

| Dependência | Contrato |
|---|---|
| [GameplayCore](README.md) | Limite oficial por classe/raça/pedra preservado por padrão na adaptação |
| [Replicação de NPCs](../sincronizacao-de-npcs/README.md) | Modelos, identidade, host e cópias locais do ator |
| SkySocial | Amizade e proteção do dono/grupo |
| SkySummonAI-v7 | Opcional na adaptação; requer Skyrim 1.6.1170.0; acompanhamento alternativo usa PathToReference |

## Evidências e limites

- **Separação de arquivos:** 21 arquivos movidos com SHA-256 conferido; 13 testes do candidato aprovados na nova pasta. Inspeção de 31 dependências dos módulos de Guildas retidos no runtime não encontrou o controlador; pacote de Guildas com 151 entradas sem scripts de conjuração. Runtime R23 e 14 arquivos protegidos anteriores conferidos. A consulta registrou duas criaturas ativas no SkySync; não houve novo teste visual ou multiplayer nesta etapa.
- **135/135 testes privados** de Sync aprovados após a alteração.
- **Teste nativo isolado aprovado** com 179 magias catalogadas, portas/áreas, duas criaturas, substituição FIFO e validação de autoridade. Sem clientes humanos nem armazenamento persistente nesse teste.
- **Usuário confirmou**: as duas criaturas permanecem e acompanham, após o roteiro de caminhada e porta/instância.
- Leitura posterior do privado: duas referências ativas no servidor, duas cópias carregadas seguindo por `native-package-v2`, sem erros no diagnóstico; eventos também registraram substituições por conjurações seguintes.
- **13/13 testes da adaptação** aprovados, incluindo os quatro testes de amizade do pacote original e a recriação da cópia local ao trocar de área com reinicialização do contexto. Simulação/aplicação em cópia do bundle, idempotência e reversão exata aprovadas. O servidor usado no teste do instalador foi um fixture, não a v0.38 completa.
- Não executados: build/suíte completa da v0.38, aplicação sobre os fontes completos puros, oficial com dois jogadores humanos e teste de troca de host entre humanos.

## Próximos passos

Obter os fontes completos da revisão oficial, integrar o complemento, compilar e comparar as falhas com a base. Homologar no oficial aparência, instâncias, duas pessoas, troca de host, desconexão e expiração. A aprovação no privado não é evidência de implantação pública.
