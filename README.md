Here’s a ready-to-paste, detailed `README.md` for your GitHub repo.

***

# Event-Driven Microservices with Kafka

This repository contains an end-to-end, event-driven microservice system built with Spring Boot, Apache Kafka, React, and MySQL, designed to mimic a realistic production setup with full observability using Prometheus and Grafana.[1][2][3]

***

## Features

- **Event-driven architecture** using Apache Kafka as the backbone for communication between services.[2][4][5]
- **Microservice-style separation** with dedicated Auth Service and User Service (in progress from Episode 15+).[3][6][2]
- **Full-stack implementation** with a React frontend calling Spring Boot REST APIs.[7][1]
- **Relational persistence** using MySQL with Spring Data JPA.[2][7]
- **Production-style monitoring & observability** using Spring Boot Actuator, Micrometer, Prometheus, and Grafana dashboards.[8][9][10]
- **Realistic deployment topology**: Kafka, Prometheus, Grafana on Ubuntu Server VM; application stack on Windows host (VirtualBox NAT + port forwarding).[11][8]

***

## Architecture Overview

At a high level:

- The **React frontend** sends HTTP requests to the **Spring Boot backend**.  
- Spring Boot exposes REST endpoints that act as **Kafka producers** and **Kafka consumers**, sending and receiving messages on configured topics.[1][7]
- User-related data is stored in **MySQL**, accessed via Spring Data JPA repositories.[7][2]
- **AuthService** and **UserService** are structured as separate services (or modules) communicating via Kafka, modeling an event-driven microservice environment.[6][12][2]
- **Prometheus** scrapes metrics from Spring Boot’s `/actuator/prometheus` endpoint, and **Grafana** visualizes JVM, HTTP, and application metrics via dashboards.[9][10][8]

***

## Tech Stack

**Backend**

- Java 21  
- Spring Boot 3.x (Web, Actuator, Spring Data JPA)  
- Spring for Apache Kafka  
- MySQL + `mysql-connector-j`  
- Micrometer + Prometheus registry  
- Gradle (build tool)[13][14]

**Frontend**

- React (Vite / Create React App based)  
- Axios / fetch for API calls  

**Infrastructure & Monitoring**

- Apache Kafka  
- Prometheus  
- Grafana  
- Ubuntu Server (VM, VirtualBox with NAT & port forwarding)  
- JMeter (optional, for REST API load testing)[15][16]

***

## Project Structure

```text
my-spring-boot-project-1/
├── src/
│   └── main/
│       ├── java/
│       │   └── org/example/
│       │       ├── MySpringBootProject1Application.java
│       │       └── controller/
│       │           ├── ProducerController.java
│       │           └── ConsumerController.java
│       └── resources/
│           ├── application.properties
│           └── static/
├── frontend/                    # React app
│   ├── node_modules/
│   ├── public/
│   ├── src/
│   └── package.json
├── build.gradle
└── README.md
```

The backend and frontend run as **separate processes** during development.

***

## Key Components

### Kafka Integration

- Kafka broker running on the Ubuntu VM (port `9092`).  
- Example topic: `testy` with a consumer group like `metrics-consumer-group`.  
- Spring Boot producers and consumers implemented via `ProducerController` and `ConsumerController`.[1][7]

The flow:

> React → Spring Boot REST endpoint → Kafka topic → Spring Boot consumer → Logs / further processing.

### Database (MySQL)

- MySQL configured as the main persistence layer for user-related data.  
- Spring Data JPA entities and repositories to handle CRUD and transactional operations.[2][7]

### Monitoring & Observability

- `spring-boot-starter-actuator` and `micrometer-registry-prometheus` enabled.[10][9]
- Prometheus scraping from `/actuator/prometheus`.  
- Grafana dashboard (e.g., JVM metrics, HTTP request rate/duration, uptime, etc.).[17][8]

***

## Configuration

### `application.properties` (Backend)

Key configuration (simplified):

```properties
# Kafka
spring.kafka.bootstrap-servers=localhost:9092
spring.kafka.consumer.group-id=metrics-consumer-group

# Actuator / Prometheus
management.endpoints.web.exposure.include=health,info,prometheus,metrics
management.endpoint.prometheus.enabled=true
management.metrics.export.prometheus.enabled=true
```

### `prometheus.yml` (VM)

```yaml
- job_name: 'spring-boot'
  metrics_path: '/actuator/prometheus'
  static_configs:
    - targets: ['10.0.2.2:8080']  # Windows host from VM perspective
```

Prometheus and Grafana are configured as services on the VM.[8][11]

***

## How to Run

### 1. Start Services on the VM (Ubuntu)

SSH into the VM and run:

```bash
# Start Kafka
/home/kafka/kafka/bin/kafka-server-start.sh -daemon /home/kafka/kafka/config/server.properties

# Prometheus and Grafana are already running via systemd services
```

Ensure:

- Kafka: `9092`  
- Prometheus: `9090`  
- Grafana: `3001` (changed from `3000` to avoid React conflict)  

### 2. Start Backend (Spring Boot) on Windows

```bash
cd D:\Coding\JAVA\Development\my-spring-boot-project-1
./gradlew bootRun
```

Backend will be available at: `http://localhost:8080`

### 3. Start Frontend (React) on Windows

```bash
cd D:\Coding\JAVA\Development\my-spring-boot-project-1\frontend
npm install   # first time only
npm start
```

Frontend will be available at: `http://localhost:3000`

***

## URLs

- React UI: `http://localhost:3000`  
- Spring Boot API: `http://localhost:8080`  
- Prometheus: `http://localhost:9090`  
- Grafana: `http://localhost:3001` (default login: `admin/admin`)[11][17]

***

## Example Flow

1. Open the React UI.  
2. Enter a message and submit.  
3. React sends a POST request to the Spring Boot API.  
4. Spring Boot publishes the message to the Kafka topic.  
5. A Spring Boot Kafka consumer listens to the topic and processes/logs the message.[7][1]
6. Metrics for JVM, HTTP, and custom application data are exposed via `/actuator/prometheus` and visualized in Grafana.[9][8]

***

## Troubleshooting Notes

- **Kafka storage formatting issue**  
  - If Kafka fails to start due to log dir errors, run `kafka-storage.sh format` with the correct cluster ID and log directory.[5][18]

- **Prometheus target DOWN**  
  - Ensure the target points to `10.0.2.2:8080` (Windows host from VM), not `localhost:8080`.[8][11]

- **Grafana port conflict**  
  - Changed Grafana from `3000` → `3001` to avoid conflict with React dev server.  

- **React/Spring Boot CORS or proxy issues**  
  - Use a development proxy configuration in React (e.g., `proxy` in `package.json`) or CORS configuration in Spring if needed.[1][7]

***

## Possible Extensions

Planned / potential enhancements based on this setup:

- Real-time chat application using Kafka topics.[5][1]
- Ticket booking or order-processing workflow with event sourcing patterns.[12][2]
- JMeter-based load testing scenarios for REST + Kafka flows.[16][15]
