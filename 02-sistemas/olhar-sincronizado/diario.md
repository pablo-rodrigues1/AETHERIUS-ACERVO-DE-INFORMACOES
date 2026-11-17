---
titulo: Diário do Olhar sincronizado
tipo: historico
status: ativo
responsaveis: [kdu]
atualizado: 2026-11-17
tags: [diario, olhar, head-tracking]
relacionados: [README.md]
---

# Diário do Olhar sincronizado

## Resumo
Registro cronológico do desenvolvimento, do mais recente para o mais antigo. Entradas curtas.

## 2026-11-17 (kdu / Claude Opus 5.5)
- **Feito:** bug relatado em teste: em vez de virar só a cabeça, o boneco remoto girava o corpo inteiro (em 1ª e em 3ª pessoa). Causa: o cliente do SkyMP (`movementApply`) chama `setHeadTracking(false)` a cada quadro em todo boneco de jogador, e nós só religávamos a cada 2 s. Com o `SetLookAt` ativo e o head tracking desligado, a IA vira o corpo para encarar o marcador. Correção: enquanto o boneco olha para um ponto, o head tracking é religado a cada quadro, logo depois do SkyMP.
- **Parâmetro:** limite do pescoço de 110° para 75°, porque acima disso a IA também vira o corpo.
- **Entrega:** testes 13/13; patch refeito (aplica na fonte do servidor); kit de teste atualizado; zip "OLHAR SINCRONIZADO 17-11".
- **Próximo:** reiniciar o servidor de teste e conferir em jogo com 2 jogadores.
- **Bloqueios:** nenhum

## 2026-11-14, fim da tarde (kdu / Claude Opus 5.5)
- **Feito:** versão seamless, a pedido do kdu ("todo mundo ver pra onde cada um está olhando"): o PC manda o ponto para onde a câmera aponta (também para o vazio), e os outros PCs fazem o boneco olhar para um marcador invisível naquele ponto. Ainda só JavaScript, sem DLL. Testes 12/12. Servidor de teste reiniciado com a versão nova.
- **Entrega:** pacote separado (patch sobre a v0.38 + arquivos soltos + LEIA-ME) enviado ao Mercurius. Aplica sozinho e junto com o pacotão de dano e o lobisomem, em qualquer ordem.
- **Próximo:** teste com 2 jogadores.
- **Bloqueios:** nenhum

## 2026-11-14 (kdu / Claude Opus 5.5)
- **Feito:** a pedido do kdu, depois da conversa com o Mercurius sobre o head tracking do TDM não funcionar no multiplayer. Serviço novo para todos os jogadores: o PC de cada um diz para quem está mirando, o servidor repassa e os outros PCs viram a cabeça do boneco para o mesmo alvo; sem alvo, o boneco para de encarar quem chega perto. Código próprio em JavaScript, sem DLL. Testes 10/10. Ligado no servidor de teste local.
- **Próximo:** testar em jogo com 2 jogadores.
- **Bloqueios:** nenhum
