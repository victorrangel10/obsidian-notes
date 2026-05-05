---
type: meta
area: finanças
prazo: 2028-04-30
alvo: 25000
aporte_mensal: 1000
aporte_inicial: 2800
status: ativa
Categorias:
  - "[[Metas]]"
---

# Casamento - Juntar 50%

## Plano

Casamento previsto para abril/2028. Meta de R$ 25.000 (com folga pra inflação do setor — mirar R$ 28-30k real).

**Estratégia:**
- Aporte inicial de R$ 2.800 em Tesouro Selic 2028
- Aportes mensais de R$ 1.000 a partir do mês 1
- Acompanhamento via notas em `Anexos/Movimentações/` com `direcao: aporte` e `categoria: casamento`. O gráfico abaixo soma os aportes cumulativos rumo aos R$ 25k.

Plano detalhado em [[Plano Financeiro]].

## Marcos

- [ ] R$ 6.250 (25%) — ~mês 6
- [ ] R$ 12.500 (50%) — ~mês 12
- [ ] R$ 18.750 (75%) — ~mês 18
- [ ] R$ 25.000 (100%) — abril/2028
- [ ] R$ 30.000 (folga inflação) — meta real

## Progresso

```dataviewjs
const aportes = dv.pages('"Anexos/Movimentações"')
  .where(p => p.direcao === "aporte" && p.categoria === "casamento")
  .sort(p => p.data, 'asc');

const meta = dv.current().alvo || 25000;

let cumsum = 0;
const labels = [];
const data = [];
const metaLine = [];
for (const a of aportes) {
  cumsum += a.valor || 0;
  labels.push(moment(a.data.toString()).format("DD/MM/YY"));
  data.push(Number(cumsum.toFixed(2)));
  metaLine.push(meta);
}

if (labels.length === 0) {
  dv.paragraph("_Sem aportes ainda. Comece registrando o aporte inicial em [[Anexos/Movimentações]]._");
} else {
  const chart = {
    type: 'line',
    data: {
      labels: labels,
      datasets: [
        {
          label: 'Aportes cumulativos (R$)',
          data: data,
          borderColor: 'rgba(75, 192, 192, 1)',
          backgroundColor: 'rgba(75, 192, 192, 0.2)',
          tension: 0.1,
          fill: true
        },
        {
          label: `Meta (R$ ${meta.toLocaleString('pt-BR')})`,
          data: metaLine,
          borderColor: 'rgba(255, 159, 64, 1)',
          borderDash: [5, 5],
          fill: false,
          pointRadius: 0
        }
      ]
    },
    options: {
      scales: {
        y: {
          beginAtZero: true,
          suggestedMax: meta * 1.1
        }
      }
    }
  };
  window.renderChart(chart, this.container);

  const pct = (cumsum / meta * 100).toFixed(1);
  const falta = meta - cumsum;
  dv.paragraph(`**Atual:** R$ ${cumsum.toFixed(2)} · **${pct}%** da meta · **R$ ${falta.toFixed(2)}** restantes`);
}
```
