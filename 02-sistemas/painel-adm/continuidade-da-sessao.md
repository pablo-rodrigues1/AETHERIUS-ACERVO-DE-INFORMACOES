---
titulo: Continuidade da sessão administrativa em jogo
tipo: referencia
status: ativo
responsaveis: []
atualizado: 2026-11-14
tags: [integracao]
relacionados: [README.md]
fonte: Projeto de continuidade da sessão administrativa de 08/11/2026
---

# Continuidade da sessão administrativa em jogo

## Resumo

Proposta de 08/11 para que o F7 consiga revalidar o acesso depois do vencimento inicial de 15 minutos, enquanto a mesma conexão autenticada continuar ativa.

## Fluxo proposto

1. **No login autenticado**, enquanto a credencial inicial ainda é válida, a autoridade de autenticação fornece ao servidor do jogo um vínculo renovável para a sessão administrativa daquele operador. Vincular a conta à instância e à conexão atual. O desenho deve atender também quem abre o painel pela primeira vez depois dos 15 minutos.
2. **Durante a conexão**, o backend usa esse vínculo para obter uma autorização administrativa de curta duração. A autoridade define o prazo e a política de revogação. O servidor usa o vencimento informado para renovar com antecedência quando o painel está em uso. Ao abrir ou retomar um painel ocioso, pode obter uma autorização nova sob demanda enquanto o vínculo continuar válido.
3. **Antes de liberar dados ou executar uma ação**, o servidor confere a identidade da conexão, a situação da conta e as permissões atuais da operação sobre o alvo. Todas as rotas administrativas do jogo e do site devem respeitar a mesma revogação. Revalidar a conexão após chamadas assíncronas para descartar respostas de uma conexão anterior.
4. **Na desconexão, troca de identidade ou revogação explícita**, invalidar o vínculo, cancelar renovações pendentes e descartar credenciais em memória. Uma conexão que reutilize o mesmo identificador de jogador inicia com nova autenticação. O responsável pelo login deve definir o efeito de logout global, troca de senha e remoção da sessão de origem.
5. **Se a renovação falhar**, bloquear a operação até haver autorização válida. Recusa explícita exige o fluxo oficial de autenticação. Em falha temporária, permitir nova tentativa limitada de autorização. Agrupar pedidos de renovação concorrentes e nunca repetir automaticamente uma ação administrativa pendente. Manter o comportamento de pausa por perda de foco.

O vínculo renovável fica exclusivamente no backend. A interface CEF recebe o estado de acesso. O projeto não prevê estender diretamente `sessions.expires_at`, aceitar um token inicial vencido ou usar uma chave de servidor como autorização individual.

## Contrato que precisa ser confirmado

O responsável por login/site deve indicar se já existe um mecanismo oficial de renovação ou de consulta e revogação da sessão de jogo. A integração precisa de quatro operações lógicas: estabelecer o vínculo durante o login, obter/renovar a autorização, consultar sua validade e encerrá-lo. Nomes de endpoints e formato das credenciais dependem do contrato real.

Confirmar quem emite a autorização, como o servidor se autentica, quais eventos invalidam o vínculo e como cada rota detecta uma revogação. A política precisa distinguir **vencimento normal da credencial inicial** de **revogação intencional do acesso**. Se hoje ambos resultam apenas na remoção da mesma linha, a autoridade precisa fornecer essa distinção antes da implementação.

## Critérios de aceite em homologação

| Cenário | Resultado esperado |
|---|---|
| Abrir o painel pela primeira vez após 20 minutos de jogo ou mantê-lo em uso por 45 minutos | Acesso contínuo através de mais de um vencimento inicial, sem relogar. |
| Perder o foco antes do vencimento e retomar com F7 depois | O painel valida a conexão atual e reabre para o operador autorizado. |
| Rebaixar cargo, banir, revogar sessão ou efetuar logout global | A próxima ação protegida segue a decisão da autoridade. A autorização antiga não permite executar uma ação revogada. |
| Desconectar e reconectar durante uma renovação | A resposta da conexão antiga é descartada. |
| Ter duas consultas simultâneas no vencimento ou perder a resposta de uma entrega de item | Uma renovação em andamento por conexão. Nenhuma entrega duplicada. |
| Abrir via perfil offline sem login autenticado ou perder contato com a autoridade | Acesso administrativo permanece bloqueado até validação oficial. |

Registrar horário, tipo de evento, motivo da decisão e identificador de correlação para diferenciar expiração, revogação, vínculo perdido e indisponibilidade. Os registros devem excluir tokens e segredos.

**Próxima decisão:** revisar o contrato com a equipe de autenticação e identificar os pontos de integração na revisão em uso. Só depois orçar e implementar a mudança em um pacote separado para homologação.
