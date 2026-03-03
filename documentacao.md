# Master Guide: Fastify API + Prisma + Better Auth

This is a comprehensive step-by-step documentation for creating this professional API.

---

## Phase 1: Initialization and Code Standardization

*The goal here is to ensure clean code and guarantee that all developers follow the same standards from the very first minute.*

**1. Initialize the project and base dependencies**

```bash
npm init -y 

```

and also:

```bash
npm add typescript @types/node -D

```

> Node doesn't understand TypeScript natively. We need the compiler (`typescript`) and type definitions (`@types/node`) to enable autocomplete and avoid basic typing errors.

**2. TypeScript Configuration (`tsconfig.json`)**

Run:

```bash
npx tsc --init 

```

And configure the file as follows:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext", // Essential for running ESM in Node 24
    "rootDir": "src",
    "outDir": "dist",
    "esModuleInterop": true,
    "strict": true,
    "skipLibCheck": true
  }
}

```

> Using `NodeNext` ensures the project supports ECMAScript Modules (ESM) natively. Without this, you would face constant "module not found" issues when using modern libraries.

**3. Locking the Node Version (Consistency)**

Add the following to `package.json` and create a `.npmrc` file:

```json
// package.json
"engines": { "node": "24.x" },
"scripts": { "dev": "tsx --watch src/index.ts" }

```

```text
// .npmrc
engine-strict=true

```

> This avoids the "it works on my machine" syndrome. If another dev tries to run the project with an outdated Node version, NPM will block the execution. The `tsx` tool in the `dev` script allows running `.ts` files with hot-reload without manual builds.

**4. Setting up Linting and Formatting (ESLint + Prettier)**

```bash
npm add prettier eslint eslint-config-prettier eslint-plugin-simple-import-sort -D

```

> ESLint catches logical errors, while Prettier handles aesthetics (semicolons, quotes). The `import-sort` plugin automatically keeps imports organized, preventing merge conflicts in Git.

**5. Editor Integration and Automation**

Create the `.vscode/settings.json` file:

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": { "source.fixAll.eslint": "always" }
}

```

> Automation is key. If you have to format code manually, you will eventually forget. Let the editor do it for you every time the file is saved.

---

## Phase 2: API Core and Validation

*Here we spin up the server and ensure that incoming data is reliable.*

**6. Install Fastify and Environment Variables**

```bash
npm i fastify dotenv 

```

And create a `.env` file with:

```text
PORT=8081

```

> Fastify is one of the fastest frameworks on the market. We use `dotenv` to manage secrets (like database passwords) outside the source code for security reasons.

**7. Server Boilerplate Setup (`src/index.ts`)**

```typescript
import "dotenv/config";
import Fastify from "fastify";

const app = Fastify({ logger: true });

app.listen({ port: Number(process.env.PORT) || 8081 }, (err) => {
  if (err) { app.log.error(err); process.exit(1); }
});

```

> Environment variables are always strings. Always convert to `Number` and define a fallback (`|| 8081`) to ensure the server starts even in environments without a port configuration.

**8. Adding Typing and Validation with Zod**

```bash
npm add zod fastify-type-provider-zod

```

```typescript
import { serializerCompiler, validatorCompiler, ZodTypeProvider } from "fastify-type-provider-zod";

const app = Fastify({ logger: true }).withTypeProvider<ZodTypeProvider>();
app.setValidatorCompiler(validatorCompiler);
app.setSerializerCompiler(serializerCompiler);

```

> Zod validates data at runtime (preventing "garbage" data from reaching the database). The `type-provider` integrates this into TypeScript, providing full autocomplete in routes without requiring manual interfaces.

---

## Phase 3: Automatic Documentation

*An API without documentation is an API that nobody knows how to use.*

**9. Configure Swagger**

```bash
npm add @fastify/swagger

```

> Swagger reads your Zod routes and schemas to automatically generate a technical JSON file that describes everything your API does.

**10. Configure Scalar (Doc Interface)**

```bash
npm add @scalar/fastify-api-reference

```

```typescript
await app.register(fastifyApiReference, {
  routePrefix: "/docs",
  configuration: {
    sources: [{ title: "API Doc", url: "/swagger.json" }]
  }
});

```

> Scalar is a modern and much more aesthetic interface than the default Swagger UI. It allows you to test routes and view request examples intuitively.

---

## Phase 4: Data Layer (Persistence)

*Configuring how the API communicates with the database.*

**11. Infrastructure with Docker (`docker-compose.yml`)**

```yaml
services:
  postgres:
    image: postgres:16-alpine
    container_name: yamada-api-db
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
      POSTGRES_DB: yamada-db
    ports: ["5432:5432"]

```

> Using Docker prevents you from needing to install the database directly on your OS, facilitating project setup on any machine with a single command.

**12. Initialize Prisma ORM**

```bash
npm add prisma -D 

```

and also:

```bash
npm add @prisma/client @prisma/adapter-pg

```

Run:

```bash
npx prisma init

```

> Prisma is an ORM that generates TypeScript types based on your database. The `adapter-pg` ensures top performance with native PostgreSQL drivers.

**13. Database Sync**

Run:

```bash
npx prisma db push

```

> In early development, `db push` is much faster than creating Migrations for every small field change, allowing for rapid schema iteration.

**14. Generate Prisma Client**

Run:

```bash
npx prisma generate

```

> This updates the Prisma "brain." Whenever you change the database schema, run this command so TypeScript recognizes the new tables and columns.

---

## Phase 5: Authentication and Security

*Protecting the API and allowing user login.*

**15. Configure Better-Auth (`src/lib/auth.ts`)**

```typescript
export const auth = betterAuth({
  database: prismaAdapter(prisma, { provider: "postgresql" }),
  emailAndPassword: { enabled: true },
  plugins: [openAPI()] // Automatically documents auth routes
});

```

> Delegating authentication to a specialized library prevents critical security flaws. The `openAPI` plugin integrates login routes directly into your Scalar/Swagger UI.

**16. Enable CORS**

```bash
npm i @fastify/cors

```

```typescript
await app.register(fastifyCors, {
  origin: ["http://localhost:3000"],
  credentials: true,
});

```

> Browsers block requests from different domains by default. CORS allows your Frontend (e.g., React) to communicate with your Backend.

**17. Create Auth Proxy Route**

```typescript
app.route({
  method: ["GET", "POST"],
  url: "/api/auth/*",
  async handler(request, reply) {
    const response = await auth.handler(request.raw);
    return reply.send(response);
  },
});

```

> This route centralizes all login/signup calls, forwarding them to the Better-Auth engine without cluttering your main route files.

---

## Phase 6: Finalization and Fine-Tuning

**18. Generate Auth Schemas in Docs**

Reference the Better-Auth schema in your Scalar registration.

> This ensures that anyone reading the documentation knows exactly how to log in without you needing to write extra manuals.

**19. Create `.gitignore**`

Add `node_modules`, `.env`, and `/dist`.

> Basic security. You should never send heavy dependencies or secret keys to version control (GitHub).

**20. Test the full flow**

Start the server and access `/docs`.

> The final test validates that the database is up via Docker, Prisma has connected, and the documentation is live. If "Hello World" appears in Scalar, you are ready to start coding real routes.

---