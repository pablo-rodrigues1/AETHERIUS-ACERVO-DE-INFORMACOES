# Propostas — HUD da Reserva de Sangue · 20/11/2026

**Status (20/11):** dev aprovou a Proposta A, Cálice da Noite, com brilho carmesim crescente conforme a reserva se esvazia e sem rachaduras no Feral. Integração candidata preparada no módulo nativo; revisão cruzada e teste em jogo pendentes. Arquivo autocontido: `C:\Code\Aetherius - New Systems\Sobrenatural\preview\hud-sangue-propostas.html`. A cena, o valor numérico, o slider e os botões são exclusivos da prévia. O desenho da HUD não tem número nem tooltip.

- **A — Cálice da Noite:** relicário vertical de vidro com moldura prateada e presas; um filete de sangue carmesim preenche o centro.
- **B — Sigilo Rubro:** gota alongada entre asas finas, com interior carmesim; silhueta mais orgânica e ritualística.

Ambas seguem a direção de arte de `C:\Code\Aetherius - New Systems\Sobrenatural\docs\DIRECAO-DE-ARTE.md`: branco sobre preto transparente, traço fino e carmesim pontual. A cena é uma ilustração CSS, não uma captura do jogo.

## Posição e estados propostos

Para avaliação no jogo, cada desenho usaria uma caixa de **99 × 180 px a 1920 × 1080**, no **canto inferior direito**, com recuos de aproximadamente **32 px à direita e 76 px abaixo**; em 1280 × 720, escala proporcional de **66 × 120 px**, recuos **21 px e 50 px**. As caixas englobam ornamentos e preenchimento; posições e legibilidade ainda precisam de teste real. Na prévia as caixas são ampliadas para comparação, não representam escala de jogo.

- **Normal:** sangue acima de 30; enchimento proporcional, sem pulso.
- **Sede Crítica:** de 1 até 30 inclusive; halo e marcas carmesim surgem gradualmente e respiram em batimento sutil.
- **Feral:** 0; conteúdo vazio, batimento permanece e brilho carmesim atinge seu máximo; não há fissuras. Ao recuperar sangue, as camadas revertem suavemente.

O preenchimento e as camadas de estado interpolam em 1,5 s; retargeting parte do ponto visual corrente, inclusive em novas ações durante uma transição. Movimento reduzido desativa animações e transições. Os botões partem do valor simulado atual e respeitam 0–100; a drenagem demonstrativa é acelerada para um ponto a cada 1,5 s, não representa a cadência do servidor.

## Integração candidata, após aprovação

Adaptados somente `client/ui-core/modules/supernatural/blood-reserve-hud.js` e `supernatural-hud.css`, em `C:\Code\Aetherius - Servidor Local\client-source`, preservando o contrato privado validado e o ciclo de exibição/limpeza existentes. O valor e estado de gameplay continuam autoritativos no servidor; a animação interpola apenas a apresentação e não gera dados ou efeitos de jogo. Preservar a guarda de exclusividade `window.aetheriusVeilHudActive`: HUD de sangue e selo da Bruxa não coexistem. Inicialização/reconexão devem apresentar o primeiro estado imediatamente e atualizações repetidas não devem reiniciar animações; testar troca de maldição, watchdog, recuperação, CEF e jogo. Revisão cruzada por outro modelo e validação em CEF/jogo permanecem pendentes; não houve instalação no MO2. O HTML legado de compatibilidade em `client/index.html` é uma superfície separada e conserva a arte anterior; a integração aqui é da HUD nativa do UI Core.

**Base consultada:** `Sobrenatural/docs/ETAPA-3_vampirismo-mecanicas.md`, `ETAPA-4_bruxaria-vinculo-do-veu.md` e arquivos atuais `blood-reserve-hud.js`, `supernatural-hud.css`, `veil-seal.js`, `veil-seal.css` do cliente local, HEAD `5e8b491c7288578eb7b91437aa507a14a3a0103e`; o selo usa transição de 1500 ms e não apresenta número ou tooltip. Validação no navegador de prévia não equivale a validação no CEF ou no jogo.

**Entrega candidata:** cliente `afd96a0`, patch `C:\Code\Aetherius - Servidor Local\overlays\cliente-local\034-hud-calice-noite.patch`, laboratório `4c69023`. Testes de sangue e selo 11/11; TypeScript e webpack passaram; reverse-check do patch passou. Revisão independente e validação CEF/jogo pendentes.
