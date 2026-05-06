<%*
  if (tp.file.folder(true).startsWith("Templates")) {
    new Notice("⚠️ Não rode este template direto. Crie nota nova em Anexos/Movimentações/.");
    return;
  }

  const dataCriacao = tp.date.now("YYYY-MM-DD HH:mm");

  const direcao = await tp.system.suggester(
    ["💸 Saída", "💰 Entrada", "📥 Aporte", "📤 Resgate", "🔄 Transferência"],
    ["saída", "entrada", "aporte", "resgate", "transferencia"]
  );
  if (!direcao) {
    new Notice("Direção não escolhida. Abortado.");
    return;
  }

  const valorRaw = await tp.system.prompt("Valor (R$) — só número, ex: 25.50");
  const valor = parseFloat(valorRaw.replace(",", "."));
  if (isNaN(valor)) {
    new Notice("Valor inválido. Abortado.");
    return;
  }

  const descricao = await tp.system.prompt("Descrição");
  if (!descricao) {
    new Notice("Descrição obrigatória. Abortado.");
    return;
  }

  const categoriasPorDirecao = {
    "saída": ["alimentação", "transporte", "lazer", "roupa", "moto", "saúde", "desenvolvimento", "namoro", "buffer", "outros"],
    "entrada": ["salário", "restituição", "freela", "13º", "extra", "outros"],
    "aporte": ["casamento", "longo_prazo", "reserva", "pos_casamento", "outros"],
    "resgate": ["casamento", "longo_prazo", "reserva", "pos_casamento", "outros"],
    "transferencia": ["pagamento_fatura", "transferencia_caixinha", "outros"]
  };

  const categorias = categoriasPorDirecao[direcao];
  const categoria = await tp.system.suggester(categorias, categorias);
  if (!categoria) {
    new Notice("Categoria não escolhida. Abortado.");
    return;
  }

  let fonte = "";
  let destino = "";
  let formaPagamento = "";

  if (direcao === "entrada") {
    fonte = await tp.system.prompt("Fonte (ex: Inbazz, cliente X) — opcional", "");
  }

  if (direcao === "saída") {
    formaPagamento = await tp.system.suggester(
      ["🏦 Débito", "💳 Crédito"],
      ["débito", "crédito"]
    );
    if (!formaPagamento) {
      new Notice("Forma de pagamento não escolhida. Abortado.");
      return;
    }
  }

  if (direcao === "aporte" || direcao === "resgate") {
    const destinos = ["Tesouro Selic 2028", "Tesouro IPCA+", "CDB Inter Liquidez Diária", "Caixinha Inter", "Outros"];
    destino = await tp.system.suggester(destinos, destinos);
    if (!destino) { destino = ""; }
  }

  const data = await tp.system.prompt("Data (YYYY-MM-DD)", tp.date.now("YYYY-MM-DD"));
  if (!moment(data, "YYYY-MM-DD", true).isValid()) {
    new Notice("Data inválida. Abortado.");
    return;
  }
  const hora = await tp.system.prompt("Hora (HH:mm)", tp.date.now("HH:mm"));
  if (!moment(hora, "HH:mm", true).isValid()) {
    new Notice("Hora inválida. Abortado.");
    return;
  }

  let frontmatter = `---
type: movimentacao
direcao: ${direcao}
data: ${data}
hora: ${hora}
data_criacao: ${dataCriacao}
valor: ${valor}
descricao: ${descricao}
categoria: ${categoria}`;

  if (formaPagamento) {
    frontmatter += `\nforma_pagamento: ${formaPagamento}`;
  }
  if (fonte) {
    frontmatter += `\nfonte: ${fonte}`;
  }
  if (destino) {
    frontmatter += `\ndestino: ${destino}`;
  }

  frontmatter += `
Categorias:
  - "[[Movimentações]]"
---
`;

  tR += frontmatter;

  await tp.file.move(`Anexos/Movimentações/${data} - ${descricao}`);
%>
