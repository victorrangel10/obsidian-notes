---
type: manifesto
data_criacao: 2026-05-03
Categorias:
  - "[[Organização]]"
---

# Manifesto do Vault

## O que é

Vault Obsidian que serve como tracker de vida — aglutina conhecimento, hábitos diários, metas de longo prazo, finanças e listas. Organização baseada em **categorias unificadas**: toda nota carrega uma property `Categorias` (array de wiki links), e essas categorias se tornam views navegáveis via Bases.

A vault está em `/home/vrangel/Documents/Obsidian Vault/obsidian/`, que também é o root do repositório git.

---

## Como Bases viram Views (o coração da navegação)

O fluxo central do vault é uma cadeia transparente: `Base → Categoria → Notas`.

```
Bases/<Nome>.base              ← filtro: Categorias contém [[Nome]]
        ▲
        │ embed via ![[Nome.base]]
        │
Categorias/<Nome>.md           ← view navegável (lista todas as notas filtradas)
        ▲
        │ inclusão automática via property
        │
notas espalhadas pelo vault    ← com Categorias: ["[[Nome]]"]
```

### Criar uma nova categoria do zero

1. **Filtro:** cria `Bases/<Nome>.base` com `filters: { and: [Categorias.contains(link("Nome"))] }`
2. **View:** cria `Categorias/<Nome>.md` com `tags: [categorias]` no frontmatter e `![[<Nome>.base]]` no body
3. **Inclusão:** notas que devem aparecer ganham `Categorias: ["[[Nome]]"]` no frontmatter
4. Abrir `Categorias/<Nome>.md` mostra todas elas em tabela

Categorias é o sistema unificado de classificação. As "áreas de responsabilidade" do PARA (Saúde, Finanças, Carreira) são apenas categorias específicas — não pastas.

---

## Estrutura de pastas

### `Inbox/`

Captura rápida. Tudo que entra na cabeça e não tem casa óbvia: ideia solta, link salvo, recomendação de livro. Cria nota com nome livre, joga aqui sem categorizar.

Esvazia toda semana no weekly review — cada item vira Projeto, vira Referência, ganha categoria existente, ou vai pro lixo.

### `Diário/`

Destino do plugin Periodic Notes. Recebe:

- **Daily** (`YYYY-MM-DD.md`) — frontmatter com métricas quotidianas (peso, gasto, água, treino, dieta, leitura) que alimentam trackers de metas. Body com notas livres e reflexão.
- **Weekly** (`YYYY-Www.md`) — métricas agregadas via tracker blocks, processamento do Inbox, reflexão semanal, planejamento da próxima.

### `Projetos/`

Metas com prazo definido. Toda nota aqui tem `prazo`, `alvo` e `status` (`ativa` / `pausada` / `concluida`) no frontmatter. Inclui tracker block embutido lendo dados das dailies.

Quando uma meta termina, `status` muda pra `concluida` — some das views ativas mas continua acessível via `Categorias/Metas.md`.

### `Referências/`

Material de consulta de longa duração. Subpastas:

- `Cursos/` — anotações de cursos
- `Capturas/` — destino do Web Clipper
- `Listas/` — listas curadas (livros pra ler, vídeos, compras)
- Notas soltas no nível da pasta — anotações sobre pessoas, temas, conceitos

### `Bases/`

Arquivos `.base` — definições de filtro/view. Cada `.base` define UM filtro, geralmente sobre a property `Categorias`. Exemplo: `Bases/Saúde.base` filtra notas onde `Categorias.contains(link("Saúde"))`.

Os `.base` não são abertos diretamente. São consumidos via transclusão (`![[X.base]]`) por notas em `Categorias/`.

### `Categorias/`

Notas-índice — cada arquivo é uma view navegável de notas filtradas por uma categoria.

Padrão da nota:
```markdown
---
tags:
  - categorias
---
![[NomeDaBase.base]]
```

### `Templates/`

Templater templates pra criação automática de novos arquivos:
- `Daily.md` — Periodic Notes daily
- `Semanal.md` — Periodic Notes weekly
- `Meta.md` — novas metas em `Projetos/`

### `Anexos/`

Imagens e binários colados em notas.

### `Anexos/Movimentações/`

Pasta de notas de movimentação financeira individual (livro-razão unificado). Cada movimentação = uma nota com frontmatter (`direcao`, `valor`, `descricao`, `data`, `categoria`, opcional `fonte` ou `destino`). Criadas via Templater hotkey + prompts (sem fricção). Visão consolidada em `Categorias/Movimentações.md`. Dashboard mensal em `Categorias/Finanças.md`.

`direcao` discrimina: `entrada` (renda externa), `saída` (gasto/consumo), `aporte` (poupança/investimento), `resgate` (saída de investimento).

Convenção: você nunca abre os arquivos individuais. Sempre acessa via view consolidada.

### Notas no vault root

Documentos de longa duração que pertencem a uma ou mais categorias mas não cabem em uma subpasta específica vivem na raiz do vault com `Categorias: ["[[Saúde]]"]` (ou outra categoria). Exemplos atuais: `Treino PPL.md`, `Dieta.md`, `Plano Financeiro.md`. Acessíveis via `Categorias/<X>.md`.

---

## Convenção de Categorias (a property)

Toda nota relevante carrega uma property `Categorias` no frontmatter como **array de wiki links**:

```yaml
Categorias:
  - "[[Saúde]]"
```

**Não usar:**
- ❌ Strings: `Categorias: [saúde]` (sempre array de wikilinks)
- ❌ Categoria inline no body: `#saúde` — usar property `Categorias`

**Por que wiki links:** os filters de Bases usam `Categorias.contains(link("X"))`. Cada link aponta pra uma nota em `Categorias/` que embeda a Base. Backlinks aparecem de graça.

**Valores convencionados:**

| Valor | Onde aparece |
|---|---|
| `[[Diário]]` | Toda nota daily |
| `[[Semanas]]` | Toda nota weekly |
| `[[Metas]]` | Toda nota em `Projetos/` |
| `[[Saúde]]` | Notas relacionadas a saúde (treino, dieta, exames) |
| `[[Finanças]]` | Notas relacionadas a finanças (plano financeiro) |
| `[[Cursos]]` | Notas/anotações de cursos |
| `[[Vídeos]]` | Vídeos pra ver |
| `[[Movimentações]]` | Cada nota de transação financeira em `Anexos/Movimentações/` (entrada, saída, aporte, resgate) |
| `[[Organização]]` | Meta-notas sobre o vault (manifesto, skills) |
| `[[Conceitos]]` | Termos, ideias, definições referenciadas |
| `[[Pessoas]]` | Pessoas referenciadas (autores, mestres, etc) |
| `[[Lugares]]` | Lugares referenciados |

Uma nota pode ter múltiplas categorias. Ex: nota de treino que também é referência sobre fisiologia → `Categorias: ["[[Saúde]]", "[[Tema]]"]`. Aparece em ambas as views.

**Estado de meta** vai em property separada:
```yaml
status: ativa | pausada | concluida
```
Não em `Categorias` — estado muda, classificação não.

---

## Convenção de Tags (a property)

Property `tags` (nativa do Obsidian) **coexiste** com `Categorias`. Resolvem problemas ortogonais:

- **`Categorias`** — estrutura. Onde a nota vive, qual view ela alimenta. Vocabulário fechado (ver tabela acima). Drive de Bases.
- **`tags`** — tópicos cross-cutting. "Sobre o quê é essa nota?" Vocabulário livre. Drive de busca/Tag Pane/Dataview.

Exemplo: nota sobre suplementação pra testosterona pode ter `Categorias: ["[[Cursos]]"]` (estrutural — é um curso) e `tags: [suplementacao, hormonal]` (tópicos — pra encontrar quando buscar por "hormonal" sem importar se é curso, vídeo ou conceito).

```yaml
tags:
  - filosofia
  - tomismo
```

**Regras:**
- Vocabulário **livre** — descobre orgânico via Tag Pane. Consolida duplicatas só quando inflação aparecer (>50 tags ativas).
- Sem hierarquia — `tags: [filosofia]`, não `tags: [area/filosofia]`. Nesting cria fricção, busca já encontra substring.
- Frontmatter é o lugar canônico. Inline no body OK pra anotações pontuais.

---

## Stack de plugins

| Plugin | Função |
|---|---|
| **Bases** (core) | Views/queries dinâmicas via `.base` files |
| **dataview** | Queries SQL-like pra agregações cross-folder (sum, group by, médias) que Bases não cobre nativamente |
| **obsidian-tracker** | Gráficos numéricos (line/bar/summary) lendo properties das dailies |
| **Templater** | Templates dinâmicos com tokens processados na criação |
| **Periodic Notes** | Cria daily e weekly automáticos com template aplicado |
| **obsidian-tasks-plugin** | Tasks com prazo, recorrência (`🔁`), queries |
| **kanban-bases-view** | Adiciona view kanban dentro de Bases |
| **obsidian-kanban** | Boards manuais standalone |
| **obsidian-git** | Sync via git |
| **calendar** | Navegação visual de datas |
| **homepage** | Nota de abertura ao iniciar Obsidian |

---

## Frontmatter contracts

### Daily (`Diário/YYYY-MM-DD.md`)

```yaml
---
date: YYYY-MM-DD
data_criacao: YYYY-MM-DD HH:mm
peso: 76.6        # número, kg
agua: 0           # número 0-3, litros
treino: false     # bool
dieta: false      # bool
leitura: false    # bool
Categorias:
  - "[[Diário]]"
---
```

> Nota: `gasto` removido. Movimentações viraram notas individuais em `Anexos/Movimentações/` (ver schema abaixo).

### Weekly (`Diário/YYYY-Www.md`)

```yaml
---
semana: YYYY-Www
inicio: YYYY-MM-DD     # domingo
fim: YYYY-MM-DD        # sábado
data_criacao: YYYY-MM-DD HH:mm
Categorias:
  - "[[Semanas]]"
---
```

### Meta (`Projetos/<nome>.md`)

```yaml
---
type: meta
data_criacao: YYYY-MM-DD HH:mm
area: saúde | finanças | carreira | ...
prazo: YYYY-MM-DD
alvo: <número>
status: ativa
Categorias:
  - "[[Metas]]"
---
```

### Movimentação (`Anexos/Movimentações/<data> - <descricao>.md`)

Livro-razão unificado: entradas, saídas, aportes, resgates numa única estrutura.

```yaml
---
type: movimentacao
direcao: saída            # entrada | saída | aporte | resgate
data: YYYY-MM-DD
hora: HH:mm
data_criacao: YYYY-MM-DD HH:mm
valor: 25.50              # número, R$ (sempre positivo; sinal é dado por `direcao`)
descricao: Almoço shopping
categoria: alimentação    # vocabulário muda por direção (ver Templates/Movimentação.md)
fonte: Inbazz             # opcional, só pra direcao=entrada
destino: Tesouro Selic 2028  # opcional, só pra direcao=aporte ou resgate
Categorias:
  - "[[Movimentações]]"
---
```

**Categorias por direção:**
- `entrada`: salário, restituição, freela, 13º, extra, outros
- `saída`: alimentação, transporte, lazer, roupa, moto, saúde, desenvolvimento, namoro, buffer, outros
- `aporte` / `resgate`: casamento, longo_prazo, reserva, pos_casamento, outros

### Documento de categoria (vault root, Referências, etc)

Pra qualquer nota persistente que pertence a uma ou mais categorias:

```yaml
---
type: documento
data_criacao: YYYY-MM-DD HH:mm
Categorias:
  - "[[Saúde]]"     # uma ou mais categorias existentes
---
```

### Curso (`Referências/<curso>/<curso>.md` ou `Referências/<curso>.md`)

```yaml
---
type: curso
data_criacao: YYYY-MM-DD HH:mm
autor:
  - "[[Pessoa]]"
plataforma: youtube           # youtube | udemy | curso.dev | presencial | outro
status: pendente              # pendente | em_andamento | concluido | abandonado
data_inicio: YYYY-MM-DD
data_finalizacao: YYYY-MM-DD
rating:                       # 1-5, opcional
tags:
  - topico
Categorias:
  - "[[Cursos]]"
---
```

Body inclui `![[Aulas.base]]` pra view de cards das aulas filhas.

### Aula (`Referências/<curso>/<aula>.md`)

```yaml
---
type: aula
data_criacao: YYYY-MM-DD HH:mm
curso: "[[Nome do Curso]]"
ordem:                        # opcional, número da aula
tags:
  - topico
---
```

Aulas **não têm `Categorias`** — são acessadas via parent course page que embeda `![[Aulas.base]]`. A base filtra `type == "aula" AND curso == this.file.link`.

### Conceito / Pessoa / Lugar (`Referências/<nome>.md`)

Schema único, `type` distingue:

```yaml
---
type: conceito                # ou: pessoa | lugar
data_criacao: YYYY-MM-DD HH:mm
tags:
  - topico
Categorias:
  - "[[Conceitos]]"           # ou "[[Pessoas]]" / "[[Lugares]]"
---
```

### Captura (`Referências/Capturas/<nome>.md`)

Output do Web Clipper. Schema canônico:

```yaml
---
type: captura
fonte: video                  # video | artigo | gist | post
title: ...
source: <url>
autor:
  - "[[Autor]]"
published: YYYY-MM-DD
data_criacao: YYYY-MM-DD HH:mm
status: pendente              # pendente | lido
rating:                       # opcional
tags:
  - clippings
  - topico-real               # tópico real, não só "clippings"
Categorias:
  - "[[Vídeos]]"              # ou outra
---
```

---

## Processos

### Daily flow

1. `Cmd+P` → "Periodic Notes: Open today's daily note"
2. Properties view: preencher peso/gasto/agua, togglar treino/dieta/leitura
3. Body: anotar coisas livres em "Notas livres"
4. Antes de dormir: responder as duas perguntas de "Reflexão"

### Weekly review

1. `Cmd+P` → "Periodic Notes: Open this week's note"
2. Tracker blocks renderizam métricas da semana automaticamente
3. **Processar `Inbox/`:** cada item vira Projeto, Referência, ganha categoria existente, ou vai pro lixo. Inbox termina vazia.
4. Reflexão (3 prompts): rolou bem / podia ter sido melhor / aprendi
5. Anotar 1-3 prioridades pra próxima semana

### Distribuição mensal do salário

Recurring task em `Plano Financeiro.md` (seção 9) — dispara dia 5 todo mês.

Workflow:
1. Distribuir salário conforme tabela (seção 2 do mesmo doc): poupança primeiro, gastos depois
2. Marcar task como done — `obsidian-tasks` cria a próxima automaticamente
3. (Opcional) registrar a distribuição na daily de hoje em "Notas livres"

### Tracking de metas

Cada meta tem tracker block embutido lendo properties das notas relevantes:

- **Casamento - Juntar 50%** → `valor` cumulativo das notas em `Gastos/` (bar chart)
- **Perder 7kg** → `peso` ao longo do tempo, das dailies (line chart)

Logar gasto via `Gastos/` (Templater hotkey) alimenta automaticamente o tracker da Casamento. Preencher peso na daily alimenta o tracker de Perder 7kg.

### Logar gastos

Hotkey configurada (Settings → Hotkeys → "Templater: Create new note from template Gasto") dispara prompts em sequência:
1. Valor (R$)
2. Descrição
3. Categoria (dropdown: alimentação, transporte, lazer, etc)

Templater cria a nota em `Gastos/<data> - <descricao>.md` automaticamente. Você nunca abre o arquivo. Visão consolidada em `Categorias/Gasto.md`.

---

## Convenções visuais nos gráficos

Toda configuração de tracker block deve seguir:

| Setting | Valor | Quando usar |
|---|---|---|
| `xAxisTickLabelFormat` | `"DD/MM"` | **Sempre** — sem ano no eixo X |
| `yAxisTickLabelFormat` | `".1f"` | Valores decimais (peso, kg, medidas) |
| `yAxisTickLabelFormat` | `".0f"` | Inteiros (R$, contagens) |
| Locale moment | `pt-br` | Datas e dias da semana em português |

Para summary trackers de hábitos (treino, dieta, leitura), usar `{{sum()}}` com parênteses — sintaxe atual do plugin.

---

> Vault pessoal · Sync via git · Editar com cuidado e respeitar as convenções acima.
