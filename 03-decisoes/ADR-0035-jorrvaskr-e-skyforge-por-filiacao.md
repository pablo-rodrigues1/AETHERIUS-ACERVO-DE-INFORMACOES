---
titulo: Jorrvaskr e Skyforge por filiação
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-24
tags: [guildas, companions, skyforge, permissoes]
relacionados: [../02-sistemas/guildas/companions-jorrvaskr.md, ADR-0032-sede-alquimica-em-ghostshelf.md]
---

# ADR-0035 — Jorrvaskr e Skyforge por filiação

## Resumo

Aplicar a gestão física de sede aos Companions somente em Jorrvaskr/Whiterun. A fabricação especial da Skyforge pertence a qualquer membro atual, conforme a instrução mais recente do dev.

## Contexto

O dev pediu o painel marrom, portas como nos Magos, coletivo e baú da liderança, sem livros. Alterou a regra anterior que limitava a forja ao Forjador Mestre. Depois pediu apenas a correção dos títulos fixos conforme a lore, preservando cargos personalizados e permissões.

## Opções consideradas

| Opção | Benefício | Limite |
|---|---|---|
| Manter exclusividade do ferreiro | Reutiliza regra anterior | Contraria o pedido atual |
| Liberar receitas apenas no cliente | Mostra o menu | Não autoriza nem contabiliza os itens no servidor |
| Fabricar por filiação, proximidade e recibo | Valida e cobra os materiais reais | Exige catálogo e transporte nativo conferidos |

## Decisão

- Catálogo vencedor instalado: 13 passagens/18 referências de Jorrvaskr, incluindo os aposentos e as duas pontas dos teleportes. Excluir vitrines e o restante de Whiterun. Só a liderança controla trancas; trancado bloqueia todos, inclusive a liderança, pelos dois lados.
- Usar dois baús CONT existentes. Coletivo para membros atuais; particular para a liderança atual. Conteúdo continua no mesmo objeto ao trocar o titular. Movimentos confirmados do coletivo entram nos registros dos Companions.
- Skyforge especial: 12 receitas COBJ do keyword da Skyforge, com ingredientes/resultado conferidos no registro instalado. Inclui os itens de herói nórdico do conteúdo Fishing já instalado. Filiação substitui a liberação de missão dessas receitas especiais; não altera receitas ou bancadas de outros lugares.
- Fabricação exige sessão aberta fisicamente na Skyforge, jogador disponível, filiação atual, distância, token e journal. Custo e entrega são atômicos no inventário; pedidos repetidos, receitas inventadas e membro removido são recusados.
- Cargo de ferreiro permanece nomeável e sem poder de gestão. Nenhuma Biblioteca ou fabricação de livros nesta guilda. Conjuração segue independente.
- Títulos de apresentação: Arauto, Mestre de Armas, Mestre Ferreiro e Companheiro. Esta revisão de nomes não migra IDs, não muda permissões e não renomeia cargos criados.

## Consequências

Ensaios com bots conferem regras/transporte, sem certificar aparência, animações ou dois clientes humanos. Instalar só no privado, com backup, preservando os demais sistemas e os mods de origem.
