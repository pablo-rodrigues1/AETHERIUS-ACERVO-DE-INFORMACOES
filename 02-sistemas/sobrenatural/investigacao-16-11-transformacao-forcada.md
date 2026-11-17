---
titulo: Transformação forçada - guardas e apresentação
tipo: referencia
status: rascunho
responsaveis: [pablo-rodrigues1, kdu]
atualizado: 2026-11-16
tags: [sobrenatural, lobisomem, servidor, testes]
relacionados: [README.md, diario.md]
---

# Investigação 16/11 — guardas e apresentação da transformação forçada

Autor: Sol High. Base: commit raiz `79098ad`, fonte `310cb598fd382cf4d6598cb34eada5db1a3fa9f8` em `C:\Code\Aetherius - Servidor Local`. Sem teste no jogo; achados por leitura do código, houseCARL e testes Node. Revisão independente pendente.

## Achados e evidências

1. `source/server/gamemode/werewolf-service.js` validava keyword na entrada, mas durante `turning` só conferia online/vida antes de `becomeBeast`. Remover/reconceder maldição durante a animação permitia finalizar a raça antiga; uma troca de personagem podia também alcançar a sessão transitória. Agora a sessão guarda grantId/characterId, revalida antes da raça e no tick e cancela sem restaurar o rosto antigo sobre um personagem substituto. A fase leaving continua seu timer de limpeza após remover a maldição.
2. `source/server/gamemode/supernatural/forced-werewolf-client.js` aplicava o filtro hold só uma vez. houseCARL no perfil AETHERIUS - LOCAL confirmou `000801:AETHERIUS - LOBISOMEM.esp`, `AethLobisomemSangueSeguraImod`, Duration=3 s (epoch e2-880b44abe6db79b6); o pré-aviso precisa dele por 10 s. Correção: remover/reaplicar a cada 1,5 s enquanto a propriedade privada é renovada; null/death/watchdog removem a instância própria.
3. As fontes do rascunho existiam somente em `source/`, ignorada pelo Git raiz. Versionados os dois módulos novos, o serviço kdu adaptado e o executor F7 em `overlays/sobrenatural/files/`; boot/catálogo/auditoria em `009-transformacao-forcada.patch`. Não se alterou o pacote original nem o modpack.

## Verificação

`node --test overlays/sobrenatural/teste-transformacao-forcada.cjs` testa morte/remoção/reconcessão/desconexão durante turning, troca de personagem sem sobrescrita da aparência, limpeza de leaving, renovação finita do IMAD e watchdog. A suíte combinada passou 122 testes, incluindo 29 de compulsão, e os scripts de serviço/F7 passaram. `evidence/forced-base-tests.log` registra os resultados. Roteiro humano e limites em `C:\Code\Aetherius - New Systems\Sobrenatural\docs\ETAPA-2_transformacao-forcada.md`; nenhum crash, som ou efeito foi declarado homologado só pelo mock.
