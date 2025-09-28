#Requisitos e Regras de Negócio

## 1. Perfis de Usuários

1.  **Gestor/Voluntário:** Usuário com acesso total aos módulos de gestão interna (Cadastro, Saúde, Listagem, Tarefas e Gerenciamento de Adoção) via App Mobile e Web.
2.  **Comunidade/Adotante:** Usuário externo que interage com o formulário de pré-adoção (Web/App).

## 2. Requisitos Funcionais (RF) - Válidos para Mobile e Web

| ID | Requisito | Módulo |
| :--- | :--- | :--- |
| **RF1** | O sistema deve permitir o cadastro de um animal com Nome, Raça, Gênero, Status, e status de Castração. | Cadastro |
| **RF2** | O sistema deve listar todos os animais cadastrados em uma lista (Listagem). | Listagem |
| **RF3** | O sistema deve permitir a seleção/upload de no máximo 4 fotos para o perfil do animal. | Módulo de Fotos |
| **RF5** | O sistema deve calcular e agendar um alerta (via `AlarmManager` no Mobile) 365 dias após o registro da última data de vacina. | Saúde/Alertas |
| **RF11** | O sistema deve permitir a submissão de uma Solicitação de Pré-Adoção (Formulário). | Adoção |
| **RF12** | O Gestor deve poder visualizar os detalhes da solicitação de adoção e alterar o `statusAprovacao` para 'Aprovado' ou 'Rejeitado'. | Gerenciamento Adoção |
| **RF19** | O Gestor deve poder criar, priorizar ('Baixa', 'Média', 'Alta', 'URGENTE') e listar tarefas de voluntariado. | Voluntários |
| **RF20** | O sistema deve permitir que o Voluntário marque uma tarefa como 'Concluída'. | Voluntários |

## 3. Requisitos Não Funcionais (RNF)

| Categoria | RNF | Detalhe |
| :--- | :--- | :--- |
| **Persistência** | **RNF-P1:** O banco de dados central (PostgreSQL) deve garantir a integridade dos dados e o histórico completo de transações. | Banco de Dados |
| **Desempenho** | **RNF-D1:** As consultas da API devem responder em menos de 3 segundos para garantir a fluidez da aplicação móvel. | API |
| **Segurança** | **RNF-S1:** O acesso à API RESTful deve ser protegido por autenticação baseada em tokens (JWT) para módulos de gestão. | Backend/API |
| **Disponibilidade** | **RNF-D2:** O sistema deve ter 99% de tempo de atividade (uptime) garantido pelo serviço de hospedagem do Backend. | Infraestrutura |

## 4. Regras de Negócio

* **RN1 (Saúde):** A data da Próxima Vacina é sempre calculada como Última Vacina + 365 dias.
* **RN2 (Status):** O status inicial de qualquer `Adotante` é 'Pendente'.
* **RN3 (Inventário):** O envio de uma ficha de animal requer obrigatoriamente Nome e Raça.

## 5. Histórias de Usuário (Casos de Uso)

* **HU1 (Gestor/Saúde):** Como **Gestor**, eu quero poder registrar a **última data de vacinação** de um animal para que o sistema **gere um alerta automático** para a próxima dose.
* **HU2 (Voluntário/Tarefas):** Como **Voluntário**, eu quero **visualizar todas as tarefas pendentes** e marcá-las como **concluídas** para manter a organização do abrigo.
* **HU3 (Gestor/Adoção):** Como **Gestor**, eu quero **analisar os detalhes** de uma solicitação de adoção e **alterar seu status** para 'Aprovado' ou 'Rejeitado'.
