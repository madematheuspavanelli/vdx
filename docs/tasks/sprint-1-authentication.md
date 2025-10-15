# Sprint 1 - Autenticação e Gestão de Usuários

**Duração**: 2 semanas  
**Objetivo**: Implementar sistema completo de autenticação, autorização e gestão básica de usuários

## Metas do Sprint

- ✅ Autenticação JWT funcional
- ✅ Sistema RBAC implementado
- ✅ CRUD de usuários
- ✅ Login, logout, recuperação de senha
- ✅ Proteção de rotas no frontend
- ✅ Testes de integração

---

## US-101: Login com Email e Senha

**Como**: Usuário  
**Quero**: Fazer login com email e senha  
**Para**: Acessar o sistema de forma segura

### Critérios de Aceitação

- [ ] Usuário pode fazer login com credenciais válidas
- [ ] Token JWT é retornado após login bem-sucedido
- [ ] Credenciais inválidas retornam erro apropriado
- [ ] Sessão persiste com refresh token
- [ ] Rate limiting após 5 tentativas falhas

### Tarefas Técnicas - Backend

#### T-101.1: Criar entidade User (Domain)

`apps/backend/src/domain/identity/entities/user.entity.ts`:

```typescript
import { Email } from "../value-objects/email.vo";
import { Password } from "../value-objects/password.vo";

export type UserRole = "admin" | "hr" | "manager" | "employee";

export interface UserProps {
  id: string;
  name: string;
  email: Email;
  passwordHash: string;
  role: UserRole;
  isActive: boolean;
  createdAt: Date;
  updatedAt: Date;
}

export class User {
  private constructor(private props: UserProps) {}

  static create(
    props: Omit<UserProps, "id" | "createdAt" | "updatedAt">
  ): User {
    return new User({
      ...props,
      id: crypto.randomUUID(),
      createdAt: new Date(),
      updatedAt: new Date(),
    });
  }

  static reconstitute(props: UserProps): User {
    return new User(props);
  }

  get id(): string {
    return this.props.id;
  }

  get name(): string {
    return this.props.name;
  }

  get email(): Email {
    return this.props.email;
  }

  get passwordHash(): string {
    return this.props.passwordHash;
  }

  get role(): UserRole {
    return this.props.role;
  }

  get isActive(): boolean {
    return this.props.isActive;
  }

  updatePassword(newPasswordHash: string): void {
    this.props.passwordHash = newPasswordHash;
    this.props.updatedAt = new Date();
  }

  deactivate(): void {
    this.props.isActive = false;
    this.props.updatedAt = new Date();
  }
}
```

#### T-101.2: Criar Value Objects

`apps/backend/src/domain/identity/value-objects/email.vo.ts`:

```typescript
export class Email {
  private readonly value: string;

  private constructor(email: string) {
    this.value = email;
  }

  static create(email: string): Email {
    if (!this.isValid(email)) {
      throw new Error("Invalid email format");
    }
    return new Email(email.toLowerCase().trim());
  }

  private static isValid(email: string): boolean {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return emailRegex.test(email);
  }

  toString(): string {
    return this.value;
  }

  equals(other: Email): boolean {
    return this.value === other.value;
  }
}
```

`apps/backend/src/domain/identity/value-objects/password.vo.ts`:

```typescript
import * as bcrypt from "bcrypt";

export class Password {
  private static readonly SALT_ROUNDS = 10;
  private static readonly MIN_LENGTH = 8;

  static async hash(plainPassword: string): Promise<string> {
    this.validate(plainPassword);
    return bcrypt.hash(plainPassword, this.SALT_ROUNDS);
  }

  static async compare(plainPassword: string, hash: string): Promise<boolean> {
    return bcrypt.compare(plainPassword, hash);
  }

  private static validate(password: string): void {
    if (password.length < this.MIN_LENGTH) {
      throw new Error(
        `Password must be at least ${this.MIN_LENGTH} characters`
      );
    }

    // Adicionar mais validações conforme necessário
    const hasUpperCase = /[A-Z]/.test(password);
    const hasLowerCase = /[a-z]/.test(password);
    const hasNumber = /\d/.test(password);

    if (!hasUpperCase || !hasLowerCase || !hasNumber) {
      throw new Error("Password must contain uppercase, lowercase, and number");
    }
  }
}
```

#### T-101.3: Criar Use Case de Autenticação

`apps/backend/src/application/identity/dtos/authenticate.dto.ts`:

```typescript
export interface AuthenticateDto {
  email: string;
  password: string;
}

export interface AuthenticateResponseDto {
  accessToken: string;
  refreshToken: string;
  expiresIn: number;
  user: {
    id: string;
    name: string;
    email: string;
    role: string;
  };
}
```

`apps/backend/src/application/identity/use-cases/authenticate-user.use-case.ts`:

```typescript
import { Email } from "@/domain/identity/value-objects/email.vo";
import { Password } from "@/domain/identity/value-objects/password.vo";
import { UserRepository } from "../repositories/user.repository.interface";
import { TokenService } from "../services/token.service.interface";
import {
  AuthenticateDto,
  AuthenticateResponseDto,
} from "../dtos/authenticate.dto";

export class AuthenticateUserUseCase {
  constructor(
    private readonly userRepository: UserRepository,
    private readonly tokenService: TokenService
  ) {}

  async execute(dto: AuthenticateDto): Promise<AuthenticateResponseDto> {
    const email = Email.create(dto.email);
    const user = await this.userRepository.findByEmail(email);

    if (!user) {
      throw new Error("Invalid credentials");
    }

    if (!user.isActive) {
      throw new Error("User account is inactive");
    }

    const isPasswordValid = await Password.compare(
      dto.password,
      user.passwordHash
    );
    if (!isPasswordValid) {
      throw new Error("Invalid credentials");
    }

    const accessToken = await this.tokenService.generateAccessToken({
      userId: user.id,
      role: user.role,
    });

    const refreshToken = await this.tokenService.generateRefreshToken({
      userId: user.id,
    });

    return {
      accessToken,
      refreshToken,
      expiresIn: 3600, // 1 hora
      user: {
        id: user.id,
        name: user.name,
        email: user.email.toString(),
        role: user.role,
      },
    };
  }
}
```

#### T-101.4: Implementar Repository Interface e Implementação

`apps/backend/src/application/identity/repositories/user.repository.interface.ts`:

```typescript
import { User } from "@/domain/identity/entities/user.entity";
import { Email } from "@/domain/identity/value-objects/email.vo";

export interface UserRepository {
  findById(id: string): Promise<User | null>;
  findByEmail(email: Email): Promise<User | null>;
  save(user: User): Promise<void>;
  update(user: User): Promise<void>;
  delete(id: string): Promise<void>;
}
```

`apps/backend/src/infrastructure/database/repositories/user.repository.ts`:

```typescript
import { Pool } from "pg";
import { User, UserRole } from "@/domain/identity/entities/user.entity";
import { Email } from "@/domain/identity/value-objects/email.vo";
import { UserRepository } from "@/application/identity/repositories/user.repository.interface";

export class PostgresUserRepository implements UserRepository {
  constructor(private readonly pool: Pool) {}

  async findById(id: string): Promise<User | null> {
    const result = await this.pool.query("SELECT * FROM users WHERE id = $1", [
      id,
    ]);

    if (result.rows.length === 0) {
      return null;
    }

    return this.toDomain(result.rows[0]);
  }

  async findByEmail(email: Email): Promise<User | null> {
    const result = await this.pool.query(
      "SELECT * FROM users WHERE email = $1",
      [email.toString()]
    );

    if (result.rows.length === 0) {
      return null;
    }

    return this.toDomain(result.rows[0]);
  }

  async save(user: User): Promise<void> {
    await this.pool.query(
      `INSERT INTO users (id, name, email, password_hash, role, is_active, created_at, updated_at)
       VALUES ($1, $2, $3, $4, $5, $6, $7, $8)`,
      [
        user.id,
        user.name,
        user.email.toString(),
        user.passwordHash,
        user.role,
        user.isActive,
        new Date(),
        new Date(),
      ]
    );
  }

  async update(user: User): Promise<void> {
    await this.pool.query(
      `UPDATE users SET name = $1, password_hash = $2, role = $3, is_active = $4, updated_at = $5
       WHERE id = $6`,
      [
        user.name,
        user.passwordHash,
        user.role,
        user.isActive,
        new Date(),
        user.id,
      ]
    );
  }

  async delete(id: string): Promise<void> {
    await this.pool.query("UPDATE users SET is_active = false WHERE id = $1", [
      id,
    ]);
  }

  private toDomain(row: any): User {
    return User.reconstitute({
      id: row.id,
      name: row.name,
      email: Email.create(row.email),
      passwordHash: row.password_hash,
      role: row.role as UserRole,
      isActive: row.is_active,
      createdAt: row.created_at,
      updatedAt: row.updated_at,
    });
  }
}
```

#### T-101.5: Implementar Token Service (JWT)

`apps/backend/src/application/identity/services/token.service.interface.ts`:

```typescript
export interface TokenPayload {
  userId: string;
  role?: string;
}

export interface TokenService {
  generateAccessToken(payload: TokenPayload): Promise<string>;
  generateRefreshToken(payload: TokenPayload): Promise<string>;
  verifyAccessToken(token: string): Promise<TokenPayload>;
  verifyRefreshToken(token: string): Promise<TokenPayload>;
}
```

`apps/backend/src/infrastructure/auth/jwt-token.service.ts`:

```typescript
import * as jwt from "jsonwebtoken";
import {
  TokenService,
  TokenPayload,
} from "@/application/identity/services/token.service.interface";

export class JwtTokenService implements TokenService {
  private readonly accessTokenSecret: string;
  private readonly refreshTokenSecret: string;
  private readonly accessTokenExpiry: string = "1h";
  private readonly refreshTokenExpiry: string = "7d";

  constructor() {
    this.accessTokenSecret = process.env.JWT_SECRET || "change-me";
    this.refreshTokenSecret = process.env.JWT_REFRESH_SECRET || "change-me-too";
  }

  async generateAccessToken(payload: TokenPayload): Promise<string> {
    return jwt.sign(payload, this.accessTokenSecret, {
      expiresIn: this.accessTokenExpiry,
    });
  }

  async generateRefreshToken(payload: TokenPayload): Promise<string> {
    return jwt.sign(payload, this.refreshTokenSecret, {
      expiresIn: this.refreshTokenExpiry,
    });
  }

  async verifyAccessToken(token: string): Promise<TokenPayload> {
    try {
      return jwt.verify(token, this.accessTokenSecret) as TokenPayload;
    } catch (error) {
      throw new Error("Invalid or expired token");
    }
  }

  async verifyRefreshToken(token: string): Promise<TokenPayload> {
    try {
      return jwt.verify(token, this.refreshTokenSecret) as TokenPayload;
    } catch (error) {
      throw new Error("Invalid or expired refresh token");
    }
  }
}
```

#### T-101.6: Criar Controller e Rota

`apps/backend/src/presentation/http/controllers/auth.controller.ts`:

```typescript
import { Request, Response } from "express";
import { AuthenticateUserUseCase } from "@/application/identity/use-cases/authenticate-user.use-case";

export class AuthController {
  constructor(
    private readonly authenticateUserUseCase: AuthenticateUserUseCase
  ) {}

  async login(req: Request, res: Response): Promise<void> {
    try {
      const { email, password } = req.body;

      if (!email || !password) {
        res.status(400).json({
          success: false,
          error: {
            code: "VALIDATION_ERROR",
            message: "Email and password are required",
          },
        });
        return;
      }

      const result = await this.authenticateUserUseCase.execute({
        email,
        password,
      });

      res.status(200).json({
        success: true,
        data: result,
      });
    } catch (error: any) {
      if (error.message === "Invalid credentials") {
        res.status(401).json({
          success: false,
          error: {
            code: "INVALID_CREDENTIALS",
            message: "Email or password is incorrect",
          },
        });
        return;
      }

      res.status(500).json({
        success: false,
        error: {
          code: "INTERNAL_ERROR",
          message: "An error occurred",
        },
      });
    }
  }
}
```

`apps/backend/src/presentation/http/routes/auth.routes.ts`:

```typescript
import { Router } from "express";
import { AuthController } from "../controllers/auth.controller";

export function createAuthRoutes(authController: AuthController): Router {
  const router = Router();

  router.post("/login", (req, res) => authController.login(req, res));

  return router;
}
```

#### T-101.7: Migration para tabela users

`apps/backend/src/infrastructure/database/migrations/001_create_users_table.sql`:

```sql
CREATE TABLE IF NOT EXISTS users (
  id UUID PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  role VARCHAR(50) NOT NULL CHECK (role IN ('admin', 'hr', 'manager', 'employee')),
  is_active BOOLEAN DEFAULT true,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_role ON users(role);
CREATE INDEX idx_users_is_active ON users(is_active);
```

### Tarefas Técnicas - Frontend

#### T-101.8: Criar API Client para Autenticação

`apps/frontend/src/shared/api/client.ts`:

```typescript
import axios from "axios";

export const apiClient = axios.create({
  baseURL: import.meta.env.VITE_API_URL || "http://localhost:3000/api/v1",
  headers: {
    "Content-Type": "application/json",
  },
});

// Interceptor para adicionar token
apiClient.interceptors.request.use((config) => {
  const token = localStorage.getItem("accessToken");
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Interceptor para refresh token
apiClient.interceptors.response.use(
  (response) => response,
  async (error) => {
    const originalRequest = error.config;

    if (error.response?.status === 401 && !originalRequest._retry) {
      originalRequest._retry = true;

      try {
        const refreshToken = localStorage.getItem("refreshToken");
        const response = await axios.post("/auth/refresh", { refreshToken });
        const { accessToken } = response.data.data;

        localStorage.setItem("accessToken", accessToken);
        apiClient.defaults.headers.common[
          "Authorization"
        ] = `Bearer ${accessToken}`;

        return apiClient(originalRequest);
      } catch (refreshError) {
        localStorage.removeItem("accessToken");
        localStorage.removeItem("refreshToken");
        window.location.href = "/login";
        return Promise.reject(refreshError);
      }
    }

    return Promise.reject(error);
  }
);
```

#### T-101.9: Criar Store de Autenticação (Pinia)

`apps/frontend/src/features/authentication/model/auth.store.ts`:

```typescript
import { defineStore } from "pinia";
import { ref, computed } from "vue";
import { authApi } from "../api";
import type { User } from "@/entities/user";

export const useAuthStore = defineStore("auth", () => {
  const user = ref<User | null>(null);
  const accessToken = ref<string | null>(localStorage.getItem("accessToken"));
  const isLoading = ref(false);
  const error = ref<string | null>(null);

  const isAuthenticated = computed(() => !!accessToken.value);

  async function login(email: string, password: string) {
    isLoading.value = true;
    error.value = null;

    try {
      const response = await authApi.login({ email, password });

      accessToken.value = response.accessToken;
      user.value = response.user;

      localStorage.setItem("accessToken", response.accessToken);
      localStorage.setItem("refreshToken", response.refreshToken);

      return true;
    } catch (err: any) {
      error.value = err.response?.data?.error?.message || "Login failed";
      return false;
    } finally {
      isLoading.value = false;
    }
  }

  async function logout() {
    accessToken.value = null;
    user.value = null;
    localStorage.removeItem("accessToken");
    localStorage.removeItem("refreshToken");
  }

  return {
    user,
    accessToken,
    isLoading,
    error,
    isAuthenticated,
    login,
    logout,
  };
});
```

#### T-101.10: Criar Página de Login

`apps/frontend/src/pages/login/LoginPage.vue`:

```vue
<script setup lang="ts">
import { ref } from "vue";
import { useRouter } from "vue-router";
import { useAuthStore } from "@/features/authentication/model/auth.store";

const router = useRouter();
const authStore = useAuthStore();

const email = ref("");
const password = ref("");

async function handleSubmit() {
  const success = await authStore.login(email.value, password.value);

  if (success) {
    router.push("/dashboard");
  }
}
</script>

<template>
  <div class="min-h-screen flex items-center justify-center bg-gray-50">
    <div class="max-w-md w-full space-y-8 p-8 bg-white rounded-lg shadow">
      <div>
        <h2 class="text-center text-3xl font-bold text-gray-900">VDX</h2>
        <p class="mt-2 text-center text-sm text-gray-600">
          Faça login para continuar
        </p>
      </div>

      <form @submit.prevent="handleSubmit" class="mt-8 space-y-6">
        <div class="space-y-4">
          <div>
            <label for="email" class="block text-sm font-medium text-gray-700">
              Email
            </label>
            <input
              id="email"
              v-model="email"
              type="email"
              required
              class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500"
            />
          </div>

          <div>
            <label
              for="password"
              class="block text-sm font-medium text-gray-700"
            >
              Senha
            </label>
            <input
              id="password"
              v-model="password"
              type="password"
              required
              class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500"
            />
          </div>
        </div>

        <div v-if="authStore.error" class="text-red-600 text-sm">
          {{ authStore.error }}
        </div>

        <button
          type="submit"
          :disabled="authStore.isLoading"
          class="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-blue-600 hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500 disabled:opacity-50"
        >
          {{ authStore.isLoading ? "Entrando..." : "Entrar" }}
        </button>
      </form>
    </div>
  </div>
</template>
```

### Tarefas de Testes

#### T-101.11: Testes Unitários do Use Case

`apps/backend/tests/unit/authenticate-user.use-case.spec.ts`:

```typescript
import { describe, it, expect, beforeEach, vi } from "vitest";
import { AuthenticateUserUseCase } from "@/application/identity/use-cases/authenticate-user.use-case";
import { User } from "@/domain/identity/entities/user.entity";
import { Email } from "@/domain/identity/value-objects/email.vo";

describe("AuthenticateUserUseCase", () => {
  let useCase: AuthenticateUserUseCase;
  let mockUserRepository: any;
  let mockTokenService: any;

  beforeEach(() => {
    mockUserRepository = {
      findByEmail: vi.fn(),
    };

    mockTokenService = {
      generateAccessToken: vi.fn(),
      generateRefreshToken: vi.fn(),
    };

    useCase = new AuthenticateUserUseCase(mockUserRepository, mockTokenService);
  });

  it("should authenticate user with valid credentials", async () => {
    const user = User.reconstitute({
      id: "123",
      name: "John Doe",
      email: Email.create("john@example.com"),
      passwordHash: await Password.hash("Password123"),
      role: "employee",
      isActive: true,
      createdAt: new Date(),
      updatedAt: new Date(),
    });

    mockUserRepository.findByEmail.mockResolvedValue(user);
    mockTokenService.generateAccessToken.mockResolvedValue("access-token");
    mockTokenService.generateRefreshToken.mockResolvedValue("refresh-token");

    const result = await useCase.execute({
      email: "john@example.com",
      password: "Password123",
    });

    expect(result.accessToken).toBe("access-token");
    expect(result.user.email).toBe("john@example.com");
  });

  it("should throw error for invalid credentials", async () => {
    mockUserRepository.findByEmail.mockResolvedValue(null);

    await expect(
      useCase.execute({
        email: "john@example.com",
        password: "wrong",
      })
    ).rejects.toThrow("Invalid credentials");
  });
});
```

### DoD

- [ ] Login funciona com credenciais válidas
- [ ] Erro apropriado para credenciais inválidas
- [ ] JWT tokens gerados corretamente
- [ ] Frontend armazena tokens
- [ ] Testes unitários > 80% coverage
- [ ] Testes de integração passando

---

**Continuar com US-102 (Logout), US-103 (Recuperação de Senha), US-104 (CRUD Usuários), US-105 (Middleware de Autenticação), US-106 (RBAC)...**

## Checklist Final do Sprint 1

- [ ] Login/Logout funcionais
- [ ] Recuperação de senha
- [ ] CRUD de usuários
- [ ] RBAC implementado
- [ ] Rotas protegidas
- [ ] Testes > 80% coverage
- [ ] Documentação atualizada

**Entregável**: Sistema de autenticação e autorização completo
