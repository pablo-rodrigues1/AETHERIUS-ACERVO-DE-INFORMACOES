---
titulo: Como funcionam as profissões
tipo: guia-staff
status: ativo
responsaveis: [pablo-rodrigues1, mercurius17]
atualizado: 2026-11-18
tags: [staff, profissoes, ranks, vigor]
fonte_tecnica: [../../02-sistemas/profissoes/README.md, ../../02-sistemas/profissoes/catalogo-e-progressao.md]
relacionados: [README.md, coletores.md, artesaos-e-refino.md, ../economia/comercio-empresas-e-profissoes.md, ../progresso.md]
---

# Como funcionam as profissões

## Em uma frase
Cada personagem escolhe **uma única profissão** para evoluir do rank Novato ao Mestre, usando **Vigor Profissional** para coletar ou produzir itens no servidor.

> [!NOTE]
> **O que vale hoje:** no código do servidor há 9 profissões e 180 receitas esperando a nova interface para testes em jogo. A documentação detalhada no `AetheriusGameplayCore` reúne o desenho completo de carreira, coleta, artesanato e refino — várias partes (como Refino e Encantador) ainda são planejamento.

## Como funciona
1. **Escolha única:** o personagem escolhe **1 profissão especializada**. Não dá para trocar livremente na interface.
2. **Coleta básica para todo mundo:** mesmo sendo artesão (ou tendo outra profissão), qualquer personagem pode coletar como **Novato** (minerar ferro, colher planta básica, trabalhar na fazenda ou processar carcaça), se tiver a ferramenta e Vigor. Porém, **só a profissão escolhida ganha XP e sobe de rank**.
3. **Cinco ranks:** toda profissão evolui em `Novato → Aprendiz → Adepto → Especialista → Mestre`. O rank **não** depende das perks comuns do Skyrim (exceto Alquimista e Encantador, ainda em decisão).
4. **Vigor Profissional:** é uma barra própria de trabalho (não é a Stamina de combate). Ela limita quantas ações seguidas o jogador faz. Quando acaba, ele precisa esperar regenerar.
5. **Mestres pagam mais Vigor em receitas básicas:** se um artesão Mestre faz uma receita de Novato, ele gasta **4 vezes** mais Vigor. Assim, veteranos não dominam o mercado básico e deixam espaço para iniciantes.
6. **Mestre pode ficar travado pela Staff:** mesmo atingindo o XP máximo, o rank Mestre pode permanecer fechado globalmente até o time liberar aquele conteúdo no servidor.
7. **Sem "Guilda dos Ferreiros" ou "Curtidores":** profissões são ofícios individuais, não guildas.

## Exemplo prático
> Um jogador escolhe **Ferreiro**. Ele pode ir até uma mina com uma picareta e minerar ferro como **Minerador Novato** para conseguir minério, mas não ganha XP de Minerador nem vira Aprendiz de mineração. Quando ele chega a Ferreiro Mestre e tenta forjar uma espada de Ferro (Novato), gasta 20% de Vigor por tentativa em vez de 5%.

## Tabela rápida

| Regra | Valor padrão (configurável) | Observação |
|---|---|---|
| Profissões por personagem | 1 especializada | Coleta Novato é liberada para todos, sem XP |
| XP por rank | 0 / 500 / 1.500 / 3.500 / 6.000 | Separado do nível de combate do personagem |
| XP ao fazer conteúdo mais fácil | 100% (igual) / 50% (1 abaixo) / 10% (2+ abaixo) | Evita evoluir rápido com receita fraca |
| Barra de Vigor Profissional | 100% (5% por ação básica = 20 ações) | Espera 60 s sem trabalhar e regenera 1% por minuto |
| Custo extra de Vigor (artesão) | 1x / 1,5x / 2x / 3x / 4x | Quanto mais abaixo do rank, mais Vigor gasta |
| Encantador | **Fora do Alpha** | Ainda sem planejamento técnico fechado |
| Alquimista | **Em definição** | Desbloqueios por rank e uso de perks ainda em aberto |
| Guarda e Mensageiro | **Fora desta rodada** | Reservados para definição futura |

## Perguntas comuns de jogadores
- **Posso ter duas profissões, tipo Minerador e Ferreiro?** Não. Cada personagem só tem uma profissão principal. Um Ferreiro até minera ferro no nível Novato, mas não evolui em mineração e precisa comprar minérios avançados de Mineradores.
- **Por que peguei a perk de Smithing no Skyrim e não liberou receita de Aço?** No Aetherius, perks normais de Smithing não liberam profissão, receita nem refino. O que vale é o rank da profissão no servidor.
- **Comprar uma loja/empresa me dá as receitas dos funcionários?** Não. Mesmo no planejamento do sistema de COMÉRCIO, o dono administra e contrata profissionais; cada pessoa continua usando só a própria profissão.
- **Já posso virar Encantador?** Não na versão Alpha: o sistema de Encantamento não entra no Alpha e não usa a mesa comum do Skyrim como substituto.

## O que a Staff pode e não pode fazer
- **Pode:** explicar as diferenças entre profissões, informar se o rank Mestre está liberado ou bloqueado no servidor, e acompanhar trocas administrativas quando autorizadas.
- **Não pode:** prometer datas de lançamento de sistemas em planejamento (Encantador, Alquimista, Refino ou COMÉRCIO), nem inventar exceções de perks ou receitas fora das regras oficiais.

## Quer o detalhe técnico?
Veja a documentação técnica em [Profissões](../../02-sistemas/profissoes/README.md) e [Catálogo e progressão](../../02-sistemas/profissoes/catalogo-e-progressao.md).
