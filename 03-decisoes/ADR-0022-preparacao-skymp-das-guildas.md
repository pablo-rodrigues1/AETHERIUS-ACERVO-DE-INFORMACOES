---
titulo: Preparação verificável das Guildas para SkyMP
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-17
tags: [guildas, skymp, inventario]
relacionados: [../02-sistemas/guildas/README.md, ../02-sistemas/guildas/escopo.md]
---

# ADR-0022 — Preparação verificável das Guildas para SkyMP

## Resumo

Distribuir o módulo ativo com bootstrap verificável, presets portáteis, ferramentas isoladas e guia de homologação, publicando somente documentação no acervo.

## Contexto

O dev pediu preparar todo o sistema de Guildas para compatibilidade/sincronização SkyMP antes de atualizar seu escopo completo no acervo. A instalação privada depende de contratos adicionais; copiar arquivos e declarar compatibilidade universal ocultaria essas dependências.

## Opções consideradas

| Opção | Avaliação |
|---|---|
| Copiar o ambiente privado inteiro | Inclui dados, binários e sistemas fora do escopo |
| Carregar parcialmente sem diagnosticar | Pode deixar permissões/inventários incompletos |
| Entrada verificável e pacote restrito | Expõe contratos e recusa instalação completa sem recursos |

## Decisão

- `bootstrap.cjs` resolve caminhos por `serverRoot` explícito, verifica recursos antes da instalação e conecta o callback real da Staff. Presets separados de painel básico e versão completa; básico não certifica recursos completos.
- Completo exige journal compatível, recibos nativos comprovados no addon exato, proteção de morte das insígnias, autoridade administrativa, catálogo autorizado, recursos e referências de mundo. Não distribuir/compilar esses binários ou inventar adaptadores de produção.
- API auditada: SkyMP `2849e676fe008925384b26f34476e382a30bd877`. O contrato adicional `skySocialReceipt-v1` não está no InventoryBinding padrão consultado. Prova isolada pode suprir uma propriedade ainda não inicializada, vinculada ao SHA-256 do addon.
- Painel usa a propriedade configurada; referências nativas são readquiridas em update; selagem reaplica após streaming; animação compensa diferença de relógio sem transferir autoridade da fabricação. Serviços aposentados não publicam permissões antigas.
- Pacote local inclui somente módulos ativos de Guildas, configurações sem dados privados, testes e quatro ferramentas portáteis. Exclui livros autorais/editor, conjuração, operadores privados, estado, backups, mods e addons. Testar a própria pasta distribuída e incluir hashes/relatório.
- Consolidar escopo, instalação, validação, guia Staff, diário e status. Publicar apenas Markdown na main do acervo. Repositório do código continua sem URL até ser informado.

## Consequências e limites

Dev do destino precisa fornecer os serviços reais, conferir mods/ordem nos dois clientes e executar o checklist de homologação, persistência, rollback e recuperação de transações. Aprovação privada/simulada não significa build oficial validado. A atualização privada preservou sessão, inventários e arquivos protegidos; não sobrescreveu o bundle carregado nem alterou conjuração. Ver [matriz de validação](../02-sistemas/guildas/validacao-skymp.md).
