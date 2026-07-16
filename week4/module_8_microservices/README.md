# Week 4 - Module 8: Creating Microservices for Account and Loan

## 📌 Overview

This module demonstrates the creation of two independent **Spring Boot Microservices**:

| Microservice      | Port | Base URL                        |
|-------------------|------|---------------------------------|
| Account Service   | 8081 | `http://localhost:8081/accounts`|
| Loan Service      | 8082 | `http://localhost:8082/loans`   |

Each microservice is **independently deployable**, has its **own data store**, and exposes a **RESTful API**.

---

## 🏗️ Project Structure

```
week4/
└── module_8_microservices/
    ├── account-service/                  ← Account Microservice (port 8081)
    │   ├── pom.xml
    │   └── src/
    │       ├── main/
    │       │   ├── java/com/cognizant/accountservice/
    │       │   │   ├── AccountServiceApplication.java   ← @SpringBootApplication
    │       │   │   ├── model/
    │       │   │   │   └── Account.java                 ← Domain model
    │       │   │   ├── service/
    │       │   │   │   └── AccountService.java          ← Business logic
    │       │   │   └── controller/
    │       │   │       └── AccountController.java       ← REST endpoints
    │       │   └── resources/
    │       │       └── application.properties
    │       └── test/
    │           └── java/com/cognizant/accountservice/
    │               └── AccountServiceApplicationTests.java
    │
    └── loan-service/                     ← Loan Microservice (port 8082)
        ├── pom.xml
        └── src/
            ├── main/
            │   ├── java/com/cognizant/loanservice/
            │   │   ├── LoanServiceApplication.java      ← @SpringBootApplication
            │   │   ├── model/
            │   │   │   └── Loan.java                    ← Domain model
            │   │   ├── service/
            │   │   │   └── LoanService.java             ← Business logic + EMI calc
            │   │   └── controller/
            │   │       └── LoanController.java          ← REST endpoints
            │   └── resources/
            │       └── application.properties
            └── test/
                └── java/com/cognizant/loanservice/
                    └── LoanServiceApplicationTests.java
```

---

## 🚀 How to Run

### Run Account Service
```bash
cd account-service
mvn spring-boot:run
```
Access: `http://localhost:8081/accounts`

### Run Loan Service
```bash
cd loan-service
mvn spring-boot:run
```
Access: `http://localhost:8082/loans`

---

## 📡 Account Service API Endpoints

| Method | Endpoint                              | Description               |
|--------|---------------------------------------|---------------------------|
| GET    | `/accounts`                           | Get all accounts          |
| GET    | `/accounts/{accountNumber}`           | Get account by number     |
| GET    | `/accounts/customer/{customerId}`     | Get accounts by customer  |
| POST   | `/accounts`                           | Create new account        |
| PUT    | `/accounts/{accountNumber}`           | Update account            |
| DELETE | `/accounts/{accountNumber}`           | Delete account            |

### Sample POST Body (Create Account)
```json
{
  "accountType": "SAVINGS",
  "customerId": "CUST005",
  "customerName": "John Doe",
  "balance": 50000.00,
  "ifscCode": "AXIS0001234",
  "branchName": "Delhi Central"
}
```

---

## 📡 Loan Service API Endpoints

| Method | Endpoint                              | Description               |
|--------|---------------------------------------|---------------------------|
| GET    | `/loans`                              | Get all loans             |
| GET    | `/loans/{loanId}`                     | Get loan by ID            |
| GET    | `/loans/customer/{customerId}`        | Get loans by customer     |
| POST   | `/loans`                              | Apply for a new loan      |
| PATCH  | `/loans/{loanId}/status`             | Update loan status        |
| DELETE | `/loans/{loanId}`                    | Delete loan application   |
| GET    | `/loans/emi-calculator`              | Calculate EMI             |

### Sample POST Body (Apply Loan)
```json
{
  "loanType": "HOME",
  "customerId": "CUST005",
  "customerName": "John Doe",
  "principalAmount": 1000000,
  "interestRate": 8.5,
  "tenureMonths": 240
}
```

### EMI Calculator
```
GET /loans/emi-calculator?principal=500000&annualRate=8.5&tenureMonths=60
```
Response:
```json
{
  "principal": 500000.0,
  "annualRatePercent": 8.5,
  "tenureMonths": 60,
  "monthlyEmi": 10224.13,
  "totalPayable": 613447.8,
  "totalInterest": 113447.8
}
```

### Update Loan Status
```
PATCH /loans/LOAN003/status
Body: { "status": "APPROVED" }
```

---

## 🏥 Health Check (Actuator)

| Service         | URL                                       |
|-----------------|-------------------------------------------|
| Account Health  | `http://localhost:8081/actuator/health`   |
| Account Info    | `http://localhost:8081/actuator/info`     |
| Loan Health     | `http://localhost:8082/actuator/health`   |
| Loan Info       | `http://localhost:8082/actuator/info`     |

---

## 🧠 Key Concepts Demonstrated

1. **Microservice Architecture** – Two separate, independently deployable services
2. **Spring Boot REST API** – Full CRUD with `@RestController`, `@GetMapping`, `@PostMapping`, `@PutMapping`, `@PatchMapping`, `@DeleteMapping`
3. **Service Layer** – Business logic separated from controllers (`@Service`)
4. **In-memory Data Store** – HashMap used to simulate database (no JPA dependency required)
5. **EMI Calculation** – Standard formula: `EMI = [P × r × (1+r)^n] / [(1+r)^n − 1]`
6. **Spring Boot Actuator** – `/actuator/health` and `/actuator/info` endpoints
7. **Separation of Concerns** – model / service / controller layers
8. **Port Isolation** – Each microservice runs on a different port (8081, 8082)
