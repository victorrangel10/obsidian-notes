---
date: <% tp.date.now("YYYY-MM-DD") %>
peso: 
gasto: 
agua: 0
treino: false
dieta: false
leitura: false
Categorias:
  - "[[Diário]]"
---

# <% moment().locale("pt-br").format("dddd, DD [de] MMMM").replace(/^./, c => c.toUpperCase()) %>

⬅️ [[<% tp.date.now("YYYY-MM-DD", -1) %>|Ontem]] | [[<% tp.date.now("YYYY-[W]ww") %>|Semana]] | [[<% tp.date.now("YYYY-MM-DD", 1) %>|Amanhã]] ➡️

## Notas livres


## Reflexão

- O que rolou bem?
- O que poderia ter sido melhor?
