Here is the professional English version of your README, optimized for GitHub and Notion.

---

# 🏋️ Yamada Training

A robust and modern API for workout plan and routine management, built with Fastify, Prisma, TypeScript, and Better Auth.

## 📋 Description

**Yamada Training** is a professional REST API designed to manage:

- Users and secure authentication (Better Auth)
- Personalized workout plans
- Exercise routines organized by day of the week
- Exercise sets with specific reps and weight tracking
- Email verification system
- Session management and linked accounts

## 🚀 Tech Stack

- **[Fastify](https://www.fastify.io/)** - High-performance HTTP framework
- **[Prisma](https://www.prisma.io/)** - Modern ORM for Node.js and TypeScript
- **[TypeScript](https://www.typescriptlang.org/)** - Static and type-safe development
- **[Better Auth](https://www.better-auth.com/)** - Complete authentication and authorization
- **[Zod](https://zod.dev/)** - TypeScript-first schema validation
- **[PostgreSQL](https://www.postgresql.org/)** - Relational database
- **[Docker Compose](https://docs.docker.com/compose/)** - Containerization

## 📦 Dependencies

### Runtime

- `@prisma/client` - Prisma Client
- `@prisma/adapter-pg` - PostgreSQL adapter for Prisma
- `fastify` - Core web framework
- `@fastify/cors` - CORS middleware
- `@fastify/swagger` - Automatic documentation generator
- `@fastify/swagger-ui` - Swagger UI interface
- `@scalar/fastify-api-reference` - Modern API reference alternative
- `fastify-type-provider-zod` - Fastify + Zod integration
- `better-auth` - Authentication system
- `zod` - Data validation
- `dotenv` - Environment variable management
- `tsx` - TypeScript execution engine

### Development

- `typescript` - TypeScript compiler
- `eslint` - Linting tool
- `prettier` - Code formatter
- `typescript-eslint` - ESLint for TypeScript

## ⚙️ Configuration & Installation

### Prerequisites

- Node.js 24.x or higher
- npm or yarn
- PostgreSQL (or use Docker Compose)
- Git

### Installation

1. **Clone the repository**

```bash
git clone <your-repository-url>
cd yamada-training

```

2. **Install dependencies**

```bash
npm install

```

3. **Configure environment variables**

```bash
cp .env.example .env

```

Edit the `.env` file with your specific settings:

```env
DATABASE_URL=postgresql://user:password@localhost:5432/yamada_training
NODE_ENV=development
# Additional variables as required

```

4. **Initialize the database with Docker**

```bash
docker-compose up -d

```

5. **Run Prisma migrations**

```bash
npx prisma migrate dev

```

6. **Start the development server**

```bash
npm run dev

```

The server will be available at `http://localhost:8081`.

## 📁 Project Structure

```
yamada-training/
├── src/
│   ├── index.ts              # API Entry point
│   ├── lib/
│   │   └── auth.ts           # Auth configuration
│   └── generated/
│       └── prisma/           # Auto-generated code
├── prisma/
│   └── schema.prisma         # Data model definition
├── docker-compose.yml        # Docker configuration
├── eslint.config.js          # ESLint configuration
├── tsconfig.json             # TypeScript configuration
├── package.json              # Project dependencies
└── README.md                 # Project documentation

```

## 📊 Data Model

### Key Entities

- **User** - System user with authentication credentials
- **WorkoutPlan** - Personalized training plan
- **WorkoutDay** - Specific training day within a plan
- **WorkoutExercise** - Exercise details including sets and reps
- **Session** - Active user authentication sessions
- **Account** - Linked user accounts (OAuth/Credentials)
- **Verification** - Email verification tokens

## 🔧 Available Scripts

```bash
# Development
npm run dev                   # Starts the server with hot-reload

# Prisma
npx prisma migrate dev        # Creates and applies migrations
npx prisma studio             # Visual UI for the database
npx prisma generate           # Regenerates Prisma Client

# Linting & Formatting
npx eslint src/               # Checks for linting errors
npx eslint src/ --fix         # Automatically fixes linting errors
npx prettier --check src/     # Checks formatting
npx prettier --write src/     # Formats the code

```

## 📚 API Endpoints

The API is automatically documented via Swagger and Scalar:

- **Scalar Reference:** `http://localhost:8081/docs`
- **Swagger JSON:** `http://localhost:8081/swagger.json`

## 🔐 Authentication

Project powered by **Better Auth** handling:

- User registration and login
- Token generation and validation
- Session management
- Email verification
- Password recovery

## 🌐 CORS

CORS is configured via `@fastify/cors`. Adjust the permitted origins in `src/index.ts` to match your frontend environment.

## 🐳 Docker

Streamline local development using:

```bash
# Start containers
docker-compose up -d

# View logs
docker-compose logs -f

# Stop containers
docker-compose down

```

## 📝 Coding Standards

- **TypeScript Strict Mode** - Rigorous typing required
- **ESLint + Prettier** - Consistent code style
- **Zod Validation** - Strict input/output validation
- **Format on Save** - Recommended VS Code configuration

Editor Setup:

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "always"
  }
}
```

## 🚧 Roadmap / Future Improvements

- [ ] Premium plan purchases
- [ ] Exercise recommendation system
- [ ] Workout history and progress tracking
- [ ] Fitness sensor integration
- [ ] Mobile App (React Native)
- [ ] Unit and E2E Testing

## 🤝 Contributing

1. Create a feature branch (`git checkout -b feature/AmazingFeature`)
2. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
3. Push to the branch (`git push origin feature/AmazingFeature`)
4. Open a Pull Request

## 📜 License

This project is licensed under the ISC License - see the `package.json` file for details.

## 👤 Author

**Yamada Training**

- GitHub: [Yudi Yamada](https://github.com/YudiYamada)
- LinkedIn: [Yudi Yamada](https://www.linkedin.com/in/yudi-yamada-0a10181b9/)

---

**Developed with ❤️ using Fastify + Prisma**
