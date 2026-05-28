<div align="center">
	<h1>IMG_ORDENS_SERVICOS</h1>
</div>

Descrição: tabela que guarda as imagens relacionadas às ordens de serviço.

---

## 📖 Índice

- [Visão Geral](#-visão-geral)
- [Campos](#-campos)
- [Constraints / Triggers](#-constraints--triggers)
- [Exemplos (queries/rotinas)](#-exemplos-queriesrotinas)
- [Contribuir](#-contribuir)

---

## 🔎 Visão Geral

Armazena os arquivos de imagem vinculados a uma OS cadastrada, permitindo registrar evidências e documentos relacionados.

## 🧾 Campos

- `num_img` — NUMBER — not null — identificador da imagem
- `fk_ordens_servicos` — NUMBER — not null — chave estrangeira que aponta para uma OS
- `arq` — BLOB — not null — arquivo da imagem
- `filename` — VARCHAR2(200) — not null — nome do arquivo da imagem
- `mimetype` — VARCHAR2(200) — not null — extensão/tipo do arquivo da imagem

## ⚙️ Constraints

- `IMG_ORDENS_SERVICOS_PK` - Define a coluna de identificador como primary key.
```sql
CONSTRAINT "IMG_ORDENS_SERVICOS_PK" PRIMARY KEY ("NUM_IMG") USING INDEX ENABLE
```

- `IMG_ORDENS_SERVICOS_CON` - Define a coluna de identificador da OS como foreign key.
```sql
CONSTRAINT "IMG_ORDENS_SERVICOS_CON" FOREIGN KEY ("FK_ORDENS_SERVICOS") REFERENCES "ORDENS_SERVICOS" ("ID_OS") ENABLE
```

## ⚙️ Triggers

Não há triggers relacionadas a essa tabela.

## 🧪 Exemplos (queries/rotinas)

- Exemplo: listar imagens de uma OS específica

```sql
SELECT *
  FROM img_ordens_servicos
 WHERE fk_ordens_servicos = :P1_ID_OS;
```

- Exemplo: contar imagens por OS

```sql
SELECT fk_ordens_servicos, COUNT(*) AS total_imagens
  FROM img_ordens_servicos
 GROUP BY fk_ordens_servicos;
```

---

Voltar para: [Visão geral do BD](../README.md)
