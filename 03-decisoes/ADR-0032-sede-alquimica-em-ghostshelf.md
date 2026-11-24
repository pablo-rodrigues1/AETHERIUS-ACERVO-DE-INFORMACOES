---
titulo: Sede alquímica em Ghostshelf
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-23
tags: [guildas, alquimistas, portas, ponte, armazenamento]
relacionados: [../02-sistemas/guildas/alquimistas-ghostshelf.md, ../02-sistemas/guildas/README.md, ADR-0026-cenario-ghostshelf-isolado-no-privado.md]
---

# ADR-0032 — Sede alquímica em Ghostshelf

## Resumo

Integrar a instalação privada existente de Ghostshelf Keep ao painel verde musgo dos Alquimistas, com portas, dois baús físicos e ponte, sem alterar os mods de origem ou a gestão dos Magos.

## Contexto

O dev pediu a mesma premissa do painel dos Magos para a sede alquímica. O mapa já está instalado, mas a presença de seus scripts não comprova execução/sincronização no SkyMP.

## Opções consideradas

| Opção | Prós | Contras |
|---|---|---|
| Reativar scripts originais sem autoridade de guilda | Reusa o mod | Não valida cargo, sessão, persistência ou sincronização |
| Criar outros objetos e modificar o mod de origem | Permite outro layout | Contraria isolamento e duplica móveis/ponte |
| Gerenciar as referências existentes no módulo de Guildas | Mantém o cenário e as regras no servidor | Exige catálogo conferido e aplicação de geometria no cliente |

## Decisão

- Usar as 13 passagens/16 referências conferidas, agrupando portas de teleporte nos dois sentidos. Acesso das portas restrito aos membros atuais; tranca geral/individual exclusiva do Mestre. Uma passagem trancada bloqueia inclusive o líder, até sua abertura pelo painel.
- Usar dois baús CONT existentes: coletivo dos membros e particular da liderança atual. Trocar o líder não transfere nem apaga o estoque. Depositar/retirar passa pelas permissões do servidor e pelo transporte nativo. Auditoria confirmada do coletivo pertence aos registros dos Alquimistas.
- A aba `Portas e ponte` aparece somente ao líder. Pedidos do painel exigem sessão/token atual e revisão; alavancas físicas exigem líder online, proximidade e personagem disponível.
- A ponte usa as duas poses STAT originais e os dois comandos ACTI. O servidor guarda o estado e replica a todos os clientes, inclusive não membros. Este build não instancia as peças STAT como objetos nativos; o cliente aplica Enable/Disable nas peças reais durante update e reaplica ao carregar a área. Os controles ACTI são tratados no servidor, sem delegar à lógica original da ponte.
- Estado e registros próprios dos Alquimistas. Não renomear/remapear IDs dos plugins, não alterar o Aetherius original, não reinstalar quartos ou conjuração nas Guildas.

## Consequências

Registros nativos, permissões, mensagens e persistência podem ser verificados sem carregar o mundo dos jogadores. Colisão/aparência no Skyrim e visão simultânea de dois jogadores exigem teste humano. Não tratar ensaio com bots como homologação oficial.
