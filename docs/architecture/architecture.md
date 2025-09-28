# Arquitetura do Sistema Móvel (Android)

## 1. Descrição da Arquitetura

O sistema é baseado em uma arquitetura modular que segue os princípios do **Clean Architecture**, implementada utilizando o padrão **MVVM (Model-View-ViewModel)**. O foco é na separação de responsabilidades para garantir escalabilidade, testabilidade e manutenção do código.

## 2. Componentes do Sistema

| Componente | Conteúdo | Responsabilidade |
| :--- | :--- | :--- |
| **Camada UI / View** | Activities (MainActivity, Dashboard, DetalhesSaude), Adapters (AnimalAdapter, TarefaAdapter) | Exibir dados e capturar eventos do usuário. |
| **Camada Lógica (ViewModel)** | Coroutines, Schedulers (VaccineScheduler) | Gerenciar o fluxo de dados, lógica de negócios e comunicação com a Camada de Dados. |
| **Camada de Dados (Model)** | Entidades (Animal, Adotante, Tarefa), DAOs (AnimalDao, AdotanteDao, TarefaDao), AppDatabase | Abstração dos dados, acesso ao Room (SQLite) e definição do esquema. |
| **Serviços de Automação** | `AlarmManager`, `BroadcastReceiver` | Agendamento e disparo de notificações fora do ciclo de vida da aplicação. |

## 3. Padrões Arquiteturais Utilizados

* **MVVM (Model-View-ViewModel):** Usado para separar a lógica de negócios da interface. A View observa o estado (dados) e o ViewModel gerencia a lógica.
* **Repository Pattern (Implícito):** Os DAOs agem como um repositório local, fornecendo uma interface limpa para o acesso aos dados, isolando a lógica de negócios do detalhe da base de dados (SQLite/Room).
* **Injeção de Dependência (Manual):** As dependências (como a instância do `AppDatabase`) são inicializadas no `ApplicationContext` e passadas explicitamente para as Activities.

## 4. Decisões Técnicas e Justificativas

| Decisão | Justificativa |
| :--- | :--- |
| **Kotlin Coroutines** | Essencial para operações assíncronas de I/O (Banco de Dados), garantindo que a UI não seja bloqueada (RNF-D1). |
| **Room/SQLite** | Garante persistência robusta, tipagem segura (via Entidades) e consulta eficiente para um aplicativo móvel que opera offline (RNF-P1). |
| **AlarmManager/Receiver** | Único método confiável para agendar eventos de alarme (vacinas) que disparam notificações mesmo se o aplicativo estiver fechado (RF5). |
| **Uso de TypeConverter (Gson)** | Necessário para contornar a limitação do Room em armazenar tipos complexos como `List<String>` (URIs de fotos). |