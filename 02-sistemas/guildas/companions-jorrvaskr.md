---
titulo: Companions em Jorrvaskr e Skyforge
tipo: referencia
status: ativo
responsaveis: [Melekyy]
atualizado: 2026-11-24
tags: [guildas, companions, jorrvaskr, skyforge]
relacionados: [README.md, validacao-skymp.md, ../../03-decisoes/ADR-0035-jorrvaskr-e-skyforge-por-filiacao.md]
---

# Companions em Jorrvaskr e Skyforge

Painel marrom escuro no Y, com salão e aposentos de Jorrvaskr. Sem Biblioteca/livros. A gestão é do **Arauto**; Mestres de Armas, Mestre Ferreiro e Companheiros consultam informações e usam o coletivo. Os cargos criados pelos jogadores mantêm seus nomes e atribuições.

## Portas e baús

13 passagens/18 referências de porta, sem vitrines. Arauto tranca uma ou todas; fechadas impedem passagem inclusive dele. Referências estáveis dos baús: coletivo cd42b:Skyrim.esm, Arauto 795d9:Skyrim.esm. Todos os membros atuais acessam o coletivo, somente o atual Arauto acessa o particular. Trocar liderança não desloca nem esvazia o estoque. Registro de depósitos/retiradas é exclusivo da liderança.

## Skyforge especial

Referência 1ad6e:Skyrim.esm. Só perto dessa forja, pelo menu de interação, o membro atual recebe as 12 receitas especiais: armas do Herói Nórdico e quatro peças de armadura nórdica antiga. Inclui adaga, maça e martelo de guerra de Fishing, já instalado. Custos são os CNTO/resultado do COBJ vencedor; não há receita criada pelo navegador. Componentes equipados não são consumidos.

Membro, Mestre de Armas, Mestre Ferreiro e Arauto usam a mesma autorização por filiação. O cargo de ferreiro não dá administração. A mudança substitui a exclusividade antiga. Nada é fabricado no painel Y aberto longe da forja; não altera as demais bancadas do mapa.

## Validação

Ensaios nativos isolados aprovaram todas as 12 receitas, trancas, acesso, depósitos/reabertura/retirada e troca de líder. UI headless conferiu tema, controles e ausência de livros. Instalação e carga conjunta estão em preparação; visual no Skyrim e dois jogadores continuam pendentes.

O título Arauto traduz o Harbinger usado para Kodlak no [guia de Companions](https://primagames.com/eguides/elder-scrolls-v-skyrim-special-edition-eguide/Quests/the-companions-quests/take-up-arms). Preceptor de Alquimia, no painel da guilda criada para o projeto, é adaptação de apresentação e não um cargo canônico novo do jogo.
