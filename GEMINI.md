# Project: Lunas (pfm-lunas-app)

## Context
Lunas is a personal financial management application that integrates with Google Sheets and Firestore for data storage, using AI (Claude/Gemini) for deep analysis. The project follows a **Feature-First Clean Architecture** and a design-first workflow.

## Source of Truth (Documentation)
All engineering decisions and product goals are documented in the `docs/` directory:
- **Architecture:** [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) (Clean Architecture layers, tech stack, `useEffect` rules, and Zod validation).
- **Design System:** [docs/DESIGN.md](docs/DESIGN.md) (Figma, shadcn/ui, and Storybook).
- **Design Tokens:** [docs/DESIGN-TOKENS.md](docs/DESIGN-TOKENS.md) (Colors, semantics, and typography).
- **Storybook Plan:** [docs/STORYBOOK.md](docs/STORYBOOK.md) (Component library conventions).
- **Product Management:** [docs/PRODUCT-MANAGEMENT/README.md](docs/PRODUCT-MANAGEMENT/README.md) (MVP roadmaps and goals).
- **Epic Sprints:** [docs/EPIC-SPRINTS/](docs/EPIC-SPRINTS/) (Sprint-level tracking).

## Engineering Standards
- **Tech Stack:** Next.js (App Router), TypeScript, TanStack React Query, Zod, Tailwind CSS, shadcn/ui, and Storybook.
- **Architecture:** Feature-first layers (`presentation/` -> `application/` -> `data/` -> `model/`). Presentation never calls APIs directly.
- **Data Fetching:** Use React Query (`useQuery`, `useMutation`). **Avoid `useEffect` for data fetching.**
- **Validation:** Use **Zod** for server contracts (request/response) and runtime validation.
- **Component Strategy:** Use shadcn/ui components. Document and test in Storybook.
- **Design Workflow:** Design in Figma before any code implementation.
- **AI Integration:** Server-side only (Claude Sonnet for deep analysis, Gemini for fast fallback). Manual trigger only.
- **Authentication:** Google OAuth 2.0 (NextAuth.js).
- **Testing:** Ensure all new features or bug fixes include corresponding tests.

## Instructions for Gemini CLI
- **Collaboration:** I (Gemini CLI) am part of a multi-agent workflow, specializing in fast calculations, quick queries, and supporting the development lifecycle alongside the Claude agent.
- **Process:** Adhere to the Research -> Strategy -> Execution lifecycle for all tasks.
- **Architectural Integrity:** Rigorously enforce Clean Architecture boundaries and `useEffect` restrictions.
- **Visuals:** Prioritize visual polish and user experience, following the design system and tokens.
- **Verification:** Always verify changes with project-specific build and linting commands.
