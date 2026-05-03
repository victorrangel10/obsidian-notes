---
type: meta
area: saúde
peso_inicial: 76.6
prazo: 2026-12-31
alvo: 69.6
status: ativa
Categorias:
  - "[[Meta]]"
---

# Perder 7kg

## Plano

Recomposição corporal: reduzir gordura preservando massa muscular. Partindo de ~76.6kg pra ~69.6kg (–7kg em ~7-8 meses).

**Estratégia:**
- **Treino:** PPL 2x (Push/Pull/Legs A+B), 4-6x/semana — ver [[Treino PPL]]
- **Dieta:** ~2.100 kcal/dia, ~145g proteína/dia, deficit de 400-500 kcal — ver [[Dieta Recomposição]]
- **Cardio:** 20-30 min zone 2 nos dias de Push/Pull, caminhada leve nos dias de descanso
- **Sono:** mínimo 7h (crítico em deficit calórico)

**Acompanhamento:**
- Property `peso` nas dailies → tracker abaixo
- Property `treino` (bool) e `dieta` (bool) nas dailies → contagem semanal nas weeklies

## Marcos

- [ ] –2kg (~74.6kg) — fase inicial, water weight + fat
- [ ] –4kg (~72.6kg)
- [ ] –5,5kg (~71.1kg)
- [ ] –7kg (~69.6kg) — alvo

## Progresso

```tracker
searchType: frontmatter
searchTarget: peso
folder: Diário
line:
    title: "Evolução do peso"
    yAxisLabel: "kg"
    yAxisTickLabelFormat: ".1f"
    xAxisTickLabelFormat: "DD/MM"
```
