---
titulo: Origem de grants e poderes COTV
tipo: sistema
status: ativo
responsaveis: [pablo-rodrigues1, kdu]
atualizado: 2026-11-16
tags: [sobrenatural, investigacao]
relacionados: [../README.md, ../formas-e-poderes.md]
---

# Investigação 16/11 — origem dos grants, disputa de perks e poderes COTV

## Resumo

Identificadas remoções sem autoria, disputa de perks e divergências do COTV; correções locais e limites documentados, sem teste humano.

Autor: Sol High; base local 68a6e2b, servidor source 310cb598fd382cf4d6598cb34eada5db1a3fa9f8, cliente e39163cc952169c148b2fbc00002637fe7a88fe3. Raiz C:\Code\Aetherius - Servidor Local; documentação C:\Code\Aetherius - New Systems\Sobrenatural\docs. Análise de código e testes simulados; nenhum teste humano deste lote.

## Causas verificadas

1. supernatural-service::applyEffects removia qualquer magia conhecida que estivesse na união dos catálogos, mesmo de personagem sem maldição. client::perks também adotava perk que já existia antes do AddPerk. A prova reproduz ambos sem usar mods reais; registro antigo não tinha autoria recuperável.
2. Um owned.add após hasPerk não bastava: classe e Bruxa podem conceder o mesmo record nas duas ordens. GameplayGrantService já possuía autoria durável/ACK e verificação tardia de remoção; ignorar esse receptor real criaria outra disputa. Compartilhado um ledger de claims e proteção externa, mantendo os gates do protocolo.
3. Core::Actors usa characterKey=profile:N nesta cópia, enquanto Sobrenatural usa UUID de personagem. O hash de apresentação das perks foi separado por UUID do diretório ativo em ambos os transportes; não foi alterada a persistência antiga de classes por perfil.
4. O código guardado de powers.js supunha cloak sem condições. O boot 23:57 de 15/11 mostrou a condição adicional HasMagicEffect(19A676) == 0. houseCARL confirmou o marcador COTV_DevouringSwarmCooldownEffect; seu equivalente é o prazo servidor. As seis condições do absorb têm cinco exclusões HasKeyword e IsHostileToActor; esta última é substituída conservadoramente por hostilidade evidenciada em golpe aceito, sem dano automático a neutros.
5. magic-service::onSkySocialSpellCast tentava converter o Health ValueModifier da Força em cura antes da validação de poder. O domínio agora se identifica por powerActions.owns e o handler genérico só pula a parte mecânica depois de respeitar o veto anterior. Não houve bypass de locks nem do dispatcher.
6. Quests SSP só passam a concluídas após resolução/parada individual. A assinatura Ed25519 faltante explicava a recusa dos scripts JS, mas não substitui nem corrige os eventos Papyrus bloqueados; essas causas não foram confundidas.

## Solução e recuperação

ReconcileSpells persiste intenção antes do AddSpell, confirma retorno/readback e só então registra owned. Remoção só percorre ledger próprio e mantém entradas após no-op/falha. Intenção interrompida com spell presente é ambígua: preserva e não adota. Formas antigas sem ledger também são preservadas. Chave estável plugin definidor + id local evita amarrar autoria a índice de load order.

Perks externas observadas não são adotadas. As fontes gameplay/supernatural compartilham claims sobre adições da união, e a última liberação faz a remoção física. ACK de aplicação comprova entrega do cliente, não resultado de combate nem resistência contra cliente adulterado. Não há como detectar outra fonte que reentregue o mesmo record sem registrar origem depois da adição; essa limitação continua explícita.

## Validação e limites

122 testes: 93 de origem/poderes/formas/aparência/magia/kdu e 29 de Core/receptor real. Incluem persistência interrompida, duas ordens de claims e remoção, preexistência nas três maldições, upgrades/reduções TENEBRAE, falhas de Add/Remove, recargas 40/60/zero e reserva de sangue com cancelamento/confirmacão. Scripts serviço/F7 também passaram. Nenhum teste certifica engine, ranks em save, rosto/olhos, dash, Aura Whisper ou replicação real; revisão Terra e roteiro humano permanecem necessários.
