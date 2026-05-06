---
type: home
data_criacao: 2026-05-03
cssclasses:
  - dashboard
Categorias:
  - "[[Organização]]"
---

# 🏠 Home

--- start-multi-column: kpis
```column-settings
Number of Columns: 4
Largest Column: standard
```

#### 📅 Hoje

```dataviewjs
const today = moment().format("YYYY-MM-DD");
const file = app.vault.getAbstractFileByPath(`Diário/${today}.md`);
const dayName = moment().locale("pt-br").format("dddd").replace(/^./, c => c.toUpperCase());
if (file) {
  dv.paragraph(`<div class="kpi-value">${moment().format("DD/MM")}</div><div class="kpi-sub">${dayName} · [[${today}|abrir daily]]</div>`);
} else {
  dv.paragraph(`<div class="kpi-value kpi-warning">${moment().format("DD/MM")}</div><div class="kpi-sub">${dayName} · [[${today}|criar daily]]</div>`);
}
```

--- column-end ---

#### ⚖️ Peso

```dataviewjs
const dailies = dv.pages('"Diário"').where(p => p.peso != null).sort(p => p.date, 'desc');
if (dailies.length > 0) {
  const peso = dailies[0].peso;
  const meta = dv.page("Projetos/Perder 7kg");
  const target = meta?.alvo ?? 69.6;
  const delta = (peso - target).toFixed(1);
  const cls = peso <= target ? "kpi-positive" : "kpi-warning";
  dv.paragraph(`<div class="kpi-value ${cls}">${peso} kg</div><div class="kpi-sub">alvo ${target} · ${delta} kg pra ir</div>`);
} else {
  dv.paragraph(`<div class="kpi-value">—</div><div class="kpi-sub">sem dados</div>`);
}
```

--- column-end ---

#### 💰 Saldo conta

```dataviewjs
const saldoInicial = 0;
const dataInicio = "2026-05-05";
const movs = dv.pages('"Anexos/Movimentações"').where(p => p.data && moment(p.data.toString()).isSameOrAfter(dataInicio));
let saldo = saldoInicial;
movs.forEach(m => {
  const v = m.valor || 0;
  if (m.direcao === "entrada") saldo += v;
  else if (m.direcao === "saída" && m.forma_pagamento === "débito") saldo -= v;
  else if (m.direcao === "aporte") saldo -= v;
  else if (m.direcao === "resgate") saldo += v;
  else if (m.direcao === "transferencia") {
    if (m.fonte === "Conta Inter") saldo -= v;
    if (m.destino === "Conta Inter") saldo += v;
  }
});
const cls = saldo >= 1100 ? "kpi-positive" : saldo >= 500 ? "" : "kpi-warning";
dv.paragraph(`<div class="kpi-value ${cls}">R$ ${saldo.toFixed(2)}</div><div class="kpi-sub">alvo R$ 1.100/mês</div>`);
```

--- column-end ---

#### 💸 Gastos mês

```dataviewjs
const targetMonth = moment().format("YYYY-MM");
const movs = dv.pages('"Anexos/Movimentações"').where(p => {
  if (!p.data) return false;
  return moment(p.data.toString()).format("YYYY-MM") === targetMonth && p.direcao === "saída";
});
const total = movs.array().reduce((a, m) => a + (m.valor || 0), 0);
const credito = movs.where(m => m.forma_pagamento === "crédito").array().reduce((a, m) => a + (m.valor || 0), 0);
dv.paragraph(`<div class="kpi-value">R$ ${total.toFixed(2)}</div><div class="kpi-sub">crédito: R$ ${credito.toFixed(2)} (vai pra fatura)</div>`);
```

--- end-multi-column

--- start-multi-column: graficos
```column-settings
Number of Columns: 2
Largest Column: standard
```

#### 📈 Peso · últimos 30 dias

```dataviewjs
const dailies = dv.pages('"Diário"').where(p => p.peso != null).sort(p => p.date, 'asc');
const last30 = dailies.slice(-30).array();
if (last30.length === 0) {
  dv.paragraph("_Sem dados._");
} else {
  const labels = last30.map(p => moment(p.date.toString()).format("DD/MM"));
  const data = last30.map(p => p.peso);
  const chart = {
    type: 'line',
    data: {
      labels: labels,
      datasets: [{
        label: 'Peso (kg)',
        data: data,
        borderColor: 'rgba(75, 192, 192, 1)',
        backgroundColor: 'rgba(75, 192, 192, 0.2)',
        tension: 0.3,
        fill: true,
        pointRadius: 2
      }]
    },
    options: { plugins: { legend: { display: false } } }
  };
  window.renderChart(chart, this.container);
}
```

--- column-end ---

#### 💧 Água · últimos 7 dias

```dataviewjs
const dailies = dv.pages('"Diário"').sort(p => p.date, 'asc').array();
const last7 = dailies.slice(-7);
if (last7.length === 0) {
  dv.paragraph("_Sem dados._");
} else {
  const labels = last7.map(p => moment(p.date.toString()).format("DD/MM"));
  const data = last7.map(p => p.agua || 0);
  const chart = {
    type: 'bar',
    data: {
      labels: labels,
      datasets: [{
        label: 'Água (L)',
        data: data,
        backgroundColor: 'rgba(54, 162, 235, 0.6)',
        borderColor: 'rgba(54, 162, 235, 1)',
        borderWidth: 1
      }]
    },
    options: { plugins: { legend: { display: false } }, scales: { y: { beginAtZero: true, max: 3 } } }
  };
  window.renderChart(chart, this.container);
}
```

--- end-multi-column

#### 🔥 Hábitos · últimos 30 dias

```dataviewjs
const cells = [];
for (let i = 29; i >= 0; i--) {
  const date = moment().subtract(i, 'days').format("YYYY-MM-DD");
  const page = dv.page(`Diário/${date}`);
  let level = 0;
  let tooltip = `${date}: sem registro`;
  if (page) {
    const treino = page.treino === true ? 1 : 0;
    const dieta = page.dieta === true ? 1 : 0;
    const leitura = page.leitura === true ? 1 : 0;
    level = treino + dieta + leitura;
    const icons = [treino ? '🏋️' : '', dieta ? '🍽️' : '', leitura ? '📚' : ''].filter(x => x).join(' ');
    tooltip = `${date}: ${icons || 'nada'}`;
  }
  cells.push(`<div class="heatmap-cell level-${level}" title="${tooltip}"></div>`);
}
const container = dv.el('div', '', { cls: 'heatmap' });
container.innerHTML = cells.join('');

const last30 = dv.pages('"Diário"').sort(p => p.date, 'desc').array().slice(0, 30);
const treino = last30.filter(p => p.treino === true).length;
const dieta = last30.filter(p => p.dieta === true).length;
const leitura = last30.filter(p => p.leitura === true).length;
dv.paragraph(`🏋️ Treino ${treino}/30 · 🍽️ Dieta ${dieta}/30 · 📚 Leitura ${leitura}/30`);

const legend = dv.el('div', '', { cls: 'heatmap-legend' });
legend.innerHTML = `Menos <div class="heatmap-legend-cell"></div><div class="heatmap-legend-cell level-1"></div><div class="heatmap-legend-cell level-2"></div><div class="heatmap-legend-cell level-3"></div> Mais`;
```

--- start-multi-column: gastos-tasks
```column-settings
Number of Columns: 2
Largest Column: standard
```

#### 💼 Caixinhas mês (gasto vs orçamento)

```dataviewjs
const targetMonth = moment().format("YYYY-MM");
const movs = dv.pages('"Anexos/Movimentações"').where(p => {
  if (!p.data) return false;
  return moment(p.data.toString()).format("YYYY-MM") === targetMonth && p.direcao === "saída";
});

const orcamentos = {
  moto: 300,
  alimentação: 125,
  lazer: 125,
  namoro: 400,
  roupa: 150,
  desenvolvimento: 150,
  buffer: 250
};

const gastos = {};
movs.forEach(m => {
  const cat = m.categoria;
  gastos[cat] = (gastos[cat] || 0) + (m.valor || 0);
});

const html = Object.keys(orcamentos).map(cat => {
  const orc = orcamentos[cat];
  const g = gastos[cat] || 0;
  const pct = (g / orc) * 100;
  const cls = pct >= 100 ? 'danger' : pct >= 80 ? 'warning' : '';
  const restante = (orc - g).toFixed(2);
  return `<div class="caixinha-row">
    <div class="caixinha-label"><span>${cat}</span><span>R$ ${g.toFixed(2)} / R$ ${orc.toFixed(2)} · resta R$ ${restante}</span></div>
    <div class="progress-bar"><div class="progress-bar-fill ${cls}" style="width: ${Math.min(pct, 100)}%"></div></div>
  </div>`;
}).join('');

const semOrcamento = Object.keys(gastos).filter(c => !orcamentos[c]);
let extra = '';
if (semOrcamento.length > 0) {
  extra = '<div class="caixinha-row" style="margin-top: 12px; opacity: 0.7;"><div class="caixinha-label"><span><em>Sem orçamento fixo:</em></span></div>';
  semOrcamento.forEach(c => {
    extra += `<div class="caixinha-label"><span>${c}</span><span>R$ ${gastos[c].toFixed(2)}</span></div>`;
  });
  extra += '</div>';
}

dv.el('div', '', {}).innerHTML = html + extra;
```

--- column-end ---

#### ✅ Tasks pendentes

```tasks
not done
due before tomorrow
sort by due
limit 8
```

--- end-multi-column

#### 📥 Inbox

```dataviewjs
const inbox = dv.pages('"Inbox"');
const count = inbox.length;
if (count === 0) {
  dv.paragraph("✅ Inbox vazia.");
} else {
  dv.paragraph(`⚠️ **${count}** item${count > 1 ? 's' : ''} em **Inbox/** pra processar.`);
  dv.list(inbox.map(p => p.file.link));
}
```
