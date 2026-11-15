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
5. Somente bibliotecários nomeados fabricam pela **Biblioteca**, aberta pela bancada. O Arquimago também precisa ser nomeado bibliotecário para fabricar. Cada tomo exige 5 papéis e 1 pedra da alma.
   Na interface R21 aplicada no privado, só bibliotecários abrem a bancada, inclusive para consultar. O líder nomeia bibliotecários pela aba Professores mesmo sem poder usar o craft.
6. Cada tomo indica a classe e sua categoria, como Aprendiz ou Mestre. Quando disponível, mostra também o efeito base da magia. Esses textos ajudam na consulta; não dão acesso automático à magia nem mudam seus requisitos.
7. A Biblioteca abre primeiro a escolha de classe. Ao clicar em uma classe, aparecem seus tomos. Use Todas as classes para voltar. Um tomo compartilhado pode aparecer em mais de uma classe.
8. Os três painéis receberam o mesmo acabamento visual dos painéis de Grupo e Reis, mantendo suas cores: azul para Magos, verde musgo para Alquimistas e marrom escuro para Companions. Os botões e permissões continuam com as mesmas funções.
9. A fabricação leva três segundos por lote e mostra o progresso. Espere perto da bancada. Fechar, cancelar, afastar-se ou perder a função interrompe sem gastar os materiais; a cobrança acontece quando os tomos são entregues.
10. Os 104 tomos atuais aparecem com nome e descrição em português na Biblioteca. A pesquisa aceita o nome traduzido e o original. Essa tradução é do painel; o nome nativo do item no inventário continua vindo do plugin.
11. Só a liderança acessa **Registros**. Nas novas ações, aparece quem fez, quem foi afetado e o que mudou, com nomes, IDs de perfil, data e hora. Nomeações informam a função; substituições identificam o ocupante anterior. Renomeações e mensagens mostram o antes e o depois. Excluir um cargo mostra de quais membros ele foi retirado.
12. Líder e professores selecionam um membro e sua escola em **Professores**. A R21 ativa no privado entrega automaticamente a insígnia correspondente do Elemental Vision quando o inventário do jogador estiver disponível. A insígnia é pessoal: não pode ser descartada, guardada, dada, trocada ou roubada e permanece na morte/recuperação. A expulsão encerra o vínculo e retira a insígnia. O servidor privado confirmou a entrega e a gravação de uma unidade no personagem de teste; a conferência visual e a homologação no oficial continuam pendentes.

## Exemplo prático

Um Arquimago convida uma jogadora para os Magos. Depois que ela aceita, um professor define sua escola. Receber um cargo personalizado, como um título de estudo, não permite que ela convide ou expulse outros membros.

## Tabela rápida

| Grupo | Liderança | Funções específicas |
|---|---|---|
| Magos | Arquimago | Professores das cinco escolas e vários bibliotecários |
| Alquimistas | Mestre Alquimista | Professores alquímicos |
| Companions | Chefe Mercenário | Instrutores e Forjador Mestre |
| Fabricação de um tomo | Bibliotecário autorizado | 5 papéis e 1 pedra da alma |

## Perguntas comuns de jogadores

- **Já está disponível no servidor oficial?** Ainda depende da integração e dos testes no oficial.
- **Posso me tornar líder pelo painel?** A escolha do líder cabe à Staff autorizada.
- **Posso sair da guilda por conta própria?** Não. O líder precisa remover você. A ação fica registrada; uma tentativa recusada não conta como saída.
- **Um cargo personalizado permite administrar a guilda?** Não. A administração pertence ao líder.
- **Os livros em branco estão disponíveis?** O registro local atual informa que esse sistema foi guardado. Os tomos de magia continuam sendo uma função separada.
- **O novo ocupante de um cargo recebe os bens do anterior?** Não. Os pertences pessoais são preservados para seu dono.
- **Posso buscar a magia pela classe?** Sim. A busca da Biblioteca também reconhece o nome da classe e sua categoria.
- **Por que algum número não aparece na descrição?** Alguns registros dos mods omitem alcance, percentual ou custo. O painel informa o que está disponível, sem inventar esses valores.
- **Posso fabricar só por ser líder ou professor?** A fabricação dos tomos é exclusiva dos bibliotecários nomeados. Um título personalizado não substitui essa função.
- **O que são os três integrantes fictícios?** São registros identificados de teste no privado. Não representam jogadores, contas ou personagens reais e não devem ser anunciados no oficial.
- **O que significa o ID nos Registros?** É o identificador do perfil do jogador. O registro guarda os nomes no momento da ação, mesmo se o cargo for renomeado ou o membro sair depois.
- **Por que um registro antigo não mostra a função ou o nome?** O histórico anterior não gravava todos esses detalhes. A atualização preserva os dados existentes e informa a limitação; as novas ações passam a guardar o detalhamento completo.

## O que a Staff pode e não pode fazer

- Nomear ou remover líderes quando a autorização estiver conectada ao sistema oficial.
- Encaminhar relatos sobre convites, funções, tomos e pertences para Melekyy.
- Anunciar a disponibilidade aos jogadores depois da confirmação da implantação oficial.
- Respeitar os pertences pessoais na troca de liderança e de funções.

## Quer o detalhe técnico?

Veja [Guildas (SkyGuilds)](../../02-sistemas/guildas/README.md).
