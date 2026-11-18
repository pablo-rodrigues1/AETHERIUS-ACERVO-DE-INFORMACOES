---
titulo: Cenário Ghostshelf isolado no privado
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-18
tags: [guildas, ambiente-privado, skymp, cenario]
relacionados: [../02-sistemas/guildas/integracao-skymp.md, ../02-sistemas/guildas/diario.md]
---

# ADR-0026 — Cenário Ghostshelf isolado no privado

## Resumo

Instalar Ghostshelf Keep 1.5 como cenário separado no ambiente privado, usando cópias independentes do mod e do patch já existentes, com a origem preservada. Não muda as regras das Guildas nem certifica sincronização de scripts.

## Contexto

O dev pediu a instalação do [mod do Nexus](https://www.nexusmods.com/skyrimspecialedition/mods/195009) no privado e esclareceu que os arquivos já estavam no Mod Organizer do Aetherius, que não deve sofrer alterações. O build privado SkyMP não aceita plugins light.

## Opções consideradas

| Opção | Prós | Contras |
|---|---|---|
| Alterar o mod/perfil da origem | Dispensa cópia | Contraria o pedido de isolamento |
| Copiar apenas o cenário | Preserva origem | Deixa de aplicar o patch existente de restrição de recursos |
| Copiar cenário e patch para o privado | Mantém a configuração existente e a origem | Requer adaptar somente a marca light do patch privado |

## Decisão

- Copiar assets e plugins para dois diretórios privados independentes, sem links com a origem.
- Preservar o patch existente de desativação de renda, bônus e espelho. Retirar a marca light somente da cópia privada, após comprovar que todos os registros são sobrescritas de masters, sem IDs compactos próprios.
- Acrescentar os dois plugins depois dos 115 anteriores; manter ordem e bytes iguais entre cliente e servidor. Acrescentar os dois BSA à configuração do host.
- Ampliar a lista limitada de arquivos do cliente pelos dois nomes necessários, mantendo CRC, tamanho e proteção de caminhos.
- Guardar backup das configurações e conferir hashes de origem e sistemas privados protegidos. Não carregar mundo de jogadores em verificações isoladas; o dev faz os testes no Skyrim.
- Publicar somente documentação. Nenhum asset, patch, ZIP, token ou dado privado é enviado ao acervo.

## Consequências

- Instalação privada com 117 plugins e 62 BSA; índices anteriores preservados. Quinze arquivos de origem e sete arquivos privados protegidos permaneceram iguais.
- Carregamento nativo isolado aprovado em 212 registros, incluindo os 180 overrides do patch e as três células internas. Essa prova não valida aparência, controles, estabilidade ao atravessar áreas ou scripts Papyrus em multiplayer.
- Guildas R30, controle de portas do Arquimago, baús mantidos e conjuração separada continuam com suas regras anteriores. Nenhuma implantação oficial foi realizada.
