---
titulo: Guildas de Magos, Alquimistas e Companions
tipo: guia-staff
status: ativo
responsaveis: [Melekyy]
atualizado: 2026-11-15
tags: [staff, guildas]
fonte_tecnica: [../../02-sistemas/guildas/README.md]
relacionados: [../progresso.md]
---

# Guildas de Magos, Alquimistas e Companions

## Resumo

Melekyy está desenvolvendo as Guildas. As regras abaixo descrevem a implementação local; a liberação no servidor oficial ainda precisa ser confirmada.

## Em uma frase

Guildas são grupos de jogadores com líder, membros e funções próprias para Magos, Alquimistas e Companions.

## Como funciona

1. A Staff escolhe o líder por uma integração autorizada, que ainda precisa ser conectada ao painel oficial.
2. O líder convida os jogadores. Cada convidado precisa aceitar para entrar.
3. O líder organiza membros, cargos e funções. Criar um cargo com um nome especial não dá poderes de líder.
   O membro não pode sair sozinho: precisa ser removido pelo líder. Essa remoção fica nos Registros com o nome e o ID de quem removeu e de quem foi removido.
4. Nos Magos, o líder ou um professor define a escola do estudante. Há um professor por escola e podem existir vários bibliotecários. Somente o líder nomeia ou revoga cada um.
5. Arquimago e bibliotecários nomeados abrem e fabricam pela **Biblioteca** da bancada. Professor ou membro comum sem essa autoridade não recebe o blip nem abre para consultar. O Arquimago não precisa acumular bibliotecário. Cada tomo exige 5 papéis e 1 pedra da alma.
6. Cada tomo indica a classe e sua categoria, como Aprendiz ou Mestre. Quando disponível, mostra também o efeito base da magia. Esses textos ajudam na consulta; não dão acesso automático à magia nem mudam seus requisitos.
7. A Biblioteca abre primeiro a escolha de classe. Ao clicar em uma classe, aparecem seus tomos. Use Todas as classes para voltar. Um tomo compartilhado pode aparecer em mais de uma classe.
8. Os três painéis receberam o mesmo acabamento visual dos painéis de Grupo e Reis, mantendo suas cores: azul para Magos, verde musgo para Alquimistas e marrom escuro para Companions. Os botões e permissões continuam com as mesmas funções.
9. A fabricação leva três segundos por lote e mostra o progresso. Espere perto da bancada. Fechar, cancelar, afastar-se ou perder a função interrompe sem gastar os materiais; a cobrança acontece quando os tomos são entregues.
10. Os 104 tomos atuais aparecem com nome e descrição em português na Biblioteca. A pesquisa aceita o nome traduzido e o original. Essa tradução é do painel; o nome nativo do item no inventário continua vindo do plugin.
11. Só a liderança acessa **Registros**. Nas novas ações, aparece quem fez, quem foi afetado e o que mudou, com nomes, IDs de perfil, data e hora. Nomeações informam a função; substituições identificam o ocupante anterior. Renomeações e mensagens mostram o antes e o depois. Excluir um cargo mostra de quais membros ele foi retirado.
12. Líder e professores selecionam um membro e sua escola em **Professores**. A R21 ativa no privado entrega automaticamente a insígnia correspondente do Elemental Vision quando o inventário do jogador estiver disponível. A insígnia é pessoal: não pode ser descartada, guardada, dada, trocada ou roubada e permanece na morte/recuperação. A expulsão encerra o vínculo e retira a insígnia. O servidor privado confirmou a entrega e a gravação de uma unidade no personagem de teste; a conferência visual e a homologação no oficial continuam pendentes.
13. Professor e bibliotecário dos Magos não podem ser a mesma pessoa. Na R23, também não acumulam cargos personalizados ou liderança. Professor ocupa um único posto de escola. O vínculo pessoal com a escola e sua insígnia são preservados. Retire os cargos extras antes de nomear; para atribuir outros cargos ou promover a líder, revogue a função primeiro. A regra também vale fora da interface.
14. Na R25 privada, somente o Arquimago acessa **Y → Portas do Colégio**. Pode trancar/destrancar todas ou uma passagem, procurar pelo nome e filtrar por área. Trancar impede entrar e sair pelos dois lados, inclusive o próprio líder e os professores; é preciso destrancar para atravessar. Professores, bibliotecários e membros comuns não têm essa aba de controle geral.
15. São 68 passagens mapeadas no privado, incluindo o portão da ponte. Portas originalmente desativadas continuam assim. Os Registros mostram quem alterou, quais passagens e o antes/depois. A instalação manteve as portas no estado anterior; não tranca tudo automaticamente. Teste de travessia humana e liberação no oficial ainda precisam ser confirmados.

## Exemplo prático

### Quartos, escola e aulas na R25 privada

- Em **Y → Quartos**, professor ou Arquimago escolhe um dos dez quartos e um aluno. Cada aluno pode ter apenas um quarto. O aluno abre somente sua porta e precisa estar perto; professores e Arquimago também abrem por perto. A tranca geral do Arquimago bloqueia a passagem até ser desfeita.
- O compartimento particular já existe no quarto: baú, estante ou armário, com nome do dono. Só o dono acessa seus pertences. A troca ou liberação pode acontecer sem esvaziar: os itens e septins ficam no mesmo móvel, acessíveis ao novo dono. O anterior perde acesso após a troca. Não acrescentar baús nos quartos sem um.
- Em **Professores**, somente o Arquimago vê a opção de retirar uma escola. O membro continua na guilda; a insígnia do vínculo é retirada quando o inventário estiver disponível. Professor pode definir escola para quem está sem uma, mas não retirá-la.
- Em **Visão geral**, o contador mostra quantos bibliotecários existem. A aba Cargos continua. O cartão **Cronograma de aulas** mostra escola, dia e horário a todos os membros. Só Arquimago preenche e usa **Adicionar aula** ou **Remover aula**; pode escolher dia da semana ou data. Os horários são de Brasília. Não há alarmes automáticos.


Um Arquimago convida uma jogadora para os Magos. Depois que ela aceita, um professor define sua escola. Receber um cargo personalizado, como um título de estudo, não permite que ela convide ou expulse outros membros.

## Tabela rápida

| Grupo | Liderança | Funções específicas |
|---|---|---|
| Magos | Arquimago | Professores das cinco escolas e vários bibliotecários |
| Alquimistas | Mestre Alquimista | Professores alquímicos |
| Companions | Chefe Mercenário | Instrutores e Forjador Mestre |
| Fabricação de um tomo | Arquimago ou bibliotecário autorizado | 5 papéis e 1 pedra da alma |

## Perguntas comuns de jogadores

- **Já está disponível no servidor oficial?** Ainda depende da integração e dos testes no oficial.
- **Posso me tornar líder pelo painel?** A escolha do líder cabe à Staff autorizada.
- **Posso sair da guilda por conta própria?** Não. O líder precisa remover você. A ação fica registrada; uma tentativa recusada não conta como saída.
- **Um cargo personalizado permite administrar a guilda?** Não. A administração pertence ao líder.
- **Os livros em branco estão disponíveis?** O registro local atual informa que esse sistema foi guardado. Os tomos de magia continuam sendo uma função separada.
- **A conjuração faz parte do painel?** Não. O controlador privado pertence ao SkySync. Os arquivos candidatos foram movidos para um projeto separado, e o pacote atual dos painéis não inclui seus scripts. A escola de Conjuração e seus tomos continuam no painel como conteúdo.
- **O novo ocupante de um cargo recebe os bens do anterior?** Não. Os pertences pessoais são preservados para seu dono.
- **Posso buscar a magia pela classe?** Sim. A busca da Biblioteca também reconhece o nome da classe e sua categoria.
- **Por que algum número não aparece na descrição?** Alguns registros dos mods omitem alcance, percentual ou custo. O painel informa o que está disponível, sem inventar esses valores.
- **Posso fabricar só por ser líder ou professor?** O Arquimago pode; professor sem autoridade de líder não pode. Bibliotecários nomeados também podem. Um título personalizado não concede acesso.
- **Posso ser professor e bibliotecário ao mesmo tempo?** Não. O líder deve revogar a função anterior antes de nomear a outra.
- **Posso ser professor/bibliotecário e receber outro cargo?** Não. Só pode manter a função e o vínculo com sua escola. Um cargo personalizado com nome de escola não é exceção. Para receber outros cargos, a função precisa ser revogada antes.
- **Tranquei tudo e também fiquei sem passar. Isso é esperado?** Sim. Abra a aba Portas do Colégio e destranque a passagem ou todas. Não há liberação automática de saída para o operador.
- **O que são os integrantes fictícios?** São cadastros de teste apenas no privado. A tag visual foi retirada por pedido do dev; a identificação interna permanece. Quatro foram acrescentados aos três anteriores, sem contas ou atores reais. Não anunciar esses cadastros como jogadores do oficial.
- **O que significa o ID nos Registros?** É o identificador do perfil do jogador. O registro guarda os nomes no momento da ação, mesmo se o cargo for renomeado ou o membro sair depois.
- **Por que um registro antigo não mostra a função ou o nome?** O histórico anterior não gravava todos esses detalhes. A atualização preserva os dados existentes e informa a limitação; as novas ações passam a guardar o detalhamento completo.

## O que a Staff pode e não pode fazer

- Nomear ou remover líderes quando a autorização estiver conectada ao sistema oficial.
- Encaminhar relatos sobre convites, funções, tomos e pertences para Melekyy.
- Anunciar a disponibilidade aos jogadores depois da confirmação da implantação oficial.
- Respeitar os pertences pessoais na troca de liderança e de funções.

## Quer o detalhe técnico?

Veja [Guildas (SkyGuilds)](../../02-sistemas/guildas/README.md).
