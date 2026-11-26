---
titulo: Revisão 2 das correções de 24/11 (bug search do Discord)
tipo: revisao
status: ativo
responsaveis: [kdu]
atualizado: 2026-11-25
tags: [sobrenatural, revisao, vampirismo, racas, magias, ui, skyrim-platform]
relacionados: [../diario.md, ../README.md]
---

# Revisão 2 das correções de 24/11

## Resumo
Na madrugada de 25/11, o Opus 5.5 (esforço normal) revisou as correções feitas em 24/11 para os 29 reports do Discord. A 1ª rodada saiu no pacote `INVESTIGACAO_E_SOLUCOES_24_11`, feito em contingência. A revisão achou **um bug sério** na correção do traço de nórdico (M4) e **três pontos frágeis** (N2, M1, V3). Os quatro foram corrigidos e testados fora do jogo. Entrega: zip `AETHERIUS - INVESTIGACAO 24-11 REVISAO 2 (25-11)`, com os 49 arquivos, os diffs, os testes e o relatório `REVISAO_2_25_11.md`. **Nada foi testado em jogo.**

## Achados e correções

| Item | Achado | Correção |
|---|---|---|
| M4 traço de outra raça | O plano racial usava a raça do `appearance`. Para vampiro ela é a variante vampírica, que não tem o monitor racial do Aetherius (ex.: `MAG_NordRaceMonitor`). Para lobisomem e lorde vampiro, a fera não tem nenhuma racial. Resultado: vampiros perdiam o monitor da própria raça e, durante a forma, saíam todas as raciais. Provado com os records reais (Skyrim.esm + Aetherius.esp). | Variante vampírica usa a raça base. Fera ou raça não jogável não publica plano nem migra. Teste com dados reais: a versão da 1ª rodada falha 3 de 4; a corrigida passa 4 de 4. |
| N2 magias contínuas | A 1ª rodada tirou o `InterruptCast` do Papyrus, que era a garantia de 17/11 contra a barreira eterna. | Confirma 0,4 s depois do fim e usa o Papyrus só se a mão ainda estiver lançando. Testes do cliente 9/9; tsc sem erros novos. |
| M1 bordas pretas (DLL) | Se a RTV do back buffer falhasse, a UI sumia. O desvio valia para todos. | Só desvia quando o alvo ligado tem tamanho diferente do back buffer. Fallback para o caminho antigo. Compilado com MSVC + DirectXTK + D3D11 reais (CEF em stub). Continua suposição e exige o OK do Pablo. |
| V3 visão duplicada | `VampireHuntersSight` (0xC4DE1) vem da lista de magias de todas as raças vampíricas. `removeSpell` não tira magia de raça, então o cliente tentaria a cada segundo, sem efeito. | Tentativas limitadas (3 por concessão). Proposta de dados, não instalada: cópia do `AETHERIUS - SOBRENATURAL.esp` com 0xC4DE1 virando habilidade sem efeitos. Antes, confirmar que a Visão Vampírica (0x836) é concedida em todos os estágios. |
| B3 runa do ritual | Os efeitos de ritual do jogo base usam o mesmo `CastingRitualBody.hkx`: nenhum serve de atalho. O MSTT 0x935 ainda não existe no ESP de 25/11. | Sem mudança. A malha estática precisa ser feita no NifSkope. |

Conferidos sem problema: N2 no servidor, P2 (rota `alchemy/openWorkbench`) e L1.

## Limites
- O código completo de 24/11 não estava na máquina da revisão. Os testes rodaram com o cliente de 13/11 + os arquivos corrigidos e com os dados reais do modpack. Os testes de gamemode da 1ª rodada não foram reexecutados.
- N1 (inimigos no chão) e o ticket `classes.reset` seguem como na 1ª rodada.

## Próximos passos
1. Integrar usando o `codigo_corrigido/` da revisão 2, não o da 1ª rodada.
2. Testes em jogo:
   - vampiro Nord: monitor racial mantido e uma "Visão" só;
   - lobisomem: transformar e destransformar com as raciais intactas;
   - barreira e Dreno Vampírico com um observador.
3. Decisões:
   - M1: confirmação do Pablo;
   - V3 (ESP): decisão do dev;
   - B3: malha no NifSkope.
