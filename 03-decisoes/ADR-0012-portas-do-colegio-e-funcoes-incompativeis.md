---
titulo: Portas do Colégio e funções incompatíveis nos Magos
tipo: decisao
status: ativo
decisao: aceito
substitui: null
responsaveis: [Melekyy]
atualizado: 2026-11-15
tags: [guildas, portas, colegio, professores, bibliotecarios]
relacionados: [../02-sistemas/guildas/README.md, ADR-0010-registros-detalhados-de-guildas.md, ADR-0011-bancada-e-insignias-das-escolas.md]
---

# ADR-0012 — Portas do Colégio e funções incompatíveis

## Resumo

Líder e professores atuais dos Magos controlam as portas do Colégio por uma aba exclusiva. Uma passagem trancada bloqueia entrada e saída para todos até ser destrancada. Professor e bibliotecário não podem acumular funções.

## Contexto

O dev pediu controle de todas as portas ou de uma passagem por vez, incluindo ambos os lados. A instalação privada usa alterações do Colégio e da cidade; IDs de uma instalação vanilla ou de castelos não cobrem esse conjunto. O dev também proibiu o acúmulo de professor e bibliotecário, nos dois sentidos.

## Opções consideradas

| Opção | Benefício | Limitação |
|---|---|---|
| Só uma tranca visual no cliente | Aparência nativa | Não autoriza comandos nem impede ativação aceita pelo servidor |
| Lista por referência isolada | Cadastro simples | Pode esquecer o outro lado de uma passagem ou a segunda folha de portão |
| Passagem lógica, guarda no servidor e estado nativo no cliente | Fecha as faces em conjunto e valida autoridade | Exige catálogo conferido e teste por instalação |

## Decisão

1. Acrescentar **Portas do Colégio** ao painel dos Magos somente para o líder e os professores atuais. Bibliotecário isolado, membro comum e cargo personalizado não recebem a aba nem podem executar os comandos.
2. Oferecer trancar/destrancar todas ou uma passagem, com busca, filtro de área e estado atual. Não conceder passagem automática ao operador: a tranca vale também para líder, professores e NPCs.
3. Agrupar as duas referências de teleporte e as folhas de um mesmo portão. Conferir o registro vencedor do host, a base DOOR e o destino XTEL antes de habilitar o catálogo. Na instalação privada: 68 passagens, 86 referências, 16 pares de teleporte e quatro grupos de portão; três referências originalmente desativadas são preservadas. O portão da ponte é incluído explicitamente mesmo fora da localização College alterada pelo mod da cidade.
4. Excluir vitrines, algemas e portas vizinhas sem relação com o Colégio. Não criar ou habilitar referências desativadas por mods ou missões.
5. Persistir a política por passagem com revisão incremental. Validar identidade, autoridade atual, sessão, nonce e revisão no servidor. Salvar a alteração e seu registro juntos, com autor e diferenças antes/depois. A instalação não aplica trancas novas por padrão.
6. Recusar a ativação nativa enquanto trancado e manter a porta fechada no servidor. Replicar o estado e aplicar as chamadas de tranca/abertura no cliente somente em atualização segura, com referência carregada. Preservar o encaminhamento de ativações do SkyMP ao destrancar. Ao destrancar explicitamente, aplicar o estado destrancado; a ausência de política continua representando o padrão original do jogo.
7. A política bloqueia as passagens catalogadas; não é uma barreira global contra teleporte, movimentação administrativa ou transições já aceitas. Não reutilizar nem alterar a proposta dos portões municipais, que possui outra regra de saída.
8. Recusar professor que já seja bibliotecário e bibliotecário que já seja professor. Exigir revogação prévia pelo líder, filtrar candidatos e validar o estado persistido. Na migração privada, corrigir o conflito de teste preservando professor e revogando bibliotecário com registro.

## Consequências

- O operador precisa destrancar a passagem para poder usá-la. Apenas professores e líder alteram as portas; somente o líder continua nomeando ou revogando funções.
- Trocas de plugins exigem conferir o catálogo e a ordem no host e nos clientes. SDKs diferentes precisam homologar o transporte e as chamadas nativas.
- A R22 foi instalada no privado sem reiniciar jogo/servidor, com backups e inventários preservados. O cliente confirmou as APIs e a versão da interface.
- 112 testes automatizados e seis rechecagens após o ajuste final foram aprovados. Teste nativo isolado, sem clientes reais, recusou 166 pacotes de ativação das 83 referências ativas e liberou o teleporte entre Salão dos Elementos e Arcanaeum nos dois sentidos ao destrancar.
- A travessia humana no privado e a homologação com dois clientes reais/oficial continuam pendentes. Não houve tranca de teste aplicada ao mundo privado real.
- Não muda receita, insígnias, pertences, conjuração, vínculo Companion ou contratos de Classes.
