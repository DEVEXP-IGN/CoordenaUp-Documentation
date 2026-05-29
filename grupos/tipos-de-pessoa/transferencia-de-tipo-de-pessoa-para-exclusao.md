---
layout: default
title: Transferência de Tipo de Pessoa para Exclusão
permalink: /tipos-de-pessoa/transferencia-de-tipo-de-pessoa-para-exclusao/
group: tipos-de-pessoa
---

# Tipos de Pessoa

## Transferência de Tipo de Pessoa para Exclusão

![Figura 67 - Transferência de Tipo de Pessoa]({{ '/assets/media/images/tipos_de_pessoa/tipos_de_pessoa_transfer.png' | relative_url }})

<p class="caption">Figura 67 - Transferência de Tipo de Pessoa</p>

Processo de segurança que impede a perda de históricos e vínculos ao remover uma categoria que ainda possua registros ativos de pessoas.

### Campos da Tela

- **Tipo de Pessoa para Exclusão:** Exibe o nome do registro selecionado para remoção.
- **\*Tipo de Pessoa para Transferência:** Campo de seleção obrigatório. O usuário deve escolher um novo destino para as pessoas vinculadas ao tipo que será removido.
- **Instrução de Sistema:** O software informa que "As pessoas com tipo [Nome] serão transferidas para o tipo selecionado", garantindo que nenhuma pessoa fique sem uma classificação válida no sistema.

### Ações

- **Botão Transferir e Excluir:** Realiza a migração automática dos vínculos e a deleção da categoria antiga.
- **Botão Cancelar:** Retorna à lista sem realizar alterações.

Este fluxo garante a integridade referencial dos dados, impedindo que pessoas permaneçam sem uma classificação válida no sistema.
