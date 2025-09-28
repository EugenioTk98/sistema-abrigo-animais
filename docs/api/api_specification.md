# 5. Especificação de APIs Locais (DAOs)

O sistema de gestão é baseado em uma arquitetura de persistência local utilizando o **Room Persistence Library** (SQLite). As "APIs" da aplicação são os métodos definidos nas interfaces **Data Access Object (DAO)**, que definem os contratos de dados para as entidades `Animal`, `Adotante` e `TarefaVoluntario`.

## 1. Endpoints Previstos (Métodos DAO)

### 1.1. AnimalDao (Gestão e Saúde)

| Endpoint (Método) | Função | Parâmetros de Entrada | Formato de Resposta |
| :--- | :--- | :--- | :--- |
| `insert(animal)` | Registra um novo animal no inventário. | `animal: Animal` | `Unit` (Sucesso) |
| `update(animal)` | Atualiza os dados de saúde (vacinas) e o alerta agendado. | `animal: Animal` | `Unit` (Sucesso) |
| `getById(animalId)` | Busca um animal específico para visualização de detalhes de saúde. | `animalId: Int` | `Animal?` (Objeto ou nulo) |
| `getAll()` | Lista todos os animais cadastrados (para a lista principal). | Nenhum | `List<Animal>` |
| `getProximasVacinas()` | **Consulta de Alerta (RF5):** Busca animais cuja data da próxima vacina está próxima (Ex: dentro de 30 dias). | Nenhum | `List<Animal>` |

### 1.2. AdotanteDao (Gestão de Adoção)

| Endpoint (Método) | Função | Parâmetros de Entrada | Formato de Resposta |
| :--- | :--- | :--- | :--- |
| `insert(adotante)` | Registra uma nova candidatura de pré-adoção (Formulário). | `adotante: Adotante` | `Unit` (Sucesso) |
| `getById(adotanteId)` | Busca uma candidatura específica para análise detalhada. | `adotanteId: Int` | `Adotante?` (Objeto ou nulo) |
| `updateStatus(id, novoStatus)` | **RF11:** Atualiza o status da candidatura ('Aprovado' ou 'Rejeitado'). | `adotanteId: Int`, `novoStatus: String` | `Unit` (Sucesso) |
| `getAllPendentes()` | Lista todas as candidaturas onde `statusAprovacao` é 'Pendente'. | Nenhum | `List<Adotante>` |

### 1.3. TarefaDao (Gestão de Voluntários)

| Endpoint (Método) | Função | Parâmetros de Entrada | Formato de Resposta |
| :--- | :--- | :--- | :--- |
| `insert(tarefa)` | Adiciona uma nova tarefa à lista de pendências (RF19). | `tarefa: TarefaVoluntario` | `Unit` (Sucesso) |
| `update(tarefa)` | Atualiza a tarefa (usado para marcar `concluida` = true/false). | `tarefa: TarefaVoluntario` | `Unit` (Sucesso) |
| `getAll()` | Lista todas as tarefas (ordenadas por prioridade/data). | Nenhum | `List<TarefaVoluntario>` |

---

## 2. Formatos de Resposta e Exemplos

O sistema utiliza `suspend fun` (Kotlin Coroutines) para garantir que todas as operações de I/O sejam não-bloqueantes.

### Exemplo 1: Cadastro e Persistência

**Chamada:** `database.animalDao().insert(novoAnimal)`
**Formato de Resposta:** `Unit` (o sucesso é validado pela ausência de exceção no bloco `try/catch` do Kotlin).

### Exemplo 2: Atualização de Status

**Chamada:** `database.adotanteDao().updateStatus(42, "Aprovado")`
**Formato de Resposta:** `Unit`.
**Impacto:** Atualiza a coluna `statusAprovacao` para 'Aprovado' para a solicitação de ID 42.

### Exemplo 3: Consulta Assíncrona

**Chamada:** `database.animalDao().getProximasVacinas()`
**Formato de Resposta:** `List<Animal>`.

## 3. Autenticação e Autorização

* **Autenticação:** Não há autenticação de usuário (login/senha) implementada nesta etapa. O acesso à aplicação é concedido pela instalação do app no dispositivo do Gestor/Voluntário.
* **Autorização:** A autorização é simulada pela navegação interna do Dashboard. Todos os **DAOs** são acessíveis pela camada lógica (Activities), que opera sob a permissão de armazenamento local do Android.