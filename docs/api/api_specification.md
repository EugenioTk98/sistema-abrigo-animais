# Especificação de APIs Locais (DAOs)

O sistema utiliza o **Room Persistence Library** (SQLite) como sua API de dados. As interfaces DAO (Data Access Object) definem os contratos de dados para acesso, consulta e modificação das entidades.

## 1. Endpoints Previstos (Métodos DAO)

### 1.1. AnimalDao (Gestão e Saúde)

| Endpoint (Método) | Função | Parâmetros de Entrada | Formato de Resposta |
| :--- | :--- | :--- | :--- |
| `insert(animal)` | Registra um novo animal no inventário. | `animal: Animal` | `Unit` (Sucesso) |
| `update(animal)` | Atualiza os dados de saúde e vacina de um animal. | `animal: Animal` | `Unit` (Sucesso) |
| `getById(animalId)` | Busca um animal específico para visualização de detalhes. | `animalId: Int` | `Animal?` |
| `getAll()` | Lista todos os animais cadastrados. | Nenhum | `List<Animal>` |
| `getProximasVacinas()` | **Consulta de Alerta (RF5):** Busca animais com vacina próxima (dentro de 30 dias). | Nenhum | `List<Animal>` |

### 1.2. AdotanteDao (Gestão de Adoção)

| Endpoint (Método) | Função | Parâmetros de Entrada | Formato de Resposta |
| :--- | :--- | :--- | :--- |
| `insert(adotante)` | Registra uma nova candidatura de pré-adoção. | `adotante: Adotante` | `Unit` (Sucesso) |
| `getById(adotanteId)` | Busca uma candidatura específica para análise. | `adotanteId: Int` | `Adotante?` |
| `updateStatus(id, novoStatus)` | **RF11:** Atualiza o status da candidatura. | `adotanteId: Int`, `novoStatus: String` | `Unit` (Sucesso) |
| `getAllPendentes()` | Lista todas as candidaturas com status 'Pendente'. | Nenhum | `List<Adotante>` |

## 2. Parâmetros de Requisição

As requisições utilizam objetos de Entidade completos para `insert` e `update`, e chaves primárias (`Int`) para consultas específicas.

| Endpoint | Parâmetros de Entrada | Tipo |
| :--- | :--- | :--- |
| `AdotanteDao.updateStatus` | `adotanteId` | Int |
| | `novoStatus` | String ('Aprovado', 'Rejeitado') |
| `AnimalDao.update` | `animal` | Objeto Animal (Entidade completa) |

## 3. Formatos de Resposta

O sistema utiliza o padrão assíncrono de Kotlin Coroutines (`suspend fun`).

| Status HTTP (Simulado) | Endpoint | Formato de Resposta |
| :--- | :--- | :--- |
| **200 OK** | GET / Consulta | `JSON: [{animal_id: 1, nome: "Rex"}, ...]` |
| **201 Created** | POST / Criação | `Unit` (Sucesso sem retorno de valor) |
| **400 Bad Request** | Validações de Formulário | `Unit` (Falha assíncrona tratada com `try/catch`) |
| **404 Not Found** | GET / Item único | `null` (Objeto não encontrado) |

## 4. Autenticação e Autorização

* **Autenticação (Modelo Multiplataforma):** A API central utilizará **JSON Web Tokens (JWT)**. Os clients (App/Web) devem enviar o token no cabeçalho `Authorization: Bearer <token>`.
* **Autorização:** Apenas usuários autenticados (Gestores/Voluntários) terão acesso a todas as chamadas de **POST** e **UPDATE**. O formulário de adoção (`AdotanteDao.insert`) é simulado como um endpoint público.

## 5. Exemplos de Chamadas e Respostas

**Exemplo A: Atualizar Status de Adoção (RF12)**
```kotlin
// Chamada da Camada Lógica (DetalhesAdocaoActivity)
scope.launch {
    // Atualiza o status no banco de dados local
    database.adotanteDao().updateStatus(idDaSolicitacao, "Aprovado")
}
