---
layout: default
title: Hierarquia de Funções e Permissões
permalink: /usuarios/hierarquia-de-funcoes-e-permissoes/
group: usuarios
---

# Usuários

## Hierarquia de Funções e Permissões

![Figura 84 - Hierarquia de Funções e Permissões]({{ '/assets/media/images/usuarios/usuarios_edit_role_dropdown.png' | relative_url }})

<p class="caption">Figura 84 - Hierarquia de Funções e Permissões</p>

O sistema utiliza um modelo hierárquico onde funções superiores herdam e expandem os acessos das funções inferiores.

- **Estrutura Hierárquica (da maior para a menor):**
  1. **Administrador:** Acesso total a todas as telas do sistema, incluindo Configurações, Administração, Projetos e Financeiro.
  2. **Gestor de Unidade:** Acesso às telas operacionais e administrativas da UP, focado em supervisão.
  3. **Coordenação:** Acesso voltado à gestão de projetos e capital humano.
  4. **Financeiro:** Acesso especializado em telas de contas, categorias financeiras e lançamentos.
  5. **Pesquisador:** Acesso restrito às telas de acompanhamento de projetos e dados específicos de sua atuação.
- **Regra de Acesso:** O CoordenaUP não utiliza perfis de "somente leitura". O acesso é binário por tela: se a função permite o acesso à tela de "Projetos", o usuário pode visualizar, criar, editar e excluir informações dentro daquela tela.
