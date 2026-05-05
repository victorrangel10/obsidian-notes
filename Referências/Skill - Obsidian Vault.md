---
type: skill
data_criacao: 2026-05-03
Categorias:
  - "[[Organização]]"
  - "[[Skill]]"
---

# Skill: Obsidian Vault

> Cópia versionada (git) da skill que orienta agentes Claude trabalhando neste vault.
> **Versão ativa (carregada pelo Claude):** `~/.claude/skills/obsidian-vault/SKILL.md`
> Mantenha as duas em sync ao editar.

---

## Frontmatter da skill

```yaml
name: obsidian-vault
description: Use ao trabalhar no vault Obsidian do usuário em /home/vrangel/Documents/Obsidian Vault/obsidian/. Cobre tracker de vida (hábitos diários, metas, finanças, conhecimento, listas) com Categorias unificadas + Bases + obsidian-tracker + Templater. Aplica-se a qualquer ação no vault: criar/editar notas, ajustar templates, configurar Bases, modificar trackers.
```

---

## PRIMEIRA AÇÃO OBRIGATÓRIA

Antes de QUALQUER outra ação no vault, ler integralmente:

```
/home/vrangel/Documents/Obsidian Vault/obsidian/Manifesto.md
```

Esse arquivo descreve a estrutura, convenções, frontmatter contracts e processos do vault. **Sem ele carregado no contexto, qualquer mudança risca quebrar o sistema.** Não pular essa etapa nem mesmo pra "perguntas rápidas" ou "edits triviais".

Se o Manifesto não existir ou estiver inacessível, parar e avisar o usuário antes de prosseguir.

## Idioma

Sempre **PT-BR** — folders, properties, conteúdo, nomes de arquivo, mensagens. Inglês só quando inevitável (ex: nomes de plugins, tokens de Templater).

## Modelo central: Base → Categoria → Notas

O vault é navegado por categorias unificadas. A cadeia é:

```
Bases/<X>.base                 ← filtro: Categorias contém [[X]]
        ▲ embed
Categorias/<X>.md              ← view navegável (consome a base)
        ▲ inclusão via property
notas espalhadas pelo vault    ← com Categorias: ["[[X]]"]
```

Pra criar uma nova categoria:
1. Cria `Bases/<Nome>.base` com filtro
2. Cria `Categorias/<Nome>.md` com `tags: [categorias]` + transclusão da base
3. Notas que devem aparecer ganham `Categorias: ["[[Nome]]"]` no frontmatter

Áreas de responsabilidade (Saúde, Finanças, Carreira) **não são pastas** — são categorias.

## Property `Categorias`

- Array de **wiki links** no frontmatter:

```yaml
Categorias:
  - "[[Saúde]]"
```

- **NUNCA usar:**
  - Strings: `Categorias: [saúde]`
  - Tags inline no body: `#saúde`
  - A property `tags` nativa do Obsidian
- **Múltiplas categorias OK** — uma nota pode estar em várias views
- **Estado de meta** vai em property separada `status` (`ativa` / `pausada` / `concluida`), não em Categorias

## Bases (`.base` files)

- Vivem em `Bases/` (top-level no vault root)
- Embedded em notas de `Categorias/` via transclusão (`![[NomeDaBase.base]]`)
- Filter syntax: `Categorias.contains(link("Nome"))`
- **Toda base deve ter filter global `!file.inFolder("Templates")`** pra não incluir os templates (que tem `Categorias` no frontmatter como exemplo) nas views
- **Kanban view: criar via UI do Obsidian, não escrever YAML manual** — schema do plugin `kanban-bases-view` é instável e não documentado. Tentar adivinhar gera erros tipo `groupBy must be an object`.

## obsidian-tracker

Setup padrão pra qualquer tracker block:

```yaml
searchType: frontmatter
searchTarget: <property>
folder: Diário
xAxisTickLabelFormat: "DD/MM"
```

`xAxisTickLabelFormat: "DD/MM"` é **obrigatório** — nunca deixar ano no eixo X.

Format de Y axis:
- Peso, medidas, decimais → `yAxisTickLabelFormat: ".1f"`
- Dinheiro, contagens, inteiros → `yAxisTickLabelFormat: ".0f"`

Habit summary template: usar `{{sum()}}` (com parênteses, sintaxe atual do plugin). Versão sem parênteses está deprecated.

## Templater

- Datas dinâmicas via `tp.date.now("YYYY-MM-DD")` dentro de token Templater
- Strings em PT-BR via `moment().locale("pt-br").format(...)` dentro de token — moment default sai em inglês, locale precisa ser forçado em cada chamada
- Pra capitalizar primeira letra: `.replace(/^./, c => c.toUpperCase())` encadeado
- **Todo template novo deve incluir** property `data_criacao` com token `tp.date.now("YYYY-MM-DD HH:mm")`

> Sintaxe Templater literal omitida nesta cópia pra evitar processamento auto. Ver SKILL.md canônica em `~/.claude/skills/obsidian-vault/` ou os templates em `Templates/` pra exemplos exatos.

## Frontmatter properties

- Números: **SEM aspas** (`peso: 76.6`, não `"76.6"`) — aspas viram string e quebram parsing numérico em Bases/tracker
- Bools: `true` / `false`
- Datas: ISO `YYYY-MM-DD`
- Categorias: array de wiki links

## Tasks recorrentes (obsidian-tasks)

```
- [ ] Descrição 🔁 every month on the 5th 📅 2026-05-05
```

Símbolos:
- `🔁` — recorrência
- `📅` — due date
- `⏰` — scheduled
- `⏳` — start

Marcar como done (`[x]`) faz o plugin recriar a próxima ocorrência automaticamente.

## Periodic Notes

- Daily: folder `Diário`, format `YYYY-MM-DD`, template `Templates/Daily.md`
- Weekly: folder `Diário`, format `YYYY-[W]ww`, template `Templates/Semanal.md`

## Pitfalls comuns a checar

| Sintoma | Causa | Fix |
|---|---|---|
| Templater syntax aparece literal na nota nova | "Trigger Templater on new file creation" = OFF | Ligar no Settings → Templater |
| Bases não filtra por tag | Tag está inline no body (`#tag`), não no frontmatter | Mover pra `Categorias:` no frontmatter |
| Tracker mostra `76.600000` (muitos zeros) | Falta `yAxisTickLabelFormat` | Adicionar `yAxisTickLabelFormat: ".1f"` |
| Tracker mostra ano `2026-05-03` no eixo X | Falta `xAxisTickLabelFormat` | Adicionar `xAxisTickLabelFormat: "DD/MM"` |
| Tracker mostra data em inglês | moment default locale | Forçar `.locale("pt-br")` em cada chamada |
| Habit tracker dá erro `deprecated 'sum'` | Sintaxe antiga | Usar `{{sum()}}` com parênteses |
| Wiki link de categoria quebrado | Categoria não existe ainda em `Categorias/` | Criar `Bases/<X>.base` + `Categorias/<X>.md` antes de usar |

## Movimentação de arquivos

**Sempre via drag-drop dentro do Obsidian.** Nunca via filesystem (`mv`, `cp`). Obsidian preserva links automaticamente. Filesystem não.

## Antes de criar feature nova

Sempre brainstorm com user. Nunca presumir estrutura.

Pergunta antes de criar:
- A nota pertence a alguma categoria existente? (ver `Categorias/`)
- Precisa criar nova categoria? (Base + Categoria + property)
- Onde a nota deve ficar fisicamente? (Inbox, Projetos, Referências, raiz)

## Verificação obrigatória pós-mudança

Após qualquer edição que afete schema, template, Base ou Categoria:

1. Criar/abrir uma nota de teste afetada
2. Confirmar que a Categoria correspondente em `Categorias/` lista a nota
3. Confirmar que tracker block (se houver) renderiza sem erro
4. Confirmar que Templater processou todos os tokens (sem syntax literal sobrando)

Se algum passo falhar, **NÃO declarar a tarefa como completa**. Diagnosticar e corrigir.
