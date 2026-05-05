<%*
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
  const data = await tp.system.prompt("Data do gasto (YYYY-MM-DD)", tp.date.now("YYYY-MM-DD"));
  if (!moment(data, "YYYY-MM-DD", true).isValid()) {
    new Notice("Data inválida. Abortado.");
    return;
  }
  const hora = await tp.system.prompt("Hora do gasto (HH:mm)", tp.date.now("HH:mm"));
  if (!moment(hora, "HH:mm", true).isValid()) {
    new Notice("Hora inválida. Abortado.");
    return;
  }

  tR += `---
type: gasto
data: ${data}
hora: ${hora}
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
