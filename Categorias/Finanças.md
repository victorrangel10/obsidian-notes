---
type: area
Categorias:
  - "[[Áreas]]"
---

# Finanças

Área permanente de gestão financeira: renda, gastos, reserva, investimentos, metas.

## Dashboard mês atual

```dataviewjs
const targetMonth = moment().format("YYYY-MM");

const movs = dv.pages('"Anexos/Movimentações"').where(p => {
  if (!p.data) return false;
  return moment(p.data.toString()).format("YYYY-MM") === targetMonth;
});

const sum = (dir) => movs.where(p => p.direcao === dir).map(p => p.valor || 0).array().reduce((a,b) => a+b, 0);
const sumWhere = (predicate) => movs.where(predicate).map(p => p.valor || 0).array().reduce((a,b) => a+b, 0);

const entradas = sum("entrada");
const saidas = sum("saída");
const aportes = sum("aporte");
const resgates = sum("resgate");
const transferencias = sum("transferencia");

const saidasDebito = sumWhere(p => p.direcao === "saída" && p.forma_pagamento === "débito");
const saidasCredito = sumWhere(p => p.direcao === "saída" && p.forma_pagamento === "crédito");
const saidasSemPagamento = sumWhere(p => p.direcao === "saída" && !p.forma_pagamento);

const saldo = entradas - saidas - aportes + resgates;
const taxaPoupanca = entradas > 0 ? ((aportes - resgates) / entradas * 100) : 0;
const meta = 50;

const statusMeta = taxaPoupanca >= meta
  ? "✅ Meta atingida"
  : taxaPoupanca >= meta * 0.8
    ? "⚠️ Quase lá"
    : "🚨 Abaixo da meta";

dv.table(
  ["Métrica", "Valor"],
  [
    ["💰 Entradas", `R$ ${entradas.toFixed(2)}`],
    ["💸 Saídas total", `R$ ${saidas.toFixed(2)}`],
    ["　↳ 🏦 Débito", `R$ ${saidasDebito.toFixed(2)}`],
    ["　↳ 💳 Crédito", `R$ ${saidasCredito.toFixed(2)}`],
    ["　↳ ❓ Sem forma definida", `R$ ${saidasSemPagamento.toFixed(2)}`],
    ["📥 Aportes", `R$ ${aportes.toFixed(2)}`],
    ["📤 Resgates", `R$ ${resgates.toFixed(2)}`],
    ["🔄 Transferências (não contam como gasto)", `R$ ${transferencias.toFixed(2)}`],
    ["**Saldo do mês**", `**R$ ${saldo.toFixed(2)}**`],
    ["**% Poupança**", `**${taxaPoupanca.toFixed(1)}%** (meta ${meta}%)`],
    ["Status", statusMeta]
  ]
);
```

## Documentos ativos

- [[Plano Financeiro]] — distribuição mensal, reserva, investimentos, projeção do casamento
- [[Movimentações]] — tracker de todas transações (entradas, saídas, aportes, resgates, transferências)

## Metas vinculadas

- [[Casamento - Juntar 50%]]

## Métricas que entram nas dailies

- `gasto` — R$ do dia


![[Finanças.base]]
