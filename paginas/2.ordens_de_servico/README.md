<div align="center">
  <p align="center">
    <h1>2. Home / Listagem de OS</h1>
  </p>
</div>

---

> Página principal com listagem de Ordens de Serviço e ações (Registrar, Editar, Excluir, Gráficos).

## 🎯 Visão geral

Esta tela lista as Ordens de Serviço (OS) e permite ações rápidas: ver detalhes, editar, excluir e registrar novas OS. No topo há botões para gráficos e cadastro; a área principal contém filtros, a listagem (região dinâmica) e o painel de detalhes.

---

## 1 — Filtros (Região Estática)

Itens principais (todos disparam a ação dinâmica `Pesquisar` ao serem alterados):

- `P2_PESQUISA_NUM` — campo de texto: número da OS.
- `P2_SETOR` — lista: subsetores (EGEPAVI, SEQUALOG, ASQUALOG).
- `P2_SOLICITANTE` — lista: pessoas do setor (query abaixo).
- `P2_PESQUISA_ASSUNTO` — campo de texto: palavra-chave do assunto.
- `P2_CATEGORIA` — lista: tipo de OS.
- `P2_INSTALACAO` — lista: aparece quando `P2_CATEGORIA` = "Instalações Prediais".
- `P2_DAT_INICIO` / `P2_DAT_FIM` — seletores de data.

- `Botão Limpar_Filtros` — reseta os itens via ação dinâmica (JS) e atualiza a região.

Query de exemplo usada por `P2_SOLICITANTE` (mantida do app):

```sql
SELECT INITCAP(nom_pessoa) AS d,
    INITCAP(
        CASE
            WHEN REGEXP_LIKE(nom_pessoa, '^[^ ]+ (da|de|do|das|dos|du|d'') ', 'i')
                THEN REGEXP_SUBSTR(nom_pessoa, '(\S+\s+\S+\s+\S+)')
            ELSE REGEXP_SUBSTR(nom_pessoa, '(\S+\s+\S+)')
        END
    ) AS r
FROM dda.vinculo_sf
WHERE ind_vinculo_ativo = 'S'
    AND (
        (:P2_SETOR = 'EGEPAVI' AND sgl_orgao_exercicio = 'EGEPAVI')
        OR (:P2_SETOR = 'SEQUALOG' AND sgl_orgao_exercicio IN ('SEQUALOG', 'ASQUALOG') AND cod_tipo_vinculo <> 8)
        OR (:P2_SETOR IS NULL AND sgl_orgao_exercicio IN ('EGEPAVI', 'SEQUALOG', 'ASQUALOG') AND cod_tipo_vinculo <> 8)
    )
ORDER BY d;
```

---

## 2 — Ordens de Serviço (Região Dinâmica)

Região que renderiza a lista de OS em ordem decrescente. A saída é um container com scroll que exibe número, categoria, assunto, data e solicitante; cada item possui um link para carregar detalhes no painel lateral.

PL/SQL completo que gera a listagem (mantido integralmente):

```sql
DECLARE
    v_html CLOB := '';
    v_tem_registro  BOOLEAN := FALSE;
BEGIN
    v_html := v_html || '<div class="os-list" style="font-family:Arial, sans-serif; max-height:500px; overflow-y:auto; border:1px solid #ddd; border-radius:6px;">';

    FOR r IN (
        SELECT o.id_os, o.num_os, o.solicitante, o.assunto, o.dat_solicitacao, o.categoria, o.setor,
                     COUNT(i.num_img) AS qtd_imgs
        FROM ordens_servicos o
        LEFT JOIN img_ordens_servicos i ON o.id_os = i.fk_ordens_servicos
        WHERE
            (:P2_PESQUISA_NUM IS NULL OR LOWER(TRANSLATE(o.num_os, 'ÁÀÂÃÄÉÈÊËÍÌÎÏÓÒÔÕÖÚÙÛÜÇáàâãäéèêëíìîïóòôõöúùûüç', 'AAAAAEEEEIIIIOOOOOUUUUCaaaaaeeeeiiiiooooouuuuc')) LIKE '%' || LOWER(TRANSLATE(:P2_PESQUISA_NUM, 'ÁÀÂÃÄÉÈÊËÍÌÎÏÓÒÔÕÖÚÙÛÜÇáàâãäéèêëíìîïóòôõöúùûüç', 'AAAAAEEEEIIIIOOOOOUUUUCaaaaaeeeeiiiiooooouuuuc')) || '%')
            AND (:P2_PESQUISA_ASSUNTO IS NULL OR LOWER(TRANSLATE(o.assunto, 'ÁÀÂÃÄÉÈÊËÍÌÎÏÓÒÔÕÖÚÙÛÜÇáàâãäéèêëíìîïóòôõöúùûüç', 'AAAAAEEEEIIIIOOOOOUUUUCaaaaaeeeeiiiiooooouuuuc')) LIKE '%' || LOWER(TRANSLATE(:P2_PESQUISA_ASSUNTO, 'ÁÀÂÃÄÉÈÊËÍÌÎÏÓÒÔÕÖÚÙÛÜÇáàâãäéèêëíìîïóòôõöúùûüç', 'AAAAAEEEEIIIIOOOOOUUUUCaaaaaeeeeiiiiooooouuuuc')) || '%')
            AND (:P2_SOLICITANTE IS NULL OR LOWER(o.solicitante) = LOWER(:P2_SOLICITANTE))
            AND ((:P2_DAT_INICIO IS NULL AND :P2_DAT_FIM IS NULL)
                     OR (:P2_DAT_INICIO IS NULL AND o.dat_solicitacao <= :P2_DAT_FIM)
                     OR (:P2_DAT_FIM IS NULL AND o.dat_solicitacao >= :P2_DAT_INICIO)
                     OR (o.dat_solicitacao BETWEEN :P2_DAT_INICIO AND :P2_DAT_FIM))
            AND (:P2_CATEGORIA IS NULL
                     OR (:P2_CATEGORIA IS NOT NULL AND :P2_INSTALACAO IS NULL AND LOWER(o.categoria) LIKE LOWER('%' || :P2_CATEGORIA || '%'))
                     OR (:P2_CATEGORIA IS NOT NULL AND :P2_INSTALACAO IS NOT NULL AND LOWER(o.categoria) = LOWER(:P2_CATEGORIA || ' --> ' || :P2_INSTALACAO)))
            AND (:P2_SETOR IS NULL OR (LOWER(o.setor) = LOWER(:P2_SETOR)))
        GROUP BY o.id_os, o.num_os, o.solicitante, o.assunto, o.dat_solicitacao, o.categoria, o.setor
        ORDER BY o.dat_solicitacao DESC, o.id_os DESC
    ) LOOP
        v_tem_registro := TRUE;
        v_html := v_html || '<div class="os-item" id="os_' || TO_CHAR(r.id_os) || '" style="display:flex;justify-content:space-between;align-items:center;padding:10px;border-bottom:1px solid #eee;">'
                            || '<div class="os-content">'
                            || '<div style="font-size:18px;font-weight:bold;color:#2c3e50;">' || r.num_os || ' - ' || r.categoria || '</div>'
                            || '<div style="font-size:14px;color:#555;margin-top:2px;">' || r.assunto || '</div>'
                            || '<div style="display:flex;justify-content:space-between;font-size:12px;color:#888;margin-top:2px;">'
                            || '<span>' || TO_CHAR(r.dat_solicitacao,'DD/MM/YYYY') || '</span>'
                            || '<span>' || r.solicitante || '</span>'
                            || '</div></div>'
                            || '<div style="font-size:20px;color:#bbb;">'
                            || '<a class="os-link" href="' || APEX_PAGE.GET_URL(p_page => 2, p_items => 'P2_ID_OS,P2_EXIBIR', p_values => r.id_os || ',' || r.num_os) || '">&rsaquo;</a>'
                            || '</div></div>';
    END LOOP;

    IF NOT v_tem_registro THEN
        v_html := v_html || '<div style="padding:20px;text-align:center;color:#888;font-style:italic;">Nenhuma Ordem de Serviço encontrada para esses filtros combinados.</div>';
    END IF;

    v_html := v_html || '</div>';
    RETURN v_html;
END;
```

Após a atualização da região, a ação dinâmica executa este JavaScript para centralizar e destacar a OS selecionada:

```js
var id_os = $v('P2_ID_OS');
if (id_os) {
    var target = document.getElementById('os_' + id_os);
    if (target) {
        target.scrollIntoView({ behavior: 'smooth', block: 'center' });
        target.style.backgroundColor = '#cce5ff';
        setTimeout(function() {
            target.style.transition = 'background-color 5s';
            target.style.backgroundColor = '';
        }, 1500);
    }
}
```

---

## 3 — Detalhes da OS (Região Estática / Dinâmica)

Exibe detalhes da OS selecionada e provê ações de edição/exclusão.

- `P2_ID_OS` — item oculto: id da OS selecionada. Sua alteração ativa ações dinâmicas que controlam visibilidade e botões.
- `P2_EXIBIR` — campo de texto: exibe o número da OS (origem abaixo).

Query de origem de `P2_EXIBIR`:

```sql
SELECT num_os
FROM ordens_servicos
WHERE id_os = :P2_ID_OS;
```

Região dinâmica de detalhes (PL/SQL mantido):

```sql
DECLARE
    v_num_os        ordens_servicos.num_os%TYPE;
    v_solicitante   ordens_servicos.solicitante%TYPE;
    v_obs           ordens_servicos.obs%TYPE;
    v_cat           ordens_servicos.categoria%TYPE;
    v_dat_sol       ordens_servicos.dat_solicitacao%TYPE;
    v_assunto       ordens_servicos.assunto%TYPE;
    v_html          CLOB := '';
BEGIN
    SELECT num_os, solicitante, NVL(obs,'Não há.'), dat_solicitacao, assunto, categoria
        INTO v_num_os, v_solicitante, v_obs, v_dat_sol, v_assunto, v_cat
        FROM ordens_servicos
     WHERE id_os = :P2_ID_OS;

    v_html := v_html || '<div style="display:flex;justify-content:space-between;gap:40px;">';

    v_html := v_html || '<div style="flex:1;">'
                                         || '<div><b>Número da Ordem de Serviço</b><br>'||v_num_os||'</div><br>'
                                         || '<div><b>Solicitante</b><br>'||v_solicitante||'</div><br>'
                                         || '<div><b>Observação</b><br>'||v_obs||'</div><br>'
                                         || '<div><b>Categoria</b><br>'||v_cat||'</div><br>'
                                         || '</div>';

    v_html := v_html || '<div style="flex:1;">'
                                         || '<div><b>Data de Solicitação</b><br>'|| TO_CHAR(v_dat_sol,'DD/MM/YYYY')||'</div><br>'
                                         || '<div><b>Assunto da Solicitação</b><br>'||v_assunto||'</div><br>'
                                         || '</div>';

    v_html := v_html || '</div>';
    RETURN v_html;
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        RETURN '<div style="padding:20px;text-align:center;color:#888;font-style:italic;font-family:Arial, sans-serif;border:1px solid #ddd;border-radius:6px;background-color:#fafafa;">Selecione uma Ordem de Serviço.</div>';
END;
```

---

Ver também: [paginas/README.md](../README.md)

