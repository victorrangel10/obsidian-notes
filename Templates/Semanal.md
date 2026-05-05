---
semana: 2026-W19
inicio: 2026-05-03
fim: 2026-05-09
data_criacao: <% tp.date.now("YYYY-MM-DD HH:mm") %>
Categorias:
  - "[[Semanas]]"
---

# Semana 19/2026

⬅️ [[2026-W18|Anterior]] | [[2026-W20|Próxima]] ➡️

## Métricas da semana

```tracker
searchType: frontmatter
searchTarget: peso
folder: Diário
startDate: 2026-05-03
endDate: 2026-05-09
line:
    title: "Peso"
    yAxisLabel: "kg"
    yAxisTickLabelFormat: ".1f"
    xAxisTickLabelFormat: "DD/MM"
```

```dataviewjs
const page = dv.current();
const startDate = moment(page.inicio.toString());
const endDate = moment(page.fim.toString());

const movs = dv.pages('"Anexos/Movimentações"')
  .where(p => p.direcao === "saída")
  .where(p => {
    const d = moment(p.data.toString());
    return d.isSameOrAfter(startDate, 'day') && d.isSameOrBefore(endDate, 'day');
  })
  .sort(p => p.data, 'asc');

let cumsum = 0;
const labels = [];
const data = [];
for (const m of movs) {
  cumsum += m.valor || 0;
  labels.push(moment(m.data.toString()).format("DD/MM"));
  data.push(Number(cumsum.toFixed(2)));
}

if (labels.length === 0) {
  dv.paragraph("_Sem saídas registradas na semana._");
} else {
  const chart = {
    type: 'line',
    data: {
      labels: labels,
      datasets: [{
        label: 'Saídas cumulativas (R$)',
        data: data,
        borderColor: 'rgba(255, 99, 132, 1)',
        backgroundColor: 'rgba(255, 99, 132, 0.2)',
        tension: 0.2,
        fill: true
      }]
    },
    options: {
      scales: { y: { beginAtZero: true } }
    }
  };
  window.renderChart(chart, this.container);
  dv.paragraph(`**Total semana:** R$ ${cumsum.toFixed(2)} · **${movs.length}** saídas`);
}
```

```tracker
searchType: frontmatter
searchTarget: treino
folder: Diário
startDate: 2026-05-03
endDate: 2026-05-09
summary:
    template: "Treino: {{sum()}}/7 dias"
```

```tracker
searchType: frontmatter
searchTarget: dieta
folder: Diário
startDate: 2026-05-03
endDate: 2026-05-09
summary:
    template: "Dieta: {{sum()}}/7 dias"
```

```tracker
searchType: frontmatter
searchTarget: leitura
folder: Diário
startDate: 2026-05-03
endDate: 2026-05-09
summary:
    template: "Leitura: {{sum()}}/7 dias"
```

## Inbox

Processar `Inbox/`. Cada item: vai pra Projetos / Áreas / Referências / lixo.

- [ ] Inbox vazia

## Reflexão

- O que rolou bem essa semana?
- O que poderia ter sido melhor?
- O que aprendi?

## Próxima semana

- 
