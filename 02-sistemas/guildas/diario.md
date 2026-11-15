---
titulo: Diário de Guildas (SkyGuilds)
tipo: historico
status: ativo
responsaveis: [Melekyy]
atualizado: 2026-11-15
tags: [diario, guildas, skyguilds]
relacionados: [README.md]
---

# Diário de Guildas (SkyGuilds)

## Resumo

Registro do desenvolvimento de Guildas por Melekyy, do mais recente para o mais antigo.

## 2026-11-15, ajuste da ordem do catálogo iniciado (Melekyy / Codex)

- **Pedido:** continuidade da alteração de Biblioteca já autorizada: apresentar primeiro as classes e, dentro da classe escolhida, seus tomos.
- **Decisão de apresentação:** separar escolha de classe e lista de tomos, com retorno à lista de classes; preservar categorias, efeitos, receita e autorização.
- **Próximo:** implementar a navegação e validar filtragem e atualização privada sem reinício.
- **Bloqueios:** nenhum nesta etapa; homologação oficial continua pendente.

## 2026-11-15, Biblioteca e indicações de magias entregues (Melekyy / Codex)

- **Feito:** aba Biblioteca antiga retirada da interface; aba da produção renomeada para Biblioteca. Cada tomo mostra classes e categorias autorizadas e a descrição do efeito base, quando disponível. A liderança conserva a nomeação do bibliotecário na Biblioteca.
- **Decisão:** [ADR-0006](../../03-decisoes/ADR-0006-biblioteca-e-indicacoes-de-magias.md), usando a política vigente e os registros dos plugins; sem alterar o aprendizado, a receita ou os feitiços.
- **Verificado:** 12 testes de interface, catálogo e apresentação aprovados. Atualização privada R16 aplicada sem reiniciar e com backup; 104 tomos autorizados, todos com associação de classe, e 103 com descrição de efeito disponível. Dados, catálogo de autorização e arquivos protegidos preservados.
- **Limite:** o personagem de teste não estava conectado ao privado durante a aplicação; a nova fonte de interface está registrada para a próxima conexão. A confirmação visual em jogo e a homologação oficial continuam pendentes.
- **Impacto:** nenhum ajuste de contrato no Core ou no ritual de coleta; a busca da Biblioteca também reconhece classe e categoria. A conjuração reativada permanece separada.
- **Próximo:** confirmar a apresentação no jogo e seguir as integrações oficiais de Staff e Classes.
- **Bloqueios:** nenhum de publicação; integração oficial segue pendente.

## 2026-11-15, início do ajuste da Biblioteca (Melekyy / Codex)

- **Autorização:** o dev confirmou a continuidade de Guildas, já sob responsabilidade de Melekyy e em desenvolvimento, para esta alteração de interface.
- **Escopo aprovado:** retirar a Biblioteca antiga; chamar a aba de produção de tomos de Biblioteca, mantendo o funcionamento; indicar classes, categorias e efeitos das magias.
- **Fonte:** a associação de classe/categoria vem da política autorizada existente; efeitos serão consultados nos registros dos plugins carregados. A planilha não amplia o catálogo nem redefine o feitiço.
- **Próximo:** implementar e verificar a apresentação no privado, preservar permissões e publicar o resultado.
- **Bloqueios:** nenhum nesta etapa; integração e homologação oficiais continuam pendentes.

## 2026-11-15, impacto da conjuração (Melekyy / Codex)

- **Feito:** [conjuração](../gameplay-core/conjuracao.md) reativada no privado, com acompanhamento e duas criaturas confirmados pelo usuário; complemento v0.38 separado e ainda candidato.
- **Impacto:** biblioteca, tomos, permissões, catálogo de Classes, custos de magicka e livros autorais não foram alterados. Não interpretar o limite privado de duas como mudança automática da capacidade oficial do Core.
- **Próximo:** homologar o complemento sobre a revisão oficial e manter as integrações de Guildas alinhadas ao runtime real.
- **Bloqueios:** fontes completos/homologação oficial pendentes; parte privada confirmada.

## 2026-11-14, referência de feitiços das classes (mercurius17 / Codex)

- **Feito:** disponibilizado o [catálogo documental de feitiços](../gameplay-core/feiticos-por-classe.md), com 164 registros da planilha e diferenças dos JSONs do GameplayCore.
- **Impacto:** biblioteca, tomos, permissões e vínculo de Classes do SkyGuilds não foram alterados. A lista documental não substitui o catálogo autorizado nem comprova uma ponte de integração.
- **Próximo:** consultar a comparação ao integrar a política de magias ao GameplayCore; alinhar os contratos com Melekyy.
- **Bloqueios:** nenhum para o registro documental; continuam as pendências de integração já descritas abaixo.

## 2026-11-14 (Melekyy / IA)

- **Feito:** Melekyy confirmou que está desenvolvendo Guildas e autorizou o cadastro no acervo. Registrados o documento técnico, o guia da Staff e a atualização dos quadros e do índice. Instalado o bloco padrão do acervo no `AGENTS.md` do projeto local, que ainda não tinha esse arquivo.
- **Verificado:** leitura do acervo e das fontes locais; importação do módulo em Node.js. Sem execução do jogo, do servidor oficial ou da suíte de testes nesta sessão.
- **Próximo:** conectar e homologar os contratos de Staff, Classes e inventário na implantação oficial; informar a URL real do código.
- **Acesso ao acervo:** tentativas iniciais bloqueadas por falha do conector e ausência de credencial local. Resolvido com a instalação do GitHub CLI e a autorização de Melekyy; autenticação e permissão de escrita confirmadas. A atualização simultânea do GameplayCore foi incorporada antes do envio.
- **Bloqueios:** nenhum bloqueio de acesso ao acervo. A integração e a homologação no servidor oficial continuam pendentes.
- **Dependências:** o ritual de coleta planejado menciona vínculo real ao clã Companion; ainda não foi conectado ao SkyGuilds. Não houve alteração de código dos sistemas dependentes.
- **Decisões:** nenhuma nova decisão de design; aplicação dos ADRs de documentação e fonte da verdade já vigentes.
