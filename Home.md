---
type: home
data_criacao: 2026-05-03
Categorias:
  - "[[Organização]]"
---
w
# 🏠 Home

## 📅 Hoje

```dataviewjs
const today = moment().format("YYYY-MM-DD");
const file = app.vault.getAbstractFileByPath(`Diário/${today}.md`);
if (file) {
  dv.paragraph(`### [[${today}|Abrir daily de hoje (${today})]]`);
} else {
  dv.paragraph(`### Daily de hoje (${today}) — ainda não criado · [[${today}|Criar agora]]`);
}
```

---

## ✅ Tasks pendentes

```tasks
not done
due before tomorrow
sort by due
```

---

## ⚖️ Saúde

```dataviewjs
const dailies = dv.pages('"Diário"')
  .where(p => p.peso !== null && p.peso !== undefined)
  .sort(p => p.date, 'desc');

if (dailies.length > 0) {
  const latest = dailies[0];
  const peso = latest.peso;
  const meta = dv.page("Projetos/Perder 7kg");
  const target = meta?.alvo ?? 69.6;
  const delta = (peso - target).toFixed(1);
  dv.paragraph(`**Peso atual:** ${peso} kg → **alvo:** ${target} kg · **${delta} kg pra ir**`);
} else {
  dv.paragraph("_Sem dados de peso ainda. Preenche uma daily._");
}

const last7 = dv.pages('"Diário"')
  .sort(p => p.date, 'desc')
  .limit(7);

const treino = last7.filter(p => p.treino === true).length;
const dieta = last7.filter(p => p.dieta === true).length;
const leitura = last7.filter(p => p.leitura === true).length;

dv.paragraph(`**Últimos 7 dias:** 🏋️ Treino ${treino}/7 · 🍽️ Dieta ${dieta}/7 · 📚 Leitura ${leitura}/7`);
```

---

## 💸 Gastos

### Total geral

```dataview
TABLE WITHOUT ID
  sum(rows.valor) AS "Total geral (R$)",
  length(rows) AS "Qtd"
FROM "Anexos/Gastos"
GROUP BY ""
```

### Total do mês

```dataview
TABLE WITHOUT ID
  sum(rows.valor) AS "Total mês (R$)",
  length(rows) AS "Qtd"
FROM "Anexos/Gastos"
WHERE dateformat(date(data), "yyyy-MM") = dateformat(date(today), "yyyy-MM")
GROUP BY ""
```

### Top categorias do mês

```dataview
TABLE WITHOUT ID
  categoria_gasto AS "Categoria",
  sum(rows.valor) AS "Total (R$)",
  length(rows) AS "Qtd"
FROM "Anexos/Gastos"
WHERE dateformat(date(data), "yyyy-MM") = dateformat(date(today), "yyyy-MM")
GROUP BY categoria_gasto
SORT sum(rows.valor) DESC
LIMIT 3
```

---

## 📥 Inbox

```dataviewjs
const inbox = dv.pages('"Inbox"');
const count = inbox.length;
if (count === 0) {
  dv.paragraph("✅ Inbox vazia.");
} else {
  dv.paragraph(`⚠️ **${count}** item${count > 1 ? 's' : ''} em **Inbox/** pra processar.`);
}
```
