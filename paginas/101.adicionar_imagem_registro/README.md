<div align="center">
  <p align="center">
    <h1>101. Modal — Adicionar Imagem (Registro)</h1>
  </p>
</div>

> Modal usado durante o cadastro (`3`) para anexar imagens a uma OS em criação.

## 🎯 Visão geral

Esta tela permite o usuário a cadastrar uma imagem dentro da tabela de imagens temporárias utilizando o seu CPF. Nessa página aparece apenas um item de página visível para o usuário e ele é feito para fazer um upload de uma imagem. Depois de dar upload, basta clicar no botão 'Criar'.

---

## 1 — Adicionar Imagem (Form)

Pega os campos da tabela tmp_ordens_servicos e seta todos, menos o P101_ARQ como oculto:

- `P101_ARQ` - Upload de Arquivo - Campo que armazena o arquivo de imagem propriamente dito e armazena ele na tabela APEX_APPLICATION_TEMP_FILES para que seus valores possam ser recuperados depois.

---

## 2 — Botões (Conteúdo Estático)

Região que vai armazenar dois botões:
- `Cancel` - Roda uma ação dinâmica para cancelar a página 101 e voltar para a página 3.
- `Create` - Submete a página 101 com os valores presentes no item de página P101_ARQ para rodar o processo 'Adicionar Imagem' e depois fechar o modal.
```sql
DECLARE
    l_blob      BLOB;
    l_mime      VARCHAR2(200);
    l_filename  VARCHAR2(200);
BEGIN
    -- Assim que se recupera os metadados de um arquivo
    SELECT blob_content, mime_type, filename
    INTO l_blob, l_mime, l_filename
    FROM apex_application_temp_files
    WHERE name = :P101_ARQ;

    INSERT INTO TMP_ordens_servicos (
        cpf_solicitante,
        arq,
        mimetype,
        filename
    ) VALUES (
        :P101_CPF_SOLICITANTE,
        l_blob,![alt text](image.png)
        l_mime,
        l_filename
    );
END;
```
---

Ver também: [paginas/README.md](../README.md)
