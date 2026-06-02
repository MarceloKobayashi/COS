<div align="center">
  <p align="center">
    <h1>4. Editar OS</h1>
  </p>
</div>

---

> Página para editar uma OS já cadastrada no sistema

## 🎯 Visão geral

Esta tela permite o usuário alterar algumas informações de uma OS já cadastrada no sistema, tais como assunto, observação e imagens relacionadas a OS. Essa tela foi feita para alterar especifícações da OS ou registrar seus resultados, com observação e imagens.

---

## 1 — Itens de Página

Itens principais:

- `P4_OS_ORIGINAL` - Oculto - ID da OS passada da página 2 para essa. Esse item vai servir como parâmetro para recuperar valores de uma OS específica.
```sql
SELECT *
FROM ordens_servicos
WHERE id_os = :P4_OS_ORIGINAL
```

- `P4_NUM_OS` - Somente Exibição - Número da OS.
- `P4_CATEGORIA` - Somente Exibição - Categoria da OS.
- `P4_DAT_SOLICITACAO` - Somente Exibição - Data de solicitação da OS.
- `P4_SOLICITANTE` - Somente Exibição - Solicitante da OS.
- `P4_ASSUNTO` - Área de Texto - Aparece o assunto da OS, mas como campo modificável. 
- `P4_OBS` - Área de Texto - Aparece a observação da OS, mas como campo modificável.

---

## 2 — Imagens (Relatório Interativo)

Região que armazena as imagens da tabela definitiva relacionadas a OS que está sendo editada. Possui as colunas de edição da imagem, id, nome do arquivo e a imagem, além de um botão de 'Adicionar Imagem' que redireciona o usuário para a página 103.

SQL que gera o relatório:

```sql
SELECT
  num_img,
  fk_ordens_servicos,
  dbms_lob.getlength(arq) AS arq,
  filename,
  mimetype,
  dbms_lob.getlength(arq) AS baixar
FROM img_ordens_servicos
WHERE fk_ordens_servicos = (
  SELECT id_os
  FROM ordens_servicos
  WHERE id_os = :P4_OS_ORIGINAL
)
```

---

## 3 — Botão de 'Confirmar Edições' (Breadcrumb Bar)

Botão que submete a página com os valores dos itens :P4_ASSUNTO e :P4_OBS fazendo com que o processo 'Editar OS' seja executado. Nesse processo, a tabela ordens_servicos é atualizada e faz com que a OS específica tenha os campos assunto e obs setados com novos valores.

- `Editar OS` (PL/SQL):

```sql
BEGIN
  UPDATE ordens_servicos
  SET assunto = :P4_ASSUNTO,
    obs = :P4_OBS
  WHERE num_os = :P4_NUM_OS;
END;
```

Depois do processo, o usuário é redirecionado para a página 2 passando o id da OS como parâmetro, fazendo com que ela seja focada na página 2.

---

Ver também: [paginas/README.md](../README.md)

