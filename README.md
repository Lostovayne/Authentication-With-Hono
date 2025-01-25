
# 🔐 API de Autenticación Moderna con Hono + Cloudflare Workers

![Arquitectura del Sistema](https://via.placeholder.com/800x400.png?text=Diagrama+de+Arquitectura+Cloudflare+Workers+%2B+PostgreSQL)
*Figura 1: Diagrama de arquitectura del sistema*

## 🌟 Características Principales
- Autenticación JWT segura
- Integración con PostgreSQL vía Drizzle ORM
- Despliegue serverless en Cloudflare Workers
- Validación de esquemas con Zod
- Tipado fuerte con TypeScript

## 🛠 Prerrequisitos
```bash
npm install
```

## 📦 Dependencias Clave
| Paquete           | Versión   | Función                             |
|-------------------|-----------|-------------------------------------|
| Hono              | ^4.6.18   | Framework web modular               |
| Drizzle ORM       | ^0.29.3   | ORM para PostgreSQL                 |
| Neon.tech         | -         | Plataforma PostgreSQL serverless    |
| Zod               | ^3.24.1   | Validación de datos                 |
| Cloudflare Workers| -         | Entorno de ejecución serverless     |

---

## 📚 Tabla de Contenidos
1. [Configuración del Entorno](#-configuración-del-entorno)
2. [Modelos de Datos](#-modelos-de-datos)
3. [Endpoints de Autenticación](#%EF%B8%8F-endpoints-de-autenticación)
4. [Flujos de Trabajo](#%EF%B8%8F-flujos-de-trabajo)
5. [Ejemplos de Uso](#-ejemplos-de-uso)
6. [Despliegue](#-despliegue)

---

## ⚙️ Configuración del Entorno
### 1. Variables de Entorno (`.dev.vars`)
```env
DATABASE_URL="postgres://user:pass@neon-hostname/project"
JWT_SECRET="supersecretkey123"
```

### 2. Configuración de Drizzle ORM
```typescript
// src/db/schema.ts
import { pgTable, varchar, timestamp } from 'drizzle-orm/pg-core';

export const users = pgTable('users', {
  id: varchar('id').primaryKey(),
  email: varchar('email').unique().notNull(),
  password_hash: varchar('password_hash').notNull(),
  reset_token: varchar('reset_token'),
  created_at: timestamp('created_at').defaultNow(),
});
```

---

## 🗃️ Modelos de Datos
### Esquema de Usuario
```ts
interface User {
  id: string;
  email: string;
  password_hash: string;
  reset_token?: string;
  created_at: Date;
}
```

### Esquemas de Validación (Zod)
```ts
// src/schemas/auth.ts
import { z } from 'zod';

export const RegisterSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
});

export const LoginSchema = RegisterSchema.omit({});
```

---

## ⚡️ Endpoints de Autenticación

### 🔑 Autenticación
| Método | Endpoint       | Descripción                  |
|--------|----------------|------------------------------|
| POST   | /auth/register | Registro de nuevo usuario    |
| POST   | /auth/login    | Inicio de sesión             |
| POST   | /auth/reset    | Solicitud de reset de contraseña |
| PATCH  | /auth/reset    | Confirmación de nuevo password |

---

### 📥 Respuestas API
#### Registro Exitoso (201)
```json
{
  "success": true,
  "data": {
    "id": "usr_123",
    "email": "usuario@ejemplo.com"
  }
}
```

#### Error de Validación (400)
```json
{
  "error": "VALIDATION_ERROR",
  "details": ["Email inválido"]
}
```

---

## 🔄 Flujos de Trabajo
```mermaid
sequenceDiagram
    participant Client
    participant Worker
    participant PostgreSQL

    Client->>Worker: POST /auth/login
    Worker->>PostgreSQL: Verificar credenciales
    PostgreSQL-->>Worker: Datos de usuario
    Worker->>Client: JWT Token (200 OK)
```

---

## 💻 Ejemplos de Uso
### 1. Registro de Usuario
```bash
curl -X POST https://api.example.com/auth/register \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","password":"SecurePass123!"}'
```

### 2. Recuperación de Contraseña
```ts
// Ejemplo en TypeScript
await fetch('/auth/reset', {
  method: 'POST',
  body: JSON.stringify({ email: 'user@example.com' })
});
```

---

## 🚀 Despliegue
```bash
npm run deploy
```

### Configuración de Wrangler
```toml
# wrangler.toml
[vars]
DATABASE_URL = "postgres://${NEON_USER}:${NEON_PASSWORD}@${NEON_HOST}/${NEON_DB}"

[[d1_databases]]
binding = "DB"
database_name = "auth_db"
database_id = "xxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
```

---

## 🛡️ Consideraciones de Seguridad
1. **HTTPS Obligatorio**: Todas las comunicaciones deben usar TLS
2. **Rate Limiting**: Configurar límites de peticiones en Cloudflare
3. **Auditoría de Logs**:
   ```bash
   wrangler tail
   ```

[🔗 Documentación Oficial Cloudflare](https://developers.cloudflare.com/workers) |
[📘 Guía Drizzle ORM](https://orm.drizzle.team/docs/quick-postgresql)
