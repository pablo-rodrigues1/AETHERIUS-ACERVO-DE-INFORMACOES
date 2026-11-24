---
titulo: Fontes de scripts BSA e inicialização segura
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-24
tags: [guildas, skymp, inicializacao, bsa]
relacionados: [../02-sistemas/guildas/README.md, ../02-sistemas/guildas/validacao-skymp.md, ADR-0022-preparacao-skymp-das-guildas.md, ADR-0026-cenario-ghostshelf-isolado-no-privado.md]
---

# ADR-0034 — Fontes de scripts BSA e inicialização segura

## Resumo

Separar assets instalados de arquivos aceitos pelo carregador de scripts deste SkyMP. Validar os índices BSA antes do servidor e conferir inicialização/reconexão numa cópia protegida do mundo.

## Contexto

Após integrar Ghostshelf, o processo encerrava com 0xC0000005 ao ativar personagem/área. Desligar o NPC automático permitiu inicializar, mas não resolveu a reconexão. O depurador e os símbolos localizaram BsaArchiveScriptStorage::ListScripts; a implementação desreferencia a pasta raiz scripts sem verificar se existe. O BSA de texturas de Ghostshelf não tem essa pasta.

## Opções consideradas

| Opção | Benefício | Consequência |
|---|---|---|
| Deixar o NPC de teste desligado | Evita o primeiro gatilho | A reconexão continua falhando |
| Retirar o mod e seus assets | Evita carregar o conteúdo | Remove a sede pedida |
| Corrigir a lista de fontes e validá-la | Preserva mapa, visual e runtime nativo | Exige inspeção do índice e teste de conexão |

## Decisão

- Retirar somente EnemyModsGhostshelfKeep - Textures.bsa de archives, a lista consumida pelo carregador de scripts. O arquivo permanece instalado, assim como os 117 plugins, a BSA principal e todos os recursos do cliente. Lista fica com 61 fontes, todas com scripts na raiz.
- A checagem prévia lê o índice das versões BSA 103/104/105 e recusa arquivos sem scripts, ausentes ou truncados. Não altera nem descompacta assets.
- Preservar addon, cliente e gamemode original. Desativação do NPC foi diagnóstico temporário; a execução final inclui o NPC original e conexão ao personagem copiado.
- Preparar posição persistida do baú offline; não fazer SetPosition nativo em carga fria. Não consultar novamente referências já declaradas indisponíveis. Reconstruir o índice derivado das Tendas para a lista real, mantendo sua validação de ordem.
- Validar o conjunto instalado em cópia do mundo, portas separadas e escrita restrita à cópia. Comparar hashes dos arquivos originais, sem iniciar Skyrim ou servidor principal.

## Consequências

151 testes do workspace, ensaios nativos com quatro bots, oito serviços inicializados e uma reconexão de personagem copiado aprovados; 954 arquivos originais preservados. Testes não são homologação visual, colisão da ponte ou dois humanos no oficial. Dez recibos históricos pendentes continuam preservados; nenhuma correção forçada de inventário. A R31 privada não gera novo pacote público nesta sessão.
