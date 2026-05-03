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

⬅️ [[<% tp.date.now("YYYY-MM-DD", -1, tp.file.title, "YYYY-MM-DD") %>|Ontem]] | [[<% tp.date.now("YYYY-[W]ww", 0, tp.file.title, "YYYY-MM-DD") %>|Semana]] | [[<% tp.date.now("YYYY-MM-DD", 1, tp.file.title, "YYYY-MM-DD") %>|Amanhã]] ➡️

## Notas livres


## Reflexão

- O que rolou bem?
- O que poderia ter sido melhor?
