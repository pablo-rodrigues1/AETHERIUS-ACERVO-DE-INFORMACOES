---
titulo: Painel dos Reis para ritual de coleta nas terras do reino
tipo: referencia
status: ativo
responsaveis: []
atualizado: 2026-11-14
tags: [planejamento]
relacionados: [README.md]
fonte: Planejamento dos painéis de 13/11/2026, revisão 3
---

# Painel dos Reis para ritual de coleta nas terras do reino

## Resumo

Ritual territorial de coleta iniciado pelo Rei com mago, alquimista e Companion. O escopo é planejado. Os [contratos compartilhados](README.md) descrevem as dependências dos cinco projetos.

## Escopo confirmado

O Rei reúne um conjunto de materiais. Depois, precisa de **um mago, um alquimista e um membro do clã Companion** para prosseguir com um **ritual**. O resultado é um **buff temporário de melhoria nas coletas nas terras daquele Rei**.

Companion é vínculo de clã de um jogador, não NPC ou classe presumida. A função é exclusiva do painel dos Reis, não do painel dos Lordes. Upgrades permanentes e bônus de combate não integram este escopo esclarecido.

## Interface e sequência

Adicionar **Ritual de coleta** ao painel do Rei. Mostrar materiais exigidos/recebidos, papéis necessários, território, efeito previsto, duração, situação e tempo restante depois da ativação.

1. O Rei escolhe o ritual disponível e inicia a preparação, com cargo e reino validados no servidor.
2. Reunir materiais pelo contrato de contribuição aprovado. Registrar quantidades e recibos. Quem contribui e onde entrega ainda precisam de definição; o inventário visual não é prova autoritativa.
3. Com materiais completos, habilitar preparação dos participantes: mago, alquimista e membro do clã Companion. Não prosseguir com o ritual antes disso.
4. Validar as três participações na execução. Não aceitar nomes digitados ou caixas marcadas pelo Rei como prova de elegibilidade.
5. Executar o ritual, consumir/confirmar materiais uma vez no marco aprovado e registrar concessão territorial com início e fim.
6. Exibir benefício ativo somente depois de confirmado pelo sistema oficial de coleta. Expirar pelo relógio do servidor, removendo apenas essa concessão.

A preparação pode usar missão/objetivo com progresso. Divulgação em murais é opcional e não torna o mural autoridade sobre materiais, papéis ou conclusão.

## Participantes

Integrar classe/profissão oficiais para mago e alquimista e cadastro de membros para Companion. Não inferir por equipamento, nome ou tag do cliente. Obter o ID real do clã; não criar outro com nome parecido.

Os três papéis são obrigatórios no ritual. Ainda definir se devem ser três jogadores distintos, local/distância, presença do Rei, duração da execução, condição de combate e interrupção por morte, desconexão, mudança de classe ou saída do clã. Se alguém acumular classe e filiação, não contar automaticamente duas participações sem essa decisão.

Revalidar elegibilidade ao executar e antes da concessão. Registrar participantes e funções no recibo. Uma prova de participação em outro reino ou ritual não satisfaz este.

## Materiais e consumo

O catálogo registra receita/revisão, identificadores estáveis e quantidades dos materiais, aceitação de itens personalizados/roubados/de missão, efeito, duração e limites. Receita, intensidade e duração ainda não estão definidas.

Separar contribuição, reserva, consumo e eventual devolução. Definir quando os materiais deixam de ser recuperáveis e o tratamento de interrupção/cancelamento. Proposta técnica: reserva durável com consumo final ligado à concessão; conciliar com a política de perdas/devoluções antes de habilitar.

Para inventário nativo e estado persistente separados, usar recibo e reconciliação. Não remover material e gravar progresso depois sem recuperação. Na última contribuição concorrente, aceitar só a quantidade necessária ou exigir confirmação do excedente; não apagar itens extras.

## Buff territorial de coleta

### Afiliação das cidades — etapa futura

A função de afiliação no painel do Lorde fica adiada para a implementação futura deste sistema de ritual/buff. Não adicionar agora botão, fluxo ou exigência de afiliação no painel do Lorde.

Quando essa etapa for executada, a cidade do Lorde afiliada a um reino recebe o buff temporário ativo daquele reino. Cidade sem afiliação não recebe; cidade afiliada a outro reino não recebe o buff deste. A afiliação sozinha não gera bônus: o reino precisa ter realizado o ritual e sua concessão precisa estar vigente.

O servidor deverá consultar o vínculo oficial cidade–reino para decidir a elegibilidade territorial. Esse vínculo político é diferente da filiação individual ao clã Companion, exigida de um participante do ritual. O Lorde não passa a iniciar rituais por afiliar sua cidade.

Definir nessa etapa quem solicita e aprova a afiliação e como entrada, saída ou troca de reino durante um buff afetam a concessão. Não presumir essas transições nem atribuir afiliações automaticamente por proximidade geográfica. A condição de afiliação da cidade não resolve quem, entre os jogadores nessa cidade, será beneficiado; essa política continua pendente.

### Aplicação e duração

O benefício pertence às terras do reino que realizou o ritual. O servidor determina a jurisdição do ponto de coleta e a concessão vigente no evento autoritativo. Não aceitar cidade enviada pelo cliente nem manter multiplicador pessoal que funcione fora das terras.

Definir atividades elegíveis: plantas, mineração, madeira, caça ou outras; efeito sobre quantidade, chance, velocidade ou outro parâmetro; beneficiários; fronteiras/interiores; arredondamento; acumulação com apoiadores e outros bônus; limite simultâneo e intervalo entre rituais.

O território está confirmado, mas ainda falta definir se todos os coletores nele se beneficiam ou somente habitantes/membros autorizados. Compra, transferência de itens e retirada de baú não são coleta. Repetir um evento não pode gerar bônus duas vezes.

A concessão registra reino, território, ritual/revisão, efeito, recibo e intervalo temporal. Reinício e reconexão não reiniciam duração. Ao expirar, retorna o cálculo normal sem remover bônus de outras fontes. Definir qual instante rege uma coleta iniciada antes e concluída depois do fim.

## Autoridade e recuperação

Lorde, Mão, coletor e participantes não ganham autorização para iniciar por esta função. Definir o destino da preparação/buff quando houver troca de Rei. Proposta: progresso e concessão pertencem ao reino, o antigo líder perde autoridade e o prazo continua. Não apagar materiais ou renovar duração automaticamente na sucessão.

Estados lógicos propostos: preparação de materiais, aguardando participantes, execução, aplicação pendente, ativo, encerrado e cancelado quando permitido. As transições são autoritativas e seguem as condições aprovadas.

O consumidor oficial de coleta precisa aplicar, consultar e expirar pelo ID durável. Indisponibilidade não pode mostrar buff ativo nem repetir consumo. Desabilitar novos rituais mantém conciliação e expiração dos existentes. Um texto no painel não comprova alteração na coleta.

## Etapas e entrega

1. Definir receita, efeito/duração, participação e políticas de cancelamento/repetição.
2. Na etapa futura do ritual, definir e integrar a afiliação cidade–reino; identificar contratos de jurisdição, coleta, classe/profissão e filiação ao clã Companion. Não antecipar a afiliação na entrega atual do painel do Lorde.
3. Implementar preparação, contribuição/reserva, validação dos três papéis e execução durável.
4. Integrar o consumidor de coleta e expiração territorial.
5. Conectar painel do Rei e eventual interface de contribuição; homologar com jogadores das três funções.

Entregar um ritual piloto aprovado antes de expandir o catálogo, código incremental, testes, guia e evidência de coleta real. Se a integração exigir Papyrus, registros ou DLL, usar a skill específica no chat executor e não inventar APIs nativas.

## Aceite

| Cenário | Resultado esperado |
| --- | --- |
| Lorde ou Rei de outro reino tenta iniciar | Negado |
| Materiais incompletos | Não prossegue com o ritual |
| Falta mago, alquimista ou membro Companion | Sem concessão |
| NPC, tag falsa ou pessoa fora do clã representa Companion | Participação recusada |
| Saída do clã ou perda de elegibilidade | Revalidação conforme a fase |
| Últimas contribuições simultâneas | Quantidade consistente, sem consumo indevido de excedente |
| Falha após reserva/consumo | Recuperação pelo recibo, sem perda ou duplicação silenciosa |
| Duas conclusões ou perda da resposta | Uma concessão e um consumo final |
| Coleta dentro e fora das terras | Efeito restrito à jurisdição e beneficiários aprovados |
| Cidade afiliada ao reino com ritual ativo | Elegível ao buff desse reino conforme política de coleta |
| Cidade sem afiliação ou afiliada a outro reino | Não recebe o buff desse reino |
| Cidade afiliada, mas sem ritual ativo no reino | Sem bônus pela afiliação isolada |
| Entrada, saída ou troca de afiliação durante buff | Segue política definida antes da ativação da etapa futura |
| Baú, compra ou evento repetido | Sem bônus indevido de coleta |
| Reinício, reconexão e fim da duração | Prazo preservado e encerramento correto |
| Outro bônus já ativo | Composição conforme política, sem apagar concessões alheias |

Testes de UI não comprovam aumento de coleta no jogo. O aceite exige comparar eventos e recibos elegíveis dentro/fora do território, incluindo expiração, na revisão integrada.
