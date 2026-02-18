# Service Discovery with Eureka in Microservices (Spring Boot 3/4)

This document explains **service discovery** in microservices with a focus on **Netflix Eureka** and how to configure it using **Spring Boot 3/4** with **Spring Cloud**, **Maven**, and **WebClient**.

---

## 1) What is Service Discovery?

In microservices, services communicate over the network. The key challenge is that service instances are **dynamic**:

- Instances can be added/removed by autoscaling.
- Containers/VMs restart and get new IP addresses.
- Deployments replace instances frequently (rolling updates, blue/green, canary).

If `order-service` must call `user-service`, hardcoding `http://10.0.1.7:8081` is fragile.

**Service discovery** allows a service to call another service using a stable **logical name** (example: `USER-SERVICE`) and resolve it at runtime to an actual host/port.

---

## 2) Purpose of Service Discovery

Service discovery enables:

- **Location transparency**: call `http://USER-SERVICE/...` instead of a fixed host/port.
- **Dynamic scaling**: discover multiple instances and distribute calls across them.
- **Resilience to churn**: instances can restart or move without breaking callers.
- **Environment independence**: dev/stage/prod can have different addresses without code changes.

---

## 3) Eureka Core Concepts

### 3.1 Eureka Server (Service Registry)

A central registry that stores:

- **Service name** → **list of instances**
- Each instance typically includes: hostname/IP, port, status, and metadata.

### 3.2 Eureka Client (Registration + Discovery)

Each microservice runs a Eureka client that:

- **Registers** itself with the Eureka server at startup.
- Sends periodic **heartbeats** (lease renewals).
- **Fetches the registry** (so it knows where other services are).

### 3.3 Client-side vs Server-side Discovery

Eureka is commonly used for **client-side discovery**:

- The caller (e.g., `order-service`) queries Eureka for instances of `user-service`.
- The caller chooses an instance (client-side load balancing).
- The caller calls the chosen instance directly.

Server-side discovery is when an API gateway / reverse proxy performs the lookup and routing.

### 3.4 Consistency and Health Behavior

- Eureka is designed for high availability and uses **eventual consistency**.
- Instances can be **temporarily stale** in the registry during network issues.
- Eureka has **self-preservation mode** to avoid mass-eviction when many clients appear to go offline at once.

**Important**: Discovery reduces failures, but you still need **timeouts**, and usually **circuit breakers** in callers.

---

## 4) Features (What you typically use)

- **Service registration** (instances register on startup)
- **Service discovery** (resolve a service name to instances)
- **Client-side load balancing** (distribute calls across instances)
- **Instance metadata** (zone, version, tags)
- **Actuator integration** (health/info endpoints)

---

## 5) Pros and Cons

## Pros

- **Decoupling**: callers depend on service name, not IP address.
- **Scalability**: multiple instances are automatically discoverable.
- **Resilience to instance churn**: restarts and redeployments are normal.
- **Good local/dev fit**: easy to run with Docker Compose or directly on your machine.

## Cons

- **Extra infrastructure**: you must operate Eureka Server (and ideally run it in HA for production).
- **Not perfect health**: heartbeats do not guarantee “deep health”.
- **Eventual consistency**: stale entries can still be used briefly.
- **Version alignment**: Spring Boot and Spring Cloud versions must be compatible.

---

## 6) When to use Eureka vs alternatives

- **Use Eureka when**
  - You are not fully on Kubernetes.
  - You want Spring Cloud-native service discovery and client-side load balancing.

- **Prefer Kubernetes-native discovery when**
  - You run in Kubernetes and can rely on `Service` DNS and endpoints.

- **Consider a service mesh when**
  - You need mTLS, policy enforcement, traffic shifting, retries/timeouts centrally, and deep observability.

---

## 7) Spring Boot 3/4 configuration with Eureka (Maven)

A typical setup includes three applications:

- `eureka-server`
- `user-service`
- `order-service`

### 7.1 Eureka Server

**Annotation**:

- `@EnableEurekaServer`

**Key config**:

- Run on port `8761`
- Do not register the server itself into Eureka

Typical properties:

- `server.port: 8761`
- `eureka.client.register-with-eureka: false`
- `eureka.client.fetch-registry: false`

### 7.2 Eureka Clients (user-service / order-service)

**Annotations**:

- In modern Spring Cloud, you generally do **not** need `@EnableEurekaClient`.
- Having the Eureka client dependency is usually enough.

**Key config**:

- `spring.application.name: user-service` (or `order-service`)
- `eureka.client.service-url.defaultZone: http://localhost:8761/eureka`

### 7.3 WebClient + Load Balancing

To call by service name (example: `http://USER-SERVICE/...`) you must:

- Create a `WebClient.Builder` bean
- Annotate it with `@LoadBalanced`

This enables service-name resolution and client-side load balancing using **Spring Cloud LoadBalancer**.

---

## 8) Key Annotations (Cheat Sheet)

### Eureka

- `@EnableEurekaServer` (Eureka Server)
- `@EnableEurekaClient` (historical / usually not required in modern Spring Cloud)

### Spring Web / WebClient

- `@LoadBalanced` (on `WebClient.Builder` bean)

### Standard Spring

- `@SpringBootApplication`
- `@Configuration`, `@Bean`
- `@RestController`
- `@GetMapping`, `@PostMapping`

---

## 9) Example: user-service and order-service scenarios

Assume:

- Eureka Server: `http://localhost:8761`
- user-service instances: `8081`, `8083`
- order-service: `8082`

### 9.1 Scenario A: Registration

1. Start Eureka Server.
2. Start user-service.
3. Start order-service.

Expected:

- Eureka dashboard shows registered services.
- You see `USER-SERVICE` and `ORDER-SERVICE`.

### 9.2 Scenario B: order-service calls user-service by service name

- order-service calls: `http://USER-SERVICE/users/{id}`
- Eureka returns available user-service instances.
- Client-side LB picks an instance and the call completes.

### 9.3 Scenario C: Multiple instances and load balancing

- Start a second user-service instance on a different port.
- Eureka now lists multiple instances.
- Calls from order-service distribute across them (typically round-robin).

### 9.4 Scenario D: Failure handling

If one user-service instance goes down:

- Some calls may fail temporarily due to stale registry/caches.
- You should configure:
  - Timeouts
  - Circuit breakers (recommended)
  - Retries carefully (avoid retry storms)

---

## 10) Troubleshooting

### Service not visible in Eureka dashboard

- Validate `spring.application.name`.
- Validate `eureka.client.service-url.defaultZone`.
- Check the service can reach Eureka over the network.

### `http://USER-SERVICE/...` fails to resolve

- Ensure you are using an injected `WebClient.Builder` bean.
- Ensure the bean is annotated `@LoadBalanced`.
- Ensure Spring Cloud LoadBalancer dependency is present.

### Intermittent failures after scaling

- Normal under churn and eventual consistency.
- Add timeouts and resilience patterns.

---

## 11) Recommended dev vs prod approach

### Local development

- Run single Eureka server.
- Run services on fixed ports.
- Use logical service names for service-to-service calls.

### Production

- Run Eureka server in HA (multiple nodes).
- Enable observability (logs/metrics/tracing).
- Use resilience patterns (timeouts, circuit breakers, bulkheads).

---

## 12) Next: Copy-paste Maven + WebClient code (optional)

If you want, I can add a second document with **exact** Maven `pom.xml`, `application.yml`, and Java classes for:

- `eureka-server`
- `user-service`
- `order-service` (with `@LoadBalanced WebClient.Builder` and a sample call to `USER-SERVICE`)

To make the BOM correct, confirm your exact Spring Boot version (example: `3.2.x`, `3.3.x`, or `4.0.x`).





-----------------------------------------

# Service Discovery in Microservices (Spring Boot 3/4 + Eureka)

This document explains **service discovery** in a microservice architecture: **purpose**, **features**, **pros/cons**, Spring Boot **3/4** configuration using **Eureka**, key **annotations**, and complete **user-service + order-service** scenarios.

---

## 1) What is Service Discovery?

Service discovery is a mechanism for locating service instances dynamically.

Instead of calling a service using a fixed URL:

```text
http://localhost:8081/users/1
```

You call it using a logical name:

```text
http://USER-SERVICE/users/1
```

A discovery server maintains mappings:

| Service Name | Actual Running Instances |
| --- | --- |
| USER-SERVICE | 10.0.3.21:8081 |
| USER-SERVICE | 10.0.4.11:8081 |
| ORDER-SERVICE | 10.0.2.45:8082 |

When a service restarts or scales, the mapping updates automatically.

---

## 2) Why do we need Service Discovery?

### Problem without discovery

Microservice instances are dynamic:

- Containers restart and get a new IP.
- Multiple instances exist during scaling.
- Rolling deployments constantly replace instances.

If `order-service` calls `user-service` using a hardcoded IP, it will fail after restarts.

### With discovery

`order-service` calls using service name:

```text
http://USER-SERVICE/users/1
```

The discovery client resolves the service name to a healthy instance.

### Additional benefits

- Load balancing
- Auto scaling
- Fault tolerance (avoid calling dead instances over time)
- Zero downtime deployments (less coupling to fixed addresses)

---

## 3) Discovery patterns

### Client-side discovery (Eureka typical)

- The caller queries the registry and load-balances locally.
- Spring Cloud uses **Spring Cloud LoadBalancer** to pick an instance.

### Server-side discovery

- A gateway/proxy queries the registry and routes requests.
- Example: API Gateway routing to `lb://USER-SERVICE`.

---

## 4) Eureka fundamentals

### Components

- **Eureka Server**: registry (directory)
- **Eureka Client**: runs inside services
  - Registers on startup
  - Sends heartbeats (lease renewals)
  - Fetches registry and caches it

### Consistency and health notes

- Eureka is designed for high availability and uses **eventual consistency**.
- Entries can become temporarily stale.
- You still need timeouts/circuit breakers in callers.

---

## 5) Pros and Cons

## Pros

- No hardcoded IPs/ports in callers
- Supports scaling (multiple instances)
- Integrates well with Spring Cloud ecosystem
- Works well in VM / Docker / non-Kubernetes environments

## Cons

- Extra infrastructure (Eureka Server)
- Eventual consistency (stale instances possible temporarily)
- Needs version alignment between Spring Boot and Spring Cloud

---

## 6) Do we always need Service Discovery?

No.

| Scenario | Need Discovery? |
| --- | --- |
| Monolith | No |
| Few services with static IPs | Usually no |
| Kubernetes | Usually no (K8s DNS provides discovery) |
| Large, dynamic microservice system | Yes |

Note:

- In Kubernetes, native service discovery is typically enough:

```text
http://inventory-service.default.svc.cluster.local
```

Eureka is most common in:

- Docker Compose
- VM based infra
- Non-K8s servers

---

## 7) Spring Boot 3/4 configuration with Eureka

A typical setup:

- `eureka-server`
- `user-service`
- `order-service`

### 7.1 Eureka Server

**Annotation**:

- `@EnableEurekaServer`

Typical config:

- `server.port=8761`
- `eureka.client.register-with-eureka=false`
- `eureka.client.fetch-registry=false`

### 7.2 Eureka Clients

Typical config:

- `spring.application.name=USER-SERVICE` / `ORDER-SERVICE`
- `eureka.client.service-url.defaultZone=http://localhost:8761/eureka`

### 7.3 Calling another service by name

For service-name based calls you need a load-balancer aware client.

For WebClient:

- Create a `WebClient.Builder` bean
- Mark it `@LoadBalanced`

---

## 8) Key annotations

- `@EnableEurekaServer` (Eureka server)
- `@SpringBootApplication`
- `@LoadBalanced` (for `WebClient.Builder` / `RestTemplate`)
- `@Configuration`, `@Bean`
- `@RestController`, `@GetMapping`

---

## 9) Example scenarios (user-service + order-service)

### Scenario A: Registration

1. Start Eureka Server
2. Start user-service
3. Start order-service

Eureka dashboard shows:

- `USER-SERVICE`
- `ORDER-SERVICE`

### Scenario B: order-service calls user-service

- order-service calls: `http://USER-SERVICE/users/{id}`
- Eureka provides instances
- Load balancer picks an instance

### Scenario C: Scaling user-service

- Start another user-service instance
- Registry now has multiple `USER-SERVICE` instances
- Calls distribute across instances

### Scenario D: Failures

- If one instance dies, some calls can fail briefly due to stale registry/caches
- Add:
  - timeouts
  - circuit breakers
  - careful retries

---

## 10) Troubleshooting checklist

- Service not visible in Eureka:
  - Check `spring.application.name`
  - Check `defaultZone`
- Service-name call fails (`http://USER-SERVICE/...`):
  - Ensure `@LoadBalanced WebClient.Builder`
  - Ensure `spring-cloud-starter-loadbalancer` is included

