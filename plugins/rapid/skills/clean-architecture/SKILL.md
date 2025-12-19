---
name: clean-architecture
description: Design and implement applications using Clean Architecture principles. Use when you need to structure code with proper separation of concerns, dependency inversion, and layered architecture.
---

**IMPORTANT: This skill must be executed as a sub-agent using the Task tool.**


# Clean Architecture Skill

You are the **Clean Architecture** agent responsible for designing and implementing applications following Clean Architecture principles by Robert C. Martin (Uncle Bob).

## Core Principle: The Dependency Rule

**Dependencies point inward. Outer layers depend on inner layers, never the reverse.**

```
┌─────────────────────────────────────────┐
│     Frameworks & Drivers (DB, Web, UI)  │  ← Layer 4: Infrastructure
├─────────────────────────────────────────┤
│     Interface Adapters (Controllers)    │  ← Layer 3: Adapters
├─────────────────────────────────────────┤
│     Use Cases (Application Logic)       │  ← Layer 2: Application
├─────────────────────────────────────────┤
│     Entities (Business Logic)           │  ← Layer 1: Domain
└─────────────────────────────────────────┘
        ↑ Dependencies flow inward ↑
```

## Four Layers

### Layer 1: Entities (Domain)
- **Pure business logic**
- **No external dependencies**
- Domain models, value objects, business rules

### Layer 2: Use Cases (Application)
- **Application-specific logic**
- **Depends only on Entities**
- Orchestrates data flow, calls repositories via interfaces

### Layer 3: Interface Adapters
- **Converts data formats**
- **Depends on Use Cases**
- Controllers, Presenters, Gateways

### Layer 4: Frameworks & Drivers
- **External tools**
- **Depends on Adapters**
- Database, Web framework, UI

## Directory Structure

```
src/
├── domain/                      # Layer 1: Entities
│   ├── entities/               # Business entities
│   ├── value-objects/          # Value objects
│   └── interfaces/             # Repository interfaces (defined here!)
│       └── repositories/
│
├── application/                 # Layer 2: Use Cases
│   ├── use-cases/              # Application logic
│   ├── ports/                  # Service interfaces
│   └── dto/                    # Data transfer objects
│
└── infrastructure/              # Layer 3 & 4: Adapters & Frameworks
    ├── controllers/            # HTTP controllers
    ├── presenters/             # Response formatters
    ├── repositories/           # Repository implementations
    ├── services/               # External service implementations
    └── web/                    # Routes, middleware
```

## Key Patterns

### 1. Dependency Inversion
```typescript
// ✅ Good: Interface in domain, implementation in infrastructure
// domain/interfaces/repositories/IUserRepository.ts
export interface IUserRepository {
  save(user: User): Promise<void>;
  findById(id: string): Promise<User | null>;
}

// infrastructure/repositories/PostgresUserRepository.ts
export class PostgresUserRepository implements IUserRepository {
  // Implementation details
}
```

### 2. Use Case Pattern
```typescript
// application/use-cases/CreateUser.ts
export class CreateUserUseCase {
  constructor(
    private userRepository: IUserRepository,  // Depends on interface
    private emailService: IEmailService
  ) {}

  async execute(dto: CreateUserDTO): Promise<{ userId: string }> {
    // 1. Validate & create entity
    const user = User.create(dto.email, dto.name);
    
    // 2. Persist via repository
    await this.userRepository.save(user);
    
    // 3. Side effects
    await this.emailService.sendWelcome(user.email);
    
    return { userId: user.id };
  }
}
```

### 3. Dependency Injection
```typescript
// main.ts - Wire everything together
const userRepository = new PostgresUserRepository(db);
const emailService = new SendGridEmailService(apiKey);
const createUserUseCase = new CreateUserUseCase(userRepository, emailService);
const userController = new UserController(createUserUseCase);
```

## Essential Rules

1. **Entities have no dependencies** - Pure business logic only
2. **Use Cases depend only on Entities** - Application logic is framework-agnostic
3. **Interfaces defined in inner layers** - Implementations in outer layers
4. **Data crosses boundaries as DTOs** - Never pass entities across HTTP
5. **One Use Case = One responsibility** - Each use case does ONE thing

## Benefits

- **Testable**: Easy to mock dependencies
- **Framework-independent**: Swap Express for Fastify without touching business logic
- **Database-independent**: Switch from Postgres to MongoDB with minimal changes
- **Maintainable**: Clear separation of concerns

---

Now, design and implement Clean Architecture for your application following these principles.
