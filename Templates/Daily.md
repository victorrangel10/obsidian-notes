<%*
let title = tp.file.title;
if (!moment(title, "YYYY-MM-DD", true).isValid()) {
  title = tp.date.now("YYYY-MM-DD");
  await tp.file.rename(title);
}
const heading = moment(title, "YYYY-MM-DD")
  .locale("pt-br")
  .format("dddd, DD [de] MMMM")
  .replace(/^./, c => c.toUpperCase());
-%>
---
date: <% title %>
data_criacao: <% tp.date.now("YYYY-MM-DD HH:mm") %>
peso: 76.6
agua: 0
treino: false
dieta: false
leitura: false
Categorias:
  - "[[Diário]]"
---

# <% heading %>

## 💸 Gastos do dia

![[Gastos.base#Do dia]]
