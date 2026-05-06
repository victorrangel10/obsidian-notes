---
tags:
  - categorias
Categorias:
  - "[[Finanças]]"
---

# Movimentações

Tracker de todas as transações financeiras: entradas, saídas, aportes, resgates.

## Mês atual — Resumo por direção

```dataview
TABLE WITHOUT ID
  direcao AS "Direção",
  sum(rows.valor) AS "Total (R$)",
  length(rows) AS "Qtd"
FROM "Anexos/Movimentações"
WHERE data.year = date(today).year AND data.month = date(today).month
GROUP BY direcao
SORT direcao ASC
```

## Mês atual — Saídas por categoria

```dataview
TABLE WITHOUT ID
  categoria AS "Categoria",
  sum(rows.valor) AS "Total (R$)",
  length(rows) AS "Qtd"
FROM "Anexos/Movimentações"
WHERE data.year = date(today).year AND data.month = date(today).month
  AND direcao = "saída"
GROUP BY categoria
SORT sum(rows.valor) DESC
```

## Mês atual — Aportes detalhados

```dataview
TABLE
  categoria AS "Caixinha",
  destino AS "Destino",
  valor AS "R$"
FROM "Anexos/Movimentações"
WHERE data.year = date(today).year AND data.month = date(today).month
  AND direcao = "aporte"
SORT data DESC
```

## Mês atual — Entradas detalhadas

```dataview
TABLE
  categoria AS "Categoria",
  fonte AS "Fonte",
  valor AS "R$"
FROM "Anexos/Movimentações"
WHERE data.year = date(today).year AND data.month = date(today).month
  AND direcao = "entrada"
SORT data DESC
```

## Histórico mensal (todas direções)

```dataview
TABLE WITHOUT ID
  dateformat(date(rows[0].data), "MMM/yyyy") AS "Mês",
  rows[0].direcao AS "Direção",
  sum(rows.valor) AS "Total (R$)",
  length(rows) AS "Qtd"
FROM "Anexos/Movimentações"
GROUP BY dateformat(date(data), "yyyy-MM") + " · " + direcao
SORT key DESC
```

## Detalhamento completo

![[Movimentações.base]]
