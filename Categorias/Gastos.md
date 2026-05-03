---
tags:
  - categorias
Categorias:
  - "[[Finanças]]"
---

## Total geral

```tracker
searchType: frontmatter
searchTarget: valor
folder: Anexos/Gastos
dateField: data
summary:
    template: "**R$ {{sum()}}**"
```

## Por categoria

```dataview
TABLE WITHOUT ID
  categoria_gasto AS "Categoria",
  sum(rows.valor) AS "Total (R$)",
  length(rows) AS "Qtd"
FROM "Anexos/Gastos"
GROUP BY categoria_gasto
SORT sum(rows.valor) DESC
```

## Por mês

```dataview
TABLE WITHOUT ID
  dateformat(date(data), "MMM/yyyy") AS "Mês",
  sum(rows.valor) AS "Total (R$)",
  length(rows) AS "Qtd"
FROM "Anexos/Gastos"
GROUP BY dateformat(date(data), "yyyy-MM")
SORT key DESC
```

## Detalhamento

![[Gastos.base]]
