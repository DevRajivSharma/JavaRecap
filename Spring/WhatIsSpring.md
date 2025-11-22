# What Is Spring — Revision Notes

## Overview

- Spring is a Java application framework focused on dependency injection, modularity, and productivity.
- Provides infrastructure support: configuration, lifecycle, data access, web MVC, AOP, messaging, and testing.
- Spring Boot streamlines setup with auto-configuration, embedded servers, and opinionated defaults.

## Core Concepts

- Inversion of Control (IoC): objects don’t construct dependencies; the container provides them.
- Dependency Injection (DI): wiring dependencies via constructors, setters, or fields.
- Bean: an object managed by the Spring container.
- ApplicationContext: the IoC container that creates, configures, and manages beans.

## Configuration Styles

- Annotation-based: `@Component`, `@Service`, `@Repository`, `@Controller`, `@Configuration`, `@Bean`.
- Java Config: `@Configuration` classes with `@Bean` methods.
- XML: legacy style using `<beans>` and `<bean>`.
- Auto-configuration (Boot): classpath-driven defaults via `spring-boot-autoconfigure`.

## Bean Lifecycle

- Creation → dependency injection → post-processing → initialization → runtime → destruction.
- Scope: `singleton` (default), `prototype`, `request`, `session`, `application`.
- Lifecycle hooks: `@PostConstruct`, `@PreDestroy`, `InitializingBean`, `DisposableBean`.

## Profiles & Properties

- Profiles: `@Profile` and `spring.profiles.active` to switch config per environment.
- Properties: externalized via `application.properties`/`yaml` and `@Value`, `@ConfigurationProperties`.

## Spring Boot Essentials

- Entry point: `@SpringBootApplication` (combines `@Configuration`, `@EnableAutoConfiguration`, `@ComponentScan`).
- Embedded servers: Tomcat/Jetty/Undertow; run via `main` or `mvn spring-boot:run`.
- Actuator: operational endpoints (`/actuator/health`, metrics, env, info).

## Web (Spring MVC)

- Controllers: `@RestController`/`@Controller`, map routes with `@RequestMapping`, `@GetMapping`, etc.
- Data binding: `@RequestBody`, `@PathVariable`, `@RequestParam`.
- Validation: Bean Validation (`@Valid`, constraints like `@NotNull`).
- View layer: Thymeleaf, FreeMarker; or JSON via `@RestController`.

## Data Access

- JDBC: `JdbcTemplate` simplifies SQL operations; transaction management via `@Transactional`.
- JPA: `EntityManager` or Spring Data JPA repositories (`CrudRepository`, `JpaRepository`).
- Transactions: declarative with `@Transactional` (propagation, isolation, read-only).

## AOP (Aspect-Oriented Programming)

- Cross-cutting concerns (logging, security, transactions) via proxies.
- Key terms: Aspect, Join Point, Pointcut, Advice (`@Before`, `@Around`, `@After`).

## Messaging & Integration

- Spring Messaging: `@MessageMapping`, STOMP over WebSocket.
- Spring AMQP/Kafka: template and listener support for queues/topics.

## Security (Spring Security)

- Authentication & authorization: filter chain, `UserDetailsService`, `PasswordEncoder`.
- Method security: `@PreAuthorize`, `@Secured`.
- OAuth2/OpenID Connect support.

## Testing

- Context tests: `@SpringBootTest` loads application context.
- Slice tests: `@WebMvcTest`, `@DataJpaTest`, etc. for focused layers.
- Mocking: `@MockBean` to replace beans; `TestRestTemplate`/`WebTestClient` for HTTP.

## Common Annotations (Quick Reference)

- Stereotypes: `@Component`, `@Service`, `@Repository`, `@Controller`, `@RestController`.
- Configuration: `@Configuration`, `@Bean`, `@ConfigurationProperties`, `@Enable*`.
- Web: `@RequestMapping`, `@GetMapping`, `@PostMapping`, `@PathVariable`, `@RequestParam`, `@RequestBody`.
- Persistence: `@Entity`, `@Id`, `@GeneratedValue`, `@Transactional`.
- Validation: `@Valid`, `@NotNull`, `@Size`, `@Email`.

## Typical Project Structure (Boot)

- `Application` class with `main` and `@SpringBootApplication`.
- `controller/` for web endpoints; `service/` for business logic; `repository/` for data access.
- `domain/` for entities and DTOs; `config/` for configuration classes.
- `resources/` for `application.properties`/`yaml`, static assets, templates.

## Runtime Flow (High-Level)

- Start application → auto-config resolves beans based on classpath → component scan registers beans → DI wires dependencies → web server starts → requests routed via DispatcherServlet → controllers handle → services/repositories execute → responses returned.

## Advantages & Trade-offs

- Pros: strong ecosystem, DI/AOP, testing support, production-readiness with Boot.
- Cons: learning curve, magic via auto-config, proxy behavior surprises.

## Quick Tips

- Prefer constructor injection for immutability and testability.
- Keep configuration externalized; use profiles for environments.
- Use slice tests to speed up feedback; avoid loading full context unnecessarily.
