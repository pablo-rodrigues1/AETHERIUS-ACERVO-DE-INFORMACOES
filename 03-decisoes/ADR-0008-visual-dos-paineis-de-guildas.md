---
titulo: Visual dos painéis de Guildas com referência em Grupo e Reis
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-15
tags: [guildas, interface, cores, identidade-visual]
relacionados: [../02-sistemas/guildas/README.md, ADR-0007-biblioteca-primeiro-a-classe.md]
---

# ADR-0008 — Visual dos painéis de Guildas

## Resumo

Refinar a aparência das três Guildas seguindo os painéis locais de Grupo e Reis e preservando azul, verde musgo e marrom escuro.

## Contexto

O dev solicitou painéis mais bonitos e estilizados, mantendo as cores de cada guilda. As referências locais usam fundo escuro, brasões, molduras discretas, cartões, títulos serifados e botões com realce de cor.

## Opções consideradas

| Opção | Efeito |
|---|---|
| Alterar a identidade de cores | Contraria o pedido |
| Refinar os componentes existentes com as referências de Grupo e Reis | Mantém a identidade e melhora hierarquia e legibilidade |

## Decisão

1. Preservar as cores por guilda e os símbolos vetoriais existentes; não introduzir dependências de imagens ou mods.
2. Usar molduras suaves, brasão em destaque, marca d'água discreta e cartões para informações, liderança, membros e classes da Biblioteca.
3. Manter navegação, ações, controles, permissões, receita e catálogo. A Biblioteca continua primeiro por classe.
4. Atualizar o estilo na sessão aberta, retirando a folha antiga específica do SkyGuilds e preservando as abas estáveis durante as atualizações periódicas.
5. Separar prévias com dados fictícios da verificação no cliente real e da aceitação humana ou homologação oficial.

## Consequências

- As três guildas compartilham o acabamento visual, mantendo sua identidade de cor.
- Janelas menores usam disposição adaptada e rolagem, sem alterar regras de jogo.
- A atualização visual não conecta as integrações oficiais ainda pendentes.
