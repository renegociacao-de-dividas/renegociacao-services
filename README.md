# 💼 Plataforma de Renegociação de Dívidas (Microsserviços com Spring Boot, Kafka e AWS)

## 🎯 Desafio

Simular uma plataforma moderna para renegociação de dívidas, como as oferecidas por bancos digitais, permitindo que o próprio cliente inicie, simule e acompanhe o processo de renegociação por meio de canais digitais (app ou web).

---

## 🛠️ Tecnologias Utilizadas

| Tecnologia       | Justificativa                                                                 |
|------------------|--------------------------------------------------------------------------------|
| **Java 17 + Spring Boot 3** | Versão estável com suporte a recursos modernos (records, pattern matching) e excelente para criar REST APIs robustas com Spring Boot. |
| **Apache Kafka** | Permite comunicação assíncrona, desacoplamento entre microsserviços e orquestração baseada em eventos. |
| **PostgreSQL (RDS Aurora)** | Banco relacional robusto, compatível com uso empresarial, ótimo para dados transacionais. |
| **DynamoDB (AWS)** | Ideal para armazenar estruturas flexíveis de leitura rápida (eventos, sessões, status temporários). |
| **AWS (ECS, RDS, SQS, etc.)** | Provedor cloud robusto e amplamente utilizado; uso real de serviços ao invés de simulações. |
| **Docker + Kubernetes (KIND)** | Criação de ambiente local para testes, isolando cada microsserviço. |
| **Grafana + Prometheus** | Observabilidade de métricas, ideal para diagnosticar e monitorar em tempo real. |
| **JUnit + Testcontainers** | Testes automatizados com simulação real de dependências como banco e filas. |

---

## 🧠 Solução Proposta

A plataforma é composta por 5 microsserviços independentes, seguindo o padrão de arquitetura de microsserviços com comunicação REST e eventos via Kafka:

### Fluxo de Negócio (Resumo):

1. **Cliente acessa** o sistema autenticado via app/web
2. Envia seus dados para o **serviço de cadastro**
3. O cadastro consulta o **Core Bancário** e identifica dívidas
4. O serviço de **cálculo** gera simulações de renegociação
5. O cliente escolhe uma opção e o serviço de **proposta** registra
6. Um evento `proposta.criada` é publicado no **Kafka**
7. O serviço de **aceite** processa a resposta do cliente
8. O sistema formaliza a negociação e envia notificações
9. O cliente pode acompanhar tudo pelo serviço de **acompanhamento**

---

## 🧩 Microsserviços

| Microsserviço     | Responsabilidade                                                                 |
|--------------------|-----------------------------------------------------------------------------------|
| **Cadastro**        | Inicia o processo, coleta dados e consulta o Core Bancário.                     |
| **Cálculo**         | Gera as simulações com base nas regras de negócio.                              |
| **Proposta**        | Registra a proposta escolhida e publica eventos de criação.                     |
| **Aceite**          | Processa o aceite do cliente e finaliza a renegociação.                         |
| **Acompanhamento**  | Permite visualizar status, histórico e instruções de pagamento.                 |

---

## 🔄 Comunicação entre serviços

- REST APIs entre frontend e microsserviços
- Kafka para troca de eventos entre Proposta e Aceite
- SQS/DynamoDB podem ser utilizados para filas auxiliares ou estados intermediários

---

## 📦 Arquitetura (C4 - Nível 2)

```

App Web / Mobile
↓
\[ API Gateway (opcional) ]
↓
┌──────────────────────────────┐
│ Plataforma de Renegociação   │
│                              │
│ ┌────────────┐               │
│ │ Cadastro   │───┐           │
│ └────────────┘   │           ↓
│ ┌────────────┐   │         \[Kafka]
│ │ Cálculo    │<──┘           ↓
│ ┌────────────┐             ┌────────────┐
│ │ Proposta   │──→ eventos →│ Aceite     │
│ └────────────┘             └────────────┘
│ ┌────────────┐             \[PostgreSQL] / \[DynamoDB]
│ │ Acompanh.  │
│ └────────────┘
└──────────────────────────────┘

Externals:
→ Core Bancário (API)
→ Serviço de Autenticação (ex: Cognito)
→ Serviço de Notificação (ex: SNS, Firebase)

```

---

## 🚀 Deploy & Execução

**Local (desenvolvimento):**
- Docker Compose para banco, Kafka e microsserviços
- KIND para testes em Kubernetes local

**Cloud (produção/teste real):**
- AWS ECS (ou EKS)
- Terraform para provisionamento
- GitHub Actions para CI/CD (simulado)

---

## ✅ Decisões Técnicas

- **Kafka vs REST puro:** Kafka permite orquestração baseada em eventos e resiliência entre etapas.
- **DynamoDB:** para estados temporários, sessões ou leitura de eventos recentes com alta performance.
- **PostgreSQL:** para dados relacionais persistentes e controle de transações.
- **Java 17:** recursos modernos, LTS, performance e ampla comunidade.
- **Microsserviços:** escalabilidade, independência de deploy, separação de responsabilidade.

---

## 📈 Observabilidade

- **Actuator** exposto em cada serviço
- **Prometheus** coleta métricas
- **Grafana** com dashboards para monitoramento
- Futuro: integração com **AWS CloudWatch**

---

## 🧪 Testes

- Testes unitários com JUnit 5
- Testes de integração com Testcontainers
- CI simulado via GitHub Actions
