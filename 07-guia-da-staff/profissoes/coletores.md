---
titulo: Coletores (Fazendeiro, Minerador, Caçador e Herbalista)
tipo: guia-staff
status: ativo
responsaveis: [pablo-rodrigues1, mercurius17]
atualizado: 2026-11-18
tags: [staff, profissoes, coleta, minerador, herbalista, cacador, fazendeiro]
fonte_tecnica: [../../02-sistemas/profissoes/coleta.md, ../../02-sistemas/profissoes/catalogo-e-progressao.md]
relacionados: [README.md, como-funcionam-as-profissoes.md, artesaos-e-refino.md]
---

# Coletores (Fazendeiro, Minerador, Caçador e Herbalista)

## Em uma frase
Os quatro **Coletores** extraem matérias-primas do mundo (minérios, plantas, recursos animais e colheitas agrícolas) para abastecer os artesãos, com limites controlados pelo servidor.

## Como funciona
1. **Ferramenta na bolsa:** o servidor confere no inventário se o jogador tem a ferramenta certa antes de começar (exceto o Caçador, que usa sua arma normal).
2. **Qualquer um coleta no Novato:** quem tem outra profissão pode coletar recursos básicos (Novato), mas não ganha XP nem evolui na coleta.
3. **Tempo e estoque compartilhado:**
   - **Minerador:** até **2 pessoas** trabalhando ao mesmo tempo na mesma mina; cada ciclo dura **60 segundos**; a mina tem estoque global de **500 minérios** e, quando acaba, fecha por **72 horas** para todo mundo.
   - **Herbalista:** ciclos de **60 segundos** e cooldown de **72 horas** quando esgota; impede pegar a planta duas vezes pelo método comum do Skyrim.
   - **Fazendeiro:** trabalha em fazendas mapeadas por ciclos de **60 segundos**, com listas de itens fixas ou rotativas que a Staff pode ajustar.
   - **Caçador:** abate o animal normalmente e processa a carcaça **uma única vez** (ninguém mais consegue extrair recursos do mesmo bicho morto).

## Exemplo prático
> Dois Mineradores estão numa mina com estoque de 500 unidades. Um terceiro Minerador tenta entrar e recebe aviso de que a mina já atingiu o limite de 2 trabalhadores. Quando os 500 minérios acabam, a mina entra em descanso por 72 horas para todo o servidor.

## Tabela rápida

| Profissão | Ferramenta | Novato | Aprendiz | Adepto | Especialista | Mestre |
|---|---|---|---|---|---|---|
| **Minerador** | Picareta | Ferro (`1 Iron Ore + 1 Coal`) | +1% gema comum | Libera Corundum e Silver; 2% gema | Libera Gold; 3% gema | Orichalcum, Quicksilver, Dwemer controlado; 20% dobrar; 1% gema perfeita |
| **Herbalista** | Bolsa (`Satchel`) | 1 planta-base | 2 plantas-base | +10% ingrediente extra aleatório (qualquer um autorizado) | +20% ingrediente extra | Mantém + 20% chance de dobrar o resultado |
| **Caçador** | Nenhuma extra | 1 carne da espécie | +1 pele (`hide`) | +1 ingrediente alquímico da espécie | 2 unidades de cada item liberado | Mantém + 20% chance de dobrar |
| **Fazendeiro** | Enxada (`Hoe`) | 1 item aleatório da fazenda | 2 itens aleatórios | 3 itens aleatórios | 3 sorteios (10% dobrar cada) | 3 sorteios (20% dobrar cada) |

*Obs.: Os números de estoque (500), tempo (60 s), cooldown (72 h) e trabalhadores (2) são padrões configuráveis pelos devs.*

## Perguntas comuns de jogadores
- **Por que minerar não me deu barra (lingote) pronta?** A mineração entrega minério bruto. Transformar minério em lingote (taxa padrão `3 minérios → 1 lingote`) é uma etapa separada de fundição.
- **Um Herbalista pode achar ingrediente de monstro (como Poeira de Vampiro) colhendo plantas?** Sim, a partir de Adepto o bônus aleatório sorteia qualquer ingrediente alquímico autorizado no servidor, justamente para colocar ingredientes raros na economia sem mexer no loot normal de monstros.
- **Herbalista ou Fazendeiro plantam ervas de alquimia na fazenda?** Não. Ervas de alquimia ficam com o Herbalista; fazendas produzem grãos, vegetais, leite, ovos, queijos, farinha, sal e afins.
- **Posso desmontar sucata Dwemer de ruínas para pegar metal?** Não. Metal Dwemer vem só de depósitos controlados (e só para Minerador Mestre quando desbloqueado).

## O que a Staff pode e não pode fazer
- **Pode:** ajustar ciclos e disponibilidade de fazendas (quando habilitado nos controles administrativos) e conferir se o rank Mestre de Minerador está liberado ou travado.
- **Não pode:** resetar manualmente o cooldown de 72 h de uma mina esgotada ou devolver carcaça já processada sem orientação técnica.

## Quer o detalhe técnico?
Veja [Sistema de Coleta](../../02-sistemas/profissoes/coleta.md).
