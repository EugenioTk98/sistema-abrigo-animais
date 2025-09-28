# Modelo de Dados (Diagrama ER Simplificado)

O banco de dados utiliza o Room Persistence Library (SQLite) e está na **Versão 3**.

<br>
**[Aqui você deve inserir o Diagrama Entidade-Relacionamento (ER) simplificado]**
*Nota: Em um diagrama real, as setas e chaves primárias seriam desenhadas. Aqui representamos apenas as Entidades e suas relações.*
<br>

## 1. Descrição das Entidades e Relacionamentos

| Entidade | Chave Primária (PK) | Chaves Estrangeiras (FK) | Relacionamentos |
| :--- | :--- | :--- | :--- |
| **Animal** | `id` (Int) | N/A | 1:1 com (Potencial Adotante) |
| **Adotante** | `id` (Int) | N/A | N/A |
| **TarefaVoluntario** | `id` (Int) | N/A | N/A |

## 2. Dicionário de Dados

### Tabela: `animais`

| Campo | Tipo Kotlin | Tipo SQLite | Descrição |
| :--- | :--- | :--- | :--- |
| `id` (PK) | Int | INTEGER | ID único do animal. |
| `nome` | String | TEXT | Nome do animal. |
| `isCastrado` | Boolean | INTEGER | 1 se castrado, 0 caso contrário. |
| `ultimaVacinaData` | String? | TEXT | Última data de vacina (formato "YYYY-MM-DD"). |
| `proximaVacinaData` | String? | TEXT | Data agendada para o alerta de 1 ano. |
| `fotosUris` | List<String> | TEXT | URIs das fotos (serializadas via Gson). |
| `dataRegistro` | Long | INTEGER | Timestamp da entrada do animal no sistema. |

### Tabela: `adotantes`

| Campo | Tipo Kotlin | Tipo SQLite | Descrição |
| :--- | :--- | :--- | :--- |
| `id` (PK) | Int | INTEGER | ID da solicitação. |
| `nomeCompleto` | String | TEXT | Nome do candidato. |
| `telefone` | String | TEXT | Contato principal. |
| `tipoMoradia` | String | TEXT | Casa, Apartamento, etc. |
| `statusAprovacao` | String | TEXT | Status da solicitação ('Pendente', 'Aprovado', 'Rejeitado'). |

### Tabela: `tarefas`

| Campo | Tipo Kotlin | Tipo SQLite | Descrição |
| :--- | :--- | :--- | :--- |
| `id` (PK) | Int | INTEGER | ID da tarefa. |
| `titulo` | String | TEXT | Título da tarefa. |
| `prioridade` | String | TEXT | Nível de urgência ('Alta', 'Média', etc.). |
| `concluida` | Boolean | INTEGER | 1 se concluída, 0 caso contrário. |

## 5. Arquivo: `docs/api/api_specification.md`

```markdown
# Especificação de APIs Locais (DAOs)

O sistema utiliza a biblioteca Room para abstrair o acesso ao SQLite. As "APIs" do sistema são os métodos definidos nas interfaces DAO, que são chamados pela camada lógica (Activities/Coroutines).

## 1. Endpoints Previstos (Métodos DAO)

| Componente | Endpoint (Método) | Função |
| :--- | :--- | :--- |
| **AnimalDao** | `insert(animal)` | Registra um novo animal no inventário. |
| | `update(animal)` | Atualiza os dados de saúde e vacina de um animal existente. |
| | `getById(id)` | Busca um animal específico para visualização de detalhes de saúde. |
| **AdotanteDao** | `insert(adotante)` | Registra uma nova candidatura de pré-adoção. |
| | `getAllPendentes()` | Lista todas as candidaturas com `statusAprovacao` = 'Pendente'. |
| | `updateStatus(id, novoStatus)` | Atualiza o status da candidatura ('Aprovado'/'Rejeitado'). |
| **TarefaDao** | `insert(tarefa)` | Adiciona uma nova tarefa à lista de pendências. |
| | `update(tarefa)` | Marca uma tarefa como concluída ou altera seus detalhes. |
| | `getAll()` | Lista todas as tarefas pendentes, ordenadas por prioridade. |

## 2. Parâmetros de Requisição (Exemplo)

| Endpoint | Parâmetros de Entrada | Tipo |
| :--- | :--- | :--- |
| `AdotanteDao.updateStatus` | `adotanteId` | Int |
| | `novoStatus` | String (e.g., 'Aprovado') |
| `AnimalDao.update` | `animal` | Objeto Animal (Entity completo) |
| `TarefaDao.insert` | `tarefa` | Objeto TarefaVoluntario (Entity completo) |

## 3. Formatos de Resposta

O sistema utiliza o padrão assíncrono de Kotlin Coroutines (`suspend fun`).

| Tipo de Resposta | Conteúdo de Retorno | Status Simulado |
| :--- | :--- | :--- |
| **Sucesso (CRUD)** | `Unit` (Vazio) | Sucesso na Thread Main (via `Toast`) |
| **Consulta (Lista)** | `List<Entity>` | Retorna lista de objetos (e.g., `List<Animal>`) |
| **Consulta (Único)** | `Entity?` | Retorna o objeto (pode ser nulo) |

## 4. Autenticação e Autorização

* **Autenticação (Simulada):** Não há login/senha implementados nesta versão. A autorização é simulada pela navegação interna do `Dashboard` (apenas a equipe tem acesso aos módulos de gestão).
* **Autorização:** Todos os DAOs são acessíveis pela camada de lógica (Activities), que opera sob a permissão de armazenamento local do Android.

## 5. Exemplos de Chamadas

**Exemplo A: Atualizar Vacina (RF5)**
```kotlin
// Chamada da Activity para a Lógica
scope.launch {
    // 1. Atualizar o objeto Animal (Entity)
    // 2. Chamar o DAO para persistência
    database.animalDao().update(animalAtualizado) 
    
    // 3. Chamar o serviço de automação
    VaccineScheduler.scheduleVaccineReminder(applicationContext, animalAtualizado)
}