# Sistema de Gestão para Abrigos de Animais

## 1. Título e Descrição do Projeto

O **Sistema de Gestão para Abrigos de Animais** é uma aplicação móvel nativa (Android) projetada para centralizar e automatizar a administração de dados de resgates, saúde, alertas, gestão de voluntariado e gerenciamento de adoções em ONGs e abrigos. A solução visa otimizar as operações de campo e aumentar o foco no bem-estar animal.

## 2. Problema Abordado e Justificativa

**Problema Abordado:** A gestão de abrigos de animais frequentemente depende de registros em papel ou planilhas desorganizadas, o que leva a falhas críticas, como a perda de prazos de vacinação, dificuldades na gestão de tarefas da equipe e lentidão no processo de análise de adoção.

**Justificativa:** A solução digitaliza o fluxo de trabalho, garantindo a integridade dos dados de saúde e automatizando processos de notificação (via `AlarmManager`), liberando a equipe para o cuidado direto dos animais.

## 3. Objetivos do Sistema

* **Garantir o Bem-Estar Animal:** Implementar um sistema de alerta automático (via `AlarmManager`) para eventos médicos cruciais, como a próxima vacinação (365 dias).
* **Otimizar a Gestão de Adoção:** Criar um módulo de Gerenciamento de Solicitações para facilitar a análise e aprovação de candidatos pela equipe.
* **Aumentar a Eficiência Operacional:** Fornecer um módulo de Gestão de Tarefas para voluntários, permitindo a criação, priorização e marcação de conclusão de tarefas em campo.
* **Centralização de Dados:** Manter um banco de dados local (Room) único e seguro para todo o inventário de animais e perfis de adotantes.

## 4. Escopo do Projeto

O escopo do projeto (Etapa 2 - N708) foca exclusivamente no desenvolvimento de um **Aplicativo Móvel Nativo para Android (Kotlin)** contendo os seguintes módulos operacionais completos:

* **Módulo de Animais:** Cadastro e Módulo de Fotos.
* **Módulo de Saúde:** Controle de Vacinas e Alerta.
* **Módulo de Adoção:** Formulário de Pré-Adoção e Gerenciamento de Status.
* **Módulo de Voluntários:** Gestão de Criação e Acompanhamento de Tarefas.

## 5. Visão Geral da Arquitetura

A arquitetura do sistema segue o padrão **MVVM (Model-View-ViewModel)** com princípios de **Clean Architecture**, focando na separação de responsabilidades para escalabilidade e manutenção.

* **Camada View (UI):** Activities (Dashboard, MainActivity, DetalhesSaude, etc.) e Adapters. Responsável por renderizar a interface e capturar eventos.
* **Camada Lógica (ViewModel):** Uso de **Kotlin Coroutines** e classes **Scheduler/Receiver** para a lógica assíncrona, manipulação de dados e automação de alertas.
* **Camada de Dados (Model):** **Room Persistence Library** (SQLite) como banco de dados local. Contém as Entidades (`Animal`, `Adotante`, `TarefaVoluntario`) e as interfaces DAOs.

```

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

```

## 6. Tecnologias Propostas

Categoria | Tecnologia/Framework | Uso no Projeto |

**Linguagem** | Kotlin | Linguagem de desenvolvimento nativo Android. |

**Arquitetura** | MVVM | Padrão de design para separação da lógica de UI. |

**Persistência Local** | Room Persistence Library (SQLite) | Banco de dados local para dados de animais, adotantes e tarefas. |

**Assíncrono** | Kotlin Coroutines | Gerenciamento de operações de I/O (DAO). |

**Automação** | AlarmManager e BroadcastReceiver | Disparo de notificações de vacinação agendadas. |

**Serialização** | Gson | Conversor de tipos (`TypeConverter`) para armazenar listas de URIs de fotos. |

**UI/Design** | Android View System (XML) e Jetpack Libraries | Construção de interfaces e uso de componentes modernos.


## 7. Relação com o ODS 11: Cidades Melhores

O projeto contribui diretamente para o **ODS 11 (Cidades e Comunidades Sustentáveis)**. Ao fornecer uma solução tecnológica para ONGs, o sistema:

**Aumenta a Capacidade de Gestão:** Ajuda a organizar e controlar a população animal resgatada.

**Promove a Posse Responsável:** Facilita e formaliza o processo de adoção, o que está em linha com o objetivo de tornar assentamentos humanos mais inclusivos e sustentáveis.

A tecnologia é usada como ferramenta de inclusão social e sanitária, tornando a convivência em comunidade mais segura e humana, o que está em linha com o objetivo de tornar assentamentos humanos inclusivos e sustentáveis.

## 8. Cronograma para Etapa 2 (N708)

Fase | Foco Principal 

**Fase 1: Configuração e Base de Dados** | Configuração do Ambiente e Room. Implementação do Cadastro de Animais e Módulo de Fotos.

**Fase 2: Gestão de Adoção e Saúde** | Desenvolvimento do Formulário de Adoção e das Activities de Gerenciamento. Implementação do Seletor de Data e lógica do Alerta de Vacinas.

**Fase 3: Módulo de Voluntários e Automação** | Desenvolvimento completo do Módulo de Gestão de Tarefas. Integração final do AlarmManager e testes de notificação.

**Fase 4: Finalização e Testes** | Revisão de Código, Testes de Integração e Geração do APK Final. 

## 9. Integrantes da Equipe e Seus Papéis


Mariana Ferreira Dos Santos | **Relatorio e Teste.** | Relatório e Teste. |

Francisco Ivamar Silva Leite | **matrícula** | Relatório e Teste. |

Isaias Porto de Freitas | **matrícula** | Desenvolvimento. |

Eugenio Sancho Barroso Neto | **Desenvolvimento.** | Desenvolvimento.
