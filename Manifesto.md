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

### `Anexos/Gastos/`

Pasta de notas de gasto individual. Cada gasto = uma nota com frontmatter (`valor`, `descricao`, `data`, `categoria_gasto`). Criadas via Templater hotkey + prompts (sem fricção). Visão consolidada em `Categorias/Gastos.md`. Tracker da meta Casamento lê dessa pasta.

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
- ❌ Strings: `Categorias: [saúde]`
- ❌ Tags inline no body: `#saúde`
- ❌ A property `tags` nativa do Obsidian

**Por que wiki links:** os filters de Bases usam `Categorias.contains(link("X"))`. Cada link aponta pra uma nota em `Categorias/` que embeda a Base. Backlinks aparecem de graça.

**Valores convencionados:**

| Valor | Onde aparece |
|---|---|
| `[[Diário]]` | Toda nota daily |
| `[[Semanas]]` | Toda nota weekly |
| `[[Meta]]` | Toda nota em `Projetos/` |
| `[[Saúde]]` | Notas relacionadas a saúde (treino, dieta, exames) |
| `[[Finanças]]` | Notas relacionadas a finanças (plano financeiro) |
| `[[Cursos]]` | Notas/anotações de cursos |
| `[[Vídeos]]` | Vídeos pra ver |
| `[[Gastos]]` | Cada nota de gasto individual em `Gastos/` |
| `[[Organização]]` | Meta-notas sobre o vault (manifesto, skills) |
| Livre (`[[Pessoa]]`, `[[Tema]]`) | Referências de conhecimento |

Uma nota pode ter múltiplas categorias. Ex: nota de treino que também é referência sobre fisiologia → `Categorias: ["[[Saúde]]", "[[Tema]]"]`. Aparece em ambas as views.

**Estado de meta** vai em property separada:
```yaml
status: ativa | pausada | concluida
```
Não em `Categorias` — estado muda, classificação não.

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

> Nota: `gasto` removido. Gastos viraram notas individuais em `Gastos/` (ver schema abaixo).

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
  - "[[Meta]]"
---
```

### Gasto (`Gastos/<data> - <descricao>.md`)

```yaml
---
type: gasto
data: YYYY-MM-DD
data_criacao: YYYY-MM-DD HH:mm
valor: 25.50              # número, R$
descricao: Almoço shopping
categoria_gasto: alimentação    # alimentação | transporte | lazer | roupa | moto | saúde | desenvolvimento | outros
Categorias:
  - "[[Gastos]]"
---
```

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
