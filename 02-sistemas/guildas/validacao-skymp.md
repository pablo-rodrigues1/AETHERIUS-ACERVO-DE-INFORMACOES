---
titulo: Validação de Guildas R28 e limites
tipo: referencia
status: ativo
responsaveis: [Melekyy]
atualizado: 2026-11-17
tags: [guildas, skymp, integracao]
relacionados: [README.md, diario.md]
---

# Validação e limites — Guildas R28

Revisão: 17/11/2026. Responsável: Melekyy.

Resultado final: **149 testes do workspace e 106 testes da própria distribuição aprovados**, sem falhas ou pulos. Pacote local SkyGuilds-SkyMP-2026.11.17-R28.zip, 78 arquivos, SHA-256 `1d8850eb5b831d10d1b3305f252012e976834159a5e081098b9f8248c3b66987`. ZIP e código permaneceram locais; somente os documentos foram preparados para publicação.

| Verificação | Evidência atual | Limite |
|---|---|---|
| Regras, bootstrap e sincronização | Suíte local automatizada, incluindo privacidade owner-only, sessão falsa, revogação e serviços aposentados | Host simulado e navegador headless |
| Preflight estático privado | Aprovado, 115 plugins | Não compara clientes |
| Recursos no host privado real | Aprovado, 151 referências e 104 tomos autorizados | Callback Staff verificado apenas quanto ao formato nesta checagem |
| Recibos de inventário | Ensaio isolado de escrita/leitura/preservação aprovado no mesmo addon do privado | Não testou recuperação após queda/reinício |
| Baú coletivo | Ensaio nativo isolado com 3 bots: membro deposita, outro retira, fechar/reabrir mantém estoque, terceiro/expulso bloqueados | Zero clientes humanos simultâneos |
| Auditoria coletiva | Dois movimentos reais no transporte nativo isolado, com quantidades/perfis corretos; recusas não geram movimentação | Não reconstrói histórico anterior à instalação |
| Instalação privada R28 | Cliente reportou versão R28; estado/inventários e 9 arquivos protegidos preservados, sem reinício | Não certifica todos os fluxos visuais |
| Estante coletiva no cliente | Nome “Baú Coletivo do Colégio” aplicado na referência carregada; audit instalado sem alterar conteúdo | Operação manual por dois jogadores pendente |
| Baú do Arquimago | Cliente real depositou um septim, fechou/reabriu e retirou, restaurando saldos | Prova desse compartimento; não de todos os quartos |
| Dez quartos | Ensaio nativo anterior com 60 ativações e transferência nos dez compartimentos; dono trocado com conteúdo preservado | Revalidar manualmente todos no layout público |
| Oficial | Pacote e procedimento preparados | Build/fork de destino, adaptadores reais e dois clientes ainda sem homologação |

O teste de recibos associa o relatório ao SHA-256 do addon executado. O arquivo de prova é diagnóstico do operador, não uma credencial de cliente. O módulo não supõe que um inventário sem recibo inicial tenha valor zero: verifica uma leitura nativa existente ou a prova isolada do addon exato.

A referência pública de API é o commit SkyMP `2849e676fe008925384b26f34476e382a30bd877`. O addon privado ensaiado tem SHA-256 `826a2b7560c1a34c67bb6114bb2b6d62c8d95a70b0b0a6d7d68c454d4308fc7e`. Esses dados identificam a evidência; não distribuem o binário nem garantem compatibilidade de outro fork.

O pacote inclui `VALIDATION.json` com contagens da suíte que foi executada na própria pasta de distribuição. O relatório do workspace pode conter testes adicionais de arquivos históricos e integrações privadas, excluídos do ZIP. Não confundir essas contagens. Nenhum relatório de jogador/inventário privado deve ser enviado ao acervo.
