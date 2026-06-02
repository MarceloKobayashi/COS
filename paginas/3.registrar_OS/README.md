<div align="center">
  <p align="center">
    <h1>3. Registrar OS</h1>
  </p>
</div>

---

> Página para registrar uma OS no sistema

## 🎯 Visão geral

Esta tela permite o usuário cadastrar uma OS no sistema, juntamente com imagens sobre ela, se aplicável. Na Breadcrumb bar tem um botão para adicionar a OS, na área principal os itens de página a serem preenchidos pelo usuário e no final tem uma região de relatório interativo para o usuário adicionar as imagens.

---

## 1 — Registrar O.S (Região Estática)

Itens principais:

- `P3_CPF` - Oculto - Recupera o CPF do usuário atual para inserir imagens na tabela temporária.
- `P3_NUM_OS` - Campo de Texto - Número da OS, caso aplicável. Vem 'Sem Número' como padrão.
- `P3_DAT_SOLICITACAO` - Seletor de Data - Data de solicitação da OS.
- `P3_SETOR` - Lista de Seleção - Subsetor da ASQUALOG.
- `P3_SOLICITANTE` - Lista de Seleção - Solicitante na lista de pessoas da ASQUALOG.
- `P3_CATEGORIA` - Lista de Seleção - Tipo de Serviço solicitado. Tem uma ação dinâmica associada a alteração desse item, que faz com que o item P3_INSTALACOES seja exibido ou não.
- `P3_INSTALACOES` - Lista de Seleção - Caso a categoria seja 'Instalações Prediais', precisa da subcategoria.
- `P3_ASSUNTO` - Área de Texto - Descrição da OS.
- `P3_OBS` - Área de Texto - Observação acerca da OS.

---

## 2 — Imagens (Relatório Interativo)

Região que armazena as imagens da tabela temporária relacionadas a OS sendo cadastrada no momento. Possui as colunas de edição da imagem, id, nome do arquivo e a imagem, além de um botão de 'Adicionar Imagem' que redireciona o usuário para a página 101.

SQL que gera o relatório:

```sql
SELECT
  num_img,
  cpf_solicitante,
  dbms_lob.getlength(arq) AS arq,
  filename,
  mimetype
FROM tmp_ordens_servicos
WHERE cpf_solicitante = :P3_CPF
```

---

## 3 — Botão de 'Adicionar OS'

Botão que submete a página com todos os valores dos itens de página fazendo com que o processo 'Criar OS' seja executado. Nesse processo, o registro da OS é criado e, em seguida, as imagens da tabela temporária são transferidos para a tabela definitiva que armazena imagens 'img_ordens_servicos'.

- `Criar OS` (PL/SQL):

```sql
DECLARE
    v_obs   VARCHAR2(4000);
    v_id_os ordens_servicos.id_os%TYPE;
    v_primeiro_nome VARCHAR2(200);
BEGIN
    v_obs := NVL(:P3_OBS, 'Não há.');

    v_primeiro_nome := REGEXP_SUBSTR(:P3_SOLICITANTE, '^[^ ]+');

    -- Verifica se a categoria é "Instalações prediais"
    IF :P3_CATEGORIA = 'Instalações prediais' THEN
        IF :P3_INSTALACOES IS NULL THEN
            apex_error.add_error(
                p_message => 'Para categoria "Instalações prediais", o campo Instalações não pode ser vazio.',
                p_display_location => apex_error.c_inline_in_notification
            );
            RETURN;
        END IF;

        INSERT INTO ordens_servicos (
            num_os,
            dat_solicitacao,
            solicitante,
            assunto,
            obs,
            categoria,
            setor
        ) VALUES (
            :P3_NUM_OS,
            :P3_DAT_SOLICITACAO,
            :P3_SOLICITANTE,
            :P3_ASSUNTO,
            v_obs,
            :P3_CATEGORIA || ' --> ' || :P3_INSTALACOES,
            :P3_SETOR
        )
        RETURNING id_os INTO v_id_os;
    ELSE
        -- Para outras categorias, insere apenas a categoria
        INSERT INTO ordens_servicos (
            num_os,
            dat_solicitacao,
            solicitante,
            assunto,
            obs,
            categoria,
            setor
        ) VALUES (
            :P3_NUM_OS,
            :P3_DAT_SOLICITACAO,
            :P3_SOLICITANTE,
            :P3_ASSUNTO,
            v_obs,
            :P3_CATEGORIA,
            :P3_SETOR
        )
        RETURNING id_os INTO v_id_os;
    END IF;

    FOR img_rec IN (
        SELECT arq, mimetype, filename
        FROM tmp_ordens_servicos
        WHERE cpf_solicitante = :P3_CPF
    ) LOOP
        INSERT INTO img_ordens_servicos (
            fk_ordens_servicos,
            arq,
            mimetype,
            filename
        ) VALUES (
            v_id_os,
            img_rec.arq,
            img_rec.mimetype,
            img_rec.filename
        );
    END LOOP;

    DELETE FROM tmp_ordens_servicos
    WHERE cpf_solicitante = :P3_CPF;

    :P3_ID_OS := v_id_os;
END;
```

---

Ver também: [paginas/README.md](../README.md)

