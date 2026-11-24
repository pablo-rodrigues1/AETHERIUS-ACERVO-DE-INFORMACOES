---
titulo: Irmandade Sombria nas duas sedes e Mãe Noite
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-24
tags: [guildas, irmandade-sombria, portas, mae-noite]
relacionados: [../02-sistemas/guildas/irmandade-sombria.md, ../02-sistemas/guildas/mae-noite-frases.md, ADR-0032-sede-alquimica-em-ghostshelf.md]
---

# ADR-0036 — Irmandade Sombria nas duas sedes e Mãe Noite

## Resumo

Criar o painel da Irmandade para Falkreath e Dawnstar, com autorização atual da guilda nas Portas Negras, baús por sede e 200 falas de texto da Mãe Noite apenas ao Ouvinte.

## Contexto

As portas de entrada ocultas não são instanciadas como MpObjectReference neste SkyMP. A presença dos scripts vanilla não garante acesso, resposta por vínculo ou sincronização. A Mãe Noite é nesta etapa uma conversa ambiental; o dev decidirá depois o sistema definitivo.

## Opções consideradas

| Opção | Benefício | Limite |
|---|---|---|
| Dar quests/facções vanilla indiscriminadamente | Tenta liberar os diálogos | Muda a campanha e não impõe a filiação atual |
| Deixar o cliente decidir acesso | Fácil apresentação | Não impede pedidos sem autorização |
| Pergunta e passagem autorizadas no servidor | Revalida membro, tranca e distância | Precisa de interação própria nas portas ocultas |

## Decisão

- Acrescentar a guilda brotherhood e o vínculo calculado MembroIrmandadeSombria. Staff nomeia a liderança pelo callback existente. Só o Ouvinte gerencia cargos/membros e trancas; cargos personalizados seguem intactos.
- Identificar os dois santuários nos registros vencedores: 18 passagens/21 referências. Duas referências externas originalmente ocultas são geometria/entrada própria do cliente; as demais são nativas. Todas compartilham a mesma autoridade de tranca no servidor.
- Ao interagir perto da Porta Negra, somente membro atual recebe a pergunta e a resposta certa daquela sede. Confirmar exige token, filiação, distância e passagem destrancada; usar chegada XTEL real sem conceder estágios de quests. Saídas também respeitam a tranca.
- Cada sede tem coletivo e baú do Ouvinte, estoques distintos. Reusar cinco baús existentes entre as novas guildas; criar apenas o coletivo adicional de Falkreath com modelo CONT existente. Guardar a referência dinâmica por chave, posicionar só enquanto nova/desativada e reutilizar sem limpar conteúdo nas próximas cargas.
- Mostrar a Mãe Noite por interação próxima aos pontos dos sarcófagos existentes. Só o Ouvinte recebe uma fala por vez. Biblioteca de 200 textos originais em português, sorteio sem repetição antes de esgotar o ciclo e fila persistente, com intervalo mínimo entre pedidos. Revogar a liderança corta o acesso.
- Não gerar contratos, vítimas, recompensas, quests ou áudio nesta fase. As frases não são gravações ou transcrições vanilla. Títulos fixos de apresentação: Ouvinte, Orador e Assassino da Irmandade.

## Consequências

O estado de portas/visibilidade é replicado para todos; perguntas e falas usam a propriedade privada do dono. Testes nativos e headless não são aceite de colisão, streaming, cursor ou dois humanos no jogo. O servidor principal e o Skyrim permanecem fechados durante esta preparação.
