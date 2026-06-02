<div align="center">
  <p align="center">
    <h1>5. Gráficos</h1>
  </p>
</div>

---

> Página para visualizar a distribuição de OS por mês e por solicitante, com a possibilidade de aplicar filtros

## 🎯 Visão geral

Esta tela permite o usuário visualizar dois gráficos de barra (número de OS's por mês e por solicitante), podendo filtrar as OS por meio de itens de página com ação dinâmica em suas alterações que atualizam os gráficos em tempo real. Esses filtros englobam intervalo de data, setor, solicitante, categoria e ano de solicitação.

---

## 1 — Filtro (Conteúdo Estático)

Itens presentes para filtrar os gráficos:

- `P5_INICIO` - Seletor de Data - Data inicial do intervalo.
- `P5_FIM` - Seletor de Data - Data final do intervalo.
- `P5_SETOR` - Lista de Seleção - Subsetor da ASQUALOG.
- `P5_SOLICITANTE` - Caixa de Combinação - Solicitantes das OS, permite a múltipla escolha de solicitantes.
- `P5_CATEGORIA` - Caixa de Combinação - Categoria das OS, permite a múltipla escolha de categorias.
- `P5_ANO` - Lista de Seleção - Anos de 2025 e 2026.
- `P5_LIXO` - Oculto - Armazena valores inválidos digitados nos itens do tipo caixa de combinação.

- `Limpar_filtros` - Botão que ativa uma ação dinâmica para setar como vazio todos os itens de página dessa região.

---

## 2 — OS por Mês (Gráfico de Barras Verticais)

Região que apresenta um gráfico com todos os meses do ano escolhido no eixo X e a quantidade de OS no eixo Y. Ele mostra a quantidade de OS cadastradas por mês.

SQL que gera o gráfico:

```sql
DECLARE
    v_sql   CLOB;
    v_solicitantes  VARCHAR2(4000);
    v_categorias    VARCHAR2(4000);
BEGIN
    IF :P5_SOLICITANTE IS NOT NULL THEN
        v_solicitantes := '''' || REPLACE(:P5_SOLICITANTE, ':', ''',''') || '''';
    END IF;

    IF :P5_CATEGORIA IS NOT NULL THEN
        v_categorias := '''' || REPLACE(:P5_CATEGORIA, ':', ''',''') || '''';
    END IF;

    v_sql := '
        WITH MESES AS (
            SELECT
                RTRIM(TO_CHAR(ADD_MONTHS(TO_DATE(''01/01/'' || :P5_ANO, ''DD/MM/YYYY''), LEVEL - 1), 
                    ''MONTH'', ''NLS_DATE_LANGUAGE=PORTUGUESE'')) || ''/'' ||
                    TO_CHAR(ADD_MONTHS(TO_DATE(''01/01/'' || :P5_ANO, ''DD/MM/YYYY''), LEVEL - 1), ''YYYY'') AS MES
            FROM DUAL
            CONNECT BY LEVEL <= 12
        ),
        OS_AGRUPADAS AS (
            SELECT
                RTRIM(TO_CHAR(os.dat_solicitacao, ''MONTH'', ''NLS_DATE_LANGUAGE=PORTUGUESE'')) || ''/'' ||
                    TO_CHAR(os.dat_solicitacao, ''YYYY'') AS MES,
                COUNT(*) AS QTD
            FROM ordens_servicos os
            WHERE 1 = 1 ';
    
    IF :P5_INICIO IS NOT NULL THEN
        v_sql := v_sql || ' AND os.dat_solicitacao >= :P5_INICIO';
    END IF;

    IF :P5_FIM IS NOT NULL THEN
        v_sql := v_sql || ' AND os.dat_solicitacao <= :P5_FIM';
    END IF;

    IF v_solicitantes IS NOT NULL THEN
        v_sql := v_sql || ' AND os.solicitante IN (' || v_solicitantes || ')';
    END IF;

    IF v_categorias IS NOT NULL THEN
        v_sql := v_sql || ' AND os.categoria IN (' || v_categorias || ')';
    END IF;

    IF :P5_SETOR <> 'ASQUALOG' THEN
        v_sql := v_sql || ' AND os.setor = :P5_SETOR';
    END IF;

    v_sql := v_sql || '
            GROUP BY RTRIM(TO_CHAR(os.dat_solicitacao, ''MONTH'', ''NLS_DATE_LANGUAGE=PORTUGUESE'')) || ''/'' ||
                     TO_CHAR(os.dat_solicitacao, ''YYYY'')
        )
        SELECT
            m.mes AS label,
            NVL(r.qtd, 0) AS value,
            ''Ordens de Serviço'' AS series
        FROM meses m
        LEFT JOIN os_agrupadas r ON m.mes = r.mes
        ORDER BY TO_DATE(m.mes, ''MONTH/YYYY'', ''NLS_DATE_LANGUAGE=PORTUGUESE'')
    ';
    RETURN v_sql;
END;

```

---

## 2 — OS por pessoa (Gráfico de Barras Verticais)

Região que apresenta um gráfico com todos os solicitantes que tem pelo menos uma OS cadastrada no sistema no eixo X e a quantidade de OS no eixo Y. Ele mostra a quantidade de OS cadastradas por solicitante.

SQL que gera o gráfico:

```sql
DECLARE
    v_sql   CLOB;
    v_solicitantes  VARCHAR2(4000);
    v_categorias    VARCHAR2(4000);
BEGIN
    IF :P5_SOLICITANTE IS NOT NULL THEN
        v_solicitantes := '''' || REPLACE(:P5_SOLICITANTE, ':', ''',''') || '''';
    END IF;

    IF :P5_CATEGORIA IS NOT NULL THEN
        v_categorias := '''' || REPLACE(:P5_CATEGORIA, ':', ''',''') || '''';
    END IF;

    v_sql := '
        SELECT
            os.solicitante AS label,
            COUNT(*) AS value,
            ''Ordens de Serviço'' AS series
        FROM ordens_servicos os
        WHERE 1 = 1 ';

    IF :P5_INICIO IS NOT NULL THEN
        v_sql := v_sql || ' AND os.dat_solicitacao >= :P5_INICIO';
    END IF;

    IF :P5_FIM IS NOT NULL THEN
        v_sql := v_sql || ' AND os.dat_solicitacao <= :P5_FIM';
    END IF;

    IF :P5_ANO IS NOT NULL THEN
        v_sql := v_sql || ' AND os.dat_solicitacao >= TO_DATE(''01/01/'' || :P5_ANO, ''DD/MM/YYYY'')
            AND os.dat_solicitacao < ADD_MONTHS(
                TO_DATE(''01/01/'' || :P5_ANO, ''DD/MM/YYYY''),
                12
            )';
    END IF;

    IF v_solicitantes IS NOT NULL THEN
        v_sql := v_sql || ' AND os.solicitante IN (' || v_solicitantes || ')';
    END IF;

    IF v_categorias IS NOT NULL THEN
        v_sql := v_sql || ' AND os.categoria IN (' || v_categorias || ')';
    END IF;

    IF :P5_SETOR <> 'ASQUALOG' THEN
        v_sql := v_sql || ' AND os.setor = :P5_SETOR';
    END IF;

    v_sql := v_sql || '
        GROUP BY os.solicitante
        ORDER BY value DESC
    ';

    RETURN v_sql;
END;
```
---

Ver também: [paginas/README.md](../README.md)

