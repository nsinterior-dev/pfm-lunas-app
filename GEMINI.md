# Project: Lunas (pfm-lunas-app)

## Context
Lunas is a personal financial management application that integrates with Google Sheets and Firestore for data storage, using AI (Claude/Gemini) for deep analysis. The project follows a **Feature-First Clean Architecture** and a design-first workflow.

## Source of Truth (Documentation)
All engineering decisions and product goals are documented in the `docs/` directory:
- **Architecture:** [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) (Clean Architecture layers, tech stack, `useEffect` rules, and Zod validation).
- **Design System:** [docs/DESIGN.md](docs/DESIGN.md) (Figma, shadcn/ui, and Storybook).
- **Design Tokens:** [docs/DESIGN-TOKENS.md](docs/DESIGN-TOKENS.md) (Colors, semantics, and typography).
- **Data Schema:** [docs/DATA-SCHEMA.md](docs/DATA-SCHEMA.md) (Translation layer, sheet mappings, Firestore collections, API reference).
- **Storybook Plan:** [docs/STORYBOOK.md](docs/STORYBOOK.md) (Component library conventions).
- **Product Management:** [docs/PRODUCT-MANAGEMENT/README.md](docs/PRODUCT-MANAGEMENT/README.md) (MVP roadmaps and goals).
- **Epic Sprints:** [docs/EPIC-SPRINTS/](docs/EPIC-SPRINTS/) (Sprint-level tracking).

## Engineering Standards

- **Tech Stack:** Next.js (App Router), TypeScript, TanStack React Query, Zod, Tailwind CSS, shadcn/ui, and Storybook.
- **Three top-level directories:** `client/` (frontend), `server/` (backend), `app/` (pages + thin API routes).
- **Client feature layers:** `presentation/` → `application/` → `client/lib/server/` (API client). `model/` is a sibling layer for domain types.
- **Server feature layers:** `route.ts` (thin) → `service/` → `repository/` → `server/lib/` (SDK clients). `model/` holds Zod schemas + domain types. `parser/` is optional for data transformation.
- **Presentation never imports from `server/`** — the server directory does not exist to client code.
- **Data Fetching:** Use React Query (`useQuery`, `useMutation`). **Avoid `useEffect` for data fetching.**
- **Validation:** Use **Zod** for server contracts (request/response) in `server/features/[feature]/model/`.
- **Error Handling:** Use `AppError` subclasses (`BadInputError`, `NotFoundError`, `SheetAccessError`) — never raw throws in server code.
- **Component Strategy:** Use shadcn/ui components. Document and test in Storybook.
- **Design Workflow:** Design in Figma before any code implementation.
- **AI Integration:** Server-side only (Claude Sonnet for deep analysis, Gemini for fast fallback). Manual trigger only.
- **Authentication:** Google OAuth 2.0 (NextAuth.js).
- **Google Sheets IS the database** — financial data stays in user's Sheet, Firestore = glue only.
- **Testing:** Ensure all new features or bug fixes include corresponding tests.

## Instructions for Gemini CLI
- **Collaboration:** I (Gemini CLI) am part of a multi-agent workflow, specializing in fast calculations, quick queries, and supporting the development lifecycle alongside the Claude agent.
- **Process:** Adhere to the Research → Strategy → Execution lifecycle for all tasks.
- **Architectural Integrity:** Rigorously enforce Clean Architecture boundaries — `presentation/` → `application/` → `client/lib/server/` on the client, `route.ts` → `service/` → `repository/` → `server/lib/` on the server. Enforce `useEffect` restrictions.
- **Visuals:** Prioritize visual polish and user experience, following the design system and tokens.
- **Verification:** Always verify changes with project-specific build and linting commands.
