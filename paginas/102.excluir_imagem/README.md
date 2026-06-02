<div align="center">
  <p align="center">
    <h1>102. Modal — Excluir Imagem (Registro)</h1>
  </p>
</div>

> Modal utilizado para confirmar a remoção de imagens durante o cadastro de uma OS.

## 🎯 Visão geral

Esta tela permite o usuário a deletar alguma imagem que ele adicionou por engano no cadastro de uma OS. Essa página possui apenas 2 campos visíveis ao usuário, uma que mostra o id da imagem e outra o nome do arquivo da imagem. No canto inferior direito tem um botão 'Excluir'.

---

## 1 — Excluir Imagem (Form)

Pega os campos da tabela tmp_ordens_servicos que tem o id igual ao da imagem a ser deletada.

- `P102_NUM_IMG` - Campo de Número - ID da imagem.
- `P101_FILENAME` - Campo de Texto - Nome do arquivo da imagem.

---

## 2 — Botões (Conteúdo Estático)

Região que vai armazenar apenas um botão:

- `Excluir` - Submete a página 102 com o valor presente no item de página P102_NUM_IMG para rodar o processo 'Deletar' e depois fechar o modal.
```sql
BEGIN
    DELETE FROM tmp_ordens_servicos
    WHERE num_img = :P102_NUM_IMG;

    COMMIT;
END;
```
---

Ver também: [paginas/README.md](../README.md)
