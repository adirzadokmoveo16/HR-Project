# Tech Stack

This document defines the technical stack for Vestor Real Estate platform. This serves as a reference for all team members and helps maintain consistency across the project.

## Framework & Runtime

- **Application Framework:** Next.js 15.5.9 (App Router), NestJS 11.1.6
- **Language/Runtime:** TypeScript 5.9.2, Node.js (>=18.0.0)
- **Package Manager:** Yarn 1.22.0

## Frontend

- **JavaScript Framework:** React 19.1.2
- **CSS Framework:** Tailwind CSS 3.4.7
- **UI Components:** Radix UI (accessible primitives), custom UI library (@monorepo-apps/ui)
- **Form Management:** React Hook Form 7.54.2
- **Validation:** Zod 4.1.11
- **Animation:** Framer Motion 11.15.0
- **Icons:** Lucide React 0.544.0
- **Carousel/Slider:** Embla Carousel React 8.3.0
- **Internationalization:** i18next 25.5.2, react-i18next 15.7.3
- **Authentication:** NextAuth.js 4.24.8
- **Theming:** next-themes 0.4.6
- **Maps:** @react-google-maps/api 2.20.7
- **Content Rendering:** @strapi/blocks-react-renderer 1.0.0-beta.0
- **AWS SDK:** @aws-sdk/client-s3 3.400.0, @aws-sdk/s3-request-presigner 3.400.0

## Backend

- **Application Framework:** NestJS 11.1.6
- **ORM:** TypeORM 0.3.20
- **Authentication:** passport 0.7.0, passport-jwt 4.0.1, @nestjs/jwt 11.0.0, @nestjs/passport 11.0.5
- **Validation:** class-validator 0.14.1, class-transformer 0.5.1
- **API Documentation:** @nestjs/swagger 11.2.0
- **Configuration:** @nestjs/config 4.0.2, env-var 7.5.0
- **Health Checks:** @nestjs/terminus 11.0.0
- **Database Integration:** @nestjs/typeorm 11.0.0

## Content Management System

- **CMS Platform:** Strapi 5.23.6
- **Image Processing:** Sharp 0.34.4
- **Media Storage (Development):** MinIO (strapi-provider-upload-ts-minio 3.1.0)
- **Media Storage (Production):** AWS S3 (@strapi/provider-upload-aws-s3 5.6.0)
- **Database Drivers:** PostgreSQL (pg 8.11.3), MySQL (mysql2 3.12.0), SQLite (better-sqlite3 12.3.0)

## Database & Storage

- **Database:** PostgreSQL 8.11.3
- **ORM/Query Builder:** TypeORM 0.3.20
- **Caching:** (To be determined based on performance requirements)

## Testing & Quality

- **Test Framework:** Jest 30.1.3
- **Testing Utilities:** @testing-library/react 16.3.0, @testing-library/jest-dom 6.1.5
- **Test Environment:** jest-environment-jsdom 30.1.2 (frontend), node (backend)
- **Linting:** ESLint 9.36.0
- **Formatting:** Prettier 3.6.2
- **Type Checking:** TypeScript 5.9.2

## Development & Infrastructure

- **Monorepo Management:** Nx 22.0.2
- **Build Tool:** Turbo 2.5.8
- **Version Control:** Git
- **Commit Linting:** @commitlint/cli 19.8.1 (conventional commits)
- **Git Hooks:** Husky 9.1.7
- **Pre-commit:** lint-staged 16.2.0
- **Containerization:** Docker, Docker Compose
- **CI/CD:** Jenkins (Jenkinsfile present in project)

## Third-Party Services & Integrations

- **Blockchain Platform:** DigiShares (enterprise blockchain platform for securities tokenization)
  - Handles: Share issuance, trading, portfolio management, regulatory compliance, financial backend
  - Integration: GraphQL API
- **Blockchain Network:** Ethereum (for token contracts)
- **Cloud Storage:** AWS S3 (production media storage)
- **Authentication:** NextAuth.js (JWT-based, stateless)
- **Email:** (To be determined)
- **Monitoring:** (To be determined)

### Figma Design Reference

- **File Key:** Stored in `FIGMA_FILE_KEY` environment variable
- **URL Format:** `https://www.figma.com/design/<FIGMA_FILE_KEY>/<file-name>`
- **Main Page Node:** `933-20670`
- **Integration:** Official Figma MCP (Model Context Protocol)
- **Workflow:** See `agent-os/standards/global/figma-integration.md` for detailed integration standards

### DigiShares API Reference

- **API URL (Development):** `https://api-vestor-dev.digishares.tech/gql`
- **Protocol:** GraphQL
- **Explorer:** [Apollo Studio Sandbox](https://studio.apollographql.com/sandbox/explorer) with the API URL

**Authentication Flow:**

1. Call `SignIn` mutation with `stoId: 0`, `email`, `password`
2. Response returns JWT token in `data.signIn` field
3. Include token in `Authorization` header as `Bearer <token>` for authenticated requests

**Test Credentials (Development):**

- Email: Stored in `DIGISHARES_TEST_EMAIL` environment variable
- Password: Stored in `DIGISHARES_TEST_PASSWORD` environment variable
- stoId: `0`

**Key Mutations:**

```graphql
# Sign In - Returns JWT token
mutation SignIn($stoId: Int!, $password: String!, $email: String!) {
  signIn(stoID: $stoId, password: $password, email: $email)
}

# Sign Up - Register new user
mutation SignUp($data: SignUpInput!) {
  signUp(data: $data)
}
# SignUpInput: { email, firstName, lastName, password, stoID }
```

**Key Queries (Authenticated):**

```graphql
# Get active properties for investment
query InvestorActiveProperties {
  investorActiveProperties {
    ID
    title
    details
    picture
    projectCost
    createdAt
    totalShares
    availableShare
  }
}
```

**Response Examples:**

SignIn Response:

```json
{
  "data": {
    "signIn": "eyJhbGciOiJIUzI1NiJ9..."
  }
}
```

InvestorActiveProperties Response:

```json
{
  "data": {
    "investorActiveProperties": [
      {
        "ID": 1,
        "title": "test asset - merchant",
        "details": "Premier office condominiums...",
        "picture": "https://storage.googleapis.com/digishares-bucket-eu-vestor/...",
        "projectCost": 0,
        "createdAt": "2026-01-19T15:43:19.000Z",
        "totalShares": 1001000,
        "availableShare": 1000998
      }
    ]
  }
}
```

## Architecture Decisions

1. **Monorepo Structure:** Nx-managed monorepo with shared packages for UI components, configuration, and utilities - enables code sharing and consistent tooling

2. **BFF Pattern:** NestJS backend serves as Backend-for-Frontend, abstracting DigiShares complexity and providing a clean API for the frontend

3. **Server Components:** Next.js 15 App Router with React Server Components for improved performance and SEO

4. **Type Safety:** End-to-end TypeScript with Zod validation ensures runtime safety

5. **Headless CMS:** Strapi provides flexibility for marketing team to update content without developer involvement

6. **JWT Authentication:** Stateless authentication suitable for scalable deployment

7. **GraphQL Integration:** Efficient data fetching from DigiShares with minimal over-fetching

## Shared Packages

- **@monorepo-apps/ui:** Shared UI component library
- **@monorepo-apps/cva:** Component variant utilities
- **@monorepo-apps/eslint-config:** Shared ESLint configuration
- **@monorepo-apps/prettier-config:** Shared Prettier configuration
- **@monorepo-apps/tailwind-config:** Shared Tailwind CSS configuration
- **@monorepo-apps/tsconfig:** Shared TypeScript configuration

## Development Environment Requirements

- **Node.js:** >=18.0.0
- **npm:** >=9.0.0
- **yarn:** >=1.22.0
