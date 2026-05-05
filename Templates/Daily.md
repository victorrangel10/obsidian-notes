---
date: <% tp.file.title %>
data_criacao: <% tp.date.now("YYYY-MM-DD HH:mm") %>
peso: 76.6
agua: 0
treino: false
dieta: false
leitura: false
Categorias:
  - "[[Diário]]"
---

# <% moment(tp.file.title, "YYYY-MM-DD").locale("pt-br").format("dddd, DD [de] MMMM").replace(/^./, c => c.toUpperCase()) %>

## 💸 Gastos do dia

![[Gastos.base#Do dia]]


