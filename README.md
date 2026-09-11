<p align="center">
  <a href="https://github.com/your-org/bispay-revora">
    <img src="https://img.shields.io/badge/BisPay-Revora-00C853?style=for-the-badge&logo=bispay&logoColor=white&labelColor=00C853" alt="BisPay Revora"/>
  </a>
</p>

<h1 align="center">BisPay Revora Payments Revenue</h1>

<p align="center">
  <strong>Revenue infrastructure for recurring payments, billing and business operations.</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Node.js-22.x-339933?style=flat-square&logo=node.js&logoColor=white" alt="Node.js 22.x"/>
  <img src="https://img.shields.io/badge/JavaScript-ESM-F7DF1E?style=flat-square&logo=javascript&logoColor=black" alt="JavaScript ESM"/>
  <img src="https://img.shields.io/badge/Vue.js-3.x-4FC08D?style=flat-square&logo=vue.js&logoColor=white" alt="Vue.js 3.x"/>
  <img src="https://img.shields.io/badge/Nuxt-4.x-00DC82?style=flat-square&logo=nuxt.js&logoColor=white" alt="Nuxt 4.x"/>
  <img src="https://img.shields.io/badge/MySQL-Relational-4479A1?style=flat-square&logo=mysql&logoColor=white" alt="MySQL"/>
  <img src="https://img.shields.io/badge/Redis-Cache--DC382D?style=flat-square&logo=redis&logoColor=white" alt="Redis"/>
  <img src="https://img.shields.io/badge/Docker-Containerized-2496ED?style=flat-square&logo=docker&logoColor=white" alt="Docker"/>
  <img src="https://img.shields.io/badge/Google-OAuth-4285F4?style=flat-square&logo=google&logoColor=white" alt="Google OAuth"/>
</p>

<p align="center">
  <a href="#-sobre-a-bispay-revora">Sobre</a> •
  <a href="#-arquitetura">Arquitetura</a> •
  <a href="#%EF%B8%8F-technology-stack">Stack</a> •
  <a href="#-authentication">Auth</a> •
  <a href="#-payment-providers">Providers</a> •
  <a href="#-design-principles">Princípios</a>
</p>

---

## 🏛️ Sobre a BisPay Revora

A **BisPay Revora Payments Revenue** é um subserviço da **BisPay Gateway**, desenvolvido para centralizar operações relacionadas a **receitas recorrentes, cobranças, pagamentos, clientes e gestão de vendas**.

A Revora foi projetada com uma arquitetura orientada a serviços, processamento assíncrono e integração com múltiplos provedores de pagamento, mantendo o domínio de negócio independente das implementações específicas de cada provider.

A **BisPay Revora** existe como uma camada especializada da infraestrutura da **BisPay Gateway**.

Enquanto a BisPay Gateway atua como infraestrutura de integração e processamento com diferentes meios e provedores de pagamento, a Revora trabalha em uma camada superior, concentrando a lógica necessária para que negócios possam:

- Cadastrar e gerenciar clientes
- Criar e administrar cobranças
- Trabalhar com receitas recorrentes
- Acompanhar pagamentos
- Disponibilizar diferentes métodos de pagamento
- Gerar e acompanhar transações
- Integrar diferentes providers
- Processar eventos e tarefas de forma assíncrona
- Enviar comunicações transacionais
- Controlar operações com segurança e idempotência

A separação permite que a Revora evolua seu domínio de negócio sem acoplar sua aplicação diretamente à implementação interna de cada provedor.

```mermaid
graph TD
    A[BisPay Gateway] --> B[Revora Payments Revenue]
    B --> C[Customers]
    B --> D[Billing]
    B --> E[Payments]
    C --> F[Payment Integrations]
    D --> F
    E --> F
    F --> G[Stripe]
    F --> H[Mercado Pago]
    F --> I[Stone]
    F --> J[...]

    style A fill:#00C853,color:#fff
    style B fill:#00C853,color:#fff
    style C fill:#4FC08D,color:#fff
    style D fill:#4FC08D,color:#fff
    style E fill:#4FC08D,color:#fff
    style F fill:#339933,color:#fff
    style G fill:#635bff,color:#fff
    style H fill:#009ee3,color:#fff
    style I fill:#2d2d2d,color:#fff
    style J fill:#8a8a8a,color:#fff
```

---

## 🧱 Architecture

A Revora utiliza uma arquitetura composta por aplicações especializadas, serviços de domínio, persistência, processamento assíncrono e integrações externas.

O objetivo é manter responsabilidades separadas sem criar abstrações desnecessárias.

```mermaid
graph TD
    Client --> Nuxt[Nuxt Vue.js]
    Nuxt -->|HTTP| API[Node.js API]
    API --> MySQL
    API --> Redis
    API --> Services
    Services --> ExternalProviders[External Providers]
    Redis --> Queues
    Queues --> Workers
    Workers --> AsyncProcessing[Async Processing]
    MySQL --> PersistentData[Persistent Data]

    style Client fill:#f5f5f5,color:#333
    style Nuxt fill:#00DC82,color:#fff
    style API fill:#339933,color:#fff
    style MySQL fill:#4479A1,color:#fff
    style Redis fill:#DC382D,color:#fff
    style Services fill:#4FC08D,color:#fff
    style ExternalProviders fill:#8a8a8a,color:#fff
    style Queues fill:#DC382D,color:#fff
    style Workers fill:#DC382D,color:#fff
    style AsyncProcessing fill:#DC382D,color:#fff
    style PersistentData fill:#4479A1,color:#fff
```

A aplicação é dividida principalmente entre:

| Componente | Responsabilidade |
|------------|------------------|
| **API/Core** | Regras de negócio e exposição HTTP |
| **Services** | Operações de domínio |
| **Workers** | Processamento assíncrono |
| **Queues** | Comunicação entre operações síncronas e assíncronas |
| **MySQL** | Persistência relacional |
| **Redis** | Cache, filas, controle temporário e mecanismos auxiliares |
| **Payment Providers** | Integrações externas |
| **Email Service** | Processamento e envio de comunicações transacionais |

---

## ⚙️ Technology Stack

### Backend

<p>
  <img src="https://img.shields.io/badge/Node.js-22.x-339933?style=for-the-badge&logo=node.js&logoColor=white" alt="Node.js"/>
  <img src="https://img.shields.io/badge/JavaScript-ESM-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" alt="JavaScript"/>
</p>

- Node.js 22.x
- JavaScript / ECMAScript Modules
- HTTP APIs
- Service-oriented application structure
- Workers & Queue processing
- Idempotent operations

### Frontend

<p>
  <img src="https://img.shields.io/badge/Vue.js-3.x-4FC08D?style=for-the-badge&logo=vue.js&logoColor=white" alt="Vue.js"/>
  <img src="https://img.shields.io/badge/Nuxt-4.x-00DC82?style=for-the-badge&logo=nuxt.js&logoColor=white" alt="Nuxt"/>
</p>

- Vue.js 3.x
- Nuxt 4.x
- Pinia (State Management)
- Axios (HTTP Client)
- Component-driven architecture
- Client-side state management

### Infrastructure

<p>
  <img src="https://img.shields.io/badge/Docker-Containerized-2496ED?style=for-the-badge&logo=docker&logoColor=white" alt="Docker"/>
  <img src="https://img.shields.io/badge/Redis-Cache--DC382D?style=for-the-badge&logo=redis&logoColor=white" alt="Redis"/>
  <img src="https://img.shields.io/badge/MySQL-Relational-4479A1?style=for-the-badge&logo=mysql&logoColor=white" alt="MySQL"/>
</p>

- Docker (Containerized environments)
- Redis (Cache & Queues)
- MySQL (Relational Database)
- Persistent storage
- Environment-based configuration

### Authentication

<p>
  <img src="https://img.shields.io/badge/Google-OAuth-4285F4?style=for-the-badge&logo=google&logoColor=white" alt="Google OAuth"/>
</p>

- Password authentication
- Google OAuth integration
- Provider-based identity
- Token validation
- Email verification
- Password recovery & reset
- Session/token based API authentication

---

## 💳 Payment Providers

A Revora foi construída para trabalhar com múltiplos provedores de pagamento.

```mermaid
graph TD
    A[Revora] --> B[Payment Service]
    B --> C[Payment Integration]
    C --> D[Stripe]
    C --> E[Mercado Pago]
    C --> F[Stone]
    D --> G[Provider]
    E --> G
    F --> G

    style A fill:#00C853,color:#fff
    style B fill:#339933,color:#fff
    style C fill:#4FC08D,color:#fff
    style D fill:#635bff,color:#fff
    style E fill:#009ee3,color:#fff
    style F fill:#2d2d2d,color:#fff
    style G fill:#8a8a8a,color:#fff
```

| Provider | Status |
|----------|--------|
| Stripe | Implementado |
| Mercado Pago | Implementado |
| Nubank | Planejado |
| Stone | Implementado |

Cada integração é tratada como uma implementação específica da infraestrutura de pagamentos, enquanto a Revora mantém seus próprios conceitos de:

- **Payment** — Pagamento
- **Transaction** — Transação
- **Customer** — Cliente
- **Billing** — Cobrança
- **Status** — Estado da operação
- **Idempotency** — Controle de duplicidade
- **Provider Reference** — Referência externa

---

## 🔐 Authentication

### Password

Fluxo completo de autenticação por senha:

```mermaid
graph TD
    A[Criação de Conta] --> B[Validação de Senha]
    B --> C[Confirmação de E-mail]
    C --> D[Login]
    D --> E[Recuperação de Senha]
    D --> F[Controle de Status]
    E --> G[Código Temporário]
    G --> H[Alteração de Senha]

    style A fill:#00C853,color:#fff
    style B fill:#339933,color:#fff
    style C fill:#4FC08D,color:#fff
    style D fill:#4479A1,color:#fff
    style E fill:#DC382D,color:#fff
    style F fill:#DC382D,color:#fff
    style G fill:#F7DF1E,color:#333
    style H fill:#339933,color:#fff
```

### Google OAuth

```mermaid
graph TD
    A[Google] --> B[Authentication]
    B --> C[Provider Identity]
    C --> D{Existing provider?}
    D -->|Yes| E[Login]
    D -->|No| F{Existing user?}
    F -->|Yes| G[Link provider]
    F -->|No| H[Create account]

    style A fill:#4285F4,color:#fff
    style B fill:#339933,color:#fff
    style C fill:#4FC08D,color:#fff
    style D fill:#F7DF1E,color:#333
    style E fill:#00C853,color:#fff
    style F fill:#F7DF1E,color:#333
    style G fill:#4FC08D,color:#fff
    style H fill:#00C853,color:#fff
```

A identidade do provider é separada do e-mail como mecanismo de identificação, evitando depender do endereço de e-mail como identificador permanente da conta externa.

---

## 📬 Email Infrastructure

O envio de e-mails é desacoplado da requisição HTTP principal.

```mermaid
graph TD
    A[HTTP Request] --> B[Business Service]
    B --> C[Email Job]
    C --> D[Redis Queue]
    D --> E[Email Worker]
    E --> F[SMTP / Email Provider]
    F --> G[Recipient]

    style A fill:#f5f5f5,color:#333
    style B fill:#339933,color:#fff
    style C fill:#4FC08D,color:#fff
    style D fill:#DC382D,color:#fff
    style E fill:#DC382D,color:#fff
    style F fill:#8a8a8a,color:#fff
    style G fill:#00C853,color:#fff
```

Isso evita que operações como criação de conta, recuperação de senha, confirmação de e-mail e notificações de pagamento fiquem bloqueadas aguardando o envio do e-mail.

---

## ⚡ Redis

O Redis é utilizado como infraestrutura de alta velocidade para informações temporárias e processamento assíncrono.

| Uso | Descrição |
|-----|-----------|
| Filas | Comunicação entre processos |
| Jobs | Tarefas assíncronas |
| Dados temporários | Informações de curta duração |
| Códigos de recuperação | Tokens temporários |
| Controle de expiração | TTL e validade |
| Cache | Aceleração de consultas |
| Concorrência | Locks e controles |

```mermaid
graph TD
    A[Application] --> B[MySQL]
    A --> C[Redis]
    B --> D[Durable Data]
    C --> E[Temporary Data]

    style A fill:#f5f5f5,color:#333
    style B fill:#4479A1,color:#fff
    style C fill:#DC382D,color:#fff
    style D fill:#4479A1,color:#fff
    style E fill:#DC382D,color:#fff
```

---

## 📨 Queues & Workers

Operações que não precisam ser concluídas dentro do ciclo imediato da requisição podem ser processadas através de filas.

```mermaid
graph TD
    A[Producer] --> B[Queue]
    B --> C[Job 1]
    B --> D[Job 2]
    B --> E[Job 3]
    B --> F[Job 4]
    C --> G[Worker]
    D --> G
    E --> G
    F --> G
    G --> H[Processing]

    style A fill:#339933,color:#fff
    style B fill:#DC382D,color:#fff
    style C fill:#DC382D,color:#fff
    style D fill:#DC382D,color:#fff
    style E fill:#DC382D,color:#fff
    style F fill:#DC382D,color:#fff
    style G fill:#DC382D,color:#fff
    style H fill:#4FC08D,color:#fff
```

Exemplos de uso:

- Envio de e-mail
- Processamento de notificações
- Tarefas de integração
- Processamento de eventos
- Operações externas que podem ser reprocessadas

---

## 🔁 Idempotency

Operações financeiras precisam considerar que uma mesma solicitação pode ser recebida mais de uma vez.

```mermaid
graph TD
    A[Request] --> B[Idempotency Key]
    B --> C{Already processed?}
    C -->|Yes| D[Return existing result]
    C -->|No| E[Process]
    E --> F[Persist]
    F --> G[Store result]

    style A fill:#f5f5f5,color:#333
    style B fill:#F7DF1E,color:#333
    style C fill:#F7DF1E,color:#333
    style D fill:#00C853,color:#fff
    style E fill:#339933,color:#fff
    style F fill:#4479A1,color:#fff
    style G fill:#4FC08D,color:#fff
```

A idempotência reduz o risco de duplicidade quando existem retries, timeouts, falhas de rede, reprocessamento de jobs e callbacks duplicados.

---

## 🔄 Retry & Failure Handling

```mermaid
graph TD
    A[Job] --> B[Processing]
    B --> C{Success?}
    C -->|Yes| D[Complete]
    C -->|No| E[Retry]
    E --> F[Idempotency]
    F --> G[Safe]

    style A fill:#f5f5f5,color:#333
    style B fill:#339933,color:#fff
    style C fill:#F7DF1E,color:#333
    style D fill:#00C853,color:#fff
    style E fill:#DC382D,color:#fff
    style F fill:#F7DF1E,color:#333
    style G fill:#00C853,color:#fff
```

**Retry e idempotência trabalham juntos.** Um retry não deve transformar uma operação financeira em uma segunda operação financeira.

---

## 🗄️ MySQL

O MySQL é responsável pela persistência dos dados permanentes da aplicação.

| Entidade | Descrição |
|----------|-----------|
| Users | Usuários do sistema |
| Authentication Providers | Provedores de identidade |
| Accounts | Contas de negócio |
| Account Members | Membros das contas |
| Customers | Clientes |
| Quotes / Quote Items | Orçamentos |
| Payments | Pagamentos |
| Payment Integrations | Integrações de pagamento |
| Payment Transactions | Transações |
| Credentials | Credenciais |
| Email Verification | Verificação de e-mail |
| User Profiles | Perfis de usuário |

A aplicação utiliza relacionamentos e constraints do banco para preservar a integridade dos dados. Identificadores internos utilizam UUIDs binários, enquanto chaves técnicas e índices são estruturados para favorecer operações eficientes.

---

## 👤 User Profiles & Media

Dados de autenticação e dados de apresentação são separados:

```mermaid
graph TD
    A[users] --> B[Authentication / Identity]
    C[user_profiles] --> D[Presentation / Profile]
    D --> E[Avatar]

    style A fill:#4479A1,color:#fff
    style B fill:#339933,color:#fff
    style C fill:#4479A1,color:#fff
    style D fill:#4FC08D,color:#fff
    style E fill:#8a8a8a,color:#fff
```

Fluxo de processamento de imagem:

```mermaid
graph TD
    A[Image] --> B[Upload / External Image]
    B --> C[Validation]
    C --> D[Sharp]
    D --> E[Resize]
    D --> F[WebP]
    E --> G[Hash]
    F --> G
    G --> H[Storage]

    style A fill:#f5f5f5,color:#333
    style B fill:#f5f5f5,color:#333
    style C fill:#F7DF1E,color:#333
    style D fill:#DC382D,color:#fff
    style E fill:#DC382D,color:#fff
    style F fill:#DC382D,color:#fff
    style G fill:#4FC08D,color:#fff
    style H fill:#4479A1,color:#fff
```

---

## 🐳 Docker

O ambiente de infraestrutura pode ser executado de forma containerizada utilizando Docker.

```mermaid
graph TD
    A[Docker Environment] --> B[API]
    A --> C[Worker]
    A --> D[Redis]
    A --> E[MySQL]

    style A fill:#2496ED,color:#fff
    style B fill:#339933,color:#fff
    style C fill:#DC382D,color:#fff
    style D fill:#DC382D,color:#fff
    style E fill:#4479A1,color:#fff
```

A aplicação utiliza variáveis de ambiente para separar configurações de desenvolvimento, homologação e produção.

---

## 🧠 Design Principles

| Princípio | Descrição |
|-----------|-----------|
| **Separation of Concerns** | Cada camada possui uma responsabilidade específica |
| **Idempotency First** | Operações que podem ser repetidas devem considerar reexecução |
| **Async When Necessary** | Operações secundárias não devem bloquear operações críticas |
| **Database Integrity** | A consistência deve ser protegida pela aplicação e pelo banco |
| **Provider Independence** | O domínio não deve depender de um único gateway |
| **Explicit Architecture** | Código explícito e previsível em vez de abstrações excessivas |
| **Fail Safely** | Falhas externas devem ser tratadas sem comprometer o estado interno |

---

## 🚀 Project Status

A **BisPay Revora Payments Revenue** está em desenvolvimento.

Foco inicial:

- Gestão de clientes
- Cobranças e pagamentos
- Receitas recorrentes
- Autenticação
- Integração com providers
- Infraestrutura de pagamentos
- Processamento assíncrono
- Comunicação transacional

Novos módulos serão adicionados conforme o domínio da plataforma evoluir.

---

## 🗺️ Roadmap

```mermaid
graph TD
    A[Authentication] --> B[Accounts & Users]
    B --> C[Customers]
    C --> D[Quotes / Billing]
    D --> E[Payments]
    E --> F[Payment Providers]
    F --> G[Recurring Revenue]
    G --> H[Automation & Infrastructure]

    style A fill:#00C853,color:#fff
    style B fill:#339933,color:#fff
    style C fill:#4FC08D,color:#fff
    style D fill:#4479A1,color:#fff
    style E fill:#DC382D,color:#fff
    style F fill:#8a8a8a,color:#fff
    style G fill:#F7DF1E,color:#333
    style H fill:#2496ED,color:#fff
```

A arquitetura foi concebida para permitir que novos módulos sejam incorporados sem alterar o núcleo dos módulos existentes.

---

## 📁 Repository Philosophy

A Revora não é apenas uma interface sobre gateways de pagamento. Ela representa uma camada de negócio responsável por organizar o ciclo de receita de uma empresa, utilizando a infraestrutura da **BisPay Gateway** para conectar operações financeiras a diferentes providers.

```mermaid
graph TD
    A[Business] --> B[Revora]
    B --> C[BisPay Gateway]
    C --> D[Payment Providers]
    C --> E[Financial Infrastructure]
    C --> F[External Integrations]

    style A fill:#f5f5f5,color:#333
    style B fill:#00C853,color:#fff
    style C fill:#339933,color:#fff
    style D fill:#4FC08D,color:#fff
    style E fill:#4479A1,color:#fff
    style F fill:#8a8a8a,color:#fff
```

---

<p align="center">
  <strong>BisPay Revora</strong><br/>
  <em>Payments. Revenue. Infrastructure.</em>
</p>

<p align="center">
  Built with<br/>
  <img src="https://img.shields.io/badge/Node.js-22.x-339933?style=flat-square&logo=node.js&logoColor=white" alt="Node.js"/>
  <img src="https://img.shields.io/badge/Vue.js-3.x-4FC08D?style=flat-square&logo=vue.js&logoColor=white" alt="Vue.js"/>
  <img src="https://img.shields.io/badge/Nuxt-4.x-00DC82?style=flat-square&logo=nuxt.js&logoColor=white" alt="Nuxt"/>
  <img src="https://img.shields.io/badge/MySQL-8.x-4479A1?style=flat-square&logo=mysql&logoColor=white" alt="MySQL"/>
  <img src="https://img.shields.io/badge/Redis-7.x-DC382D?style=flat-square&logo=redis&logoColor=white" alt="Redis"/>
  <img src="https://img.shields.io/badge/Docker-24.x-2496ED?style=flat-square&logo=docker&logoColor=white" alt="Docker"/>
  <img src="https://img.shields.io/badge/Google-OAuth-4285F4?style=flat-square&logo=google&logoColor=white" alt="Google"/>
</p>
