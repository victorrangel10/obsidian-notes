---
type: manifesto
data_criacao: 2026-05-03
Categorias:
  - "[[Organização]]"
---

# Manifesto do Vault

## O que é

Vault Obsidian que serve como tracker de vida — aglutina conhecimento, hábitos diários, metas de longo prazo, finanças e listas. Organização baseada em **PARA leve** (Projects / Areas / Resources / Archives), com property `Categorias` (array de wiki links no frontmatter) fazendo o trabalho transversal de classificação.

A vault está em `/home/vrangel/Documents/Obsidian Vault/obsidian/`, que também é o root do repositório git.

---

## Estrutura de pastas

### `Inbox/`

Captura rápida. Tudo que entra na cabeça e não tem casa óbvia: ideia solta, link salvo, recomendação de livro, anotação de meio do dia. Cria nota com nome livre, joga aqui sem categorizar.

Esvazia toda semana no weekly review — cada item passa por uma decisão: vira Projeto, vira nota de Área, vira Referência, ou vai pro lixo. Inbox sempre termina vazia.

### `Diário/`

Destino do plugin Periodic Notes. Recebe dois tipos de nota:

- **Daily** (`YYYY-MM-DD.md`) — uma por dia. Frontmatter contém as métricas quotidianas (peso, gasto, água, treino, dieta, leitura) que alimentam trackers de metas. Body tem espaço pra notas livres do dia e duas perguntas de reflexão.
- **Weekly** (`YYYY-Www.md`) — uma por semana. Métricas agregadas via tracker blocks, processamento do Inbox, reflexão semanal (3 perguntas), planejamento de prioridades pra próxima semana.

### `Projetos/`

Metas com prazo definido (PARA P). Toda nota aqui tem `prazo`, `alvo` e `status` (`ativa` / `pausada` / `concluida`) no frontmatter. Inclui tracker block embedado puxando dados das dailies pra renderizar progresso visual.

Quando uma meta termina (atinge o alvo ou é abandonada), `status` muda pra `concluida` e ela some das views ativas mas continua acessível via Base de Metas.

### `Áreas/`

Containers permanentes de responsabilidade (PARA A) — Saúde, Finanças, Carreira, etc. Não terminam, evoluem com o tempo. A nota raiz da área é um índice (`Áreas/Saúde.md`) que aponta pra documentos de detalhamento na subpasta (`Áreas/Saúde/Treino PPL.md`, `Áreas/Saúde/Dieta Recomposição.md`).

### `Referências/`

Material de consulta (PARA R) — conhecimento estável e de longo prazo. Subpastas:

- `Cursos/` — anotações de cursos (índice em `Categorias/Cursos.md`)
- `Clippings/` — destino do Web Clipper
- `Listas/` — placeholder pra listas curadas (livros pra ler, vídeos, compras, etc)
- Notas soltas no nível da pasta — anotações sobre pessoas, temas, conceitos

Sem prazo. Cresce conforme se aprende.

### `Categorias/`

Notas-índice com Bases embedadas. Cada arquivo aqui é uma view navegável de notas do vault filtradas por uma categoria.

Padrão da nota:
```markdown
---
tags:
  - categorias
---
![[NomeDaBase.base]]
```

A Base correspondente vive em `Templates/Bases/` e contém o filtro real (ex: `Categorias.contains(link("Meta"))`).

### `Templates/`

Templater templates pra criação automática de novos arquivos:
- `Daily.md` — usado pelo Periodic Notes daily
- `Semanal.md` — usado pelo Periodic Notes weekly
- `Meta.md` — usado quando se cria nova meta em `Projetos/`

A subpasta `Templates/Bases/` armazena os arquivos `.base` (definições de filtro/views consumidas pelas notas em `Categorias/`).

### `Anexos/`

Imagens e binários colados em notas. Destino padrão de attachments do Obsidian.

---

## Convenção de Categorias (a property)

Toda nota relevante carrega uma property `Categorias` no frontmatter como **array de wiki links**:

```yaml
Categorias:
  - "[[Diário]]"
```

**Não usar:**
- ❌ Strings: `Categorias: [diário]`
- ❌ Tags inline no body: `#diário`
- ❌ A property `tags` nativa do Obsidian (substituída por Categorias pra alinhar com a estrutura `Categorias/`)

**Por que wiki links:** os filters de Bases usam `Categorias.contains(link("X"))`. Cada categoria é uma nota em `Categorias/` com Base embedada mostrando todas as notas filtradas. Backlinks vêm de graça.

**Valores convencionados:**

| Valor | Onde aparece |
|---|---|
| `[[Diário]]` | Toda nota daily |
| `[[Semanas]]` | Toda nota weekly |
| `[[Meta]]` | Toda nota em Projetos/ |
| `[[Área]]` | Toda nota raiz de área |
| Livre (`[[Pessoa]]`, `[[Tema]]`, `[[Filosofia]]`) | Notas em Referências/ |

**Estado de meta** vai numa property separada:
```yaml
status: ativa | pausada | concluida
```
Não em `Categorias` — porque estado muda, e Categorias é classificação.

---

## Stack de plugins

| Plugin | Função |
|---|---|
| **Bases** (core) | Views/queries dinâmicas de notas filtradas por property/categoria |
| **obsidian-tracker** | Gráficos numéricos (line, bar, summary) lendo properties das dailies |
| **Templater** | Templates dinâmicos com tokens de data/locale/lógica processados na criação do arquivo |
| **Periodic Notes** | Cria daily e weekly automáticos com template aplicado |
| **obsidian-tasks-plugin** | Tasks com prazo, recorrência (`🔁`), queries inline |
| **kanban-bases-view** | Adiciona view tipo kanban dentro de Bases |
| **obsidian-kanban** | Boards manuais standalone |
| **obsidian-git** | Sync via git pra versionamento e backup |
| **calendar** | Navegação visual de datas |
| **homepage** | Define nota de abertura ao iniciar Obsidian |

---

## Frontmatter contracts

### Daily (`Diário/YYYY-MM-DD.md`)

```yaml
---
date: YYYY-MM-DD
data_criacao: YYYY-MM-DD HH:mm
peso: 76.6        # número, kg
gasto: 0          # número, R$
agua: 0           # número 0-3, litros
treino: false     # bool
dieta: false      # bool
leitura: false    # bool
Categorias:
  - "[[Diário]]"
---
```

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
alvo: <número>          # R$ pra finanças, kg pra fitness, etc
status: ativa           # | pausada | concluida
Categorias:
  - "[[Meta]]"
---
```

### Área (`Áreas/<nome>.md`)

```yaml
---
type: area
Categorias:
  - "[[Área]]"
---
```

---

## Processos

### Daily flow

1. `Cmd+P` → "Periodic Notes: Open today's daily note"
2. Properties view do Obsidian: preencher peso/gasto/agua, togglar treino/dieta/leitura
3. Body: anotar coisas livres do dia em "Notas livres"
4. Antes de dormir: responder as duas perguntas de "Reflexão"

### Weekly review

1. `Cmd+P` → "Periodic Notes: Open this week's note"
2. Tracker blocks renderizam métricas da semana automaticamente (peso, gasto cumulativo, contagem de hábitos)
3. **Processar `Inbox/`:** para cada item, decidir destino (Projeto, Área, Referência, lixo). Inbox termina vazia.
4. Reflexão (3 prompts): rolou bem / podia ter sido melhor / aprendi
5. Anotar 1-3 prioridades pra próxima semana

### Distribuição mensal do salário

Recurring task em `Áreas/Finanças/Plano Financeiro.md` (seção 9): dispara dia 5 todo mês.

Workflow:
1. Distribuir salário conforme tabela (seção 2 do mesmo doc): poupança primeiro, gastos depois
2. Marcar task como done — `obsidian-tasks` cria a próxima automaticamente
3. (Opcional) registrar a distribuição na daily de hoje em "Notas livres"

### Tracking de metas

Cada meta tem tracker block embutido:

- **Casamento - Juntar 50%** → `gasto` cumulativo das dailies (bar chart)
- **Perder 7kg** → `peso` ao longo do tempo (line chart)

Os trackers leem as dailies via `searchType: frontmatter`. Ou seja: o ato de preencher peso/gasto na daily alimenta automaticamente os gráficos de meta.

---

## Convenções visuais nos gráficos

Toda configuração de tracker block deve seguir:

| Setting | Valor | Quando usar |
|---|---|---|
| `xAxisTickLabelFormat` | `"DD/MM"` | **Sempre** — sem ano no eixo X |
| `yAxisTickLabelFormat` | `".1f"` | Valores decimais (peso, kg, medidas) |
| `yAxisTickLabelFormat` | `".0f"` | Inteiros (R$, contagens) |
| Locale moment | `pt-br` | Datas e dias da semana em português |

Para summary trackers de hábitos (treino, dieta, leitura), usar `{{sum()}}` (com parênteses — sintaxe atual do plugin).

---

> Vault pessoal · Sync via git · Editar com cuidado e respeitar as convenções acima.
