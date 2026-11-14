# Índice do Acervo

> Mantenha esta lista atualizada a cada commit. Formato: `caminho` — resumo de uma linha.

## 00 — Guia
- `00-guia/como-usar.md` — Como navegar, consultar e contribuir com o acervo.
- `00-guia/glossario.md` — Termos do projeto e do SkyMP.

## 01 — Visão geral
- `01-visao-geral/README.md` — Visão do servidor Aetherius e mapa da arquitetura.
- `01-visao-geral/repositorios.md` — Repositórios do projeto e responsabilidade de cada um.

## 02 — Sistemas
- `02-sistemas/README.md` — Quadro de status, estrutura de pastas e mapa de dependências dos sistemas.
- `02-sistemas/gameplay-core/README.md` — AetheriusGameplayCore: lógica autoritativa de gameplay, módulos, parâmetros e pendências.
- `02-sistemas/gameplay-core/diario.md` — Diário do GameplayCore (sincronizações SYNC-1102 e SYNC-1114).
- `02-sistemas/gameplay-core/correcoes-de-dano.md` — Correções de dano no servidor: chaves por fase, armadura, resistências, pendências.
- `02-sistemas/ui-core/README.md` — Aetherius UI Core: interface Meridian/CEF, bridge SKSE, mapeamento de pastas cliente↔repo.
- `02-sistemas/ui-core/diario.md` — Diário do UI Core (sincronização SYNC-1114).
- `02-sistemas/lobisomem/README.md` — Lobisomem sincronizado: forma controlada pelo servidor, animação própria, dano de prata.
- `02-sistemas/lobisomem/diario.md` — Diário do Lobisomem sincronizado.
- `02-sistemas/sincronizacao-de-npcs/README.md` — Investigação da IA de NPCs travada (NPC recriado em laço no cliente).
- `02-sistemas/sincronizacao-de-npcs/diario.md` — Diário da Sincronização de NPCs.

## 03 — Decisões
- `03-decisoes/README.md` — Registro de ADRs e seus status.
- `03-decisoes/ADR-0000-adocao-do-acervo.md` — Adoção deste repositório e de suas regras.
- `03-decisoes/ADR-0001-integracao-continua-por-ia.md` — IAs documentam o desenvolvimento continuamente via commit na main.
- `03-decisoes/ADR-0002-sincronizacao-dos-cores-com-os-repositorios-oficiais.md` — O que volta do servidor/cliente para os repos dos Cores e o que fica de fora.
- `03-decisoes/ADR-0003-codigo-prevalece-sobre-planilhas-de-design.md` — Quando planilha de design e código divergem, vale o código.

## 04 — Histórico
- `04-historico/CHANGELOG.md` — Mudanças consolidadas por versão.

## 05 — Processos
- `05-processos/README.md` — Fluxos de trabalho do time.
- `05-processos/integracao-ia.md` — Como a IA se integra ao acervo; bloco padrão para o AGENTS.md dos projetos.
- `05-processos/sincronizacao-dos-cores.md` — Passo a passo para sincronizar GameplayCore e UI Core com as cópias oficiais.

## 06 — Referências
- `06-referencias/README.md` — Resumos de material externo.
- `06-referencias/planilha-classes-efeitos-encantamentos.md` — Planilha de design de classes, leveling, raças, poções, encantamentos e maldições, conferida contra o código.

## 07 — Guia da Staff
- `07-guia-da-staff/README.md` — Explicações simples de mecânicas do jogo para toda a Staff.
- `07-guia-da-staff/progresso.md` — Painel com a situação atual de cada sistema.
- `07-guia-da-staff/glossario.md` — Termos do jogo em linguagem leiga.
- `07-guia-da-staff/classes/como-funcionam-as-classes.md` — As 18 classes, marcos, troca de classe, Winterhold e ações da Staff.
- `07-guia-da-staff/progressao/nivel-xp-e-atributos.md` — XP, XP em grupo, fadiga diária e pontos de atributo.
- `07-guia-da-staff/dungeons/inimigos-e-masmorras.md` — Respawn no mundo aberto e gerações/reset das masmorras.
- `07-guia-da-staff/itens/manutencao-de-equipamentos.md` — Kits de manutenção, cargas, penalidade e /manutencao.
- `07-guia-da-staff/sobrenatural/lobisomem.md` — Como funciona o lobisomem (em teste): transformação, tamanho, prata.
- `07-guia-da-staff/classes/ficha-conjuradores.md` — Skills, perks e feitiços por marco das 6 classes conjuradoras (do código).
- `07-guia-da-staff/classes/ficha-guerreiros.md` — Skills, perks e feitiços por marco das 6 classes guerreiras (do código).
- `07-guia-da-staff/classes/ficha-especialistas.md` — Skills, perks e feitiços por marco das 6 classes especialistas (do código).
- `07-guia-da-staff/racas-e-pedras/README.md` — Tema: raças e pedras guardiãs.
- `07-guia-da-staff/racas-e-pedras/racas-e-pedras-guardias.md` — Habilidade de cada raça e de cada pedra guardiã.
- `07-guia-da-staff/itens/pocoes-e-encantamentos.md` — Design: poções e encantamentos do Alquimista e Encantador Mestre (não implementado).
- `07-guia-da-staff/sobrenatural/maldicoes.md` — Design: vampirismo, licantropia e bruxaria em 6 estágios (não implementado).
