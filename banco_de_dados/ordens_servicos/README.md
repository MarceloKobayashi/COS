<div align="center">
	<h1>ORDENS_SERVICOS</h1>
</div>

Descrição: tabela central do COS, responsável por guardar as ordens de serviço propriamente ditas.

---

## 📖 Índice

- [Visão Geral](#-visão-geral)
- [Campos](#-campos)
- [Constraints / Triggers](#-constraints--triggers)
- [Exemplos (queries/rotinas)](#-exemplos-queriesrotinas)
- [Contribuir](#-contribuir)

---

## 🔎 Visão Geral

Armazena as ordens de serviço cadastradas no sistema, com informações da solicitação, descrição, responsável, categoria e setor de origem.

## 🧾 Campos

- `id_os` — NUMBER — not null — identificador da OS
- `num_os` — VARCHAR2(4000) — not null — número da OS; quando não houver, usar um identificador como "Sem número"
- `dat_solicitacao` — DATE — not null — data em que a OS foi solicitada
- `solicitante` — VARCHAR2(20) — not null — quem fez a solicitação
- `assunto` — VARCHAR2(4000) — not null — descrição da OS
- `obs` — VARCHAR2(4000) — observação sobre a OS
- `categoria` — VARCHAR2(4000) — categoria em que a OS se enquadra
- `setor` — VARCHAR2(100) — setor da ASQUALOG ao qual a OS pertence, como SEQUALOG ou EGEPAVI

## ⚙️ Constraints

- `ORDENS_SERVICOS_PK` - Define a coluna de identificador como primary key.
```sql
CONSTRAINT "ORDENS_SERVICOS_PK" PRIMARY KEY ("ID_OS") USING INDEX ENABLE
```

## ⚙️ Triggers

Não há triggers relacionados a essa tabela.

## 🧪 Exemplos (queries/rotinas)

- Exemplo: listar as OS mais recentes

```sql
SELECT *
  FROM ordens_servicos
 ORDER BY dat_solicitacao DESC;
```

- Exemplo: filtrar OS de um setor específico

```sql
SELECT *
  FROM ordens_servicos
 WHERE setor = 'SEQUALOG';
```

---

Voltar para: [Visão geral do BD](../README.md)
