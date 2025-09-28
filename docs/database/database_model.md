## Arquivo: `docs/database/database_model.md` (Completo)

```markdown
# 4. Modelo de Dados

O modelo de dados é relacional, projetado para o PostgreSQL (Backend central).

## 1. Descrição das Entidades e Relacionamentos

| Entidade | Propriedades Chave | Relacionamento Chave |
| :--- | :--- | :--- |
| **Animal** | ID, Nome, Raça, Status, Fotos (URIs) | N/A |
| **Adotante** | ID, Nome, Endereço, TipoMoradia, StatusAprovacao | N/A |
| **TarefaVoluntario** | ID, Título, Descrição, Prioridade, Concluida | N/A |
| **HistoricoSaude** (Implícito) | AnimalID (FK), DataVacina, TipoVacina | **Animal** 1:N **HistoricoSaude** (Um animal pode ter muitos registros de saúde) |

## 2. Diagrama ER Simplificado

Abaixo está a representação simplificada do Diagrama Entidade-Relacionamento, focando nas entidades principais e no relacionamento entre Animais e seu Histórico de Saúde:


┌──────────────────────────────────────┐
│             ANIMAL (PK)              │
│ ──────────────────────────────────── │
│ \*animal\_id (PK)                      │
│ nome                                 │
│ raça                                 │
│ status                               │
└──────────────────────────────────────┘
|
| 1:N (Um Animal tem muitos registros de Saúde)
▼
┌──────────────────────────────────────┐
│        HISTORICOSAÚDE (N)            │
│ ──────────────────────────────────── │
│ \*saude\_id (PK)                       │
│  animal\_id (FK) \<────────────────────│
│  data\_vacina                         │
│  tipo\_vacina                         │
└──────────────────────────────────────┘

┌──────────────────────────────────────┐
│          ADOTANTE (PK)               │
│ ──────────────────────────────────── │
│ \*adotante\_id (PK)                    │
│ nome\_completo                        │
│ telefone                             │
│ status\_aprovacao                     │
└──────────────────────────────────────┘

┌──────────────────────────────────────┐
│      TAREFAVOLUNTARIO (PK)           │
│ ──────────────────────────────────── │
│ \*tarefa\_id (PK)                      │
│ titulo                               │
│ prioridade                           │
│ concluida                            │
└──────────────────────────────────────┘



## 3. Dicionário de Dados

### Entidade: `Animal`

| Campo | Tipo SQL (PostgreSQL) | Regras | Descrição |

| `animal_id` (PK) | SERIAL / UUID | NOT NULL, PK | Identificador único do animal. |
| `nome` | VARCHAR(100) | NOT NULL | Nome de registro do animal. |
| `raca` | VARCHAR(100) | NOT NULL | Raça do animal. |
| `status` | ENUM | NOT NULL | 'Em Adoção', 'Em Tratamento', 'Adotado'. |
| `is_castrado` | BOOLEAN | NOT NULL | Status de castração. |
| `ultima_vacina_data` | DATE | NULLABLE | Data da última vacina principal registrada. |
| `proxima_vacina_alerta` | DATE | NULLABLE | Data calculada para alerta (usada no App/API). |
| `fotos_uris` | TEXT[] / JSONB | NOT NULL | Lista de caminhos/URIs das fotos. |
| `observacoes` | TEXT | NULLABLE | Notas de saúde/temperamento. |

### Entidade: `Adotante`

| Campo | Tipo SQL (PostgreSQL) | Regras | Descrição |

| `adotante_id` (PK) | SERIAL / UUID | NOT NULL, PK | ID da solicitação/perfil. |
| `nome_completo` | VARCHAR(255) | NOT NULL | Nome do candidato. |
| `telefone` | VARCHAR(20) | NOT NULL | Contato. |
| `tipo_moradia` | VARCHAR(50) | NOT NULL | Tipo de moradia (Casa, Apartamento). |
| `status_aprovacao` | ENUM | NOT NULL | 'Pendente', 'Aprovado', 'Rejeitado'. |

### Entidade: `TarefaVoluntario`

| Campo | Tipo SQL (PostgreSQL) | Regras | Descrição |

| `tarefa_id` (PK) | SERIAL / UUID | NOT NULL, PK | ID da tarefa. |
| `titulo` | VARCHAR(150) | NOT NULL | Título da tarefa. |
| `prioridade` | ENUM | NOT NULL | 'Baixa', 'Média', 'Alta', 'URGENTE'. |
| `concluida` | BOOLEAN | NOT NULL | Status de conclusão (default FALSE). |
