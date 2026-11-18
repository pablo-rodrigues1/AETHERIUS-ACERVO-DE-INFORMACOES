---
titulo: Artesãos e Refino
tipo: guia-staff
status: ativo
responsaveis: [pablo-rodrigues1, mercurius17]
atualizado: 2026-11-18
tags: [staff, profissoes, artesaos, ferreiro, curtidor, cozinheiro, artifice, alfaiate, refino]
fonte_tecnica: [../../02-sistemas/profissoes/artesanato.md, ../../02-sistemas/profissoes/refino-e-encantamento.md]
relacionados: [README.md, como-funcionam-as-profissoes.md, coletores.md, ../itens/manutencao-de-equipamentos.md]
---

# Artesãos e Refino

## Em uma frase
Os **Artesãos** transformam matérias-primas em armas, armaduras, roupas, comida, bebidas e acessórios, enquanto o **Refino** (planejado) melhora armas e armaduras já prontas em até três níveis com risco de falha.

## Como funciona

### 1. Bancadas isoladas por profissão
Cada artesão só abre e vê as receitas da própria profissão. Se um **Ferreiro** e um **Artífice** usarem a mesma Forja, o Ferreiro só vê receitas de Ferreiro e o Artífice só vê receitas de Artífice.

### 2. Fabricação tolerante vs. Refino arriscado
- **Criar o item comum (Artesanato):** é pensado para ser acessível. Subir de rank libera materiais melhores, **não** deixa o minigame mais difícil.
  - **Ferreiro:** 4 etapas (*Aquecer Forja → Derreter Metal → Moldar Metal → Resfriar*).
  - **Curtidor:** transformar pele em couro (`3 peles → 1 couro`) não tem minigame; fazer armadura tem 4 etapas (*Preparar Mesa → Construir Molde → Costura → Retoques*).
  - **Cozinheiro:** agora inclui também tudo que era do antigo **Cervejeiro** (vinhos, hidroméis, bebidas fortes e, no Mestre, Skooma ilegal — esta última sem bônus de economia ou duplicação).
- **Melhorar o item pronto (Refino — planejamento):** exclusivo de **Ferreiro** e **Curtidor**. É aqui que ficam a dificuldade e o risco.

### 3. Como funciona o Refino (Tier I, II e III)
1. Só vale para **armas, arcos/bestas, armaduras e escudos**, melhorando **um item específico** na ordem `Tier 0 → Tier I (+25%) → Tier II (+50%) → Tier III (+75%)`.
2. **O bônus é permanente no item:** funciona em todos os golpes ou defesas e **não gasta** nem cai por lutar, bloquear ou fazer manutenção.
3. Antes de tentar, o artesão usa um **molde de couro** (`1 / 2 / 3` couros) e faz um minigame em ciclos (encaixar a peça + sequência de teclas em 5 segundos).
4. **Se falhar demais:** perde os materiais e o item **cai 1 tier** (ex.: de Tier II tentando Tier III volta para Tier I; nunca é destruído).

## Exemplo prático
> Um Ferreiro Especialista cria um conjunto completo de armadura metálica (armadura, peito, bota, luva e escudo), consumindo **30 lingotes** no total. Depois, tenta refinar a espada para **Tier II** usando 2 couros de molde; se errar 3 vezes nos 10 ciclos, a espada volta para **Tier 0** e ele perde os materiais da tentativa.

## Tabela rápida

| Profissão | O que produz | Destaque por rank / observação |
|---|---|---|
| **Ferreiro** | Armas, flechas, armaduras pesadas, picaretas, kits | Ferro (Novato) → Guildas/10% poupar (Aprendiz) → Aço (Adepto) → Steel Plate (Especialista, teto regular) → Nordic/Dwemer/Orcish (Mestre) |
| **Curtidor** | Armaduras leves e kits de couro/pele | Pele (Novato) → Guildas/10% poupar (Aprendiz) → Couro (Adepto) → Élfico (Especialista) → Culturais (Mestre) |
| **Cozinheiro** | Comidas, vinhos, hidroméis, destilados | Inclui antigo Cervejeiro; 20% poupar ingrediente (Especialista); receitas raras + Skooma + 20% dobrar (Mestre) |
| **Artífice** | Enxadas, lanternas, tochas, bolsas, mochilas, joias, Soul Gems preenchidas | Usa várias bancadas (Forja, Curtume etc.); 20% poupar ingrediente (Mestre) |
| **Alfaiate** | Roupas comuns, capas, capuzes, roupas de guilda e nobres | 20% poupar material (Mestre); bancada própria em definição |
| **Encantador / Alquimista** | Encantamentos / Poções e venenos | **Encantador fora do Alpha**; **Alquimista em definição** |

| Regra econômica (padrão configurável) | Quantidade |
|---|---|
| Minério → Lingote / Pele → Couro | `3 → 1` |
| Set completo (metal ou couro) | `30` lingotes ou `30` couros |
| Set de pele pura | `15` couros + `5` peles brutas |
| Kits de manutenção (`50` cargas) | Ver [Manutenção de equipamentos](../itens/manutencao-de-equipamentos.md) |
| Refino Tier I / II / III | `+25%` (5 ciclos, 3 erros) / `+50%` (10 ciclos, 2 erros) / `+75%` (15 ciclos, 0 erros) |

## Perguntas comuns de jogadores
- **Onde foi parar a profissão de Cervejeiro?** Foi unida ao **Cozinheiro**. Quem faz comida também faz vinhos, hidroméis e bebidas especiais na mesma carreira.
- **O bônus de Refino (+25%/+50%/+75%) acaba depois de bater algumas vezes?** Não. Ele fica naquela peça para sempre e só muda se alguém tentar refinar de novo e falhar.
- **Falhar no Refino destrói a arma?** Não. O item não some, mas regride um tier se já tiver algum aprimoramento.
- **Para que servem os kits de manutenção feitos por Ferreiro e Curtidor?** Eles dão 50 cargas para evitar a perda de 30% de eficiência em combate (veja [Manutenção de equipamentos](../itens/manutencao-de-equipamentos.md)).

## O que a Staff pode e não pode fazer
- **Pode:** orientar sobre receitas, bancadas separadas e lembrar que Encantador está fora do Alpha.
- **Não pode:** liberar itens culturais de Mestre se estiverem bloqueados globalmente, nem devolver materiais perdidos em falha normal de refino.

## Quer o detalhe técnico?
Veja [Sistema de Artesanato](../../02-sistemas/profissoes/artesanato.md) e [Refino e Encantamento](../../02-sistemas/profissoes/refino-e-encantamento.md).
