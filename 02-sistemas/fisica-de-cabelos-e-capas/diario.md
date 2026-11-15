---
titulo: Diário da Física de cabelos e capas
tipo: historico
status: ativo
responsaveis: [kdu]
atualizado: 2026-11-15
tags: [diario, modpack, fisica]
relacionados: [README.md]
---

# Diário da Física de cabelos e capas

## Resumo
Registro cronológico, do mais recente para o mais antigo.

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
