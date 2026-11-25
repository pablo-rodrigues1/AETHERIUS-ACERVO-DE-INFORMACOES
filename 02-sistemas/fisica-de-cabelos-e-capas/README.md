---
titulo: Física de cabelos e capas
tipo: sistema
status: rascunho
fase: em-teste
responsaveis: [kdu]
repositorio: "pacote separado: mods AETHERIUS - FISICA DE CABELOS E CAPAS + AETHERIUS - FISICA - MOTOR FSMP 4.1.1 (sem plugin)"
depende_de: []
atualizado: 2026-11-25
tags: [modpack, cabelo, capa, fisica, fsmp, estudio-de-cabelos]
relacionados: [diario.md]
---

# Física de cabelos e capas

## Resumo
Faz o cabelo comprido e as capas balançarem com o movimento do personagem. É só visual e roda no jogo de cada jogador; o servidor não participa.

## Onde está o código
- Ferramenta que gera tudo: `ferramentas/fisica-cabelos` (projeto local do kdu, C++ com nifly; simulador de bancada com Bullet).
- Entrega: dois mods para o MO2, sem plugin `.esp`.
  - `AETHERIUS - FISICA - MOTOR FSMP 4.1.1`: Faster HDT-SMP 4.1.1 oficial (GPL-3.0), versão AVX.
  - `AETHERIUS - FISICA DE CABELOS E CAPAS`: 910 modelos de cabelo, 6 de capa e 742 arquivos de física.

## Escopo
- Inclui: todos os cabelos do modpack que têm parte solta (Vanilla hair remake e addon, HammerHair, Apachii, KS Hairdos Lite, BeastHHBB, Khajiit) e as capas do Cloaks and Capes.
- Não inclui: cabelos curtos ou presos (1.030 modelos, continuam parados); chifres, barbatanas e penas; perucas; colisão malha contra malha; NPCs com rosto pré-gerado.

## Funcionamento
- Cabelo: a ferramenta separa o que está colado na cabeça do que está solto. O solto vira até 8 correntes de 1 a 6 ossos, penduradas no osso da cabeça. Os pesos da malha são refeitos; a posição dos vértices não muda.
- Capa: os modelos já são presos a três ossos de saia traseira do esqueleto XPMSSE. A física solta esses três ossos; a malha não muda.
- Barreiras: cada osso solto tem um plano preso à parte do corpo mais próxima (cabeça, costas, ombro; na capa, quadril, coxas e panturrilhas). O osso não passa do plano. Faz o papel da colisão com custo de uma conta por osso.
- Nada foi copiado de mods de física de terceiros: ossos, pesos e arquivos de física saem da ferramenta.
- Os dois mods andam juntos. O mod de cabelos sem o motor deixa os cabelos longos quebrados.

## Estúdio de Cabelos (v2.2, 25/11)
Programa com interface para criar cabelos exclusivos do Aetherius. Fonte em `ferramentas/estudio-cabelos` (projeto local do kdu); abre por `Estudio de Cabelos.exe`, que roda em qualquer PC com Windows e Skyrim SE sem instalar nada.
- **Leitura automática:** acha sozinho o Skyrim, o Mod Organizer 2 e o perfil ativo; lê os plugins ativos para dar nome, sexo e linha do cabelo; acha cada modelo como o MO2 (arquivo solto, pasta do jogo ou BSA). Primeira leitura ~30 s; depois fica guardada. Pode trocar tudo em Configurações.
- **Telas:** Início, Biblioteca (1.234 cabelos hoje), Criar cabelo, Meus cabelos, Tutorial e Configurações. Visual no estilo do launcher do Aetherius.
- **Mudzin:** mascote (um siri cinza piadista) que comenta o uso e guia o modo **Faça comigo**, um passo a passo com destaque na tela que avança quando a pessoa faz cada ação.
- **Visualizador 3D:** cabeça, olhos e corpo do jogo; texturas reais; brilho de cabelo, sombras e reflexos; quatro luzes (estúdio, dia, taverna, noite); foto PNG.
- **Criação:** junta peças de cabelos diferentes com ajuste de posição, tamanho e giro; a linha do cabelo vem junto. Mechas com os mesmos valores da ferramenta de física e física gerada sozinha a cada mudança. Prévia de movimento (cabeça, andar, correr, pular, girar, vento). "Ver mechas" pinta cada mecha de uma cor.
- **Criar de verdade (v2.2):** tesoura por região (frente, lados, trás), desfiado das pontas, volume e corte por clique no cabelo; camadas duplicáveis; acessórios (tiaras, diademas, coroas, faixas) que saem parados junto da linha do cabelo; biblioteca com filtros e foto de cada cabelo.
- **Exportar:** gera o mod `AETHERIUS - CABELOS EXCLUSIVOS` com o modelo, a física, a linha do cabelo como parte separada (como nos cabelos do jogo), as texturas numa pasta só do cabelo e o plugin `AETHERIUS - CABELOS EXCLUSIVOS.esp` (ESL). "Meus cabelos" reabre, apaga e gera o zip do mod.
- **Limites:** a prévia de física é aproximada (no jogo quem simula é o FSMP); cabelo juntado ou ajustado sai sem o arquivo de formas do rosto (.tri). Para os jogadores verem, o plugin precisa entrar no modpack e na ordem de carregamento do servidor.
- **Origem dos modelos:** o kdu pediu e recebeu permissão dos autores dos mods de cabelo para usar os modelos em cabelos exclusivos do servidor (25/11; os e-mails ficam com ele).

## Parâmetros e fórmulas
| Parâmetro | Valor | Descrição |
|---|---|---|
| Folga da cabeça | 0,8 (2,0 na metade de cima) | Até essa distância o cabelo fica preso |
| Fatias em volta da cabeça | 16 | Uma corrente por fatia com cabelo solto |
| Ossos por corrente | 1 a 6 (trecho de 4,5 ou mais) | Máximo de 40 por cabelo; média de 12 |
| Rigidez da junta | 25000 × r(r+1)/2 | r = ossos da junta até a ponta (a junta é tão firme quanto a carga que segura) |
| Inércia do osso | rigidez / 250 (mínimo 20) | O Bullet limita a rigidez pela inércia |
| Amortecimento da mola | 2 × 1,3 × raiz(rigidez × inércia) | Para o cabelo parar rápido |
| Massa / gravidade | 1,0 / 0,4 | O modelo já foi feito "caído" |
| Limites por junta | 57° para fora, 6° para dentro, 46° de lado, 11° de torção | Folgados; quem segura o cabelo fora do corpo são as barreiras |
| Barreiras do cabelo | até 14 de alcance; plano a 3,5 da pele; correção de 80% por passo | 3.948 barreiras em 477 cabelos |
| Capa | abre 31° para trás, 2° para o corpo, 13° de lado; 9 barreiras | Por junta, três juntas |
| Personagens com física | 10 | Limite do motor, ajustado pelo desempenho |

## Dependências
| Depende de | Por quê |
|---|---|
| XPMSSE (já no modpack) | Ossos de saia usados pela capa |
| Address Library (já no modpack) | Exigido pelo FSMP |

## Decisões relacionadas
- Barreiras por osso em vez de colisão malha contra malha, para ficar leve com muitos jogadores. Rever depois do teste em jogo.

## Diário
Ver [`diario.md`](diario.md).

## Pendências conhecidas
- Nunca foi visto em jogo. Falta conferir o registro do FSMP, o visual e o desempenho com vários jogadores.
- Capa não passou pelo simulador; falta ver sentado, agachado e correndo.
- As barreiras seguram os ossos; a malha entre eles e armaduras volumosas ainda podem raspar.
- 19 modelos do addon do Vanilla hair remake já vêm quebrados do mod (pele sem ossos) e não foram tocados.
- Estúdio de Cabelos: nenhum cabelo gerado por ele foi visto em jogo ainda; a prévia de física é aproximada.
