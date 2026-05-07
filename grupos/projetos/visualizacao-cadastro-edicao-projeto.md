---
layout: default
title: Visualização, Cadastro e Edição de Projeto
permalink: /projetos/visualizacao-cadastro-edicao-projeto/
group: projetos
---

# Gestão de Projetos

## Visualização, Cadastro e Edição de Projeto

![Figura 93 - Visualização, Cadastro e Edição de Projeto]({{ '/assets/media/images/projetos/projetos_show.png' | relative_url }})

<p class="caption">Figura 93 - Visualização, Cadastro e Edição de Projeto</p>

O formulário de cadastro é exaustivo para garantir que todas as métricas do contrato estejam registradas. Abaixo, a relação completa de campos:

- **Identificação e Controle:**
  - **\*Nome:** Título completo do projeto.
  - **\*Abreviação / Sigla:** Nome curto para identificação rápida.
  - **Código do Projeto:** Identificador oficial (ex: código SAP ou número do contrato).
  - **Status:** Situação atual da iniciativa (ex: Em Elaboração, Ativo, Finalizado).
  - **Valor Total:** Montante global aprovado para o projeto.

- **Vínculos Financeiros e Operacionais (A Amarra do Sistema):**
  - **\*Centro de Custo:** Seleção do centro de custo exclusivo do projeto (relação 1:1).
  - **\*Conta Financeira:** Define a conta bancária (Exclusiva ou Compartilhada).
  - **Coordenador:** Seleção da pessoa responsável.
  - **Financiador:** Empresa ou órgão que aporta os recursos.
  - **\*Unidade de Pesquisa (UP):** Unidade à qual o projeto está vinculado.

- **Prazos e Descrições:**
  - **Data de Início e Fim:** Vigência oficial.
  - **Descrição e Observações:** Resumo do escopo e notas internas.

- **Mecanismo de Alerta:**
  - **Data de Lembrete e Descrição:** Alimenta automaticamente o dashboard da Home.

- **Parcelas do Projeto (Cronograma de Desembolso):**
  - Gestão dos aportes financeiros previstos.
  - **Informações:** Número da parcela, Valor, Data Prevista e Status de Recebimento.
