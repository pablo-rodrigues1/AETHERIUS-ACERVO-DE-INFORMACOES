---
titulo: Sincronização dos Cores com o servidor e o cliente
tipo: processo
status: ativo
responsaveis: [mercurius17]
atualizado: 2026-11-14
tags: [gameplay-core, ui-core, sincronizacao, processo]
relacionados: [../03-decisoes/ADR-0002-sincronizacao-dos-cores-com-os-repositorios-oficiais.md, ../02-sistemas/gameplay-core/README.md, ../02-sistemas/ui-core/README.md]
---

# Sincronização dos Cores com o servidor e o cliente

## Resumo
Passo a passo para trazer as mudanças das cópias `server/gameplay/core` e `client/ui-core` de volta ao `AetheriusGameplayCore` e ao `AetheriusUI_Core`, seguindo o [ADR-0002](../03-decisoes/ADR-0002-sincronizacao-dos-cores-com-os-repositorios-oficiais.md).

## Pré-requisitos
- Checkouts atuais de `aetherius-server` e `aetherius-client`, lado a lado numa mesma pasta.
- Clones de `mercurius17/AetheriusGameplayCore` e `mercurius17/AetheriusUI_Core` na mesma pasta (os testes procuram `../aetherius-server` e `../aetherius-client`).
- Node 22, `npm ci` no GameplayCore.

## Passos

### 1. Listar as diferenças
```bash
diff -rq --strip-trailing-cr -x .git -x docs -x integrations -x node_modules -x dist \
  AetheriusGameplayCore aetherius-server/server/gameplay/core
```
No UI Core, comparar pelo mapeamento de pastas do [README do sistema](../02-sistemas/ui-core/README.md#funcionamento).

### 2. Classificar a direção de cada arquivo
Ler o `diff -u` de cada arquivo e marcar:

| Situação | Ação |
|---|---|
| Cópia oficial mais nova | copiar |
| Core mais novo (ex.: tem DS-7 e a cópia não) | manter o Core |
| Import de código fora do Core | copiar e remover a linha |
| `catalog/*.json`, `.gitattributes`, `PROVENANCE.md` | não copiar |
| Módulo de outro sistema (UI) | não copiar |
| `CMakeLists.txt` do UI | aplicar só as mudanças reais, mantendo `../frontend/Data/MeridianUI/aetheriusui` e `../tests/` |

### 3. Testar contra a linha de base
```bash
# GameplayCore
(cd modules/damage-system/compiler && ../../../node_modules/.bin/tsc -p tsconfig.json)
npm run typecheck
GAMEPLAY_CORE_TEST_EXPORTS=<exports houseCARL> GAMEPLAY_CORE_TEST_DATA_DIR=<aetherius-server/build/dist/server/data> npm test
```
Rodar o mesmo comando com `git stash` (árvore limpa) e comparar a lista de `not ok`: a sincronização não pode acrescentar falhas. Sem os exports, as variáveis podem apontar para pastas vazias; as falhas de ambiente aparecem nas duas árvores.

No UI Core: `node --check` nos JS alterados, `node --test tests/character-creation-import.test.cjs` e os testes `tests/native-*.test.cpp` (C++20 ou superior; `native-localized-presentation` precisa de nlohmann_json).

### 4. Registrar
- Nota no repositório: `docs/operations/SINCRONIZACAO_SERVIDOR_AAAAMMDD.md` (GameplayCore) ou `docs/SINCRONIZACAO_CLIENTE_AAAAMMDD.md` (UI Core): o que voltou, adaptações, o que ficou de fora, tabela de testes.
- Commit `SYNC-MMDD: <resumo>` na `main`.
- No acervo: entrada no `diario.md` do sistema, quadro de status e `CHANGELOG.md`.

## Verificação
- A lista de testes `not ok` depois da sincronização é igual à da árvore limpa (ou menor).
- `git diff` da sincronização não toca em `catalog/*.json`, `.gitattributes`, nem em arquivos marcados como "Core à frente".
- Nenhum arquivo do repositório importa caminhos fora dele (`grep -rn "\.\./\.\./\.\./" runtime/src`).
- A nota de sincronização e a entrada do diário citam o commit gerado.
