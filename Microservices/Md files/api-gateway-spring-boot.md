# API Gateway in Microservices (Spring Boot 3/4)

This document explains **API Gateway** in a microservice architecture: **purpose**, **features**, **pros/cons**, how to configure it with **Spring Boot 3/4** (Spring Cloud Gateway), key **annotations**, and a complete **user-service + order-service** scenario.

---

## 1) What is an API Gateway?

An **API Gateway** is a single entry point for clients (web/mobile/3rd party) to access multiple backend microservices.

Instead of clients calling services directly:

```text
Client -> user-service
Client -> order-service
Client -> payment-service
```

Clients call the gateway:

```text
Client -> API Gateway -> (routes to user-service / order-service / ...)
```

The gateway performs routing and cross-cutting concerns such as security, throttling, and observability.

---

## 2) Why do we need an API Gateway?

### Problem without a gateway

- **Too many endpoints** for clients to manage.
- Client must know each service’s address (and deal with discovery).
- Each service must implement cross-cutting concerns (auth, CORS, rate limits) repeatedly.
- Service boundaries leak to clients (hard to evolve).

### With a gateway

- Client only talks to **one** endpoint.
- Central place for:
  - Authentication/Authorization
  - Rate limiting
  - Request/response logging
  - CORS
  - Request shaping (headers, paths)
  - Aggregation (sometimes)

---

## 3) Core API Gateway capabilities (feature list)

A typical gateway provides:

- **Routing**
  - Map paths/hosts to backend services.
- **Load balancing**
  - Route to multiple instances.
- **Service discovery integration**
  - Route using service IDs (example: `lb://USER-SERVICE`).
- **Filters / middleware**
  - Add/remove headers, rewrite paths, etc.
- **Security edge**
  - OAuth2/JWT validation at the edge.
- **Rate limiting**
  - Prevent abuse and protect backends.
- **Resilience**
  - Timeouts, retries (careful), circuit breakers.
- **Observability**
  - Logs/metrics/tracing from a single choke point.

---

## 4) Pros and Cons

## Pros

- **Single entry point** for clients.
- **Hides internal service topology**.
- **Centralized cross-cutting concerns**.
- **Better client experience** (stable API surface).
- **Easier versioning / deprecation**.

## Cons

- **Extra hop** adds latency.
- **Operational complexity** (another component to run/scale).
- **Can become a bottleneck** if not scaled.
- **Risk of “god gateway”** (too much business logic in gateway).

Rule of thumb:

- Gateway should handle **routing + policies**, not business rules.

---

## 5) API Gateway patterns

- **Edge Gateway**
  - Public entry point from outside.
- **BFF (Backend For Frontend)**
  - Separate gateway per client type (web vs mobile).
- **Internal Gateway**
  - Used for service-to-service routing inside the platform.

---

## 6) Spring Boot 3/4: How to implement an API Gateway

In Spring ecosystem, the most common choice is:

- **Spring Cloud Gateway** (reactive, Netty)

### Key dependencies (conceptual)

- `spring-cloud-starter-gateway`
- If using Eureka: `spring-cloud-starter-netflix-eureka-client`

### Key configuration approach

- Configure routes in `application.yml`:
  - Predicates (when to match)
  - Filters (rewrite/strip prefix)
  - URI (where to send)

Example style:

- Route `/api/users/**` -> `lb://USER-SERVICE`
- Route `/api/orders/**` -> `lb://ORDER-SERVICE`

---

## 7) Annotations you can use

Spring Cloud Gateway is mostly configuration-driven.

Common annotations in a gateway app:

- `@SpringBootApplication`
- `@Configuration` / `@Bean`

Security (if enabled):

- `@EnableWebFluxSecurity` (optional)

Observability:

- Spring Boot Actuator endpoints and configuration (no special annotation required beyond dependencies).

Note:

- There is no equivalent to `@EnableGateway` for Spring Cloud Gateway.

---

## 8) Example: Gateway + Eureka + user-service + order-service scenarios

Assume services:

- `USER-SERVICE` (example endpoint: `/users/{id}`)
- `ORDER-SERVICE` (example endpoint: `/orders/{id}`)

Gateway routes:

- `GET /api/users/1` -> forwards to `USER-SERVICE` `/users/1`
- `GET /api/orders/100` -> forwards to `ORDER-SERVICE` `/orders/100`

### Scenario A: Client calls gateway only

```text
Client -> GET /api/orders/100 -> Gateway -> ORDER-SERVICE
```

### Scenario B: Gateway uses discovery to find service instances

- Gateway routes to `lb://ORDER-SERVICE`
- With multiple instances registered in Eureka:
  - Gateway load-balances across instances

### Scenario C: Service scaling

- Start 2 instances of user-service
- Gateway routes traffic across both instances automatically

### Scenario D: Service restart

- user-service restarts with a new port/IP
- Eureka updates registry
- Gateway continues routing (no client changes)

---

## 9) How do user-service and order-service use the API Gateway?

In a typical setup, **services do not “use” the gateway**. **Clients** use the gateway.

### Client-to-service traffic (north-south)

- Clients (browser/mobile/3rd party) call the gateway.
- The gateway routes the request to the correct backend service.

Example:

```text
Client -> GET /api/users/1  -> API Gateway -> USER-SERVICE (/users/1)
Client -> GET /api/orders/100 -> API Gateway -> ORDER-SERVICE (/orders/100)
```

### Service-to-service traffic (east-west)

For calls between services (example: `order-service` calling `user-service`), the most common approach is:

- Call **directly** using service discovery (Eureka) / internal DNS.
- Avoid calling through the gateway (extra hop + more coupling).

In this demo, `order-service` calls:

```text
http://USER-SERVICE/users/{id}
```

So the full flow for `GET /api/orders/100` is:

```text
Client -> API Gateway -> ORDER-SERVICE
ORDER-SERVICE -> (Eureka + LoadBalancer) -> USER-SERVICE
```

### When would a service call the gateway?

Not common, but possible if you intentionally design:

- The gateway as an internal policy enforcement point for all traffic
- An internal gateway pattern (usually replaced by service mesh in modern setups)

---

## 10) What you might be missing in API Gateway (common checklist)

These are often overlooked when people first adopt a gateway:

- **CORS** policy at gateway (so services don’t each implement it)
- **Request size limits** (protect backends)
- **Rate limiting** and/or **WAF** integration
- **JWT validation** at edge (and propagating identity via headers)
- **Timeouts** and **circuit breakers** (gateway should fail fast)
- **Structured logging** and **correlation IDs**
- **Distributed tracing** propagation (`traceparent`)
- **Route versioning** (`/v1/**`, `/v2/**`)
- **Canary routing** (advanced: route by header or weight)

---

## 11) Final recommendations

- If you already have many services, a gateway is usually a net win.
- Keep business logic **out of the gateway**.
- For Spring Boot 3/4, prefer **Spring Cloud Gateway**.
- If you are already using Eureka (VM/Docker without K8s), configure gateway routes using `lb://SERVICE-ID`.

