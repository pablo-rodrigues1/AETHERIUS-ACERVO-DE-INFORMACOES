---
titulo: Recuperação de entrada e callbacks das Guildas
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-17
tags: [guildas, skymp, sincronizacao]
relacionados: [../02-sistemas/guildas/README.md, ADR-0022-preparacao-skymp-das-guildas.md]
---

# ADR-0023 — Recuperação de entrada e callbacks das Guildas

## Resumo

Corrigir as causas reproduzidas da falha de entrada e aposentar callbacks antigos exclusivamente das Guildas. Separar recuperação observada, testes nativos e aceite humano de cada fluxo.

## Contexto

Após a atualização privada, o dev relatou que nenhum painel abria. A primeira correção da lista de plugins não bastou. Logs e ensaios localizaram atualizações para ator sem modelo e callbacks legados de foco/modelo. O dev também exige validar todos os perfis e convite por ID.

## Opções consideradas

| Opção | Avaliação |
|---|---|
| Desativar verificação de arquivos ou zerar o estado | Retira garantias e põe dados preservados em risco |
| Reescrever todos os sistemas ou atualizar bundle em uso | Amplia o escopo e produz mistura de versões |
| Corrigir falhas reproduzidas e conferir cada perfil | Preserva contratos e permite avaliar cada evidência |

## Decisão

- Preservar CRC/tamanho e restrição de caminhos; incluir apenas os plugins efetivamente instalados que faltavam na lista limitada.
- Ignorar propriedade sem modelo de ator, sem inventar modelo; CreateActor continua sendo responsável pelo estado inicial. Não abortar o restante do lote por esse caso.
- R29 aposenta apenas callbacks legados listados de Guildas, inclusive quando a entrada registra o renderer atual. Não retirar handlers de Casas, Grupo ou outros sistemas.
- Bundle só é substituído com o jogo fechado, backup e verificação de hashes protegidos. Retirar diagnóstico temporário que usa IDs estáticos na API exclusiva de referências dinâmicas, sem mexer no renderer funcional.
- Convite exibe ID do jogador. A igualdade entre ID público e chave persistente foi conferida nesta instalação; um destino com identidade diferente exige adaptador explícito, não só alteração do texto.
- Validar Arquimago, professor, bibliotecário e membro, inclusive recusas no servidor. Não conceder poderes ao esconder/exibir apenas botões. Ensaios nativos de bots e prévias não equivalem a duas pessoas conectadas.

## Consequências

Backups e dados privados permanecem locais. A matriz registra o que foi instalado, observado e o que aguarda reteste. Nenhuma mudança no controlador de conjuração, no conteúdo dos baús ou na política de cargos. O pacote R28 anterior continua identificável; a recuperação não certifica o oficial.
