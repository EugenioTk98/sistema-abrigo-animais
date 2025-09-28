# Arquitetura e Decisões Técnicas

## 1. Descrição da Arquitetura

O sistema utiliza uma arquitetura **3-Tier (Três Camadas)** para garantir a separação clara de responsabilidades, escalabilidade e facilidade de manutenção.

1.  **Camada de Apresentação (Frontend):** Interfaces para o usuário.
2.  **Camada de Aplicação (Backend/API):** Lógica de negócios centralizada.
3.  **Camada de Dados:** Armazenamento persistente.

## 2. Componentes e Padrões

| Componente | Tecnologia | Padrão/Função |
| :--- | :--- | :--- |
| **Frontend Mobile** | Kotlin / Android Views | Padrão MVVM (Model-View-ViewModel) para gestão local de UI e chamadas de API. |
| **Frontend Web** | React.js | Single Page Application (SPA) para interface de gestão. |
| **Backend** | Node.js / Express.js | API RESTful (Interface principal de comunicação). |
| **Persistência** | PostgreSQL | Base de dados central e relacional (RNF-P1). |
| **Automação** | AlarmManager / BroadcastReceiver | Serviço local do Android para notificações (RF5). |
| **ORM** | Prisma / Knex (A ser definido) | Mapeamento Objeto-Relacional para interagir com PostgreSQL. |

## 3. Decisões Técnicas e Justificativas

| Decisão | Justificativa |
| :--- | :--- |
| **Node.js (Backend)** | Alta performance, escalabilidade e linguagem assíncrona (JavaScript) ideal para operações de I/O em APIs REST. |
| **PostgreSQL** | Flexibilidade e robustez para modelagem relacional complexa (inventário, saúde) e transações seguras. |
| **RESTful API** | Padrão leve e universal para desacoplar o Frontend do Backend, permitindo que tanto o App Android quanto o Web usem o mesmo conjunto de Endpoints. |
| **Kotlin Coroutines (Mobile)** | Essencial para operações assíncronas no App, garantindo que a UI não seja bloqueada durante chamadas de API ou acesso local (RNF-D1). |
| **MVVM (Mobile)** | Promove a testabilidade e separa a View (XML) da lógica de manipulação de dados. |

## 4. Diagrama de Arquitetura

*O diagrama em blocos é o mesmo da seção 5 do README, ilustrando as três camadas.*
