# StockPilot Gateway

API Gateway responsável pelo roteamento, autenticação e controle de acesso dos microsserviços da plataforma StockPilot.

## Tecnologias

- Java 21
- Spring Boot 3.5.x
- Spring Cloud Gateway
- Spring Cloud Netflix Eureka Client
- Spring Security
- Docker
- Railway

## Responsabilidades

- Centralizar todas as requisições da plataforma
- Descobrir serviços via Eureka
- Validar autenticação dos usuários
- Encaminhar requisições para os microsserviços
- Configurar CORS
- Rate Limiting
- Logging e rastreabilidade

## Arquitetura

```text
Frontend
    |
    v
+----------------+
| StockPilot     |
| Gateway        |
+----------------+
    |
    +----------------------+
    |                      |
    v                      v
+-----------+       +-------------+
| Auth      |       | Product     |
| Service   |       | Service     |
+-----------+       +-------------+
    |
    +----------------------+
    |
    v
+-------------+
| Eureka      |
| Server      |
+-------------+
```

## Serviços Registrados

| Serviço | Nome Eureka |
|----------|------------|
| Auth | stockpilot-auth |
| Product | stockpilot-product |
| Company | stockpilot-company |
| Inventory | stockpilot-inventory |

## Configuração

### application.yml

```yaml
server:
  port: 8080

spring:
  application:
    name: stockpilot-gateway

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
```

## Rotas

### Auth

```yaml
routes:
  - id: auth-service
    uri: lb://stockpilot-auth
    predicates:
      - Path=/api/v1/auth/**
```

### Product

```yaml
routes:
  - id: product-service
    uri: lb://stockpilot-product
    predicates:
      - Path=/api/v1/products/**
```

## Execução Local

### Eureka

```bash
mvn spring-boot:run
```

### Gateway

```bash
mvn spring-boot:run
```

Acesse:

```text
http://localhost:8080
```

## Fluxo de Autenticação

1. Usuário realiza login via Gateway
2. Gateway encaminha para Auth Service
3. Auth Service gera JWT e grava HttpOnly Cookie
4. Gateway repassa o cookie para o navegador
5. Demais requisições utilizam o cookie automaticamente

## Deploy

### Railway

Variáveis obrigatórias:

```env
EUREKA_ADDRESS=https://eureka-production.up.railway.app/eureka/
SPRING_PROFILES_ACTIVE=prod
```

## Roadmap

- [ ] JWT Validation Filter
- [ ] Rate Limiting
- [ ] Circuit Breaker
- [ ] Request Logging
- [ ] Distributed Tracing
- [ ] OpenTelemetry
- [ ] API Documentation Aggregation

## Autor

Rhuan Resende

Projeto StockPilot - Plataforma de Gestão de Estoque e Operações.