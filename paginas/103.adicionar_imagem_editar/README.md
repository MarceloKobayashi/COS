<div align="center">
  <p align="center">
    <h1>103. Modal — Adicionar Imagem (Edição)</h1>
  </p>
</div>

> Modal usado na página de edição (`4`) para anexar imagens a uma OS já registrada.

## 🎯 Visão geral

Esta tela permite o usuário a inserir uma imagem que se associa com a OS específica. Essa página possui apenas 2 itens de página visíveis para o usuário e um botão 'Criar' para adicionar tal imagem na tabela definitiva de imagens.

---

## 1 — Adicionar Imagem (Form)

Pega os campos da tabela img_ordens_servicos e exibe apenas fk_ordens_servicos e arq.

- `P103_FK_ORDENS_SERVICOS` - Somente Exibição - Valor do número da OS passado da página 4 para essa.
- `P103_ARQ` - Upload de Arquivo - Campo para o usuário fazer o upload de arquivo, armazenando seus metadados na tabela temporária do APEX: APEX_APPLICATION_TEMP_FILES

---

## 2 — Botões (Conteúdo Estático)

Região que vai armazenar dois botões:

- `Cancel` - Cancela o modal e volta para a página 4.
- `Create` - Submete a página 103 com os valores presentes nos itens P103_FK_ORDENS_SERVICOS e P103_ARQ para rodar o processo 'Adicionar Imagem' e depois fechar o modal
```sql
DECLARE
    l_blob      BLOB;
    l_mime      VARCHAR2(200);
    l_filename  VARCHAR2(200);
BEGIN
    -- Recuperar os metadados do arquivo
    SELECT blob_content, mime_type, filename
    INTO l_blob, l_mime, l_filename
    FROM apex_application_temp_files
    WHERE name = :P103_ARQ;

    INSERT INTO img_ordens_servicos (
        fk_ordens_servicos,
        arq,
        mimetype,
        filename
    ) VALUES (
        :P103_ID_OS,
        l_blob,
        l_mime,
        l_filename
    );
END;
```
---

Ver também: [paginas/README.md](../README.md)

