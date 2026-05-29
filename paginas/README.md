<div align="center">

<p align="center">
    <h1>Páginas</h1>
</p>

</div>

---

## 🎯 Visão Geral

Este documento descreve os principais fluxos de navegação entre páginas da aplicação COS e apresenta a convenção de numeração usada para páginas e modais.

## 🔄 Fluxos principais do usuário (passo a passo)

1. `Entrar no sistema`
    <br>1.1. O usuário autentica-se via SSO.
    <br>1.2. Após autenticação, o usuário acessa a página `1` (verificação de setor).
    <br>1.3. Se estiver autorizado, o sistema redireciona automaticamente para a página `2` (home); caso contrário, mantém o usuário na página `1` com instruções.

2. `Registrar uma OS`
    <br>2.1. Na página `2`, clicar em **Registrar OS** → abre a página `3` (cadastro).
    <br>2.2. Preencher os campos obrigatórios do formulário.
    <br>2.3. Para adicionar imagens: clicar **Adicionar imagem** → abrir modal `101` → fazer upload e fechar o modal (retorna para `3`).
    <br>2.4. Para remover imagem durante a criação: clicar no ícone de remoção → confirmar (modal `102`, se aplicável) → imagem removida.
    <br>2.5. Clicar **Adicionar OS** (submeter). Ao salvar com sucesso, retornar para a página `2`.

3. `Editar uma OS`
    <br>3.1. Na página `2`, selecionar uma OS e clicar **Editar** → abrir a página `4`.
    <br>3.2. Alterar os campos necessários.
    <br>3.3. Para incluir imagem: clicar **Adicionar imagem** → abrir modal `103` → upload → fechar modal (retorna para `4`).
    <br>3.4. Para excluir imagem: clicar ícone de exclusão → abrir modal `104` → confirmar exclusão.
    <br>3.5. Clicar **Confirmar edições** → gravar alterações e retornar para a página `2`.

4. `Deletar uma OS`
    <br>4.1. Na página `2`, selecionar a OS e clicar **Excluir**.
    <br>4.2. Exibir diálogo de confirmação.
    <br>4.3. Ao confirmar, remover o registro e atualizar a listagem na página `2`.

5. `Ver gráficos`
    <br>5.1. Na página `2`, clicar **Gráficos** → abrir a página `5`.
    <br>5.2. Aplicar filtros (período, responsável, status) para ajustar a visualização.
    <br>5.3. Visualizar/baixar resultados e, quando desejar, retornar para a página `2`.

## 🔢 Convenção de numeração

- Páginas principais: números baixos (0–99) — ex.: `0`, `1`, `2`, `3`, `4`, `5`.
- Modais e componentes auxiliares: números na casa das centenas — ex.: `101`, `102`, `103`, `104`.

## 🧾 Descrição rápida das páginas

- `0` — Página global: contém regiões compartilhadas (header, footer, scripts globais).
- `1` — Verificação de setor/autorização: valida se o usuário pertence ao setor esperado (ASQUALOG/SEQUALOG/EGEPEAVI) e bloqueia acesso quando necessário.
- `2` — Home / Listagem de OS: listagem principal com botões de ação (registrar, editar, excluir, gráficos).
- `3` — Cadastro de OS: formulário de criação, upload de imagens via modal `101`.
- `4` — Edição de OS: formulário de edição com imagens gerenciadas pelos modais `103` (incluir) e `104` (remover).
- `5` — Gráficos: dashboards e visualizações filtráveis sobre OS.
- `101` — Modal: upload de imagem para OS em criação.
- `102` — Modal: remover imagem (OS em criação).
- `103` — Modal: upload de imagem para OS já registrada.
- `104` — Modal: remover imagem de OS já registrada.

---
