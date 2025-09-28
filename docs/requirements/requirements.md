# 2. Requisitos e Regras de Negócio do Sistema

Este documento define os requisitos funcionais (RF), não-funcionais (RNF), perfis de usuários e regras de negócio para o Sistema de Gestão para Abrigos de Animais.

## 1. Perfis de Usuários

O sistema deve atender a dois perfis principais:

1.  **Gestor/Voluntário:** Usuário com acesso total aos módulos de gestão interna (Cadastro, Saúde, Listagem, Tarefas e Gerenciamento de Adoção).
2.  **Comunidade/Adotante:** Usuário externo que interage com o formulário de pré-adoção.

## 2. Requisitos Funcionais (RF)

| ID | Requisito | Módulo |
| :--- | :--- | :--- |
| **RF1** | O sistema deve permitir o cadastro de um animal com Nome, Raça, Gênero, Status, Observações de Saúde e status de Castração. | Cadastro |
| **RF2** | O sistema deve listar todos os animais cadastrados em uma lista (`RecyclerView`). | Listagem |
| **RF3** | O sistema deve permitir a seleção de no máximo 4 fotos (URIs) da galeria para o perfil do animal, armazenando-as no banco de dados. | Módulo de Fotos |
| **RF5** | O sistema deve calcular e agendar um alerta automático (via `AlarmManager`) para a próxima vacinação (365 dias após o registro da última data). | Saúde/Alertas |
| **RF11** | O sistema deve permitir que um Adotante envie uma Solicitação de Pré-Adoção (Formulário completo com dados de moradia e compatibilidade). | Adoção |
| **RF12** | O Gestor deve poder visualizar os detalhes da solicitação de adoção e alterar o `statusAprovacao` para 'Aprovado' ou 'Rejeitado'. | Gerenciamento Adoção |
| **RF19** | O Gestor deve poder criar, priorizar ('Baixa', 'Média', 'Alta', 'URGENTE') e listar tarefas de voluntariado. | Voluntários |
| **RF20** | O sistema deve permitir que o Voluntário marque uma tarefa como 'Concluída', removendo-a da lista de pendências. | Voluntários |

## 3. Requisitos Não Funcionais (RNF)

| Categoria | RNF | Detalhe |
| :--- | :--- | :--- |
| **Persistência** | **RNF-P1:** Todos os dados (Animais, Adotantes, Tarefas) devem ser persistidos localmente usando o Room Database (SQLite). | Garantir a integridade dos dados no dispositivo móvel. |
| **Desempenho** | **RNF-D1:** Todas as operações de I/O (leitura/escrita no banco) devem ser assíncronas (Kotlin Coroutines). | Evitar travamento da interface do usuário (UI) e garantir fluidez. |
| **Segurança** | **RNF-S1:** A lógica de atualização de status (Ex: `updateStatus`) deve ser isolada e acessível apenas pelos módulos internos (Gestor). | Proteger os dados críticos contra modificações externas. |
| **Usabilidade** | **RNF-U1:** O layout (Dashboard) deve ser intuitivo, com acesso direto aos principais módulos, facilitando o trabalho em campo. | Usabilidade eficiente para voluntários em ambientes dinâmicos. |

## 4. Regras de Negócio

* **RN1 (Saúde):** A data da Próxima Vacina é calculada como Última Vacina registrada + 365 dias.
* **RN2 (Status):** O status inicial de qualquer `TarefaVoluntario` e de qualquer solicitação de `Adotante` é 'Pendente'.
* **RN3 (Inventário):** O envio de uma nova ficha de animal requer obrigatoriamente o preenchimento dos campos Nome e Raça.
* **RN4 (Tarefas):** O `TarefaAdapter` deve filtrar a lista padrão, exibindo apenas tarefas onde `concluida` é igual a `false`.

## 5. Histórias de Usuário (Casos de Uso)

* **HU1 (Gestor/Saúde):** Como **Gestor**, eu quero poder registrar a **última data de vacinação** de um animal para que o sistema **gere um alerta automático** para a próxima dose.
* **HU2 (Voluntário/Tarefas):** Como **Voluntário**, eu quero **visualizar todas as tarefas pendentes** e marcá-las como **concluídas** para manter a organização do abrigo.
* **HU3 (Gestor/Adoção):** Como **Gestor**, eu quero **analisar os detalhes** de uma solicitação de adoção e **alterar seu status** para 'Aprovado' ou 'Rejeitado'.