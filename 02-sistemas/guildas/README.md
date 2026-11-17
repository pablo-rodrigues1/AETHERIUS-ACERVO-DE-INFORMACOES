---
titulo: Guildas (SkyGuilds)
tipo: sistema
status: ativo
fase: em-teste
responsaveis: [Melekyy]
repositorio: null
depende_de: [../gameplay-core/README.md]
atualizado: 2026-11-17
tags: [guildas, skyguilds, magos, alquimistas, companions]
relacionados: [escopo.md, integracao-skymp.md, validacao-skymp.md, diario.md, ../ui-core/README.md, ../painel-adm/README.md, ../../03-decisoes/ADR-0021-bau-coletivo-do-colegio.md, ../../03-decisoes/ADR-0022-preparacao-skymp-das-guildas.md, ../../07-guia-da-staff/regras-de-roleplay/guildas.md]
---

# Guildas (SkyGuilds)

## Resumo

**Melekyy** desenvolve Magos, Alquimistas e Companions. A R28 está preparada para integração SkyMP com bootstrap verificável, configuração portátil e pacote local. O privado recebeu a atualização sem reinício. **Integração no fork oficial e homologação com dois clientes humanos continuam pendentes.**

- [Escopo completo e permissões atuais](escopo.md)
- [Instalação passo a passo, contratos, sincronização e rollback](integracao-skymp.md)
- [Evidências e limites da validação](validacao-skymp.md)
- [Guia simples da Staff](../../07-guia-da-staff/regras-de-roleplay/guildas.md)
- [Histórico de desenvolvimento](diario.md)

## Onde está o código

Projeto local `Sistema de Guildas 2`, pacote CommonJS `sky-guilds` 1.1.0. Entrada recomendada: `bootstrap.cjs`; implementação em `src/`; presets `config/skymp-panel.example.json` e `config/skymp-full.example.json`. A URL de repositório do código ainda não foi informada/configurada. Preencher `repositorio` somente quando existir URL real.

O pacote de desenvolvedor R28 permanece local; somente Markdown é publicado neste acervo. Não se publicam código, ZIP, binários, backups ou dados de jogadores.

## Funcionalidades

| Área | Comportamento atual |
|---|---|
| Três guildas | Magos azul/Arquimago; Alquimistas verde musgo/Mestre Alquimista; Companions marrom escuro/Chefe Mercenário |
| Gestão | Staff nomeia líder; líder convida/remove, administra cargos e funções. Convite exige aceite; não há saída voluntária |
| Funções | Cinco professores canônicos e vários bibliotecários nos Magos; professores alquímicos diversos; instrutores e um Forjador Mestre nos Companions |
| Exclusividade | Professor/bibliotecário dos Magos não acumulam liderança, outros cargos ou outra função; escola pessoal permanece separada |
| Classes | Três permissões virtuais de filiação, com consulta no servidor; integração real de Classes deve conectar os requisitos |
| Biblioteca | Só Arquimago/bibliotecários, na bancada; primeiro classe, depois tomos autorizados; 104 tomos atuais em 12 classes, traduções no painel |
| Fabricação | 5 papéis + 1 pedra da alma por tomo, 3 segundos por lote, progresso/animação, cancelamento e cobrança validada pelo servidor |
| Escolas | Uma por membro, definida por professor/Arquimago; só Arquimago retira; insígnia Elemental Vision II protegida e revogada ao encerrar vínculo |
| Portas | Controle geral exclusivo do Arquimago; 68 passagens/86 referências, ambos os sentidos, inclusive operador |
| Quartos | Dez quartos e compartimentos existentes; atribuição por professor/Arquimago, acesso por proximidade, conteúdo permanece ao trocar dono |
| Pertences | Baú exclusivo do Arquimago por cargo; compartimentos dos quartos pelo dono; estante coletiva para todos os membros atuais dos Magos |
| Cronograma | Escola, dia semanal/data e horário de Brasília; só Arquimago altera, membros consultam |
| Registros | Apenas líder; nomes/IDs, mudanças detalhadas e depósitos/retiradas confirmados do coletivo; 100 exibidos por guilda/5.000 retidos globalmente |

## Contratos e estado de integração

A versão completa exige journal SkySocial, extensão nativa de recibos, adaptador de morte das insígnias, autoridade Staff real, plugins/recursos e referências de mundo. O bootstrap recusa recursos ausentes antes de registrar/gravar. O perfil de painel básico não certifica a instalação completa. O SkyMP padrão da revisão auditada não contém o contrato adicional de recibos.

Modelos de painel são owner-only; ações autenticadas pelo ator/sessão e permissões revalidadas no servidor. Fabricação publica animação para vizinhos. Portas/nomes reaplicam após streaming sem reter handles nativos; estoque físico usa inventário/transporte SkyMP. Persistência exige JSON de Guildas e mundo/journal nativos consistentes.

Referência de API: SkyMP commit `2849e676fe008925384b26f34476e382a30bd877`, sem presumir compatibilidade universal ou versão mais recente. Preflight privado conferiu 115 plugins; checagem do host aprovou 151 referências e 104 tomos. Ensaio nativo com três bots confirmou estoque coletivo, permissões e logs de duas transferências. Testes simulados/nativos não substituem dois Skyrims humanos; ver matriz completa.

## Fora do módulo ativo

Livros autorais/em branco e editor continuam arquivados/desativados. [Conjuração](../gameplay-core/conjuracao.md) é independente e não integra o ZIP/importação das Guildas. Bancos, Casas, impostos, murais e seus operadores ficam fora. Não foi reinstalado nem alterado o controlador de conjuração nesta preparação.

## Próximo passo

O dev do servidor conecta os adaptadores reais de Staff/Classes/inventário/morte, confere mods e ordem nos clientes, executa o guia em homologação e o checklist com dois clientes. Só após isso liberar no oficial. O acervo documenta o escopo atual; não declara implantação oficial.
