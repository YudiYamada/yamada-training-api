# Master Guide: Fastify API + Prisma + Better Auth

This document explains, step by step, how the **Yamada Training** backend is built and how to get it running today. It is written in phases that mirror the order in which the codebase was originally created.

---

## Phase 1 – Project Foundation & Tooling

_Establish the language, style and basic workflows used across the repository._

1. **Initialize repository**

   ```bash
   mkdir yamada-training && cd yamada-training
   git init
   npm init -y
   ```

2. **TypeScript**

   ```bash
   npm install typescript @types/node -D
   npx tsc --init
   ```

   Update `tsconfig.json`:

   ```json
   {
     "compilerOptions": {
       "target": "ES2022",
       "module": "NodeNext",
       "moduleResolution": "NodeNext",
       "rootDir": "src",
       "outDir": "dist",
       "esModuleInterop": true,
       "strict": true,
       "skipLibCheck": true
     }
   }
   ```

3. **Lock Node version & helper script**

   Add to `package.json`:

   ```json
   "engines": { "node": "24.x" },
   "scripts": {
     "dev": "tsx --watch src/index.ts"
   }
   ```

   And create `.npmrc` with `engine-strict=true`.

4. **Linting & formatting**

   ```bash
   npm install eslint prettier eslint-config-prettier eslint-plugin-simple-import-sort -D
   ```

   Recommended VS Code settings (`.vscode/settings.json`):

   ```json
   {
     "editor.formatOnSave": true,
     "editor.defaultFormatter": "esbenp.prettier-vscode",
     "editor.codeActionsOnSave": { "source.fixAll.eslint": "always" }
   }
   ```

5. **Additional dev utilities**
   - `prisma` CLI (used for migrations)
   - `@types/*` packages whenever necessary

---

## Phase 2 – Server & Validation

_Bring up Fastify and ensure typed request/response handling._

1. **Install runtime dependencies**

   ```bash
   npm install fastify dotenv zod fastify-type-provider-zod
   ```

2. **Create entrypoint `src/index.ts`**

   ```ts
   import "dotenv/config";

   import fastifyCors from "@fastify/cors";
   import fastifySwagger from "@fastify/swagger";
   import fastifyApiReference from "@scalar/fastify-api-reference";
   import Fastify from "fastify";
   import {
     jsonSchemaTransform,
     serializerCompiler,
     validatorCompiler,
     ZodTypeProvider,
   } from "fastify-type-provider-zod";

   import { auth } from "./lib/auth.js";
   import { env } from "./lib/env.js";
   import { aiRoutes } from "./routes/ai.js";
   import { homeRoutes } from "./routes/home.js";
   import { meRoutes } from "./routes/me.js";
   import { statsRoutes } from "./routes/stats.js";
   import { workoutPlanRoutes } from "./routes/workout-plan.js";

   const app = Fastify({ logger: true });
   app.setValidatorCompiler(validatorCompiler);
   app.setSerializerCompiler(serializerCompiler);

   // later: register routes, cors, swagger, etc.

   await app.listen({ port: Number(env.PORT) || 8081 });
   ```

3. **Swagger & Scalar**

   ```bash
   npm install @fastify/swagger @scalar/fastify-api-reference
   ```

   Register them in `src/index.ts` to expose `/swagger.json` and `/docs`.

   These tools read the Zod schemas you attach to each route and generate interactive documentation automatically.

---

## Phase 3 – Authentication & CORS

1. **Better Auth configuration**

   ```bash
   npm install better-auth @prisma/adapter-pg
   ```

   In `src/lib/auth.ts`:

   ```ts
   import { betterAuth } from "better-auth";
   import { prismaAdapter } from "better-auth/adapters/prisma";
   import { openAPI } from "better-auth/plugins";

   import { prisma } from "./db.js";
   import { env } from "./env.js";

   export const auth = betterAuth({
     baseURL: env.API_BASE_URL,
     trustedOrigins: [env.WEB_APP_BASE_URL],
     socialProviders: {
       google: {
         prompt: "select_account",
         clientId: env.GOOGLE_CLIENT_ID,
         clientSecret: env.GOOGLE_CLIENT_SECRET,
       },
     },
     database: prismaAdapter(prisma, {
       provider: "postgresql",
     }),
     plugins: [openAPI()],
   });
   ```

2. **CORS**

   ```bash
   npm install @fastify/cors
   ```

   Configure origins in `src/index.ts` using `WEB_APP_BASE_URL` (default `http://localhost:8081`).

3. **Proxy auth routes**

   Add a wildcard route that forwards requests to `auth.handler` (see `src/index.ts`).

---

## Phase 4 – Database with Prisma

1. **Docker & Postgres**

   Use `docker-compose.yml` with a Postgres service (`postgres:16-alpine`) and environment variables.

2. **Prisma initialization**

   ```bash
   npm install prisma -D
   npx prisma init --datasource-provider postgresql
   ```

   Edit `prisma/schema.prisma` with the models shown in the repository (User, WorkoutPlan, etc.).

3. **Migrations & client**

   ```bash
   npx prisma migrate dev --name init
   npx prisma generate
   ```

   From this point, run `prisma migrate dev` whenever you change the schema.

   Use `npx prisma studio` to inspect data.

---

## Phase 5 – Business Logic & Routes

1. **Schemas**

   Create Zod schemas in `src/schemas/index.ts` for every request/response shape. They are reused by routes and docs.

2. **Usecases**

   Implement the application logic in `src/usecases/*` classes (e.g. `CreateWorkoutPlan`, `GetStats`). They interact with `src/lib/db.ts`.

3. **Routes**

   Define Fastify route files under `src/routes/`:
   - `home.ts` – `/home/:date`
   - `me.ts` – `/me` endpoints
   - `stats.ts` – `/stats` with query parameters
   - `workout-plan.ts` – CRUD for plans, days and sessions
   - `ai.ts` – chat with AI trainer (see next phase)

   Each route validates input with Zod, checks authentication via Better Auth, calls a usecase, and handles errors.

4. **Error classes**

   Shared errors (e.g. `NotFoundError`) live in `src/errors/index.ts` and are translated to HTTP status codes in route handlers.

---

## Phase 6 – AI Personal Trainer

1. **Install AI libraries**

   ```bash
   npm install ai @ai-sdk/google
   ```

   (Optional) If you want to use OpenAI instead of Google Gemini, add `@ai-sdk/openai`.

2. **Environment**

   Copy `.env.example` to `.env` and fill the required values. Key vars include:
   - `PORT` (default 8081)
   - `DATABASE_URL` (Postgres connection string)
   - `BETTER_AUTH_SECRET` (session signing secret)
   - `API_BASE_URL` (API base URL, used by Better Auth)
   - `WEB_APP_BASE_URL` (frontend URL, used for CORS and auth callbacks)
   - `GOOGLE_CLIENT_ID` / `GOOGLE_CLIENT_SECRET` (for OAuth login)
   - `GOOGLE_GENERATIVE_AI_API_KEY` (used by the AI endpoint)

   Optional:
   - `OPENAI_API_KEY` (if you want to use OpenAI instead of Google Gemini)

3. **System prompt & tools**

   The `/ai` route (see `src/routes/ai.ts`) defines a long system prompt describing personality and rules. It also exposes four tools:
   - `getUserTrainData`
   - `updateUserTrainData`
   - `getWorkoutPlans`
   - `createWorkoutPlan`

   Tools call the corresponding usecase classes.

4. **Streaming response**

   The endpoint uses `streamText` from the `ai` SDK to send a chunked response.

---

## Phase 7 – Running the Application

1. **Start Postgres**

   ```bash
   docker-compose up -d
   ```

2. **Apply migrations**

   ```bash
   npx prisma migrate dev
   ```

3. **Run the server**

   ```bash
   npm run dev
   ```

4. **Access docs**
   - Swagger JSON: `http://localhost:8081/swagger.json`
   - Scalar UI: `http://localhost:8081/docs`

5. **Health check**

   Visit `http://localhost:8081/` to see the simple “Hello World” response.

---

## Miscellaneous Notes

- **CORS origin** is driven by `WEB_APP_BASE_URL` (default `http://localhost:8081`).
- **Auth base URL** is driven by `API_BASE_URL` (default `http://localhost:8081`).
- **Data seeding** may be done manually via `npx prisma studio` or by writing a script using Prisma Client.
- **Testing** is not included yet; consider adding Jest or Vitest for unit/e2e tests.
- **Roadmap**: premium plans, progress tracking, sensor integration, mobile app.

---

**This guide tracks the current state of the repository (March 2026).**
