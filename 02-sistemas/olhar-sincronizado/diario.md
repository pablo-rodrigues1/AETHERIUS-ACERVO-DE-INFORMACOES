---
titulo: Diário do Olhar sincronizado
tipo: historico
status: ativo
responsaveis: [kdu]
atualizado: 2026-11-14
tags: [diario, olhar, head-tracking]
relacionados: [README.md]
---

# Diário do Olhar sincronizado

## Resumo
Registro cronológico do desenvolvimento, do mais recente para o mais antigo. Entradas curtas.

## 2026-11-14, fim da tarde (kdu / Claude Opus 5.5)
- **Feito:** versão seamless, a pedido do kdu ("todo mundo ver pra onde cada um está olhando"): o PC manda o ponto para onde a câmera aponta (também para o vazio), e os outros PCs fazem o boneco olhar para um marcador invisível naquele ponto. Ainda só JavaScript, sem DLL. Testes 12/12. Servidor de teste reiniciado com a versão nova.
- **Próximo:** teste com 2 jogadores.
- **Bloqueios:** nenhum

## 2026-11-14 (kdu / Claude Opus 5.5)
- **Feito:** a pedido do kdu, depois da conversa com o Mercurius sobre o head tracking do TDM não funcionar no multiplayer. Serviço novo para todos os jogadores: o PC de cada um diz para quem está mirando, o servidor repassa e os outros PCs viram a cabeça do boneco para o mesmo alvo; sem alvo, o boneco para de encarar quem chega perto. Código próprio em JavaScript, sem DLL. Testes 10/10. Ligado no servidor de teste local.
- **Próximo:** testar em jogo com 2 jogadores.
- **Bloqueios:** nenhum
