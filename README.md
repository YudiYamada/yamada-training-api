# 🏋️ Yamada Training

Uma API robusta e moderna para gerenciamento de planos e rotinas de treino, construída com Fastify, Prisma, TypeScript e Better Auth.

![Node Version](https://img.shields.io/badge/node-24.x-green)
![TypeScript](https://img.shields.io/badge/TypeScript-5.9+-blue)
![License](https://img.shields.io/badge/license-ISC-blue)

## 📋 Descrição

**Yamada Training** é uma API REST profissional para gerenciar:

- Usuários e autenticação segura (Better Auth)
- Planos de treino personalizados
- Rotinas de exercícios por dia da semana
- Conjunto de exercícios com repetições e séries
- Sistema de verificação de email
- Gerenciamento de sessões e contas vinculadas

## 🚀 Tecnologias Utilizadas

- **[Fastify](https://www.fastify.io/)** - Framework HTTP de alta performance
- **[Prisma](https://www.prisma.io/)** - ORM modern para Node.js e TypeScript
- **[TypeScript](https://www.typescriptlang.org/)** - Tipagem estática e segura
- **[Better Auth](https://www.better-auth.com/)** - Autenticação e autorização
- **[Zod](https://zod.dev/)** - Validação de esquemas TypeScript-first
- **[PostgreSQL](https://www.postgresql.org/)** - Banco de dados relacional
- **[Docker Compose](https://docs.docker.com/compose/)** - Containerização

## 📦 Dependências

### Runtime

- `@prisma/client` - Cliente Prisma
- `@prisma/adapter-pg` - Adapter PostgreSQL para Prisma
- `fastify` - Framework web
- `@fastify/cors` - CORS middleware
- `@fastify/swagger` - Documentação automática
- `@fastify/swagger-ui` - UI para Swagger
- `@scalar/fastify-api-reference` - Referência alternativa de API
- `fastify-type-provider-zod` - Integração Fastify + Zod
- `better-auth` - Sistema de autenticação
- `zod` - Validação de dados
- `dotenv` - Variáveis de ambiente
- `tsx` - Execução de TypeScript

### Desenvolvimento

- `typescript` - Compilador TypeScript
- `eslint` - Linting
- `prettier` - Formatação de código
- `typescript-eslint` - ESLint para TypeScript

## ⚙️ Configuração e Instalação

### Pré-requisitos

- Node.js 24.x ou superior
- npm ou yarn
- PostgreSQL (ou use Docker Compose)
- Git

### Instalação

1. **Clone o repositório**

```bash
git clone <seu-repositorio>
cd yamada-training
```

2. **Instale as dependências**

```bash
npm install
```

3. **Configure as variáveis de ambiente**

```bash
cp .env.example .env
```

Edite o arquivo `.env` com suas configurações:

```env
DATABASE_URL=postgresql://user:password@localhost:5432/yamada_training
NODE_ENV=development
# Outras variáveis conforme necessário
```

4. **Inicialize o banco de dados com Docker**

```bash
docker-compose up -d
```

5. **Execute as migrações do Prisma**

```bash
npx prisma migrate dev
```

6. **Inicie o servidor em desenvolvimento**

```bash
npm run dev
```

O servidor estará disponível em `http://localhost:3000`.

## 📁 Estrutura do Projeto

```
yamada-training/
├── src/
│   ├── index.ts              # Ponto de entrada da API
│   ├── lib/
│   │   └── auth.ts           # Configuração de autenticação
│   └── generated/
│       └── prisma/           # Código gerado automaticamente
├── prisma/
│   └── schema.prisma         # Definição do modelo de dados
├── docker-compose.yml        # Configuração Docker
├── eslint.config.js          # Configuração ESLint
├── tsconfig.json             # Configuração TypeScript
├── package.json              # Dependências do projeto
└── README.md                 # Este arquivo
```

## 📊 Modelo de Dados

### Principais Entidades

- **User** - Usuário do sistema com autenticação
- **WorkoutPlan** - Plano de treino personalizado
- **WorkoutDay** - Dia específico de treino dentro de um plano
- **WorkoutExercise** - Exercício específico com séries/repetições
- **Session** - Sessão de autenticação do usuário
- **Account** - Contas vinculadas ao usuário
- **Verification** - Tokens de verificação de email

## 🔧 Scripts Disponíveis

```bash
# Desenvolvimento
npm run dev                   # Inicia o servidor com hot-reload

# Prisma
npx prisma migrate dev        # Cria migrações
npx prisma studio             # UI visual para o banco de dados
npx prisma generate           # Regenera o cliente Prisma

# Linting
npx eslint src/               # Verifica linting
npx eslint src/ --fix         # Corrige erros automáticos
npx prettier --check src/     # Verifica formatação
npx prettier --write src/     # Formata o código
```

## 📚 API Endpoints (Exemplos)

A API é documentada automaticamente via Swagger:

- UI Swagger: `http://localhost:3000/swagger/`
- Scalar Reference: `http://localhost:3000/reference`

## 🔐 Autenticação

O projeto utiliza **Better Auth** para gerenciar:

- Registro e login de usuários
- Geração e validação de tokens
- Gerenciamento de sessões
- Verificação de email
- Recuperação de senha

## 🌐 CORS

CORS está configurado via `@fastify/cors`. Ajuste as configurações conforme necessário em `src/index.ts`.

## 🐳 Docker

Para facilitar o desenvolvimento local:

```bash
# Inicia os containers
docker-compose up -d

# Acessa os logs
docker-compose logs -f

# Para os containers
docker-compose down
```

## 📝 Padrões de Código

- **TypeScript Strict Mode** - Tipagem rigorosa obrigatória
- **ESLint + Prettier** - Estilos consistentes
- **Validação com Zod** - Validação de entrada/saída
- **Formatação automática** - Ao salvar no VS Code

Configure seu editor:

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": { "source.fixAll.eslint": "always" }
}
```

## 🚧 Melhorias Futuras

- [ ] Compra de planos premium
- [ ] Sistema de recomendações de exercícios
- [ ] Histórico e progresso do treino
- [ ] Integração com sensores fitness
- [ ] App mobile com React Native
- [ ] Testes unitários e E2E

## 📄 Documentação

Para mais detalhes sobre a arquitetura e configuração, consulte [documentacao.md](documentacao.md).

## 🤝 Contribuindo

1. Crie uma branch para sua feature (`git checkout -b feature/AmazingFeature`)
2. Commit suas mudanças (`git commit -m 'Add some AmazingFeature'`)
3. Push para a branch (`git push origin feature/AmazingFeature`)
4. Abra um Pull Request

## 📜 Licença

Este projeto está licenciado sob a Licença ISC - veja o arquivo `package.json` para detalhes.

## 👤 Autor

**Yamada Training**

- GitHub: [Yudi Yamada](https://github.com/YudiYamada)
- LinkedIn: [Yudi Yamada] (https://www.linkedin.com/in/yudi-yamada-0a10181b9/)

## 💬 Suporte

Se você tiver dúvidas or problemas, abra uma [Issue](../../issues) no repositório.

---

**Desenvolvido com ❤️ usando Fastify + Prisma**
