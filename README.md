## Getting Started

First, run the development server:

```bash
npm run dev
# or
yarn dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

## Prettier

```bash
yarn add -D prettier prettier-plugin-tailwindcss eslint-config-prettier
```

```
.prettierrc
```

```json
{
  "printWidth": 120,
  "tabWidth": 2,
  "useTabs": false,
  "semi": false,
  "plugins": ["prettier-plugin-tailwindcss"]
}
```

```
.eslintrc.json
```

```json
{
  "extends": ["next", "prettier"]
}
```

```
package.json
```

```json
"format": "prettier -w -u ./"
```

## Prisma

```bash
yarn add -D prisma ts-node && yarn add @prisma/client && yarn prisma init
```

[Best practice for instantiating Prisma](https://www.prisma.io/docs/orm/more/help-and-troubleshooting/help-articles/nextjs-prisma-client-dev-practices)

```
prisma/seed.ts
```

```ts
import { PrismaClient } from "@prisma/client"

const prisma = new PrismaClient()

async function main() {
  // ...
}

main()
  .then(async () => {
    await prisma.$disconnect()
  })
  .catch(async (e) => {
    console.error(e)
    await prisma.$disconnect()
    process.exit(1)
  })
```

[Seeding Prisma](https://www.prisma.io/docs/orm/prisma-migrate/workflows/seeding)

```
src/lib/db.ts
```

```ts
import { PrismaClient } from "@prisma/client"

const prismaClientSingleton = () => {
  return new PrismaClient()
}

declare global {
  var db: undefined | ReturnType<typeof prismaClientSingleton>
}

export const prisma = globalThis.db ?? prismaClientSingleton()

if (process.env.NODE_ENV !== "production") globalThis.db = prisma
```

```
.env.example
```

```yaml
POSTGRES_PRISMA_URL="postgresql://postgres:postgres@localhost:5432/dev?schema=public"
POSTGRES_URL_NON_POOLING="postgresql://postgres:postgres@localhost:5432/dev?schema=public"
```

```
prisma/schema.prisma
```

```prisma
datasource db {
  provider  = "postgresql"
  url       = env("POSTGRES_PRISMA_URL") // uses connection pooling
  directUrl = env("POSTGRES_URL_NON_POOLING") // uses a direct connection
}
```

```
package.json
```

```json
"reset": "prisma db push --force-reset && prisma db seed"
```

```json
"prisma": {
  "seed": "ts-node --compiler-options {\"module\":\"CommonJS\"} prisma/seed.ts"
},
```

## Winston

```bash
yarn add winston
```

```
src/lib/logger.ts
```

```ts
import winston from "winston"

export const logger = winston.createLogger({
  level: "debug",
  transports: [new winston.transports.Console()],
})
```

## Shadcn/ui

[Step-by-step tutorials](https://ui.shadcn.com/docs/installation/next)

```bash
npx shadcn-ui@latest init && npx shadcn-ui@latest add
```

```
.vscode/settings.json
```

```json
{
  "tailwindCSS.experimental.classRegex": [
    ["cva\\(([^)]*)\\)", "[\"'`]([^\"'`]*).*?[\"'`]"],
    ["cx\\(([^)]*)\\)", "(?:'|\"|`)([^']*)(?:'|\"|`)"]
  ]
}
```

## Launch

```
.vscode/launch.json
```

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug server-side",
      "type": "node-terminal",
      "request": "launch",
      "command": "npm run dev"
    },
    {
      "name": "Debug client-side",
      "type": "chrome",
      "request": "launch",
      "url": "http://localhost:3000"
    },
    {
      "name": "Debug full stack",
      "type": "node-terminal",
      "request": "launch",
      "command": "npm run dev",
      "serverReadyAction": {
        "pattern": "- Local:.+(https?://.+)",
        "uriFormat": "%s",
        "action": "debugWithChrome"
      }
    }
  ]
}
```

## Husky & Commitlint

```bash
yarn add -D @commitlint/cli @commitlint/config-conventional husky && npx husky init
```

```bash
echo npm run lint > .husky/pre-commit
```

```bash
echo npx --no-install commitlint --edit > .husky/commit-msg
```

```
.commitlintrc
```

```json
{
  "extends": ["@commitlint/config-conventional"]
}
```
