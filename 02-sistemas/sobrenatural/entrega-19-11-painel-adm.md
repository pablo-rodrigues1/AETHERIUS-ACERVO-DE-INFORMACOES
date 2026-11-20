# Painel Administrativo — parte do Sistema Sobrenatural

A maldição **só** é concedida e removida pelo Painel Administrativo (F7 no jogo). Esta pasta reúne tudo do painel que diz respeito ao Sobrenatural. Os arquivos do servidor também estão no patch cumulativo (`01-servidor`); o patch do frontend F7 está **só aqui**, porque esse arquivo é distribuído pelo mod Meridian e não pelo bundle do cliente.

Documentação completa: `../05-documentacao/ETAPA-2_painel-f7.md` e a revisão `../05-documentacao/REVISAO-2_painel-f7_terra.md`.

## 1. Conteúdo
```
06-painel-adm/
├─ servidor/                         ← versões finais (servidor/gamemode/admin-panel/)
│  ├─ supernatural-admin.cjs         ← NOVO: executa as ações supernatural.* (validação, autoridade, sessão)
│  ├─ extension-catalog.cjs          ← registra as 8 ações na categoria "supernatural"
│  ├─ aetherius-tools.cjs            ← liga supernaturalAdmin.ACTIONS ao despachante de ferramentas
│  ├─ service.cjs                    ← cópia do operador/alvo e parâmetros para auditoria
│  ├─ staff-policy.cjs               ← supernatural.* exige o escopo de staff "characters"
│  ├─ audit-details.cjs              ← detalhes no log de auditoria (XP antes/depois, revisão, resultado da forma)
│  └─ aetherius-store.cjs
└─ cliente-f7/
   ├─ 004-admin-panel-client.patch   ← patch do frontend F7 (client/admin-panel/app.js)
   └─ app.js                         ← versão final testada (mod "AETHERIUS - MERIDIAN - COMPATIBILIDADE (LOCAL)")
```

## 2. Ações (aba "Sobrenatural" do F7)
Todas são registradas com a permissão **`characters.class`** (`extension-catalog.cjs:74`) e mapeadas para o escopo de staff **`characters`**, o mesmo de `class.set`. Todas exigem também, um alvo selecionado e um motivo com pelo menos 5 caracteres. Todas são auditadas.

| Ação | Rótulo | Parâmetros | Regra |
|---|---|---|---|
| `supernatural.grant` | Setar maldição | `curse`: vampire / werewolf / witch | Remova a maldição atual antes de trocar (no máximo uma por jogador). Grava a keyword permanente **só no servidor** (`JogadorVampiro`, `JogadorLobisomem` ou `JogadorBruxa`) e pede o reset de classe. |
| `supernatural.remove` | Remover maldição | — | Remove a maldição e **apaga todo o progresso** (perigoso). |
| `supernatural.xp` | Ajustar XP da maldição | `amount`: inteiro, aceita negativo | Total limitado entre 0 e 2.000.000. O campo do frontend aceita sinal negativo graças ao patch 004. |
| `supernatural.discipline` | Escolher disciplina | `id`: celeridade / fortitude / potencia / feiticaria_do_sangue / tenacidade | Vampiro. Usa a vaga disponível; não permite troca nem ignora o estágio exigido. |
| `supernatural.vertente` | Escolher vertente | `id`: mortui / daemones / tenebrae | Bruxa. Não permite troca. |
| `supernatural.endForm` | Encerrar forma (Lorde/Fera) | — | Encerra a forma temporária sem remover a maldição. |
| `supernatural.forceTransform` | Forçar transformação | — | **Teste:** antecipa as 72 h online e inicia a sequência normal. Exige noite (20h–5h), lobisomem vivo e sem forma ativa (perigoso). |
| `supernatural.status` | Consultar estado sobrenatural | — | Consulta maldição, estágio, XP e revisão. |

Se o alvo estiver offline ou carregando, a aplicação dos efeitos fica para o próximo login (`effectsDeferred`), e o log de auditoria mostra isso.

## 3. Frontend F7 (`cliente-f7`)
O patch 004 muda `client/admin-panel/app.js`:
- nova aba **"Sobrenatural"**, que só aparece se o servidor anunciar alguma ação `supernatural.*` habilitada para o operador;
- `supernatural.*` mapeada para o escopo `characters`;
- detalhes de auditoria específicos (maldição, delta de XP, escolha, XP antes/depois, revisão, resultado da forma);
- campos numéricos com `min < 0` aceitam o sinal "-" (usado no XP);
- rodapé do modo de jogo: "Sessão do jogo validada · ações de staff no servidor".

**Verificado:** o patch aplica limpo (`git apply --check`) no `client/admin-panel/app.js` do `aetherius-client-main` atual.

**Onde instalar:** o CEF carrega o painel de `MeridianUI\aetherius-client\admin-panel\app.js`, dentro do mod **`AETHERIUS - MERIDIAN - COMPATIBILIDADE`**, e não de `Platform/UI`. No teste foi usada uma cópia completa do mod, `(LOCAL)`. No oficial, aplique o patch no repositório do cliente e atualize o `app.js` do mod `AETHERIUS - MERIDIAN - COMPATIBILIDADE` original nos perfis oficiais.

## 4. Painel web oficial (eksmurf/Painel_Admin_Aetherius)
O repositório https://github.com/eksmurf/Painel_Admin_Aetherius **não foi alterado**. O contrato acima (ids das ações, parâmetros, escopo `characters`, auditoria) é a referência para expor as mesmas ações no painel web. O servidor já valida tudo sozinho: autoridade, sessão, uma maldição por jogador, faixas de XP e janela noturna. O painel web só precisa enviar a ação, o alvo, os parâmetros e o motivo pela API administrativa existente.

## 5. Checklist
- [ ] Arquivos do servidor aplicados (já vêm no `01-servidor/sobrenatural-servidor.patch`; `aetherius-tools.cjs` divergiu do oficial, mas a mesclagem automática resolveu: revisar).
- [ ] Patch 004 aplicado em `client/admin-panel/app.js` e distribuído no mod MERIDIAN - COMPATIBILIDADE.
- [ ] Staff com o escopo `characters` vê a aba Sobrenatural; sem o escopo, não vê.
- [ ] Testar grant → status → xp (+ e −) → vertente/disciplina → endForm → remove, conferindo o log de auditoria.
- [ ] Decidir se `forceTransform` fica disponível em produção (foi criado para teste).
- [ ] P10: Lorde Vampiro só por função específica do painel. Está decidido, mas **ainda não implementado**; hoje o Lorde é liberado a partir do estágio Neófito.
