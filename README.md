# 🏋️ Yamada Training

A modern API for managing workouts, personal metrics, statistics and an AI-powered trainer.

## 📋 Description

**Yamada Training** is a full-featured REST API built with Fastify and TypeScript. It allows users to:

- Authenticate securely using **Better Auth** (email verification, sessions, linked accounts)
- Create and manage personalized workout plans (7‑day week structure, rest days, cover images)
- Start and update workout sessions with built‑in validation
- Store personal training metrics (weight, height, age, body fat)
- Retrieve home‑screen data (today's workout, streaks, consistency)
- Query detailed statistics (streak, completed workouts, time spent)
- Interact with an **AI personal trainer** that can generate plans automatically

## 🚀 Tech Stack

- **Fastify** – high‑performance HTTP framework
- **TypeScript** – statically‑typed development
- **Prisma** – ORM with PostgreSQL
- **Better Auth** – authentication & authorization
- **Zod** – schema validation
- **Google AI & `ai` SDK** – AI assistant integration
- **Day.js** – date manipulation
- **Docker Compose** – development containers

## 📦 Dependencies

### Runtime

- `@prisma/client`, `@prisma/adapter-pg`
- `fastify`, `@fastify/cors`, `@fastify/swagger`, `@fastify/swagger-ui`
- `@scalar/fastify-api-reference`
- `fastify-type-provider-zod`
- `better-auth`, `dayjs`, `zod`, `dotenv`, `tsx`
- `ai`, `@ai-sdk/google`, `@ai-sdk/openai`
- `pino-pretty`

### Development

- `typescript`, `eslint`, `prettier`, `typescript-eslint`, `prisma`

## ⚙️ Configuration & Installation

### Prerequisites

- Node.js 24.x+
- npm or yarn
- PostgreSQL (Docker or local)
- Git

### Setup steps

1. **Clone the repo**

   ```bash
   git clone https://github.com/YudiYamada/yamada-training-api.git
   cd yamada-training-api
   ```

2. **Install dependencies**

   ```bash
   npm install
   ```

3. **Configure environment**

   ```bash
   cp .env.example .env
   ```

   Update the file with at least:

   ```env
   DATABASE_URL="postgresql://postgres:password@localhost:5432/yamada-training-api"
   BETTER_AUTH_SECRET=your-better-auth-secret
   BETTER_AUTH_URL=http://localhost:8081
   GOOGLE_CLIENT_ID=your-google-client-id
   GOOGLE_CLIENT_SECRET=your-google-client-secret
   GOOGLE_GENERATIVE_AI_API_KEY=your-google-generative-ai-api-key
   WEB_APP_BASE_URL="localhost:3000"
   PORT=8081
   ```

4. **Start services**

   ```bash
   docker-compose up -d
   ```

5. **Run migrations**

   ```bash
   npx prisma migrate dev
   ```

6. **Launch server**

   ```bash
   npm run dev
   ```

The API will be available at `http://localhost:8081`.

---

## 📁 Project Structure

```
yamada-training/
├── src/
│   ├── index.ts               # server entrypoint & route registration
│   ├── lib/                   # shared helpers (auth, db)
│   ├── errors/                # custom error classes
│   ├── schemas/               # Zod validation schemas
│   ├── routes/                # Fastify routes (home, stats, ai, etc.)
│   └── usecases/              # business logic implementations
│   └── generated/prisma/      # Prisma client code
├── prisma/
│   └── schema.prisma          # database models
├── workout-plan.json          # example payload
├── docker-compose.yml
├── eslint.config.js
├── tsconfig.json
├── package.json
└── README.md
```

---

## 💡 Highlights

- Authentication with Better Auth (email verification, sessions, OAuth)
- Comprehensive workout plan management
- Session start/update workflows with validation errors
- Personal statistics (streaks, consistency, time, completion rate)
- Home dashboard endpoint for frontend convenience
- Personal metrics CRUD (`/me` endpoints)
- AI trainer via `/ai` using OpenAI models and custom tools
- Automatic Swagger & Scalar API docs

---

## 📊 Data Model

Entities defined in `prisma/schema.prisma` include:

| Model                        | Description                                      |
| ---------------------------- | ------------------------------------------------ |
| User                         | application user with auth info and metrics      |
| WorkoutPlan                  | named plan containing multiple WorkoutDays       |
| WorkoutDay                   | single day (weekday, rests, exercises, sessions) |
| WorkoutExercise              | exercise details (sets, reps, order, rest time)  |
| WorkoutSession               | record of a performed day (start/completion)     |
| Session/Account/Verification | auth infrastructure models                       |

---

## 🧾 Available Scripts

```bash
# start in dev mode
npm run dev

# prisma tools
toy prisma migrate dev
npx prisma studio
npx prisma generate

# lint & format
npx eslint src/ --fix
npx prettier --write src/
```

---

## 🗂 API Endpoints

Authentication required for all routes except the root health check.

| Method         | Endpoint                                                 | Description                               |
| -------------- | -------------------------------------------------------- | ----------------------------------------- |
| GET            | `/`                                                      | health check                              |
| **Auth proxy** | `/api/auth/*`                                            | Better Auth endpoints (hidden)            |
| GET            | `/home/:date`                                            | home screen data for ISO date             |
| GET            | `/me`                                                    | fetch user training metrics               |
| PUT            | `/me`                                                    | upsert training metrics                   |
| GET            | `/stats?from=&to=`                                       | workout statistics over a date range      |
| GET            | `/workout-plans`                                         | list workout plans (filter `active=true`) |
| POST           | `/workout-plans`                                         | create plan                               |
| GET            | `/workout-plans/:workoutPlanId`                          | retrieve plan                             |
| GET            | `/workout-plans/:workoutPlanId/days/:workoutDayId`       | retrieve specific day                     |
| POST           | `/workout-plans/:planId/days/:dayId/sessions`            | start plan session                        |
| PATCH          | `/workout-plans/:planId/days/:dayId/sessions/:sessionId` | complete/update session                   |
| POST           | `/ai`                                                    | chat with AI personal trainer             |

> **Note:** `/ai` requires `OPENAI_API_KEY` to be set. See `src/routes/ai.ts` for the system prompt and tools.

API docs live at:

- Swagger JSON: `http://localhost:8081/swagger.json`
- Scalar UI: `http://localhost:8081/docs`

---

## 🔐 Authentication

Powered by **Better Auth**. Look for `/api/auth` routes. The Fastify server handles these under the hood in `src/index.ts`.

---

## 🤖 AI Personal Trainer

The `/ai` route opens a streaming conversation with a GPT‑4o‑mini model.
Tools let the model access user metrics, save metrics, list plans and create new plans automatically.
Make sure `OPENAI_API_KEY` is defined before running.

---

## 🐳 Docker Workflows

```
docker-compose up -d      # start Postgres (and others if added)
docker-compose logs -f     # stream logs
docker-compose down        # stop and remove containers
```

---

## 📁 Example Payloads

- `workout-plan.json` contains a sample plan structure used in tests or manual API calls.
- Schemas for request/response live in `src/schemas/index.ts`.

---

## ✅ Tips

- Edit CORS origins in `src/index.ts` if your frontend uses a different host.
- Run `npx prisma studio` to browse and modify the database directly.
- Documentation updates automatically when the server starts.

Enjoy building with Yamada Training! 💪

## 👤 Author

**Yamada Training**

- GitHub: [Yudi Yamada](https://github.com/YudiYamada)
- LinkedIn: [Yudi Yamada](https://www.linkedin.com/in/yudi-yamada-0a10181b9/)
