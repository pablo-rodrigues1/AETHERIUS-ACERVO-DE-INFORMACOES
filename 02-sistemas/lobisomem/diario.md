---
titulo: Diário do Lobisomem sincronizado
tipo: historico
status: ativo
responsaveis: [kdu]
atualizado: 2026-11-14
tags: [diario, lobisomem]
relacionados: [README.md]
---

# Diário do Lobisomem sincronizado

## Resumo
Registro cronológico do desenvolvimento, do mais recente para o mais antigo. Entradas curtas.

## 2026-11-15 (pablo-rodrigues1 / Claude Opus 5.5)
- **Feito:** o sistema [Sobrenatural](../sobrenatural/README.md) passa a usar este pacote como base do lobisomem. Ele acrescenta setagem pelo painel, keyword no servidor, progressão (6 estágios), perks e UI. A forma da fera continua sendo deste sistema.
- **Próximo:** combinar com o kdu a integração dos patches com o Sobrenatural.
- **Bloqueios:** nenhum

## 2026-11-14, Z não transformava (kdu / Claude Opus 5.5)
- **Achado da YuYu:** com Beast Form equipado, o Z só tocava o som; a transformação só saía pelo Kadu.
- **Causa:** o cliente avisava o servidor pelo evento `spellCast`, que não chega para poder usado pela voz.
- **Feito:** o cliente também ouve `actionVoiceFire`, e o servidor ignora o aviso repetido. Testes 29/29; patch do pacote e kit v21 atualizados.
- **Pendente:** depois de voltar à forma humana o personagem fica menor. Provável causa: a raça Nórdico do SkyMP, que fica no ator em tempo de execução.

## 2026-11-14, tarde (kdu / Claude Opus 5.5)
- **Feito:** visto em jogo pelo kdu e pelo Mercurius: a transformação funciona. Pedidos atendidos no mesmo dia:
  - tela toda vermelha no fim (aprovada em 9,5 s depois de testar 8,5 s) para esconder a pose final;
  - grito de dor masculino/feminino que vira rosnado, com carne/osso por baixo;
  - coração acelerando com pulso vermelho e tranco de câmera.
  Sons de dor e rosnados do próprio Skyrim; carne/osso e coração do Moonlight Tales (autor permite). Testes 29/29, também na v0.38 pura com o patch. Entregues o pacote e o zip só do mod; kit de teste v20.
- **Próximo:** ouvir grito e coração em jogo; teste com 2 jogadores; aplicar no oficial.
- **Bloqueios:** nenhum

## 2026-11-14 (kdu / Claude Opus 5.5)
- **Feito:** mescla das duas versões (animação + efeitos). Animação própria `IdleAethLobisomem` (10,5 s, termina no rugido), pés plantados por cinemática inversa. Sangue na tela, tremor e gore em duas camadas, uma por vez. Sem clarão branco. Opção `WEREWOLF_ANIMATION` (`aeth`/`vanilla`). Testes do serviço 28/28; bot no servidor real 21/21; patch aplica na v0.38 pura e com o pacotão de dano.
- **Próximo:** ver o conjunto em jogo; teste com 2 jogadores.
- **Bloqueios:** nenhum

## 2026-11-13 (kdu / Claude Opus 5.5)
- **Feito:** primeira versão a pedido do Mercurius: forma controlada pelo servidor, raça do Manbeast 50% maior, nome "Lobisomem" na forma de fera, +50% de dano de prata, restauração depois de reiniciar. Depois do 1º teste em jogo: troca de raça sincronizada com a animação e efeitos de sangue no lugar do clarão.
- **Próximo:** mesclar com a versão de animação feita em paralelo.
- **Bloqueios:** nenhum

## 2026-11-15 (pablo-rodrigues1 / Sol High) — integração do Sobrenatural no laboratório
- O lote Sobrenatural 3299cf1 adiciona entrada redundante cast/efeito e recarga real server-side (padrão 30 min, local 0), preservando a sessão autoritativa do kdu. 29 testes do kdu passaram; teste em jogo e revisão pendentes. Oficial não alterado; ver ../sobrenatural/formas-e-poderes.md e ADR-0014.

