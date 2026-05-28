<div align="center">
	<h1>TMP_ORDENS_SERVICOS</h1>
</div>

Descrição: tabela temporária para guardar imagens antes da criação da OS.

---

## 📖 Índice

- [Visão Geral](#-visão-geral)
- [Campos](#-campos)
- [Constraints / Triggers](#-constraints--triggers)
- [Exemplos (queries/rotinas)](#-exemplos-queriesrotinas)
- [Contribuir](#-contribuir)

---

## 🔎 Visão Geral

Armazena temporariamente as imagens enviadas pelo solicitante enquanto a ordem de serviço ainda não foi criada. Depois, esses arquivos podem ser relacionados à OS definitiva.

## 🧾 Campos

- `num_img` — NUMBER — not null — identificador da imagem
- `cpf_solicitante` — VARCHAR2(20) — not null — CPF do usuário para relacionar depois com a OS
- `arq` — BLOB — not null — arquivo da imagem
- `filename` — VARCHAR2(200) — not null — nome do arquivo da imagem
- `mimetype` — VARCHAR2(200) — not null — extensão/tipo do arquivo da imagem

## ⚙️ Constraints / Triggers

- `TMP_ORDENS_SERVICOS_PK` - Define a coluna de identificador como primary key.
```sql
CONSTRAINT "TMP_ORDENS_SERVICOS_PK" PRIMARY KEY ("NUM_IMG") USING INDEX ENABLE
```

## ⚙️ Constraints / Triggers

Não há triggers relacionados a essa tabela.

## 🧪 Exemplos (queries/rotinas)

- Exemplo: listar imagens temporárias de um solicitante

```sql
SELECT *
  FROM tmp_ordens_servicos
 WHERE cpf_solicitante = :P1_CPF_SOLICITANTE;
```

- Exemplo: remover registros temporários antigos após vinculação da OS

```sql
DELETE FROM tmp_ordens_servicos
 WHERE cpf_solicitante = :P1_CPF_SOLICITANTE;
```

---

Voltar para: [Visão geral do BD](../README.md)
