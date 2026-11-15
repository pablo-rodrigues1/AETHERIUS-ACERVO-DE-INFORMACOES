---
titulo: Changelog
tipo: historico
status: ativo
responsaveis: []
atualizado: 2026-11-15
tags: [changelog]
relacionados: []
---

# Changelog

## Resumo
Mudanças relevantes por versão, da mais recente para a mais antiga. Formato baseado em [Keep a Changelog](https://keepachangelog.com/pt-BR/).

## [Não lançado]
### Adicionado
- Guildas: aba de produção renomeada para Biblioteca, com classes, categorias e efeitos base dos tomos; antiga Biblioteca retirada. R16 instalada no privado, 12 testes aprovados e confirmação visual em jogo pendente. [Decisão](../03-decisoes/ADR-0006-biblioteca-e-indicacoes-de-magias.md), [diário](../02-sistemas/guildas/diario.md).
- Planejamento COMÉRCIO aprovado: estabelecimentos adquiríveis, contratação de profissionais, salários e Imposto de Comércio mensal definido pelo Rei; referência estrutural em Residência, consequências econômicas e políticas pendentes. Incluídos [texto completo](../02-sistemas/expansao-dos-paineis/comercio-empresas-e-profissoes.md), [ADR-0005](../03-decisoes/ADR-0005-comercio-empresas-e-profissoes.md) e guia da Staff; sem implementação ou cobrança nova nesta publicação.
- Conjuração: acompanhamento e duas criaturas instalados e confirmados no privado; complemento separado para v0.38, com backup e reversão, ainda pendente de integração/homologação oficial. [Estado e evidências](../02-sistemas/gameplay-core/conjuracao.md), [ADR-0004](../03-decisoes/ADR-0004-adaptacao-isolada-da-conjuracao.md).
- Conciliação de feitiços: Classes - Progressão B23:F40 e fichas da Staff com os 164 registros de Classes, por tier; catálogo no Acervo e no GameplayCore, com divergências de authorizedSpells/allSpellsList documentadas. Apenas planilha e documentação, sem alteração de runtime. [Catálogo](../02-sistemas/gameplay-core/feiticos-por-classe.md).
- Documentadas as entregas Loja 0.7.0, Residências 0.5.3, Painel ADM 2026.11.13.1 e governos ADM1/REIS1/TABFIX1, com as cinco expansões planejadas em 13/11. [Inventário das entregas](../06-referencias/inventario-aetherius.md).
- Criação do acervo de informações ([ADR-0000](../03-decisoes/ADR-0000-adocao-do-acervo.md)).
- Regras de integração contínua por IA, diário por sistema, quadro de status ([ADR-0001](../03-decisoes/ADR-0001-integracao-continua-por-ia.md)).
- GameplayCore SYNC-1114 (`05f5a0f`): skills de classe, capacidade de invocação, `class.level`, Anti-Mago com Maestria em Conjuração, chaves `character:<uuid>` ([diário](../02-sistemas/gameplay-core/diario.md)).
- UI Core SYNC-1114 (`e9075d0`): criador de personagem que finaliza, descrições pela bridge, tela de carregamento, fonte Sovngarde ([diário](../02-sistemas/ui-core/diario.md)).
- Guia da Staff: classes, progressão, masmorras, manutenção, fichas por classe, raças e pedras; design de poções, encantamentos e maldições ([ADR-0003](../03-decisoes/ADR-0003-codigo-prevalece-sobre-planilhas-de-design.md)).
- Processo de sincronização dos Cores ([ADR-0002](../03-decisoes/ADR-0002-sincronizacao-dos-cores-com-os-repositorios-oficiais.md)).
- Verificação de sistema já existente com confirmação do dev (REGRAS 10.5.1).
- Pasta `07-guia-da-staff/` com guias didáticos para toda a Staff (REGRAS 11).
- Painel de progresso `07-guia-da-staff/progresso.md` (REGRAS 11.4).
