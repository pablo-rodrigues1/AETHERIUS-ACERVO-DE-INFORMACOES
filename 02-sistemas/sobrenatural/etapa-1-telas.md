# Etapa 1.2 — Telas do módulo Sobrenatural

Data: 2026-11-15. Implementação: Terra High (worker Codex). Fontes: `C:\Code\Aetherius - New Systems\Sobrenatural\docs\ESPECIFICACAO.md`, `DIRECAO-DE-ARTE.md`, `PROPOSTA-TEXTOS.md`, `coleta\COLETA-1A_ui-core-contrato.md`, `coleta\COLETA-1B_class-system-estilo.md` e as cinco imagens de `C:\Code\Aetherius - New Systems\referencia\`. Cliente de referência: `C:\Code\Aetherius - OFICIAL\aetherius-client-main\client\ui-core\modules\class\`; cópia sem `.git`, commit indisponível.

## Entrega

- `C:\Code\Aetherius - New Systems\Sobrenatural\ui-core-module\modules\supernatural\supernatural-data.js`: curva acumulada 0/20.000/60.000/190.000/610.000/2.000.000, textos e dados das três maldições, disciplinas e vertentes.
- `...\supernatural\supernatural-module.js`: adapter UI Core, quatro telas, 6 marcos, magnitude por estágio, escolhas sem mutação local, ícones SVG inline, sanitização de texto e ciclo de vida.
- `...\supernatural\supernatural-module.css`: tipografia e cores base do class, destaque por maldição, grade responsiva, formas e detalhes em linha.
- `C:\Code\Aetherius - New Systems\Sobrenatural\ui-core-module\preview\index.html`: prévia local sem build com sete cenários.
- `C:\Code\Aetherius - New Systems\Sobrenatural\ui-core-module\INTEGRACAO.md`: diff de manifesto, localização, contrato e pendências do servidor.

A prévia usa dados simulados; os textos para Bruxa e Lobisomem continuam sujeitos à aprovação final do dev. A seção de lobisomem apresenta os uivos e números da forma de fera como referência da especificação; os bônus não são aplicados. A tela sem estado real mostra carregamento ou erro, não presume ausência de maldição. Em caso de atualização durante pedido em trânsito, a resposta antiga não substitui a atualização recebida. Os SVGs são desenhos vetoriais próprios simplificados inspirados nas imagens, não reproduções exatas; ícones de estágio não são os assets finais da referência. As cores são aproximações visuais. O nome lógico proposto `supernatural:choose` corresponde à ação `choose` no módulo `supernatural` pelo `context.request`, sem criar transporte paralelo.

## Verificação

`node --check` passou nos dois arquivos JavaScript. No navegador integrado Orca, o `index.html` foi aberto por `file://` e os sete cenários foram alternados: vazio com zero cartões; cada cenário com maldição com seis cartões; Apoteótico com quatro escolhas disponíveis e Celeridade selecionada; Bruxa Neófito com três escolhas disponíveis; Ancião e Alto Teurgo com suas escolhas anteriores registradas. Um clique em Fortitude no Apoteótico emitiu `{kind:"discipline",id:"fortitude"}` na prévia sem registrar outra escolha localmente. Console vazio após alternância. O teste de caminho verificou existência, prefixo `modules/supernatural/`, ausência de `..` e extensão `.css`/`.js` dos três assets da entrada proposta. Não foi executado o empacotamento completo, pois exige bridge, Meridian e bundle externos. Não foram guardadas capturas permanentes.

## Pendências

Servidor precisa registrar `snapshot` e `choose`, autorizar navegação, validar e persistir as escolhas e responder com estado completo conforme `INTEGRACAO.md`; nenhum handler ou mecanismo de jogo foi implementado. Confirmar os textos propostos, detalhes finais dos ícones e compatibilidade em CEF/cliente real. A revisão cruzada por outro modelo está pendente com o orquestrador; erros, aceites e versão final devem ser documentados após a revisão. Atualização de RETOMADA, diário e acervo cabe à consolidação do orquestrador: esta tarefa restringiu as edições a `ui-core-module/` e `docs/` e não autorizou novos workers.
