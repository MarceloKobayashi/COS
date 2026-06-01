<div align="center">

<p align="center">
  <h1>1. Verificação de Setor</h1>
</p>

</div>

---

## 🎯 Visão Geral

A página `1` valida o setor do usuário antes de liberar o acesso à aplicação. Ela funciona como uma etapa intermediária entre o login e a página inicial real do COS.

## 🧩 Objetivo

Permitir entrada automática para usuários autorizados dos setores `ASQUALOG`, `SEQUALOG` e `EGEPAVI` e exibir um aviso quando o usuário não tiver permissão.

## 🔄 Funcionamento

1. O usuário acessa o COS e é direcionado para a página `1`.
2. O item oculto `P1_ASQUALOG` carrega o setor do usuário ativo.
3. Um JavaScript executado no carregamento compara o setor com a lista permitida.
4. Se o setor for válido, o botão `Gráfico` é acionado automaticamente e o usuário segue para a página `2`.
5. Se o setor não for válido, o botão é ocultado e a mensagem de aviso é exibida.

## 🧾 Componentes da página

- Um botão de navegação para a página `2`.
- Um item de página oculto (`P1_ASQUALOG`).
- Uma região estática de aviso para acesso negado.

## 🗄 Origem do item `P1_ASQUALOG`

O item usa uma query para identificar o órgão/setor do usuário ativo.

```sql
SELECT SGL_ORGAO_EXERCICIO
FROM DDA.VINCULO_SF
WHERE NUM_CPF =
    CASE
        WHEN REGEXP_LIKE(:APP_USER, '^\d+$')
            THEN :APP_USER
        ELSE (
            SELECT u.num_cpf_pessoa
            FROM dda.usuario_rede u
            WHERE LOWER(u.txt_email_sf) = LOWER(:APP_USER) || '@senado.leg.br'
        )
    END
    AND IND_VINCULO_ATIVO = 'S'
```

## 💻 Ação Dinâmica no Carregamento da Página

- `Esconder botões` - Roda um JavaScript para verificação do setor do usuário.

```js
var setor = $v('P1_ASQUALOG');
var setoresPermitidos = ['ASQUALOG', 'SEQUALOG', 'EGEPAVI'];

if (!setoresPermitidos.includes(setor)) {
    $('#gr').hide();
    $('#aviso').show();
} else {
    $('#gr').trigger('click');
}
```

---

Ver também: [paginas/README.md](../README.md)
