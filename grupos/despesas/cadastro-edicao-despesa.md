---
layout: default
title: Cadastro e Edição de Despesa
permalink: /despesas/cadastro-edicao-despesa/
group: despesas
---

# Despesas

## Cadastro e Edição de Despesa

![Figura 103 - Cadastro e Edição de Despesa]({{ '/assets/media/images/despesas/despesas_edit.png' | relative_url }})

<p class="caption">Figura 103 - Cadastro e Edição de Despesa</p>

O registro de uma despesa requer precisão para alimentar o motor de auditoria do sistema.

- **Dados Identificadores:**
  - **\*Nome / Descrição:** Texto curto que identifica o gasto, como "Compra de Sensores".
  - **\*Centro de Custo:** Define a qual projeto ou departamento a despesa pertence.
  - **\*Favorecido:** Empresa ou pessoa selecionada a partir dos cadastros administrativos.

- **Dados Financeiros e Fiscais:**
  - **\*Valor:** Valor total da nota fiscal ou recibo.
  - **\*Conta Financeira:** Conta de onde sairá o recurso.
  - **Datas:** Emissão do documento, vencimento e pagamento.
  - **\*Status da Despesa:** Define a fase do lançamento.

- **Status da Despesa:**
  - **Previsto:** Lançamento de planejamento ou projeção de gasto.
  - **Solicitado:** Despesa encaminhada para aprovação ou processo de compra iniciado.
  - **Pago:** Recurso efetivamente retirado da conta financeira.
  - **Cancelado:** Gasto anulado.

- **Documentação:**
  - **\*Arquivo de Nota Fiscal / Recibo:** Upload do documento digital usado pela IA para auditoria.

- **Ações:**
  - **Botão Cancelar:** Aborta a operação.
  - **Botão Salvar:** Registra ou atualiza a despesa.
