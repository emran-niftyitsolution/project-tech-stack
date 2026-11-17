# project-tech-stack

The standard tech stack an **AI coding agent** must use for **backend** (NestJS), **frontend** (React) and **mobile** (Expo) apps.

> **Agent rule:** use only the tools listed here. Where a row says "A **or** B", pick one per app and never mix them. If the choice isn't clear, ask the user. Don't add anything outside this list without asking.

Versions are the latest on npm as of 2026-11.

---

## Shared tooling (all projects)

| Concern            | Choice                      | Version        | Notes                                                    |
| ------------------ | --------------------------- | -------------- | -------------------------------------------------------- |
| Language           | TypeScript (`strict`)       |                |                                                          |
| Package manager    | pnpm                        | 12.21.0        | Never npm or yarn                                        |
| Monorepo           | Turborepo + pnpm workspaces | 2.11.11        | Only for more than one app/package                       |
| Lint               | oxlint                      | 1.94.0         | No ESLint, no Biome                                      |
| Format             | oxfmt                       | 0.79.0         | No Prettier, no Biome                                    |
| Git hooks          | husky + lint-staged         | 9.1.7 / 17.6.1 | Lint and format staged files                             |
| Dependency updates | Dependabot                  |                | npm + github-actions, weekly                             |
| Validation (TS)    | zod                         | 4.6.7          | Frontend and mobile; shared schemas in `packages/shared` |
| Dates              | dayjs (`utc`, `timezone`)   | 1.11.23        | Frontend and mobile                                      |

Monorepo layout: `apps/api`, `apps/web`, `apps/mobile`, `packages/shared`.

---

## Backend

| Concern        | Choice                                                      | Version               | When / notes                                                     |
| -------------- | ----------------------------------------------------------- | --------------------- | ---------------------------------------------------------------- |
| Runtime        | Node.js (active LTS)                                        |                       |                                                                  |
| Framework      | NestJS                                                      | 12.5.1                |                                                                  |
| Validation     | class-validator + class-transformer                         | 0.15.1 / 0.5.1        | Global `ValidationPipe`, DTOs for all input                      |
| Config         | @nestjs/config                                              | 12.1.1                | Env validated at startup                                         |
| Database / ORM | Mongoose **or** Drizzle                                     | 9.12.2 / 0.45.4       | Mongoose for MongoDB; Drizzle for PostgreSQL/MySQL               |
| Migrations     | ts-migrate-mongoose **or** drizzle-kit                      | 5.3.3 / 0.31.11       | Matches the ORM                                                  |
| Tests          | Vitest + unplugin-swc + supertest                           | 5.1.0 / 2.0.0 / 7.3.2 | SWC needed for decorator metadata                                |
| DB in tests    | mongodb-memory-server **or** @testcontainers/postgresql     | 11.3.0 / 12.2.1       | Matches the ORM                                                  |
| File storage   | Cloudflare R2 via @aws-sdk/client-s3 + s3-request-presigner | 3.1170.0              | S3-compatible; presigned URLs                                    |
| SMS            | SMS BD                                                      |                       | HTTP API behind an `SmsService`                                  |
| Email          | Resend **or** AWS SES (@aws-sdk/client-sesv2)               | 6.38.1 / 3.1170.0     | Resend by default; SES on AWS or high volume. One `EmailService` |
| Rate limiting  | @nestjs/throttler                                           | 6.8.0                 | OTP and auth endpoints                                           |

---

## Frontend

| Concern        | Choice                                      | Version                 | When / notes                                              |
| -------------- | ------------------------------------------- | ----------------------- | --------------------------------------------------------- |
| UI library     | React                                       | 19.3.1                  |                                                           |
| Build          | Vite + @vitejs/plugin-react                 | 8.3.8 / 6.3.0           |                                                           |
| Routing / app  | TanStack Router **or** TanStack Start       | 1.170.46 / 1.169.5      | Router for SPAs behind login (default); Start for SSR/SEO |
| UI components  | ReUI (on shadcn/ui + Radix)                 | shadcn 4.23.0           | Only UI kit; no Ant Design/RSuite/MUI                     |
| Styling        | Tailwind CSS v4 (@tailwindcss/vite)         | 4.3.5                   | + class-variance-authority, clsx, tailwind-merge          |
| Forms          | React Hook Form + @hookform/resolvers + zod | 7.92.0 / 7.0.7          |                                                           |
| State          | Zustand **or** Redux Toolkit                | 5.1.0 / 2.14.0          | Zustand for small/medium apps; RTK for large apps         |
| Server data    | TanStack Query **or** RTK Query             | 5.107.2 / (in RTK)      | TanStack Query with Zustand; RTK Query with RTK           |
| API            | REST to the NestJS backend                  |                         | Apollo + GraphQL codegen only if the backend has GraphQL  |
| Errors         | Sentry (@sentry/react)                      | 11.13.0                 |                                                           |
| Icons / charts | react-icons / ECharts                       | 5.7.0 / 6.1.0           | Only when needed                                          |
| Markdown       | react-markdown + rehype-sanitize            |                         | No `dangerouslySetInnerHTML`                              |
| Tests          | Vitest + Testing Library + jsdom            | 5.1.0 / 16.3.3 / 31.0.1 | Node for `*.test.ts`, jsdom for `*.test.tsx`              |

Structure follows the portal: feature folders in `src/features/<feature>/`, file-based routes in `src/routes/`, path aliases (`@features`, `@common`, `@components`, `@store`).

---

## Mobile

| Concern              | Choice                                                 | Version            | When / notes                                              |
| -------------------- | ------------------------------------------------------ | ------------------ | --------------------------------------------------------- |
| Framework            | Expo (SDK 58) + React Native                           | 58.0.25 / 0.88.1   | Development builds (expo-dev-client)                      |
| Routing              | Expo Router                                            | 58.0.31            | File-based, typed routes                                  |
| Styling              | Uniwind + Tailwind CSS v4                              | 1.13.2 / 4.3.5     | No NativeWind                                             |
| UI components        | HeroUI Native                                          | 1.0.11             | Built on Uniwind; + tailwind-variants, tailwind-merge     |
| Forms                | React Hook Form + @hookform/resolvers + zod            | 7.92.0 / 7.0.7     | Same as frontend                                          |
| State                | Zustand **or** Redux Toolkit                           | 5.1.0 / 2.14.0     | Same rule as frontend                                     |
| Server data          | TanStack Query **or** RTK Query                        | 5.107.2 / (in RTK) | Same rule as frontend                                     |
| Secure storage       | expo-secure-store                                      | 58.0.3             | Tokens only                                               |
| Fast storage         | react-native-mmkv                                      | 4.3.2              | Persisted state and cache                                 |
| Lists                | @shopify/flash-list                                    | 2.3.3              | Long lists                                                |
| Images               | expo-image                                             | 58.0.18            |                                                           |
| Animation / gestures | react-native-reanimated + react-native-gesture-handler | 4.7.1 / 3.5.0      |                                                           |
| Bottom sheet         | @gorhom/bottom-sheet                                   |                    | Required by HeroUI Native                                 |
| Keyboard             | react-native-keyboard-controller                       | 1.23.1             |                                                           |
| Safe area            | react-native-safe-area-context                         | 5.11.1             |                                                           |
| Icons                | @expo/vector-icons                                     | 15.1.1             |                                                           |
| Push                 | expo-notifications                                     | 58.1.9             |                                                           |
| Build / release      | EAS Build + EAS Submit (eas-cli)                       | 27.10.0            | App Store and Play Store                                  |
| OTA updates          | EAS Update (expo-updates)                              | 58.0.22            |                                                           |
| Errors               | Sentry (@sentry/react-native)                          | 8.36.0             |                                                           |
| Tests                | jest-expo + @testing-library/react-native              | 58.0.15 / 14.1.0   | Expo's supported test setup; Vitest for plain TS packages |
| Env                  | `EXPO_PUBLIC_` variables                               |                    | Public; never put secrets in them                         |

API, auth session rules and feature-folder structure are the same as the frontend.
