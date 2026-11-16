---
titulo: Diário do AetheriusGameplayCore
tipo: historico
status: ativo
responsaveis: [mercurius17, kdu, Melekyy]
atualizado: 2026-11-15
tags: [diario, gameplay]
relacionados: [README.md]
---

# Diário do AetheriusGameplayCore

## Resumo
Registro cronológico do desenvolvimento, do mais recente para o mais antigo. Entradas curtas.

## 2026-11-16, causa do ataque travando e kit atualizado (kdu / Claude Opus 5.5)

- **Ataque/animação travando depois da tela de aparência (causa achada):** ao fechar a tela, o cliente segura os controles até a sessão nova chegar (holdControlsUntilSession). O código antigo chamava disablePlayerControls a cada quadro em que algo religava os controles, por até 90 s; o jogo forçava o estado padrão sem parar e nenhum ataque passava. Bate com o teste de 15/11 (IdleForceDefaultState ~12 vezes por segundo de 17:10:15 a 17:13:49). A correção de 14/11 (desligar uma vez e soltar 8 s depois do personagem recarregar) estava no patch do pacotão, mas **nunca tinha entrado no cliente do kit de teste**. Simulador com as funções tiradas do próprio cliente: antes 1.408 bloqueios em 2 min e 90 s preso; depois 2 bloqueios e 10 s.
- **Kit de teste:** o cliente do kit é de uma linhagem mais antiga que a v0.38 e não recebia o patch de cliente do pacotão. Aplicadas no cliente compilado do kit as correções de: controles (acima), pergaminho (scrollitem), habilidades raciais (Nórdico em qualquer raça, distorcia teste de gelo) e pacote de propriedade de ator inexistente (exceção cortava os pacotes do quadro). Ficam de fora no kit: tecla de voz no mouse (depende do launcher) e L/R (interface). Servidor do kit já tinha as correções de servidor.
- **Fogo sem dano contínuo:** não é bug. No modpack (Mysticism) o Firebolt é dano de impacto (20, duração 0); o Flames é que dá dano por segundo.
- **Atronach parada e atacando a dona:** sem registro; o código não mostra caminho para isso (servidor recusa dano da invocação na dona, cliente encerra briga com amigos, Dovahzul não tem lógica de invocação para comparar). Kit ganhou diagnóstico: linhas [magic] no servidor e Platform/Logs/aetherius-invocacao-logs.txt no cliente, recolhidos pelo MANDAR-RESULTADOS.
- **Comparação com o Dovahzul:** eles não reconectam depois da tela de criação; o nosso cliente reconecta e segura os controles nesse intervalo, que é onde o travamento nascia.

## 2026-11-15, pergaminho e ataque travando (kdu / Claude Opus 5.5)

- **Pergaminho (corrigido):** o teste de 14/11 mostrou no log do jogo "deserializeArg - Class scrollitem not found". O servidor manda o tipo do Papyrus ("scrollitem") e no SkyrimPlatform a classe do pergaminho é "Scroll", então EquipItem e EquipItemEx de pergaminho estouravam no cliente e ele nunca ia para a mão. Correção em `client/src/services/services/spSnippetService.ts` (tabela PAPYRUS_PARA_SP), dentro do patch de cliente do pacotão v0.38. Não visto em jogo.
- **Ataque/animação travando (em investigação):** no teste de 15/11, cada vez que a tela de aparência fechava o cliente reconectava e as sessões se sobrepunham. Durante 3,5 min o servidor recebeu "IdleForceDefaultState" 16 vezes por segundo (corta qualquer animação); os ataques só voltaram depois da reconexão seguinte. Origem exata do comando repetido não achada.
- **Entrega:** "ENTREGA COMPLETA 15-11" junta pacotão, física de cabelos e capas, lobisomem, olhar, ocarina e Túnica. Os 7 patches aplicam em sequência na v0.38 pura e o cliente compila com todos juntos.
- **Também pendente:** fogo sem dano contínuo; atronach invocada parada e atacando a dona (falta registro).
- **Próximo:** achar quem manda o IdleForceDefaultState repetido; teste em jogo do pergaminho.


- **Contrato:** só o Arquimago retira o vínculo de escola dos Magos sem expulsar o membro. A insígnia correspondente é revogada pelo journal; nova atribuição por professor/líder é possível após a retirada.
- **Limite:** não altera filiação à guilda, keyword do vínculo, catálogo autorizado, magias ou controlador de conjuração. O cronograma é informativo. Integração e homologação no runtime oficial permanecem pendentes.
- **Referência:** [Guildas](../guildas/README.md), [ADR-0016](../../03-decisoes/ADR-0016-quartos-escolas-e-cronograma-dos-magos.md).


## 2026-11-15, separação física da conjuração e dos painéis (Melekyy / Codex)

- **Pedido:** conferir e retirar scripts de conjuração que ainda estivessem junto do projeto de Guildas.
- **Entrega:** complemento candidato, pacotes e backup anterior transferidos ao projeto local `SIstema de Summons`, com 21 arquivos conferidos por hash. Ferramentas históricas que operavam os dois sistemas foram retiradas da distribuição atual de Guildas e preservadas no histórico separado. Nenhum código ou ZIP publicado.
- **Verificação:** 13 testes do candidato no destino aprovados. Dependências retidas e entrypoint de Guildas conferidos sem controlador de conjuração; novo pacote dos painéis inspecionado. Conjuração continua ativa no SkySync, com duas referências registradas na consulta, sem mudança de configuração ou reinício. Limites, duração, aparência e acompanhamento não foram alterados.
- **Próximo:** permanece a integração/homologação da adaptação na base oficial completa. Esta checagem não substitui jogo com dois clientes humanos; ver [estado da conjuração](conjuracao.md).

## 2026-11-15, impacto do planejamento COMÉRCIO (koy / Codex)

- **Referência:** [empresas, profissões e imposto comercial](../expansao-dos-paineis/comercio-empresas-e-profissoes.md), aprovado pelo usuário após revisão e confirmação de publicação.
- **Impacto em GameplayCore e profissões:** Empresas poderão contratar profissionais, mas emprego/titularidade não concedem profissão, rank ou receitas. A progressão permanece com os sistemas competentes; salários, vínculos e integração econômica ainda precisam de implementação. Nenhum código ou catálogo do Core foi alterado.
- **Próximo:** combinar os contratos e responsáveis antes da implementação; esta entrada registra uma dependência de planejamento.
- **Bloqueios:** nenhum para a referência documental; sem alteração da fase de implantação do sistema.

## 2026-11-15, escopo da publicação (Melekyy / Codex)

- **Pedido do dev:** subir somente a documentação no acervo. Código, ZIP candidato e backup anterior permanecem locais; não criar repositório de código nem publicar release nesta etapa.
- **Feito:** documento de conjuração, quadro técnico e progresso alinhados a esse escopo. A instalação privada confirmada e as evidências anteriores permanecem registradas.
- **Próximo:** obter os fontes completos oficiais e homologar a integração; a publicação documental não altera o estado de validação da candidata v0.38.
- **Bloqueios:** permanecem a falta da base completa e os testes oficiais pendentes. Nenhuma mudança de código ou implantação nesta atualização documental.

## 2026-11-15, conjuração no privado e complemento v0.38 (Melekyy / Codex)

- **Feito:** backup do pacote original e dos fontes/configuração/dados/arquivos privados anteriores. Conjuração reativada em execução com `skySummonActorV5`, limite de duas e acompanhamento nativo. A definição V4 arquivada foi preservada. Nenhum bundle/binário foi substituído, sem reiniciar jogo/servidor.
- **Confirmação do usuário:** as duas criaturas permanecem e acompanham após o roteiro de caminhar e atravessar porta/instância. A leitura seguinte encontrou duas referências ativas e duas cópias carregadas seguindo por `native-package-v2`, sem erros; houve substituições registradas por novas conjurações.
- **Adaptação oficial:** complemento separado com criação normal do modelo de conjuração, ordenação segura do callback, preservação do acompanhamento pelo host e opção explícita de duas criaturas. O código anterior das funções e o ZIP acumulado foram preservados. Os fontes completos puros não foram localizados; o código recuperado do mapa do cliente é pós-loader e não certifica a revisão pura.
- **Verificado:** 135/135 testes de Sync; teste isolado do addon com 179 magias, áreas/portas e FIFO; 13/13 testes do complemento, incluindo os quatro de amizade do pacotão e a recriação local com contexto novo; simulação/aplicação em cópia do bundle e reversão exata. O teste do instalador usou fixture de servidor, sem comprovar aplicação à base oficial completa.
- **Próximo:** integrar e compilar sobre os fontes completos oficiais e homologar com dois jogadores humanos, troca de host, instâncias, expiração e reconexão.
- **Bloqueios:** base completa v0.38 indisponível para build/suíte integral e homologação oficial. A parte privada foi instalada e confirmada.
- **Dependentes:** Guildas e NPCs informados nos diários; sem mudança em catálogo, magicka, veneno, reanimação, lobisomem ou livros autorais.
- **Decisão:** [ADR-0004](../../03-decisoes/ADR-0004-adaptacao-isolada-da-conjuracao.md); detalhes em [conjuracao.md](conjuracao.md).

## 2026-11-14, início da frente de conjuração (Melekyy / Codex)

- **Autorização:** o dev confirmou a atuação sobre a frente existente de mercurius17/kdu, conforme REGRAS 10.5.1. Melekyy entra apenas na comparação e correção da conjuração.
- **Objetivo:** corrigir o desaparecimento; adaptar acompanhamento normal e entre instâncias do privado; avaliar o encaixe de duas criaturas simultâneas. Também foi solicitado instalar no privado após os testes, com backup anterior.
- **Levantamento:** o pacote acumulado de 14/11 declara a invocação em aberto; seus patches tratam amizade do invocado e descarte de atores. O módulo privado tem acompanhamento e persistência próprios, mas está desativado e usa outra estrutura de código.
- **Próximo:** localizar a base completa da v0.38, preservar fontes/pacotes anteriores e isolar a alteração de conjuração das demais correções. Conferir ambiente e processos antes da instalação privada.
- **Bloqueios:** fontes completos da v0.38 ainda em localização. O teste com bot descrito por kdu comprova permanência no servidor, não a presença visual do ator no cliente.
- **Escopo preservado:** nenhuma alteração de veneno, reanimação, lobisomem, custos de magicka ou listas de feitiços nesta frente. Recomendada a coordenação da divisão do trabalho com os responsáveis existentes.

## 2026-11-14, entrega da conciliação de feitiços (mercurius17 / Codex)
- **Feito:** Classes - Progressão B23:F40 sincronizada com os 164 registros de Classes, em 12 das 18 classes. Nomes e tiers respeitam as mesclagens da origem; sem alteração das tabelas de perks/skills. Grafias corrigidas: Firebolt, Circle of Vitality, Trickster e Font of Vitality. Removidas do destino as entradas antigas sem equivalente na origem e a duplicação de Aura of Thorns.
- **Documentação:** [catálogo por classe](feiticos-por-classe.md), fichas da Staff, referência da planilha, índices, quadros e changelog atualizados. Publicado no GameplayCore o commit `e64f094ad69d16cad6f1ee056ee45dd20b690105` (somente seis arquivos .md).
- **Retificação:** a conferência anterior dizia que os feitiços eram idênticos ao código; isso só corresponde aos nomes de allSpellsList, normalizando espaços/apóstrofos. authorizedSpells tem 128 entradas (127 associações únicas), com omissões, nomes antigos e tiers divergentes; allSpellsList difere em seis tiers.
- **Verificado:** releitura da origem sem mudança; releitura do destino com todos os 164 registros; valores de A/G:J e formatos de A:J preservados. Revisão visual pelo navegador indisponível; sem teste em jogo ou alteração de código.
- **Dependentes:** UI Core e Guildas informados em seus diários; a apresentação e as permissões em execução permanecem as do código. Frente de dano de kdu preservada.
- **Próximo:** alinhar os campos de feitiços e espelhos de UI numa tarefa de implementação autorizada, sem presumir liberação ou concessão automática.
- **Bloqueios:** nenhum para a entrega documental. Git local sem rede; publicação feita pela conexão GitHub, com base na main relida e lease de SHA para preservar alterações simultâneas.
- **Decisões:** aplicação do ADR-0003; nenhuma mudança nova de design ou arquitetura.

## 2026-11-14, início da conciliação de feitiços (mercurius17 / Codex)
- **Feito:** leitura ao vivo das abas Classes e Classes - Progressão, incluindo mesclagens de tier; identificadas 164 associações classe/feitiço em 12 classes. Trabalho solicitado pelo dono: completar a progressão e as listas documentais.
- **Próximo:** espelhar os nomes e tiers de Classes na seção de feitiços da progressão e publicar catálogo documental com diferenças para authorizedSpells/allSpellsList.
- **Bloqueios:** git local sem acesso de rede; leitura e publicação disponíveis pela conexão GitHub, com atualização condicionada ao SHA atual da main.
- **Escopo:** documentação e planilha; sem alteração dos JSONs, grants, perks ou runtime, conforme ADR-0003. A frente de dano de kdu permanece independente.

## 2026-11-14, raças e invocação (kdu / Claude Opus 5.5)
- **Raças:** todo personagem tinha a resistência ao frio e o Grito de Batalha do nórdico. O jogo carrega como Nórdico, e a aparência só troca o campo de raça da base. O cliente agora tira as habilidades das outras raças jogáveis e põe as da raça do personagem a cada aparência aplicada. Patch em `missoes/2026-11-14-bugs-akatosh`.
- **Invocação:** conferida sem o jogo, com addon e load order reais e um bot. O servidor cria o Familiar e o Atronach e eles ficam vivos. O "aparece e some" relatado é a cópia local do jogo, que a limpeza do cliente descarta. Falta descobrir no cliente por que a do servidor não aparece; precisa do log do cliente de uma tentativa.
- **Bloqueios:** invocação aguardando log.

## 2026-11-14, frente de dano (kdu / Claude Opus 5.5)
- **Feito:** kdu entra como responsável no GameplayCore pela frente de dano (confirmado pelo mercurius17 em 14/11). Documentadas as correções de dano do servidor em [correcoes-de-dano.md](correcoes-de-dano.md): fases de ativação, chaves, tabela de armadura e resistências, pendências. Pacote entregue ao mercurius17 em 13/11 sobre a v0.38.
- **Próximo:** confirmar as chaves ligadas no oficial; guia da Staff de dano quando a fase 1b for ligada.
- **Bloqueios:** nenhum

## 2026-11-14, planilha de design (mercurius17 / Claude Opus 5.5)
- **Feito:** planilha "AETHERIUS - Classes, efeitos, encantamentos" conferida aba por aba contra o código ([referência](../../06-referencias/planilha-classes-efeitos-encantamentos.md)); vale o código ([ADR-0003](../../03-decisoes/ADR-0003-codigo-prevalece-sobre-planilhas-de-design.md)). Skills e feitiços por classe idênticos; perks diferem em Arqueiro (sem Steady Aim) e Anti-Mago. Leveling: a tabela "Delta" e o grupo nos chefes da planilha não valem. Fichas por classe geradas de `classes-config.json`, `perks-descriptions.json` e `spells-descriptions.json`. Raças/pedras, poções, encantamentos e maldições levados ao guia da Staff; os três últimos como design.
- **Próximo:** decidir o total de XP das maldições (a planilha se contradiz: 46.500 ou 2 milhões).
- **Bloqueios:** nenhum

## 2026-11-14, guia da Staff (mercurius17 / Claude Opus 5.5)
- **Feito:** guias leigos em `07-guia-da-staff/`: classes, progressão (XP, grupo, fadiga, atributos), inimigos e masmorras, manutenção de equipamentos; glossário e painel de progresso atualizados. Fonte: configs do GameplayCore (`leveling-system/config`, `class-system/config/classes-config.json`, `durability-system/config`) e as portas do host no `aetherius-server` (painel F7, `enemy-spawns-service.js`, `durability-service.js`, `aetheriusCoreClassUi.ts`).
- **Próximo:** atualizar os guias quando o XP por abate for ligado no oficial e quando a aba Manutenção entrar na interface.
- **Bloqueios:** nenhum

## 2026-11-14 (mercurius17 / Claude Opus 5.5)
- **Feito:** sincronização SYNC-1114, commit `05f5a0f` na `main`, a partir da cópia `server/gameplay/core` do servidor oficial de 13/11 (entrega v0.37.0). Entrou:
  - skills de classe entregues ao cliente (`runtime/src/class-skills.ts`, `modules/class-progression-system/src/skills.ts`);
  - capacidade de invocação por classe/raça/pedra (`runtime/src/summon-capacity.ts`);
  - comando `class.level` do painel da staff (`setProgressLevel` em `leveling-system/src/persistence/durable.ts`, `createLevelCommands`); se o novo nível der menos pontos que os gastos, a distribuição de atributos é zerada;
  - Anti-Mago com a Maestria em Conjuração (`skyrim.esm|0F2CA7`) no nível 1, pré-requisito do Mystic Binding no nível 10;
  - chaves `character:<uuid>` aceitas, com lock próprio; migrations `0013_gc_professions_uuid_v2.sql` e `0014_gc_profession_world.sql`.
- **Ficou de fora:** catálogos v0.37.0; o export `professionApi` (importa `../../../professions`, fora do Core); arquivos em que o servidor está atrás do Core (`ds5a/Host.h`, README do `aetherius_combat`, dois testes). Motivos no [ADR-0002](../../03-decisoes/ADR-0002-sincronizacao-dos-cores-com-os-repositorios-oficiais.md).
- **Testes:** 350/381 (antes: 334/365); as 26 falhas são as mesmas da linha de base `efe7e4c`, todas por falta de exports/`dist`. Os 16 testes novos passam. Typecheck: os mesmos 6 erros de antes em `runtime/src/combat-profile.ts`.
- **Próximo:** decidir para quais masters os catálogos são assinados; alinhar o fim de linha da `0013` entre servidor e Core; levar a DS-7 de volta à cópia do servidor.
- **Bloqueios:** nenhum
- **Decisões:** [ADR-0002](../../03-decisoes/ADR-0002-sincronizacao-dos-cores-com-os-repositorios-oficiais.md)
- **Nota completa no repositório:** `docs/operations/SINCRONIZACAO_SERVIDOR_20261114.md`

## 2026-11-02 (mercurius17 / Claude Opus 5.5)
- **Feito:** SYNC-1102 (`7fbf070`, `41e8334`, `efe7e4c`): DS-5c, DS-7 (`hitSeq` no `Host.h`), perfis `mundo.*` de XP e `formId` dos kits de durabilidade trazidos do servidor (`8b5d299`).
- **Decisões:** catálogos não reassinados, pendente do dono. Nota: `docs/operations/SINCRONIZACAO_SERVIDOR_20261102.md`.
