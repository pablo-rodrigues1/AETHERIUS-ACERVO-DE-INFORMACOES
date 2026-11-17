---
titulo: Escopo completo de Guildas R28
tipo: especificacao
status: ativo
responsaveis: [Melekyy]
atualizado: 2026-11-17
tags: [guildas, skymp, integracao]
relacionados: [README.md, diario.md]
---

# Escopo atual de Guildas — R28

Responsável: **Melekyy**. Revisão: **17/11/2026**. Implementação privada e pacote de integração; homologação oficial e conferência com dois clientes humanos pendentes.

## Guildas e permissões

| Guilda | Cor | Líder | Funções | Vínculo para Classes |
|---|---|---|---|---|
| Magos | Azul | Arquimago | Um professor por escola; vários bibliotecários | `AlunoColegioWinterhold` |
| Alquimistas | Verde musgo | Mestre Alquimista | Vários professores alquímicos | `AlunoGuildaAlquimistas` |
| Companions | Marrom escuro | Chefe Mercenário | Vários instrutores; um Forjador Mestre | `MembroGuildaCompanions` |

A Staff nomeia/revoga líderes pela API do servidor conectada à autorização administrativa real. Não é necessário vínculo prévio; a nomeação estabelece filiação e os mesmos direitos de vínculo. Nenhum botão do navegador concede liderança. Só o líder convida/remove membros, cria/renomeia/exclui cargos, atribui cargos e nomeia/revoga funções. O convite precisa de aceite do destinatário. Membros não saem voluntariamente. Cargos personalizados não concedem poderes de gestão.

Nos Magos, professor e bibliotecário são incompatíveis entre si, com liderança e com cargos personalizados. Um professor ocupa somente um posto. O vínculo pessoal de escola é separado e permanece permitido. Nos outros grupos, professores/instrutores podem ser diversos. Somente o Forjador Mestre atual acessa a Skyforge configurada.

## Painel e escolas

O painel abre em **Y**, com mouse, listas de seleção internas e fechamento por Esc. Todos os membros atuais consultam as informações que sua permissão permite. As três guildas conservam seus temas e compartilham molduras, brasões e cartões inspirados nos painéis de Grupo e Reis.

As escolas são Alteração, Conjuração, Destruição, Ilusão e Restauração. Professor ou Arquimago escolhe um membro e uma única escola. Só Arquimago retira o vínculo; para trocar, retire antes. A entrega automática da insígnia correspondente de **Elemental Vision II** aguarda inventário disponível. Ela não pode ser descartada, guardada, dada, trocada ou roubada; o contrato de morte preserva a insígnia. Retirada de escola/expulsão revoga o item pelo diário de inventário. Isso exige o plugin real e o adaptador de morte compatível.

Na Visão geral, o terceiro contador mostra bibliotecários. O cartão de cronograma permite ao Arquimago adicionar/remover aulas com escola, dia da semana ou data e horário `HH:mm` de Brasília. Todos os membros leem; até 100 aulas, sem duplicidade da mesma escola/dia/horário. Não dispara alarmes.

## Biblioteca e fabricação

- A Biblioteca existe na bancada/blip do Arcanaeum. Apenas Arquimago e bibliotecários atuais recebem a interação, abrem, consultam e fabricam. Professor ou membro comum sem essa autoridade não abre nem consulta. Não existe Biblioteca de consulta separada no painel Y comum.
- Primeiro escolhe-se a classe; dentro dela aparecem os tomos. Há busca por nome traduzido/original, filtro de plugin e retorno à seleção de classes.
- O exportador varre os plugins carregados, respeitando overrides, e identifica BOOK que ensina SPEL. O servidor aplica a política das classes/facções do GameplayCore em `config/spell-policy.json`; não disponibiliza todos os livros instalados indiscriminadamente. A base privada atual contém **104 tomos autorizados em 12 classes**.
- Nomes e descrições do catálogo atual estão traduzidos no painel, com classe, categoria/tier e efeito base disponível. A tradução não renomeia o item nativo do inventário. Uma mudança na lista de magias do GameplayCore exige revisar política e traduções e regenerar o catálogo.
- Cada tomo custa **5 papéis e 1 pedra da alma**, vazia ou preenchida. Pedras menores são usadas primeiro. Fabricação leva **3 segundos por lote**, com progresso/animação e cancelamento. Fechar, afastar-se, cancelar ou perder a função interrompe; o servidor revalida materiais, função e proximidade antes de cobrar e entregar pelo journal.
- A leitura/consumo dos tomos segue o jogo. Não se depende de mod de leitura por mana, nem se implementa outro sistema de aprendizado nesta entrega.

## Portas, dez quartos e pertences

Somente Arquimago usa o controle geral de **Portas do Colégio**, individual ou em lote. O catálogo atual possui **68 passagens / 86 referências**, incluindo faces de teleporte e acessos da ponte. A tranca impede ativação pelos dois lados, inclusive operador/professor/dono do quarto. É preciso destrancar. Três referências originalmente desativadas são preservadas. Essa proteção de portas não é uma barreira contra comandos administrativos de teleporte.

Os **dez quartos** do Hall of Attainment usam móveis existentes: seis baús, três estantes e um armário. Não se criam baús onde não há. Professor/Arquimago atribui ou libera um quarto; um dono por quarto, um quarto por membro. O dono só abre sua porta por proximidade; professores e Arquimago também operam portas de quartos por proximidade. Outros alunos não abrem quartos alheios. A tranca geral prevalece.

O compartimento atribuído recebe o nome do dono e só o dono acessa seus itens; quando livre, a administração pode acessá-lo. **Não é permitido depositar septins nos compartimentos dos quartos**, inclusive pelo Arquimago/professores quando têm acesso. Itens comuns continuam permitidos. Septins que já existiam não são apagados e podem ser retirados pelo dono autorizado. Trocar/liberar o quarto é permitido mesmo cheio: todo o conteúdo existente fica no mesmo móvel, para o novo dono. O anterior perde acesso ao trocar. Ex-membro ainda cadastrado como dono pode retirar, sem depositar, até a troca/liberação. Não há devolução ou separação automática do estoque. A restrição de depósito dos quartos não altera o baú coletivo nem o Baú do Arquimago.

O **Baú do Arquimago** nos aposentos é exclusivo do líder atual, inclusive depósito/retirada. O sucessor passa a acessar o mesmo conteúdo. É independente dos quartos e não cria uma vinculação pessoal.

O **Baú Coletivo do Colégio** usa a estante existente identificada no Arcanaeum. Qualquer membro atual dos Magos, de aluno a Arquimago, abre/deposita/retira no mesmo estoque físico. Outros jogadores e expulsos não acessam. Não é um baú por jogador. Os Registros do Arquimago mostram depósitos e retiradas confirmados, com jogador/ID, item, quantidade, compartimento, horário e antes/depois da quantidade no baú. Pedidos recusados ou sem alteração correspondente nos dois inventários não viram movimentações.

Para Alquimistas e Companions, o módulo conserva o suporte legado de pertences pessoais para líder, professores/instrutores e oficial especial autorizado, com itens/variantes e septins pelo journal. Esse suporte pode aparecer como Meu baú nesses painéis e não equivale ao mapa de dez quartos dos Magos. Após perder a função, quem já possui estoque conserva retirada, sem novos depósitos; sucessor de função não recebe esse estoque pessoal. Pontos físicos adicionais dessas guildas precisam de configuração própria. No painel dos Magos a aba de baú foi retirada.

## Persistência, sincronização e registros

Estado de filiação, funções, escolas, cronograma, atribuições, portas e auditoria é persistido em JSON pelo servidor. Inventários físicos pertencem ao mundo persistente SkyMP. Backup/rollback precisa preservar ambos; não substituir um inventário por cópia antiga para corrigir autorização.

Modelos de interface são visíveis somente ao dono, com nonce por sessão, revisão nas operações aplicáveis e revalidação no servidor. Animação de fabricação é publicada a dono/vizinhos. Nomes de compartimentos e selagem de portas são reaplicados após streaming; chamadas nativas usam `update` e referências readquiridas, sem reter handles antigos. O relógio do cliente não decide a conclusão/cobrança.

Só o líder lê Registros de sua guilda: nomes e IDs confiáveis de autor/envolvidos, função/cargo, substituído, escola, quarto, portas e antes/depois quando aplicável. Nomes ficam preservados após renomeação/expulsão. São apresentados os 100 registros mais recentes por guilda e retidos até 5.000 no estado global. O log do baú coletivo compartilha essa retenção. Não há reconstrução retroativa dos depósitos anteriores à ativação do log.

## Fora da entrega ativa

Livros autorais/em branco, editor, assinatura, cópias e posicionamento estão guardados no arquivo local e desativados. Não acompanham este pacote. Conjuração pertence a outro projeto/runtime e não é importada, instalada ou modificada por Guildas. Casas, bancos, impostos, murais e seus operadores privados também ficam fora. Não ativar funcionalidades arquivadas pelo instalador atual.

Documentação central: [Guildas no Acervo Aetherius](https://github.com/pablo-rodrigues1/AETHERIUS-ACERVO-DE-INFORMACOES/blob/main/02-sistemas/guildas/README.md).
