---
layout: default
title: Auditoria e Alocação
permalink: /despesas/auditoria-e-alocacao/
group: despesas
---

# Despesas

## Auditoria e Validação por IA

O CoordenaUP utiliza IA Generativa para verificar se os dados digitados condizem com o documento anexado.

1. **Leitura de Documento:** Assim que o arquivo é anexado, a IA lê os dados da Nota Fiscal ou recibo.
2. **Batimento de Dados:** O sistema compara descrição, CNPJ do favorecido e valor informado com os dados encontrados no documento.
3. **Sistema de Alertas:** Caso haja divergência, o sistema exibe alertas na tela de visualização para impedir que inconsistências avancem para a prestação de contas.

## Alocação e Prestação de Contas

Para que a despesa seja considerada prestada, ela passa pelo processo de Alocação Orçamentária.

- **Vínculo ao Plano de Trabalho:** O usuário indica manualmente a qual item do orçamento importado do SIGITEC/SalesForce a despesa se refere.

- **Conformidade:** O sistema só permite alocar despesas que compartilhem o mesmo Centro de Custo do projeto.

- **Resultado Final:** Uma despesa alocada e validada pela IA compõe automaticamente o dossiê de exportação para a Prestação de Contas.
