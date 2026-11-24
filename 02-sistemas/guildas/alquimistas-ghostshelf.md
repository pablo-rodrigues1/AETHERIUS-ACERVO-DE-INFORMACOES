---
titulo: Alquimistas em Ghostshelf Keep
tipo: especificacao
status: ativo
responsaveis: [Melekyy]
atualizado: 2026-11-23
tags: [guildas, alquimistas, skymp, ponte]
relacionados: [README.md, integracao-skymp.md, ../../03-decisoes/ADR-0032-sede-alquimica-em-ghostshelf.md]
---

# Alquimistas em Ghostshelf Keep

## Resumo

Sede privada da Guilda dos Alquimistas ligada ao painel em Y, com verde musgo e a mesma estrutura de membros, cargos, professores e registros. Ghostshelf Keep 1.5 e seu patch privado precisam estar nos clientes e servidor na mesma ordem. Não é uma implantação oficial nem redistribuição do mod.

## Permissões

| Recurso | Mestre | Professor | Membro | Não membro |
|---|---|---|---|---|
| Consultar painel e filiação | Sim | Sim | Sim | Não |
| Gerenciar cargos/membros/professores | Sim | Não | Não | Não |
| Abrir portas destrancadas, perto delas | Sim | Sim | Sim | Não |
| Passar por portas trancadas | Não | Não | Não | Não |
| Trancar/destrancar uma passagem ou todas | Sim | Não | Não | Não |
| Erguer/baixar ponte pelo painel ou controle físico próximo | Sim | Não | Não | Não |
| Depositar/retirar no coletivo | Sim | Sim | Sim | Não |
| Baú particular da liderança | Sim | Não | Não | Não |
| Consultar histórico da guilda | Sim | Não | Não | Não |

Os cargos personalizados não concedem autoridade. Uma troca de líder altera o acesso ao baú particular, com o estoque no mesmo compartimento. Expulsão revoga os acessos imediatamente.

## Referências existentes

Catálogo `config/alchemy-hall.json`: **13 passagens e 16 referências**, incluindo os três pares de teleporte, portão e portas interiores. As referências são verificadas pelo tipo de base e pelo destino real antes de instalar.

| Objeto | Chave estável no plugin EnemyModsGhostshelfKeep.esp |
|---|---|
| Baú coletivo, aposentos do salão | `907cf4` |
| Baú do Mestre, aposentos do salão | `90ce2f` |
| Pose erguida da ponte | `95939a` |
| Pose baixada da ponte | `9593d6` |
| Controle de erguer | `96da62` |
| Controle de baixar | `96da63` |

Nenhum móvel novo foi criado nem estoque transferido. O nome de cada baú é reaplicado no cliente durante update/carregamento. Os depósitos/retiradas do coletivo só viram log após conferir as variações reais no inventário do jogador e do compartimento.

## Estado e sincronização

`state.halls.alchemists` guarda trancas, ponte e revisão. Pedidos do painel usam token da sessão e revisão atual; o controle físico também confere distância e disponibilidade do jogador. O servidor bloqueia ativação das passagens conforme cargo/filiação/tranca. O estoque utiliza transporte e persistência nativos do SkyMP.

`skyGuildsHallWorldV1` entrega portas e visibilidade das peças a cada cliente, inclusive jogadores de fora, para verem o mesmo estado físico. O cliente aplica em update e reaplica após streaming; não retém ponteiros nativos. Os dois meshes STAT não são `MpObjectReference` neste build; não se tenta gravar neles via `mp.set`. Os dois ACTI continuam sob autoridade nativa do servidor.

## Validação e limites

Ensaio nativo isolado com quatro bots e 128 pacotes de ativação aprovou trancas nos dois sentidos, teleporte após abrir, controle da ponte, depósito/reabertura/retirada por outro membro, bloqueio de não membro e troca de liderança com estoque preservado. Estado das peças da ponte foi conferido na mensagem de cliente; controles ACTI foram acionados pelo transporte nativo.

Testes de interface verificaram o tema, comandos com token/revisão atuais e ausência de controles do Mestre para membros. O teste visual não ocorreu no Skyrim: colisão, carregamento de área, cursor, baús e dois clientes humanos continuam pendentes para o dev. Registros/estado usados no ensaio são temporários e não são o mundo dos jogadores.
