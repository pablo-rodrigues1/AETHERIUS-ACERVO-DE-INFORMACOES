# Investigação 18/11 — Ritual e poderes vampíricos: fronteiras de integração

Autor: Terra High. Base observada em `C:\Code\Aetherius - Servidor Local`: repositório laboratório `764c46af5bb5660265c530811195dd6f2d6ce9e6`, client-source `6e011b35ebbbb1ee8d7d2944f5cb1eeaf60f0900`. Teste do dev: `C:\Code\Aetherius - New Systems\Sobrenatural\docs\TESTE-JOGO_18-11.md`. Sem iniciar jogo ou servidor; sem edição de cliente, servidor ou modpack.

## Ritual B2–B4

`client-source\client\ui-core\modules\supernatural\supernatural-module.js:54–91` fecha e desmonta o módulo quando `ritual.start` é aceito, e `closeRitual` cancela o polling. Logo a HUD após fechar exige um ciclo de vida independente do módulo montado e uma ponte autenticada para `ritual.status`/`ritual.cancel`; também exige entrada nativa e limpeza em troca de sessão/personagem. O v1 em `C:\Code\Aetherius - New Systems\Sobrenatural\docs\CONTRATO_vinculo-do-veu.md:29,43–58` fornece duração e restante, mas apenas `{ritualId}` no cancelamento. Opus definiu por mensagem a v2: `reason?: "player" | "movement"`, desconhecido ou ausente equivale a `player`, e resultado com `endReason`; Sol deve publicá-la e implementá-la. Não se deve presumir entrega da v2 no servidor até verificação.

A combinação de ciclo de vida, entrada, autenticação, temporização, sincronização e concorrência com trabalho do servidor atravessa mais de três módulos e entra na classificação alta do AGENTS. O despacho determina parar e devolver quando a complexidade superar a prevista; portanto não houve implementação especulativa. B4: o SNDR/SOUN ritual vanilla ainda não foi identificado; requer localizar e verificar um marcador reproduzível e controle de encerramento junto da replicação pública do servidor.

## Vampiro V2, V4 e V7

`source\server\gamemode\supernatural\powers.js:15–65,111,191` contém a função de apresentação serializada como propriedade de servidor para dono e vizinhos. Bat envia os eventos de animação `ShoutStart` e `ShoutSprintLongStart`, sem deslocamento explícito; isso é evidência coerente com o relato, não prova de causa única. Alterar esse ponto requer mudança em fonte e overlay do servidor e validação de rede; a orientação atual limita a edição a arquivos de cliente. O dash e os sons precisam de solução coordenada com Sol.

houseCARL, perfil `AETHERIUS - LOCAL`, epoch `e2-752c963c88dbd212`, verificou `Curse of the Vampire.esp` ativo. O MGEF vencedor `09832C:Curse of the Vampire.esp` (`COTV_DevouringSwarmEffect`) referencia sons `03F2C1:Skyrim.esm` SheathDraw, `03EDD5:Skyrim.esm` Charge, `03F1F0:Skyrim.esm` Ready e `03EDD4:Skyrim.esm` Release; são referências de efeito e não foi comprovado que possam ser diretamente reproduzidas via `Sound.play`. O MGEF `68CB91:Curse of the Vampire.esp` da forma morcego e `0DF140:Curse of the Vampire.esp` da névoa têm lista Sounds vazia; não presumir som de ativação de todos os poderes sem rastrear suas outras cadeias de records. Não houve escrita pelo houseCARL.

## Verificação pendente

Nenhum teste automatizado ou em jogo foi executado; nenhuma correção foi implementada nem revisada. Teste com dois jogadores: V2 deslocamento e visual para observador; V3 invisibilidade para observador; V4 áudio e enxame, dano/dreno em alvo elegível e visual de ambos; V5 detecção de jogadores e NPCs no ambiente oficial. Também validar B2/B3, emotes K, motivo exibido e ausência de recompensa após cancelamento, e B4 perto/longe com dois jogadores e fim do som.
