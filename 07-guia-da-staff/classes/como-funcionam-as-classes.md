---
titulo: Como funcionam as classes
tipo: guia-staff
status: ativo
responsaveis: [mercurius17]
atualizado: 2026-11-14
tags: [staff, classes, perks, skills]
fonte_tecnica: [../../02-sistemas/gameplay-core/README.md]
relacionados: [../progressao/nivel-xp-e-atributos.md, ../progresso.md]
---

# Como funcionam as classes

## Em uma frase
A classe é a "profissão de combate" do personagem: ela define quais habilidades (skills) ele domina e quais talentos (perks) ganha conforme sobe de nível.

## Como funciona
1. O jogador escolhe **uma** das 18 classes. Elas são divididas em três grupos: Conjuradores, Guerreiros e Especialistas.
2. Seis classes de magia (Elementalista, Criomante, Eletromante, Piromante, Invocador e Curandeiro) são **classes do Colégio de Winterhold**. O jogador **não consegue escolhê-las sozinho** na tela de classes: aparece "Esta classe exige autorização do Colégio de Winterhold. Fale com a staff." Só a Staff define essas classes, pelo painel.
3. Cada classe tem **9 marcos**: níveis 1, 5, 10, 15, 20, 25, 30, 35 e 40. Ao chegar num marco, o servidor entrega os perks daquele marco e ajusta as skills da classe.
4. As skills da classe são **definidas pelo servidor**: a skill fica no valor que o marco atual manda. Quem sobe de nível vê as skills subirem; quem desce de nível (por ação da Staff) vê elas voltarem ao valor do marco.
5. **Feitiços não vêm de graça.** As classes de magia têm uma lista de feitiços autorizados, mas eles precisam ser aprendidos por roleplay no Colégio de Winterhold, com professores e livros.
6. **Trocar de classe recomeça do zero:** nível volta a 1, XP zera e os pontos de atributo distribuídos são devolvidos. Os perks e skills da classe antiga saem. Escolher de novo a **mesma** classe não muda nada.
7. A fadiga do dia (limite diário de XP) **não** é zerada na troca de classe.

## Exemplo prático
> Um jogador Guardião nível 12 decide virar Paladino. Ele volta ao nível 1 de Paladino, com XP zerado e todos os pontos de atributo livres para distribuir de novo. Os perks de Guardião somem e ele recebe os perks do nível 1 de Paladino.

## Tabela rápida

| Grupo | Classes |
|---|---|
| Conjuradores | Elementalista, Criomante, Eletromante, Piromante, Invocador, Curandeiro |
| Guerreiros | Guardião, Berserker, Cavaleiro Negro, Paladino, Mestre Espadachim, Druida |
| Especialistas | Arqueiro, Ranger, Viper, Assassino, Anti-Mago, Trapaceiro |

| Regra | Valor |
|---|---|
| Classes por personagem | 1 |
| Marcos (níveis com recompensa) | 1, 5, 10, 15, 20, 25, 30, 35, 40 |
| Nível máximo | 40 |
| Limite de invocações ao mesmo tempo | 1, +1 com o perk Twin Souls, +1 com a Pedra do Ritual (máximo 3) |

**Mudança de 14/11/2026:** o Anti-Mago passou a receber a Maestria em Conjuração já no nível 1. Ela é pré-requisito do Mystic Binding, que ele ganha no nível 10.

## Perguntas comuns de jogadores
- **Posso ter duas classes?** Não. Uma por personagem.
- **Não consigo escolher Piromante (ou outra classe de magia). Por quê?** As seis classes do Colégio de Winterhold só são liberadas pela Staff, depois do roleplay no Colégio.
- **Se eu trocar de classe, perco tudo?** Perde nível, XP, perks e pontos distribuídos da classe atual. A fadiga do dia continua contando.
- **Por que não ganhei os feitiços da minha classe?** Feitiços se aprendem por roleplay no Colégio de Winterhold; a classe só diz quais são permitidos.
- **Minha skill caiu sozinha. É bug?** A skill segue o marco da classe. Se o nível foi alterado pela Staff, ela volta ao valor daquele marco.
- **Quantos atronachs posso ter ao mesmo tempo?** Um. Com o perk Twin Souls ou a Pedra do Ritual ativa, mais um para cada (até três).

## O que a Staff pode e não pode fazer
- **Pode** (painel da Staff, F7, com a permissão de personagens e o GameplayCore ligado no servidor):
  - **Definir classe** de um personagem, inclusive as seis classes do Colégio de Winterhold (só a Staff libera essas). Atenção: se a classe for diferente, zera nível, XP e pontos.
  - **Definir nível de classe** (1 a 40), mantendo a classe. O XP vai para o começo do nível escolhido. Se o novo nível der menos pontos de atributo do que o jogador já gastou, a distribuição é zerada e ele redistribui.
- **Não pode:** dar feitiços de classe fora do roleplay, dar perks soltos pelo sistema de classes, nem dar pontos de atributo extras (os pontos sempre vêm do nível).
- Antes de usar essas ações, combine com o jogador: elas não têm "desfazer" automático.

## Quer o detalhe técnico?
Veja [GameplayCore](../../02-sistemas/gameplay-core/README.md). A lista completa de perks por classe e marco está no repositório do GameplayCore, em `docs/classes/PERKS_POR_CLASSE.md`.
