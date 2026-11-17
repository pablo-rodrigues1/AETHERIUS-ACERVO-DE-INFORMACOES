---
titulo: Validação de Guildas R28 e limites
tipo: referencia
status: ativo
responsaveis: [Melekyy]
atualizado: 2026-11-17
tags: [guildas, skymp, integracao]
relacionados: [README.md, diario.md]
---

# Validação e limites — Guildas R28 / recuperação R29

Revisão: 17/11/2026. Responsável: Melekyy.

## Revisão atual R30 — quartos retirados, portas mantidas

125 testes locais sem navegador aprovados, zero falhas/pulos. Comandos antigos de quartos recusados para os quatro perfis; dados/configurações antigas não reinstalam StudentRooms. Controle geral de portas permanece exclusivo do Arquimago. Insígnias, escolas, cargos, cronograma, Biblioteca, receita e ambos os baús continuam cobertos pelos testes locais.

Instalação privada conferiu os fontes ativos, bootstrap privado original, 18 inventários e cadastro de membros/escolas preservados. Mesmo baú CONT do Arquimago reposicionado no servidor, com reaplicação no carregamento do cliente. Conteúdo do coletivo e antigos móveis preservado. Fontes antigas de quartos foram arquivadas e não são carregadas.

**Testes no Skyrim são do dev:** não houve abertura automática de painel, depósito, travessia ou confirmação visual do novo local pelo agente. Nenhum teste anterior de quarto é aceitação da revisão atual; gestão de quartos deixou de existir. Global de portas, baú do Arquimago e comunitário continuam ativos. Não há ZIP novo desta revisão, nem homologação oficial/dois humanos.

## Histórico — atualização posterior: regra de quartos e relato do dev

O dev rejeitou depósito de septins nos quartos e voltou a relatar painel/porta bloqueados. A regra de depósito foi corrigida no servidor e passou nos seis testes unitários de quartos, sem executar o Skyrim. Retirada do diagnóstico antigo do cliente instalada com jogo fechado. Testes no jogo serão feitos pelo próprio dev; não há nova aceitação de painel/porta. Conteúdo e cadastro originais do quarto temporário preservados/restaurados. Ensaios nativos anteriores de depósito de septins são evidência histórica da regra antiga, não aprovação da regra atual. ZIP R29 anterior não inclui esta mudança posterior.

## Recuperação R29 em conferência

Guildas e Casas foram observados abertos no cliente real após carga fria. O cliente confirmou R29, com modelos de dono e entradas dos sistemas carregados. Lista de plugins e guarda de propriedade fora do streaming instaladas com o jogo fechado; dez callbacks antigos de Guildas aposentados. Convite apresentado por ID, com identidade pública conferida nesta instalação. Não houve alteração do controlador de conjuração.

150 testes do workspace passaram antes de acrescentar a verificação de interface com os quatro modelos reais de permissão. Depois, os 19 testes de interface, incluindo essa nova regressão, passaram. A distribuição R29 executou seus 108 testes com zero falhas/pulos; o ZIP local tem 78 arquivos, todos conferidos após extração, SHA-256 `4f040e06412d7e894473bf8657b1ce4a5a99766dee34bc3468f095669c5cbcc2`. Esses resultados são diferentes conjuntos de testes, não devem ser somados. Os ensaios nativos atuais usam o mesmo addon privado em processos isolados, **sem clientes humanos**:

| Fluxo | Arquimago | Professor | Bibliotecário | Membro comum |
|---|---|---|---|---|
| Consultar painel/cronograma | Permitido | Permitido | Permitido | Permitido |
| Convites, remoção, cargos e nomeações | Permitido | Recusado | Recusado | Recusado |
| Definir escola de membro sem vínculo | Permitido | Permitido | Recusado | Recusado |
| Retirar vínculo de escola | Permitido | Recusado | Recusado | Recusado |
| Alterar aulas e consultar registros | Permitido | Recusado | Recusado | Recusado |
| Abrir/consultar/fabricar na bancada | Permitido | Recusado | Permitido | Recusado |
| Controle geral das portas | Permitido | Recusado | Recusado | Recusado |
| Atribuir os dez quartos | Permitido | Permitido | Recusado | Recusado |
| Abrir porta de quarto por proximidade | Permitido | Permitido | Só a sua | Só a sua |
| Acessar compartimento de quarto atribuído | Só se dono | Só se dono | Só se dono | Só se dono |
| Usar estoque coletivo | Permitido | Permitido | Permitido | Permitido |
| Usar baú particular do Arquimago | Permitido | Recusado | Recusado | Recusado |
| Sair voluntariamente | Indisponível | Indisponível | Indisponível | Indisponível |

O Arquimago pode retirar qualquer membro elegível; a própria liderança é definida pela Staff. Trancas globais impedem passagem pelos dois lados inclusive dos administradores da guilda. O baú de quarto atribuído pertence ao dono, enquanto professores/Arquimago podem abrir sua porta: são permissões diferentes.

Provas nativas revalidadas: exclusividade professor/bibliotecário, cinco insígnias e proteção de inventário/morte, uma escola por membro, cronograma/registros, fabricação concorrente e cancelamento sem cobrança dos 104 tomos, 68 passagens/86 referências e 166 ativações nos dois sentidos, dez quartos/60 ativações com persistência e troca de dono, estoque coletivo e baú exclusivo com troca de liderança. Erros de acesso não se tornam operações válidas por chamar diretamente o endpoint.

**Pendências do cliente real:** Grupo, ações visuais por função e depósito/reabertura no quarto. A ativação do compartimento pelo servidor não prova transferência pelo cliente. Diagnóstico antigo de renderização identificado e remoção preparada, ainda sem instalação fria. Não extrapolar as provas nativas para declarar todos os fluxos privados ou o oficial concluídos.

## Evidência da entrega R28 anterior

Resultado final: **149 testes do workspace e 106 testes da própria distribuição aprovados**, sem falhas ou pulos. Pacote local SkyGuilds-SkyMP-2026.11.17-R28.zip, 78 arquivos, SHA-256 `1d8850eb5b831d10d1b3305f252012e976834159a5e081098b9f8248c3b66987`. ZIP e código permaneceram locais; somente os documentos foram preparados para publicação.

| Verificação | Evidência atual | Limite |
|---|---|---|
| Regras, bootstrap e sincronização | Suíte local automatizada, incluindo privacidade owner-only, sessão falsa, revogação e serviços aposentados | Host simulado e navegador headless |
| Preflight estático privado | Aprovado, 115 plugins | Não compara clientes |
| Recursos no host privado real | Aprovado, 151 referências e 104 tomos autorizados | Callback Staff verificado apenas quanto ao formato nesta checagem |
| Recibos de inventário | Ensaio isolado de escrita/leitura/preservação aprovado no mesmo addon do privado | Não testou recuperação após queda/reinício |
| Baú coletivo | Ensaio nativo isolado com 3 bots: membro deposita, outro retira, fechar/reabrir mantém estoque, terceiro/expulso bloqueados | Zero clientes humanos simultâneos |
| Auditoria coletiva | Dois movimentos reais no transporte nativo isolado, com quantidades/perfis corretos; recusas não geram movimentação | Não reconstrói histórico anterior à instalação |
| Instalação privada R28 | Cliente reportou versão R28; estado/inventários e 9 arquivos protegidos preservados, sem reinício | Não certifica todos os fluxos visuais |
| Estante coletiva no cliente | Nome “Baú Coletivo do Colégio” aplicado na referência carregada; audit instalado sem alterar conteúdo | Operação manual por dois jogadores pendente |
| Baú do Arquimago | Cliente real depositou um septim, fechou/reabriu e retirou, restaurando saldos | Prova desse compartimento; não de todos os quartos |
| Dez quartos | Ensaio nativo anterior com 60 ativações e transferência nos dez compartimentos; dono trocado com conteúdo preservado | Revalidar manualmente todos no layout público |
| Reentrada privada após R28 | Regressão reportada: nenhum painel abre; lista limitada de plugins corrigida com jogo fechado | Reteste humano/log do novo processo pendentes; testes anteriores não certificam esse caminho |
| Oficial | Pacote e procedimento preparados | Build/fork de destino, adaptadores reais e dois clientes ainda sem homologação |

O teste de recibos associa o relatório ao SHA-256 do addon executado. O arquivo de prova é diagnóstico do operador, não uma credencial de cliente. O módulo não supõe que um inventário sem recibo inicial tenha valor zero: verifica uma leitura nativa existente ou a prova isolada do addon exato.

A referência pública de API é o commit SkyMP `2849e676fe008925384b26f34476e382a30bd877`. O addon privado ensaiado tem SHA-256 `826a2b7560c1a34c67bb6114bb2b6d62c8d95a70b0b0a6d7d68c454d4308fc7e`. Esses dados identificam a evidência; não distribuem o binário nem garantem compatibilidade de outro fork.

O pacote inclui `VALIDATION.json` com contagens da suíte que foi executada na própria pasta de distribuição. O relatório do workspace pode conter testes adicionais de arquivos históricos e integrações privadas, excluídos do ZIP. Não confundir essas contagens. Nenhum relatório de jogador/inventário privado deve ser enviado ao acervo.
