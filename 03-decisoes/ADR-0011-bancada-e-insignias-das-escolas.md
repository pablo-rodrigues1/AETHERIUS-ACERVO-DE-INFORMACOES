---
titulo: Bancada exclusiva e insígnias vinculadas às escolas dos Magos
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-15
tags: [guildas, escolas, insignias, inventario, biblioteca]
relacionados: [../02-sistemas/guildas/README.md, ADR-0009-bibliotecarios-fabricacao-e-traducao.md, ADR-0010-registros-detalhados-de-guildas.md]
---

# ADR-0011 — Bancada e insígnias das escolas

## Resumo

Somente bibliotecários abrem a bancada da Biblioteca. Líder e professores definem a escola de um membro e o servidor entrega a insígnia correspondente do Elemental Vision, protegida contra transferência e perda na morte.

## Contexto

O dev esclareceu que o membro comum não deve abrir a bancada nem para consultar. Também pediu seleção de jogador e escola no painel e entrega automática de insígnia que não possa ser descartada, guardada, trocada, dada, roubada ou perdida ao morrer.

## Decisão

1. Exigir a função de bibliotecário no servidor para abrir a bancada e manter a sessão de fabricação. O cliente não apresenta a interação ou a aba a membros sem essa função. Um líder ou professor sem a função também não abre o craft.
2. Preservar a nomeação de bibliotecários pelo líder, acessível em Professores mesmo quando ele não é bibliotecário. A liderança continua sendo definida pela Staff.
3. Em Professores, oferecer seleção do membro e da escola a líder e professores. Cargos personalizados e a função de bibliotecário isolada não concedem essa autoridade. Continuar com uma escola por membro; só a expulsão encerra o vínculo.
4. Entregar automaticamente a insígnia correta quando a escola estiver definida, o jogador estiver conectado e o inventário disponível. Repetir o mesmo vínculo ou reconectar não entrega uma segunda unidade. Se a entrega ficar pendente, conservar a escola e tentar novamente.
5. Usar os cinco registros Mondstadt conferidos no Elemental Vision II: Alteração/Geo, Conjuração/Electro, Destruição/Pyro, Ilusão/Anemo e Restauração/Hydro. Preservar o mod original e os demais elementos/variantes.
6. Proteger a unidade vinculada no diário de inventário e nos caminhos nativos de descarte, depósito e retirada/roubo. O diário não permite diminuir ou alterar essa unidade por troca, doação ou outra movimentação comum. Apenas a rotina autorizada de entrega/reparo/revogação pode modificá-la.
7. No ciclo de morte instalado no privado, manter a insígnia no inventário durante a fase de espírito e a recuperação e excluí-la da seleção de perda de equipamentos. O adaptador é do SkyGuilds; não substitui o código de Morte nem altera as perdas dos outros itens. Restaurar os métodos originais ao desmontar o módulo.
8. A expulsão continua revogando a insígnia, conforme a regra anterior. Não introduzir saída voluntária.
9. Na implantação privada, ativar o plugin com uma cópia ESP completa, necessária ao endereçamento usado pelo SkyMP atual. Retirar apenas a flag ESL da cópia e anexá-la ao fim da ordem de carregamento nos dois lados; preservar o original, os índices anteriores, os modelos/texturas e os bundles do cliente. Instalar com jogo/MO2 e servidor fechados, backup e verificação de compatibilidade.

## Consequências

- O membro comum consulta as informações da guilda, mas não a bancada nem seu catálogo de fabricação.
- A escola definida fica persistente e as insígnias usam registros reais do mod; não substituir por itens genéricos se a dependência estiver ausente.
- A adaptação de morte tem cobertura automatizada e nativa isolada; visual no jogo e homologação oficial continuam sendo verificações separadas.
- A biblioteca autorizada, suas traduções, custos de tomos, conjuração, pertences e outras guildas não recebem novas permissões ou fórmulas.

## Fonte

[Elemental Vision II, de Caenarvon](https://www.nexusmods.com/skyrimspecialedition/mods/120246). O mapeamento foi conferido nos arquivos instalados, sem redistribuir os assets no acervo.
