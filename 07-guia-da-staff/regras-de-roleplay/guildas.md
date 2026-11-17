---
titulo: Guildas de Magos Alquimistas e Companions
tipo: guia-staff
status: ativo
responsaveis: [Melekyy]
atualizado: 2026-11-17
tags: [staff, guildas]
fonte_tecnica: [../../02-sistemas/guildas/README.md]
relacionados: [../progresso.md]
---

# Guildas de Magos, Alquimistas e Companions

## Resumo

Melekyy desenvolve os três grupos. A R30 está instalada no ambiente privado, com gestão de quartos retirada e portas do Arquimago preservadas. A liberação no servidor oficial ainda depende dos adaptadores do servidor e dos testes com dois jogadores reais.

**R30 privada em 17/11:** gestão de quartos retirada. O Arquimago mantém trancar/destrancar portas do Colégio, conforme esclarecimento do dev. Baú físico do cargo reposicionado e coletivo preservado, sem perder conteúdos. Os testes no Skyrim são feitos pelo próprio dev; não há novo aceite visual.

## Quem pode fazer o quê

| Função | Permissões |
|---|---|
| Staff autorizada | Nomeia ou retira os líderes pelo sistema administrativo conectado |
| Líder de cada guilda | Convida/remove membros, organiza cargos e nomeia/revoga funções |
| Arquimago | Também controla todas as portas do Colégio, retira escola e altera o cronograma |
| Professores dos Magos | Definem escola de membros sem vínculo; não gerenciam quartos |
| Bibliotecários dos Magos | Abrem a Biblioteca e fabricam tomos na bancada |
| Aluno/membro comum | Consulta seu painel, cronograma e informações permitidas; usa o baú coletivo |

O Arquimago convida pelo **ID do jogador** no campo de Membros. A entrada exige aceitar o convite. O membro não sai sozinho; o líder precisa removê-lo. Cargo com nome especial não dá poderes de gestão. Professor e bibliotecário dos Magos não acumulam um ao outro, liderança ou cargos extras. Cada professor ocupa uma escola; o vínculo pessoal de escola é separado.

## Os três grupos

- **Magos:** azul, liderados pelo Arquimago. Cinco professores, um por Alteração, Conjuração, Destruição, Ilusão e Restauração. Podem existir vários bibliotecários.
- **Alquimistas:** verde musgo, liderados pelo Mestre Alquimista. Podem existir vários professores alquímicos; não há bibliotecário.
- **Companions:** marrom escuro, liderados pelo Chefe Mercenário. Podem existir vários instrutores e um Forjador Mestre. A forja configurada é exclusiva do Forjador Mestre.

## Escolas e insígnias

Em **Y → Professores**, professor ou Arquimago seleciona um membro sem escola e define seu vínculo. Só é permitida uma escola por pessoa. Só Arquimago retira o vínculo; para mudar de escola, retire antes.

O membro recebe a insígnia correspondente do Elemental Vision II quando seu inventário estiver disponível. Não pode descartar, guardar, dar, trocar ou deixar roubar. A insígnia permanece na morte. Retirada da escola ou expulsão encerra o vínculo e remove o item pelo sistema de inventário. Essas proteções precisam estar integradas ao sistema de morte do servidor oficial.

## Biblioteca

Só Arquimago e bibliotecários atuais veem a interação da bancada no Arcanaeum, abrem e fabricam. Professor e membro comum sem essa permissão não abrem nem para consultar. O Arquimago não precisa receber outro cargo.

Primeiro escolha a classe; dentro dela aparecem os tomos autorizados. A base privada atual tem 104 tomos em 12 classes. Há busca, filtro de plugin e indicação de classe, categoria e efeito. Nomes/descrições estão em português no painel; o nome do item no inventário continua dependendo do plugin.

Cada tomo custa **5 papéis e 1 pedra da alma**, vazia ou preenchida. Pedras menores são usadas primeiro. O lote leva **3 segundos**, com progresso e animação. Cancelar, fechar, afastar-se ou perder a função interrompe; os materiais só são cobrados ao concluir com entrega validada. Os tomos seguem a leitura normal do jogo.

## Portas do Colégio

Somente o **Arquimago** mantém a aba de trancar/destrancar portas, uma por vez ou todas. A tranca vale dos dois lados, inclusive para ele. São 68 passagens; professores, bibliotecários e alunos não controlam as trancas gerais.

**O sistema de quartos foi retirado.** Não há aba Quartos, atribuição de aluno, professor administrando quartos nem baú particular por quarto. Não existe mais a restrição de septins dos quartos. Os móveis e seus conteúdos foram preservados e voltam às regras normais do jogo, respeitando as trancas gerais do Arquimago.


## Baú do Arquimago e baú coletivo

Na lateral dos aposentos, o **Baú do Arquimago**, um baú físico reposicionado com seus pertences preservados, só permite abrir/depositar/retirar a quem ocupa o cargo. Ao trocar o líder, o sucessor acessa o mesmo conteúdo. Professores e bibliotecários não ganham acesso a esse baú.

No Arcanaeum, a estante indicada é o **Baú Coletivo do Colégio**. Todos os membros atuais dos Magos usam o mesmo estoque: aluno, professor, bibliotecário e Arquimago. Externos e expulsos não acessam. É preciso estar perto e na mesma área.

Em **Registros**, somente o Arquimago vê quem depositou ou retirou, com nome e ID, item, quantidade, horário e quantidade anterior/final no baú. O log confirma a movimentação pelo servidor; pedidos recusados ou sem transferência não viram depósito/retirada. Começa na ativação do log, sem recuperar movimentos antigos. Faz parte do limite de 100 registros recentes exibidos por guilda e 5.000 guardados no sistema.

## Cronograma e registros de gestão

O cronograma fica na **Visão geral**, no lugar do antigo cartão com nomes de bibliotecários. Só Arquimago adiciona/remove aula, escolhendo escola, dia da semana ou data e horário de Brasília. Membros consultam. Não há avisos automáticos. O contador mostra o total de bibliotecários; a aba Cargos permanece.

Os registros de gestão são exclusivos do líder: nome e ID de quem agiu e de quem foi afetado, função/cargo/escola/quarto, substituições e antes/depois quando aplicável. A remoção de um membro é registrada. Os nomes históricos não desaparecem quando a pessoa é renomeada ou removida.

## Perguntas comuns

- **Livros em branco estão disponíveis?** Não. O sistema autoral/editor foi guardado; esta entrega mantém os tomos de magia.
- **Conjuração faz parte de Guildas?** Não. Tem projeto/runtime próprios e não é instalada pelo pacote de Guildas.
- **Já foi homologado no oficial?** Não. Testes automatizados, bots nativos e um cliente privado dão evidências diferentes de dois jogadores reais no servidor de destino.
- **Pode instalar em qualquer SkyMP sem conferir nada?** Não. O dev precisa conectar inventário, morte, Staff e Classes e conferir mods/ordem. O instalador recusa a versão completa quando faltam recursos.

## Referências técnicas

- [Escopo completo](../../02-sistemas/guildas/escopo.md)
- [Instalação e checklist multiplayer](../../02-sistemas/guildas/integracao-skymp.md)
- [Validação e pendências](../../02-sistemas/guildas/validacao-skymp.md)
