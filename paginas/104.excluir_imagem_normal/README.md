<div align="center">
  <p align="center">
    <h1>104. Modal — Excluir Imagem (Edição)</h1>
  </p>
</div>

> Modal utilizado na edição da OS para confirmar exclusão de imagens já registradas.

## 🎯 Visão geral

Esta tela permite o usuário a deletar alguma imagem que ele adicionou nessa OS já criada. Essa página possui apenas 2 campos visíveis ao usuário, uma que mostra o id da imagem e outra o nome do arquivo da imagem. No canto inferior direito tem um botão 'Excluir'.

---

## 1 — Excluir Imagem (Form)

Pega os campos da tabela tmp_ordens_servicos que tem o id igual ao da imagem a ser deletada.

- `P104_NUM_IMG` - Campo de Número - ID da imagem.
- `P104_FILENAME` - Campo de Texto - Nome do arquivo da imagem.

---

## 2 — Botões (Conteúdo Estático)

Região que vai armazenar apenas um botão:

- `Excluir` - Submete a página 104 com o valor presente no item de página P104_NUM_IMG para rodar o processo 'Deletar' e depois fechar o modal.
```sql
BEGIN
    DELETE FROM tmp_ordens_servicos
    WHERE num_img = :P104_NUM_IMG;

    COMMIT;
END;
```
---

Ver também: [paginas/README.md](../README.md)

