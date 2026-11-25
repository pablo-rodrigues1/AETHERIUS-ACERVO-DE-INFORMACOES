---
titulo: Diário da Física de cabelos e capas
tipo: historico
status: ativo
responsaveis: [kdu]
atualizado: 2026-11-25
tags: [diario, modpack, fisica]
relacionados: [README.md]
---

# Diário da Física de cabelos e capas

## Resumo
Registro cronológico, do mais recente para o mais antigo.

## 2026-11-25, tarde (kdu / Claude Opus 5.5)
- **Pedido:** ler tudo sozinho, zip para mandar ao Sanguine, uma área separada para criar cabelos, visualização caprichada, front no estilo do launcher do Aetherius com tutorial, modo "faça comigo" e um mascote piadista (Mudzin).
- **Feito:** Estúdio de Cabelos v2.0. Detecção automática do Skyrim, do MO2 e do perfil; biblioteca lida dos plugins (1.234 cabelos, com linha do cabelo). Telas Início, Biblioteca, Criar cabelo, Meus cabelos, Tutorial e Configurações. Mudzin com várias expressões, voz de bipinhos e o passo a passo "Faça comigo". Visual com olhos, sombras, reflexos, brilho de cabelo e quatro luzes. Linha do cabelo exportada como parte separada no plugin; opção para a linha balançar junto. Executável sem dependências e zip "AETHERIUS - ESTUDIO DE CABELOS v2.0 (25-11)" (56 MB).
- **Conferido fora do jogo:** o executável empacotado, com pasta de dados nova, achou sozinho o MO2, o perfil e o Skyrim, montou a biblioteca, gerou prévia com física, exportou, fez o zip e apagou o cabelo de teste. Telas e passo a passo conferidos num navegador controlado.
- **Próximo:** o Sanguine testar no PC dele; primeiros cabelos exclusivos em jogo; plugin no servidor.
- **Bloqueios:** nenhum
- **Decisões:** linha do cabelo como parte extra separada (mantém o .tri do cabelo principal quando é uma peça só).

## 2026-11-25 (kdu / Claude Opus 5.5)
- **Pedido:** um programa para criar cabelos: ver o modelo bonito, entender os cabelos de mods, juntar cabelos, simular mechas separadas e gerar o arquivo do cabelo, para fazer cabelos exclusivos do servidor.
- **Feito:** Estúdio de Cabelos (`ferramentas/estudio-cabelos`, executável `Estudio de Cabelos.exe`). Biblioteca com 942 cabelos (MO2, jogo base e pasta extra com o Vanilla Hair Remake SMP), visualizador 3D com cabeça e corpo do jogo, composição de várias peças, mechas com os mesmos valores da ferramenta de física, prévia de movimento e exportação para o mod `AETHERIUS - CABELOS EXCLUSIVOS` com plugin ESL. A ferramenta `aeth-cabelos` ganhou os comandos `exportar`, `juntar`, `converter` (com valores escolhidos) e `texturas`.
- **Conferido fora do jogo:** junção de dois cabelos (15 mechas, 61 ossos, 44 barreiras); plugin lido de volta (registro de cabelo jogável, sexo e lista de raças humanas); texturas copiadas para pasta própria e caminhos trocados no modelo; interface testada num navegador controlado (carregar, juntar, gerar, exportar, movimentos).
- **Próximo:** gerar os primeiros cabelos exclusivos, testar em jogo e pôr o plugin no servidor.
- **Bloqueios:** nenhum
- **Permissões:** o kdu confirmou que todos os autores dos mods de cabelo liberaram o uso (e-mails guardados com ele).
- **Decisões:** texturas de cabelos novos ficam em `textures/aetherius/cabelos/<nome>/`, nunca no caminho original, para não trocar a textura de outros cabelos.

## 2026-11-15, noite, mechas (kdu / Claude Opus 5.5)
- **Pedido:** cabelos parados e movimento em bloco; fazer mechas como nos cabelos SMP do Dovahzul.
- **Feito:** estudada a lógica (sem copiar arquivos): mechas por região, presas desde o couro cabeludo e leves. Ferramenta passou a 16 fatias (esquerda e direita independentes), mecha a partir de 0,8 da cabeça, rigidez 25.000 e freio 1,3. 910 cabelos com física (antes 577), média de 9 mechas e 19 ossos. 742 arquivos de física válidos no esquema do FSMP.
- **Próximo:** ver em jogo; ajustar se ficar mole demais.
- **Bloqueios:** nenhum

## 2026-11-15, noite (kdu / Claude Opus 5.5)
- **Retorno da YuYu (17:25–17:34):** cabelo e roupas com física boa; alguns fios entram na armadura, principalmente a trança dentro do peitoral.
- **Feito:** as barreiras do tronco e dos braços passaram a ficar 4 unidades mais longe do corpo (espessura de armadura); as da cabeça não mudaram. Kit v22 e zip da física refeitos.
- **Próximo:** a YuYu conferir a trança de novo.
- **Bloqueios:** nenhum

## 2026-11-15, tarde (kdu / Claude Fable 5.2)
- **Feito:** barreiras contra o corpo, a pedido do kdu ("pra não atravessar nunca"). Cada osso solto do cabelo ganhou um plano preso à parte do corpo mais próxima; a capa ganhou planos presos ao quadril, coxas e panturrilhas. Limites de giro das juntas afrouxados (17° para 57° para fora), porque brigavam com as barreiras.
- **Medido no simulador:** olhando 40° para cima/baixo e virando 70°, o osso entrava de 7 a 24 unidades no corpo sem barreiras e de 0,8 a 2,9 com elas, só durante o movimento rápido. O balanço ao correr não mudou.
- **Próximo:** ver em jogo; capa é a parte com mais risco.
- **Bloqueios:** nenhum
- **Decisões:** barreiras por osso em vez de colisão malha contra malha.

## 2026-11-15 (kdu / Claude Fable 5.2)
- **Feito:** ferramenta própria que converte os cabelos do modpack para o FSMP. Dos 1.626 modelos de cabelo ativos, 577 ganharam física; 1.030 são curtos e ficam como estão; 19 já vêm quebrados do mod de origem. Capas do Cloaks and Capes (6 modelos) com física pelos ossos de saia do esqueleto. Motor FSMP 4.1.1 oficial empacotado.
- **Conferido fora do jogo:** cada modelo é reaberto e comparado com o original (577 de 577); os 488 arquivos de física passam no esquema oficial do FSMP; valores do cabelo acertados num simulador com o Bullet (parado cede 2° a 4°; na arrancada a ponta balança 12° a 65° conforme o comprimento).
- **Próximo:** ver em jogo (perfil de teste), ler o registro do FSMP e medir o desempenho com mais de um jogador.
- **Bloqueios:** nenhum
- **Decisões:** sem colisão com o corpo nesta versão (revisto no mesmo dia, ver acima); chifres e barbatanas de fora.
