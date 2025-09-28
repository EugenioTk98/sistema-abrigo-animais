# Sistema de Gestão para Abrigos de Animais

## 1. Título e Descrição do Projeto

O **Sistema de Gestão para Abrigos de Animais** é uma solução multiplataforma (foco principal no Android App) projetada para centralizar e automatizar a administração de dados de resgates, saúde, alertas, gestão de voluntariado e gerenciamento de adoções em ONGs e abrigos. A solução visa otimizar as operações de campo e aumentar o foco no bem-estar animal.

## 2. Problema Abordado e Justificativa

**Problema Abordado:** A gestão de abrigos de animais frequentemente depende de registros em papel ou planilhas desorganizadas, o que leva a falhas críticas, como a perda de prazos de vacinação, dificuldades na gestão de tarefas da equipe e lentidão no processo de análise de adoção.

**Justificativa:** A solução digitaliza o fluxo de trabalho, garantindo a integridade dos dados de saúde e automatizando processos de notificação (via `AlarmManager`), liberando a equipe para o cuidado direto dos animais.

## 3. Objetivos do Sistema

* **Garantir o Bem-Estar Animal (RF5):** Implementar um sistema de alerta automático (via `AlarmManager`) para eventos médicos cruciais, como a próxima vacinação (365 dias).
* **Otimizar a Gestão de Adoção (RF11):** Criar um módulo de Gerenciamento de Solicitações para facilitar a análise e aprovação de candidatos pela equipe.
* **Aumentar a Eficiência Operacional (RF19):** Fornecer um módulo de Gestão de Tarefas para voluntários, permitindo a criação, priorização e marcação de conclusão de tarefas em campo.
* **Centralização de Dados:** Manter um banco de dados central (PostgreSQL) para todo o inventário de animais e perfis de adotantes.

## 4. Escopo do Projeto

O escopo do projeto (Etapa 2 - N708) foca no desenvolvimento dos seguintes módulos operacionais completos:

* **Módulo de Animais:** Cadastro (RF1) e Módulo de Fotos (RF3).
* **Módulo de Saúde:** Controle de Vacinas e Alerta (RF5).
* **Módulo de Adoção:** Formulário de Pré-Adoção e Gerenciamento de Status (RF11).
* **Módulo de Voluntários:** Gestão de Criação e Acompanhamento de Tarefas (RF19).

## 5. Visão Geral da Arquitetura

A arquitetura do sistema segue o padrão **MVVM (Model-View-ViewModel)** com princípios de **Clean Architecture**, utilizando uma arquitetura de **Três Camadas** (Cliente, API e Dados).

* **Camada Cliente (Mobile):** Kotlin/MVVM para interface e lógica local.
* **Camada de Aplicação:** Node.js/Express.js (API RESTful) para a lógica de negócios central.

```markdown
┌────────────────────────────────────────────────────────┐
│               CAMADA UI / VISUAL (VIEW)                │
│                                                        │
│  (Activities: Dashboard, MainActivity, DetalhesSaude)  │
└────────────────────────────────────────────────────────┘
                 │
                 │ 1. Evento do Usuário (Clique em "Salvar")
                 │
                 ▼
┌────────────────────────────────────────────────────────┐
│       CAMADA LÓGICA / DADOS (VIEWMODEL - Coroutines)   │
│                                                        │
│  (Coroutines, VaccineScheduler, Business Logic)        │
│  - Executa validações e cria Entidades.                │
├────────────────────┬───────────────────────────────────┤
│ 2a. CHAMA PERSISTÊNCIA│ 2b. CHAMA AUTOMAÇÃO (RF5)         │
│ (DAO.insert/update)│ (VaccineScheduler.schedule())     │
└────────────────────▼───────────────────────────────────┐

      │                                                 │
      │                                                 │
┌─────▼─────────────────────────┐                   ┌─────▼───────────────────────────┐
│     CAMADA DE DADOS (ROOM)    │                   │ SISTEMA ANDROID (AlarmManager)  │
│                               │                   │                                 │
│  (AppDatabase, DAOs, Entidades)│                   │ - Agenda o disparo do alarme    │
│  - Salva dados com sucesso.   │                   │   para a data agendada.         │
└───────────────────────────────┘                   └─────────┬───────────────────────┘
                                                              │ 3. Disparo na Data/Hora Certa
                                                              │
                                                              ▼
                                                 ┌───────────────────────────────┐
                                                 │   BROADCAST RECEIVER (RF5)    │
                                                 │ (VaccineReminderReceiver.kt)  │
                                                 │ - Componente de Escuta.       │
                                                 └─────────┬───────────────────────┘
                                                           │ 4. Exibe Notificação ao Usuário
                                                           ▼
                                                   (ALERTA DE VACINA)


## 6.Tecnologias Propostas

| Categoria | Tecnologia/Framework | Uso no Projeto |
| **Frontend Móvel** | Kotlin, Room Persistence Library | Interface de gestão de campo e cache local. |
| **Frontend Web** | React.js | Interface de gestão geral e consultas. |
| **Backend/API** | Node.js com Express.js | Servidor de aplicação central (Lógica de Negócios e API RESTful). |
| **Banco de Dados** | PostgreSQL | Base de dados relacional robusta e centralizada. |
| **Automação (App)** | AlarmManager e BroadcastReceiver | Disparo de notificações de vacinação agendadas (RF5). |
| **Serialização** | Gson | Conversor de tipos (`TypeConverter`) para armazenar listas de URIs de fotos. |

## 7.Relação com o ODS 11: Cidades Melhores

O projeto contribui diretamente para o **ODS 11 (Cidades e Comunidades Sustentáveis)**. Ao fornecer uma solução tecnológica para ONGs, o sistema:

1.  **Aumenta a Capacidade de Gestão:** Ajuda a organizar e controlar a população animal resgatada.
2.  **Promove a Posse Responsável:** Facilita e formaliza o processo de adoção, o que está em linha com o objetivo de tornar assentamentos humanos mais inclusivos e sustentáveis.

A tecnologia é usada como ferramenta de inclusão social e sanitária, tornando a convivência em comunidade mais segura e humana, o que está em linha com o objetivo de tornar assentamentos humanos inclusivos e sustentáveis.

## 8.Cronograma para Etapa 2 (N708)

| Fase | Duração | Foco Principal |

| **Fase 1: Configuração e Base de Dados** | 3 Semanas | Modelagem final do PostgreSQL. Configuração do Node.js/Prisma e Endpoints base. |
| **Fase 2: Frontend e Gestão de Animais** | 4 Semanas | Desenvolvimento do Frontend (Web/Mobile) para Cadastro e Listagem. Implementação da Persistência de Dados. |
| **Fase 3: Módulos de Adoção e Voluntários** | 3 Semanas | Desenvolvimento do Módulo de Adoção e Tarefas para ambas as plataformas. Implementação da lógica de Alerta de Saúde (Mobile). |
| **Fase 4: Finalização e Testes** | 2 Semanas | Testes de Integração (API $\leftrightarrow$ Frontends). Revisão de Código e Preparação para Deploy. |

## 9.Integrantes da Equipe e Seus Papéis

| Integrante | Matrícula | Papel no Projeto | Responsabilidades |


| Mariana Ferreira Dos Santos | 2326630 | **Garantia de Qualidade (QA)** | Elaboração do Relatório e Teste de Funcionalidades. |
| Francisco Ivamar Silva Leite | [matrícula] | **Gerenciamento de Requisitos** | Documentação e Gestão de Casos de Teste. |
| Isaias Porto de Freitas | 2326193 | **Arquiteto Backend/API** | Desenvolvimento do Frontend Mobile (Kotlin) e Frontend Web (React.js). |
| Eugenio Sancho Barroso Neto | 2323811 | **Arquiteto Backend/API** | Definição da Arquitetura, Modelagem de Dados (PostgreSQL) e Desenvolvimento da API. |

**Fase 4: Finalização e Testes** | Revisão de Código, Testes de Integração e Geração do APK Final. 

## 9. Integrantes da Equipe e Seus Papéis


Mariana Ferreira Dos Santos | **2326630** | Relatório e Teste. |

Francisco Ivamar Silva Leite | **matrícula** | Relatório e Teste. |

Isaias Porto de Freitas | **2326193** | Desenvolvimento. |

Eugenio Sancho Barroso Neto | **2323811** | Desenvolvimento.

