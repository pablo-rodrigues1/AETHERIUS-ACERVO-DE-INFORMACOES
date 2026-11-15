---
titulo: Criaturas conjuradas e acompanhamento
tipo: guia-staff
status: ativo
responsaveis: [Melekyy]
atualizado: 2026-11-15
tags: [staff, conjuracao, criaturas]
fonte_tecnica: [../../02-sistemas/gameplay-core/conjuracao.md]
relacionados: [../progresso.md, ../glossario.md]
---

# Criaturas conjuradas e acompanhamento

## Resumo

Melekyy preparou melhorias para as criaturas conjuradas. O acompanhamento com duas criaturas foi aprovado no servidor privado; a liberação no oficial ainda depende de integração e testes.

## Em uma frase

As criaturas conjuradas acompanham o dono e devem continuar com ele ao mudar de área.

## Como funciona

1. O jogador lança uma magia de conjuração válida.
2. O servidor cria a criatura e define seu dono. Os outros jogadores devem ver essa mesma criatura.
3. A criatura acompanha o dono, cede ao combate e retoma o acompanhamento depois.
4. No privado, duas podem permanecer ao mesmo tempo. Uma terceira conjuração válida substitui a mais antiga.
5. Ao atravessar uma porta ou mudar de área, o acompanhamento é recuperado quando dono e criatura estiverem disponíveis no novo local.

## Exemplo prático

Um jogador conjura duas criaturas e atravessa uma porta. No privado, ele confirmou que elas permanecem e acompanham. Ao conjurar outra, a mais antiga é substituída; as duas mais novas ficam.

## Tabela rápida

| Situação | Estado |
|---|---|
| Acompanhamento no privado | Instalado e confirmado pelo usuário |
| Duas criaturas no privado | Ativado |
| Terceira conjuração no privado | Substitui a mais antiga |
| Servidor oficial | A integração ainda precisa ser testada |
| Limites oficiais | Continuam os atuais, salvo escolha explícita do responsável |

## Perguntas comuns de jogadores

- **Já posso contar com isso no oficial?** Ainda é necessário confirmar a implantação oficial.
- **A magia passa a custar menos?** Esta entrega não altera os custos de magicka.
- **Reanimar Zumbi também foi corrigido?** Essa função não faz parte desta entrega.
- **Ver uma criatura aparecer e sumir prova que o servidor a apagou?** Não. O jogo pode criar uma cópia local; o diagnóstico precisa distinguir essa cópia da criatura do servidor.

## O que a Staff pode e não pode fazer

- Encaminhar a Melekyy relatos de desaparecimento, acompanhamento ou troca de área.
- Registrar a magia usada, o local e se ocorreu ao atravessar uma porta.
- Anunciar a funcionalidade no oficial depois da confirmação de implantação.
- Manter as regras oficiais de limite e de duração até uma mudança explícita ser aprovada e instalada.

## Quer o detalhe técnico?

Veja [Conjuração: privado e adaptação v0.38](../../02-sistemas/gameplay-core/conjuracao.md).
