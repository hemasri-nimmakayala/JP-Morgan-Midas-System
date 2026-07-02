# Midas Core — Financial Transaction Processing System

A Spring Boot microservice simulating high-volume financial transaction 
processing, built as part of the JPMorgan Chase Software Engineering 
Virtual Experience.

## System Architecture
Kafka Topic → [Midas Core] → H2 Database
↓
Incentive REST API (external)
↓
Balance Query REST API (port 33400)
## What It Does

- Consumes financial transactions from a **Kafka topic** asynchronously
- **Validates** each transaction: checks sender/recipient existence and 
  sufficient balance
- Persists valid transactions to an **H2 database** using Spring Data JPA,
  with many-to-one relationships between transactions and users
- Integrates with an external **Incentive REST API** via RestTemplate to 
  calculate and record incentive amounts per transaction
- Exposes a **GET /balance** REST endpoint (port 33400) to query 
  real-time user balances

## Tech Stack

- Java 17, Spring Boot 3.2.5
- Apache Kafka + Spring Kafka 3.1.4
- H2 Database (in-memory) + Spring Data JPA
- REST API (Spring MVC) + RestTemplate
- Maven, JUnit (automated test suite across all tasks)

## Key Implementation Details

- `@KafkaListener` configured via `application.yml` topic binding —
  no hardcoded topic names
- `TransactionRecord` entity (separate from `Transaction` DTO) maintains
  JPA relationships with `User` entities
- Incentive amount added to recipient balance without deducting from 
  sender — modeled as a bank-side incentive, not a transfer cost
- Balance endpoint returns 0 gracefully for non-existent users

## Running Locally

```bash
# Prerequisites: Java 17, Maven
mvn clean install
# Start incentive API (included jar)
java -jar services/transaction-incentive-api.jar
# Run Midas Core
mvn spring-boot:run
# Query balance
curl "http://localhost:33400/balance?userId=waldorf"
```
