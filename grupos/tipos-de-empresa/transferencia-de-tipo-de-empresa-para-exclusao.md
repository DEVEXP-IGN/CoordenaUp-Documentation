---
layout: default
title: Transferência de Tipo de Empresa para Exclusão
permalink: /tipos-de-empresa/transferencia-de-tipo-de-empresa-para-exclusao/
group: tipos-de-empresa
---

# Tipos de Empresa

## Transferência de Tipo de Empresa para Exclusão

![Figura 64 - Transferência de Tipo de Empresa]({{ '/assets/media/images/tipos_de_empresa/tipos_de_empresa_transfer.png' | relative_url }})

<p class="caption">Figura 64 - Transferência de Tipo de Empresa</p>

Para garantir a integridade dos dados e evitar que registros órfãos permaneçam no sistema, o CoordenaUP utiliza uma rotina de transferência obrigatória antes da exclusão definitiva.

### Campos da Tela

- **Tipo de Empresa para Exclusão:** Exibe o nome do tipo que o usuário deseja remover.
- **\*Tipo de Empresa para Transferência:** Campo de seleção obrigatório onde o usuário deve escolher para qual categoria as empresas atualmente vinculadas ao tipo "excluído" serão movidas.
- **Mensagem Informativa:** O sistema apresenta o aviso: "As empresas com tipo [Nome do Tipo] serão transferidas para o tipo selecionado".

### Ações

- **Botão Transferir e Excluir:** Executa a migração em massa das empresas vinculadas e remove a categoria antiga de forma segura.
- **Botão Cancelar:** Interrompe o processo de exclusão.

Este fluxo garante que nenhuma empresa fique sem uma classificação válida no sistema, mantendo a integridade referencial e facilitando a auditoria.
