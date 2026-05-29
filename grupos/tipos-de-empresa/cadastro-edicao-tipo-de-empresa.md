---
layout: default
title: Cadastro e Edição de Tipo de Empresa
permalink: /tipos-de-empresa/cadastro-edicao-tipo-de-empresa/
group: tipos-de-empresa
---

# Tipos de Empresa

## Cadastro e Edição de Tipo de Empresa

![Figura 63 - Cadastro e Edição de Tipo de Empresa]({{ '/assets/media/images/tipos_de_empresa/tipos_de_empresa_edit.png' | relative_url }})

<p class="caption">Figura 63 - Cadastro e Edição de Tipo de Empresa</p>

Esta tela é utilizada para definir os atributos de um novo tipo ou editar um registro existente.

### Campos do Formulário

- **\*Nome:** Campo de texto obrigatório para definir o título da categoria (ex: "Instituto de Ciência e Tecnologia").
- **\*Posição:** Campo numérico obrigatório que define a ordem de exibição na listagem e em menus de seleção.
- **Descrição:** Campo de texto para detalhamento adicional sobre o que compreende aquele tipo de empresa.
- **Seletor Subtipo (Alternador/Toggle):** Uma chave que, quando ativada, indica que o registro atual é dependente de uma categoria principal.
- **\*Tipo Empresa Raiz:** Campo de seleção (dropdown) que aparece apenas se a opção "Subtipo" estiver marcada. Permite vincular o registro a uma categoria pai (ex: vincular "ICT" à raiz "Parceira").

### Ações

- **Botão Cancelar:** Aborta a operação e retorna à lista.
- **Botão Salvar:** Confirma as alterações ou o novo cadastro no banco de dados.

Este formulário permite criar uma estrutura de classificação flexível e bem organizada, facilitando a busca e o agrupamento de empresas parceiras.
