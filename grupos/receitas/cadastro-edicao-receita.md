---
layout: default
title: Cadastro e Edição de Receita
permalink: /receitas/cadastro-edicao-receita/
group: receitas
---

# Receitas

## Cadastro e Edição de Receita

![Figura 100 - Cadastro e Edição de Receita]({{ '/assets/media/images/Receitas/receitas_edit.png' | relative_url }})

<p class="caption">Figura 100 - Cadastro e Edição de Receita</p>

O cadastro de uma receita geralmente deriva da geração de parcelas no módulo de Projetos, mas pode ser ajustado para refletir a realidade financeira.

- **Campos de Identificação:**
  - **\*Nome:** Descrição da receita, como "1ª Parcela - Projeto X".
  - **\*Projeto:** Vínculo obrigatório com o projeto, que determina o Centro de Custo.
  - **Parcela Relacionada:** Liga a receita diretamente à parcela do cronograma de desembolso.

- **Dados Financeiros:**
  - **\*Valor:** Valor bruto da receita.
  - **\*Conta Financeira:** Conta onde o recurso será ou foi depositado.
  - **\*Data de Vencimento:** Data prevista para o pagamento pelo financiador.
  - **Data de Recebimento:** Preenchida apenas quando a receita atinge o status Recebida.

- **Classificação:**
  - **Categoria Financeira:** Classificação usada em relatórios gerenciais, como "Aporte de Projeto".

- **Status da Receita:**
  - **Prevista:** O recurso está no cronograma, mas o processo de cobrança ainda não foi iniciado.
  - **Faturada:** A nota de débito ou fatura já foi enviada ao financiador.
  - **Recebida:** O recurso já está disponível e conciliado na conta financeira.
  - **Cancelada:** A parcela foi anulada ou será renegociada.

- **Ações:**
  - **Botão Cancelar:** Aborta a operação.
  - **Botão Salvar:** Registra ou atualiza a receita.
