---
titulo: Adaptação isolada da conjuração
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-15
tags: [conjuracao, compatibilidade, backup, instancias]
relacionados: [../02-sistemas/gameplay-core/conjuracao.md, ADR-0003-codigo-prevalece-sobre-planilhas-de-design.md]
---

# ADR-0004 — Adaptação isolada da conjuração

## Resumo

Complementar apenas a conjuração, preservando o pacotão anterior e mantendo a aprovação do privado separada da homologação oficial. Acompanhamento e duas criaturas foram solicitados pelo dev; a instalação privada também foi autorizada.

## Contexto

O pacote v0.38 de 14/11 declara a invocação em aberto. O privado tem acompanhamento e persistência próprios, mas seu controlador estava arquivado. As estruturas de código diferem, e os fontes completos da v0.38 pura não foram encontrados.

## Opções consideradas

| Opção | Efeito |
|---|---|
| Substituir o pacote inteiro pelo privado | Mistura estruturas e regras e pode desfazer correções anteriores |
| Complemento restrito, com backup e verificações de aplicação | Preserva o trabalho anterior e permite integrar apenas a conjuração |
| Manter o controlador privado desativado | Não atende ao pedido de acompanhamento e duas criaturas |

## Decisão

1. Salvar o pacote original e o estado anterior privado antes de alterar ou instalar.
2. Reativar o privado usando uma definição visível nova, V5, preservando a V4 arquivada; manter duas criaturas e a política anterior de duração do privado.
3. Distribuir a adaptação v0.38 separadamente. Preservar as correções anteriores de amizade, autoridade e descarte; não conservar uma criatura apenas por ser cópia local da magia.
4. Executar acompanhamento pelo host real e liberar o pacote em combate, mudança de host ou área. Preservar o pacote nas limpezas do renderer enquanto o host o controla.
5. Manter os limites e as durações oficiais por padrão. O limite fixo de duas no complemento depende de opção explícita `--two`; não alterar silenciosamente a composição de capacidade do Core.
6. Recusar aplicação quando os pontos conhecidos do código não corresponderem. Registrar as evidências do privado e os limites da adaptação, sem declarar homologação oficial.

## Consequências

- O privado recebe a funcionalidade solicitada com a versão anterior preservada.
- O instalador e a reversão conferem hashes, e o complemento pode ser revisado isoladamente.
- O servidor oficial ainda precisa fornecer a base completa e executar build/testes e homologação humana.
- A ponte nativa V7 exige Skyrim 1.6.1170.0; o caminho alternativo e outros runtimes precisam de validação própria.
