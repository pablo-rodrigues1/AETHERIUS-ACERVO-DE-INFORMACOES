# Instalação — Miggyluv's Eyes

**Data:** 18/11/2026  
**Resultado:** bloqueada antes do download; instalação e ativação não realizadas.

O controle de tela `orca computer list-apps --json` falhou com `accessibility_error`: o Windows informou que `C:\Users\pablo\AppData\Local\Programs\orca\resources\computer-use-windows\runtime.ps1` contém conteúdo considerado mal-intencionado e foi bloqueado pelo antivírus (`ScriptContainedMaliciousContent`). Conforme instrução expressa, a operação foi interrompida sem contornar a proteção. Nenhum download, instalação, alteração de perfil ou escolha de FOMOD foi realizado.

O houseCARL 2.0.0+5e3c744862bc17c85ebe850830603a9a3e6f0779 leu a instância `C:\modOrganizer`: perfil ativo `AETHERIUS - GRAFICO - QUALIDADE`. Inspeção somente leitura do perfil `AETHERIUS - LOCAL` com filtro `Miggyluv` não encontrou mod na modlist nem plugin na load order. Os arquivos do mod, o possível ESP/ESL e as variantes Magic Light, Magic Dark e Magic Glow não puderam ser verificados; a existência ou tipo desses records permanece desconhecida.

**Pendente:** após o responsável resolver legitimamente o bloqueio do antivírus, repetir o download exclusivo do arquivo MAIN v2.2 no Chrome, instalar pelo MO2, ativar somente no perfil `AETHERIUS - LOCAL` e verificar arquivos e records conforme a tarefa original.

## Tentativa de controle nativo do Codex

A pedido do coordenador, sem alterar o antivírus, foi inicializado o pacote `@oai/sky` da skill `computer-use`. A chamada `sky.list_apps()` falhou: `Computer Use native pipe is unavailable: failed to connect native pipe: O sistema não pode encontrar o arquivo especificado. (os error 2)`. Portanto, o controle nativo de aplicativos Windows também não está operacional nesta sessão. Ferramentas presentes: `mcp__node_repl` com `@oai/sky` (pipe indisponível); `mcp__cua_repl` para navegadores, com APIs de aplicativos nativos desabilitadas; `orca computer` (bloqueado pelo antivírus). Nenhuma ação no Chrome ou MO2 foi executada.

## Nova verificação do controle nativo

No novo despacho, `@oai/sky` via `mcp__node_repl` novamente retornou em `sky.list_apps()`: `Computer Use native pipe is unavailable: failed to connect native pipe: O sistema não pode encontrar o arquivo especificado. (os error 2)`. O controle de aplicativos nativos não está disponível; `mcp__cua_repl` fornece controle de navegador, mas suas APIs de aplicativos nativos estão desabilitadas. `orca computer` continua fora de uso por ter sido bloqueado pelo antivírus. Nenhum download ou instalação foi feito nesta nova tentativa.

## 18/11/2026 — Instalação concluída (Opus, após download manual do dev)

- Arquivo: `C:\modOrganizer\downloads\Miggyluv's Eyes - Main Mod 155332 2.2 2026-06-25T15-49Z 8gJHT4B7Q.zip` (Nexus 155332, fileid 767970). Sem FOMOD: extraído direto para `C:\modOrganizer\mods\Miggyluv's Eyes\` (`Miggyluv's Eyes.esp`, `meshes\`, `textures\`, `meta.ini`).
- Ativado **somente** no perfil `AETHERIUS - LOCAL`: `+Miggyluv's Eyes` no topo do `modlist.txt`; `*Miggyluv's Eyes.esp` no fim de `plugins.txt` e `loadorder.txt`. Backups `*.bak-miggy` no mesmo perfil. MO2 estava aberto no perfil `AETHERIUS - GRAFICO - QUALIDADE` (não afetado); ao abrir `AETHERIUS - LOCAL` no MO2, conferir que o mod aparece ativo (refresh).
- houseCARL (leitura do plugin fora da load order): variantes **Magic são HeadParts de olhos** (HDPT), por raça e sexo:
  - `MiggyEyesMagic01`/`02` (+`F` feminino) — humanos (raça 0A8023 e afins); Bosmer, Altmer, Dunmer, Orc, Khajiit com `Magic01`/`02`; Argonian só `Magic01`.
  - **Glow** (`MiggyEyesMagicGlow`/`MagicFGlow`) só para humanos, Bosmer, Altmer e Dunmer, com malha brilhante `meshes\...\eyeglow\glow\eyes*_magicGlow.nif`. **Sem Glow para Orc, Khajiit e Argonian** — decisão pendente para a sobrecarga nessas raças (ex.: usar Magic01).
  - Qual de `Magic01`/`Magic02` é "Light" e qual é "Dark" deve ser confirmado pelas texturas `MiggyEyes_Magic01.dds`/`Magic02.dds` na implementação.
- Troca em jogo: via headpart de olhos sincronizada pela aparência (appearance) do SkyMP — a ser implementada na Etapa 4 (Sol High).
