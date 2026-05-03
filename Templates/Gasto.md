<%*
  const data = tp.date.now("YYYY-MM-DD");
  const dataCriacao = tp.date.now("YYYY-MM-DD HH:mm");
  const valorRaw = await tp.system.prompt("Valor (R$) — só número, ex: 25.50");
  const valor = parseFloat(valorRaw.replace(",", "."));
  if (isNaN(valor)) {
    new Notice("Valor inválido. Abortado.");
    return;
  }
  const descricao = await tp.system.prompt("Descrição");
  const categoria = await tp.system.suggester(
    ["alimentação", "transporte", "lazer", "roupa", "moto", "saúde", "desenvolvimento", "outros"],
    ["alimentação", "transporte", "lazer", "roupa", "moto", "saúde", "desenvolvimento", "outros"]
  );

  tR += `---
type: gasto
data: ${data}
data_criacao: ${dataCriacao}
valor: ${valor}
descricao: ${descricao}
categoria_gasto: ${categoria}
Categorias:
  - "[[Gastos]]"
---
`;

  await tp.file.move(`Anexos/Gastos/${data} - ${descricao}`);
  %>