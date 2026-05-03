---
semana: <% tp.date.now("YYYY-[W]ww") %>
inicio: <% moment().day(0).format("YYYY-MM-DD") %>
fim: <% moment().day(6).format("YYYY-MM-DD") %>
Categorias:
  - "[[Semana]]"
---

# Semana <% tp.date.now("ww/YYYY") %>

⬅️ [[<% tp.date.now("YYYY-[W]ww", -7) %>|Anterior]] | [[<% tp.date.now("YYYY-[W]ww", 7) %>|Próxima]] ➡️

## Métricas da semana

```tracker
searchType: frontmatter
searchTarget: peso
folder: obsidian;Diário
startDate: <% moment().day(0).format("YYYY-MM-DD") %>
endDate: <% moment().day(6).format("YYYY-MM-DD") %>
line:
    title: "Peso"
    yAxisLabel: "kg"
```

```tracker
searchType: frontmatter
searchTarget: gasto
folder: Diário
startDate: <% moment().day(0).format("YYYY-MM-DD") %>
endDate: <% moment().day(6).format("YYYY-MM-DD") %>
accum: true
bar:
    title: "Gasto cumulativo"
    yAxisLabel: "R$"
```

```tracker
searchType: frontmatter
searchTarget: treino
folder: Diário
startDate: <% moment().day(0).format("YYYY-MM-DD") %>
endDate: <% moment().day(6).format("YYYY-MM-DD") %>
summary:
    template: "Treino: {{sum}}/7 dias"
```

```tracker
searchType: frontmatter
searchTarget: dieta
folder: Diário
startDate: <% moment().day(0).format("YYYY-MM-DD") %>
endDate: <% moment().day(6).format("YYYY-MM-DD") %>
summary:
    template: "Dieta: {{sum}}/7 dias"
```

```tracker
searchType: frontmatter
searchTarget: leitura
folder: Diário
startDate: <% moment().day(0).format("YYYY-MM-DD") %>
endDate: <% moment().day(6).format("YYYY-MM-DD") %>
summary:
    template: "Leitura: {{sum}}/7 dias"
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
