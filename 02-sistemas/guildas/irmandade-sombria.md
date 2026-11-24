---
titulo: Irmandade Sombria e seus santuários
tipo: referencia
status: ativo
responsaveis: [Melekyy]
atualizado: 2026-11-24
tags: [guildas, irmandade-sombria, mae-noite]
relacionados: [README.md, mae-noite-frases.md, validacao-skymp.md, ../../03-decisoes/ADR-0036-irmandade-sedes-e-mae-noite.md]
---

# Irmandade Sombria e seus santuários

Painel em vermelho escuro, pela tecla Y para membros atuais. **Ouvinte** é a liderança; **Orador** nomeia a função de formação já existente; o membro comum aparece como **Assassino da Irmandade**. A troca de nomes não acrescenta poderes aos oradores nem altera cargos personalizados. Não há fabricação de livros.

## As duas sedes

| Sede | Célula | Porta falante | Resposta oferecida ao membro |
|---|---|---|---|
| Falkreath | ad5a1:Skyrim.esm | 2b83b:Skyrim.esm | Silêncio, meu irmão. |
| Dawnstar | 193ee:Skyrim.esm | 19517:Skyrim.esm | Inocência, meu irmão. |

As perguntas seguem as portas do jogo, descritas nos guias de [Irmandade Sombria](https://primagames.com/eguides/elder-scrolls-v-skyrim-eguide/quests/quests/the-dark-brotherhood-quests) e [The Cure for Madness](https://primagames.com/eguides/elder-scrolls-v-skyrim-special-edition-eguide/Quests/dark-brotherhood-quests/the-cure-for-madness). O membro próximo interage, escolhe a resposta recebida e só entra se continuar membro e a tranca estiver aberta. Nenhuma fase de quest é concedida para isso.

Ouvinte controla uma passagem ou todas as 18, envolvendo 21 referências. Dois teleportes externos ocultos não existem como objeto nativo neste build: o cliente apresenta a geometria e a interação; o servidor valida a passagem e usa a chegada original do XTEL. Saída e entrada respeitam a mesma tranca. Não membros não recebem a resposta do painel.

## Compartimentos físicos

Falkreath: baú do Ouvinte 639f5:Skyrim.esm; novo baú coletivo junto da área comum, modelo CONT do jogo, com referência dinâmica registrada e reaproveitada. Nenhum baú de comerciante é utilizado. Dawnstar: coletivo 84b1c:Skyrim.esm e particular c88d7:Skyrim.esm. Os estoques são independentes, preservados ao mudar de líder e reabrir. O Ouvinte consulta o log confirmado dos coletivos, incluindo o compartimento de origem.

## Mãe Noite

Conversa de texto junto dos sarcófagos; só o Ouvinte atual recebe falas. Pontos conferidos em Falkreath e Dawnstar usam objetos existentes. A fila contém [200 frases originais em português](mae-noite-frases.md), sem repetição no ciclo, persistida no servidor. Só uma frase atual é enviada ao cliente autorizado; membros comuns/oradores não recebem o acervo.

Esta é a primeira etapa pedida pelo dev: sem áudio, contratos, alvos ou recompensas. O comportamento definitivo será definido depois. Testes simulados/nativos passaram; carga conjunta em preparação, inspeção visual e dois clientes humanos pendentes.
