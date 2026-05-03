---
type: gasto
data: <% tp.date.now("YYYY-MM-DD") %>
data_criacao: <% tp.date.now("YYYY-MM-DD HH:mm") %>
valor: <% await tp.system.prompt("Valor (R$)") %>
descricao: <% await tp.system.prompt("Descrição") %>
categoria_gasto: <% await tp.system.suggester(["alimentação", "transporte", "lazer", "roupa", "moto", "saúde", "desenvolvimento", "outros"], ["alimentação", "transporte", "lazer", "roupa", "moto", "saúde", "desenvolvimento", "outros"]) %>
Categorias:
  - "[[Gastos]]"
---
