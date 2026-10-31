# Spot 2 Study — Workspace Rules

These rules apply to everyone who contributes to the Spot 2 Study repository (`client/`, `api/`, `supabase/`, `docs/`).

---

## 1. Scope Rules

- Build only what the Analyst's **Level 1 Specification** and the **approved User Stories List** describe. The working breakdown is in [`docs/TASK-LIST.md`](./TASK-LIST.md).
- Do not build rejected user stories. Do not add features, screens, or data fields that the Analyst documents don't call for.
- If a requirement is unclear, add it to the *Open Questions* section of `TASK-LIST.md` and ask the Analyst before writing code.
- Use the project name **Spot 2 Study** in code, docs, and UI text.

---

## 2. Naming Conventions

### General
- Use English names that describe what the thing is or does. Avoid abbreviations unless they are standard (`id`, `url`, `api`).
- Folder names are lowercase `kebab-case`.

### Client (`/client`, React + Vite)
| Item                         | Convention              | Example                     |
| ---------------------------- | ----------------------- | --------------------------- |
| React component files        | `PascalCase.jsx`        | `LocationCard.jsx`          |
| React components             | `PascalCase`            | `LocationCard`              |
| Custom hooks                 | `camelCase`, `use` prefix | `useFavorites.js`         |
| Utility / helper files       | `camelCase.js`          | `formatDistance.js`         |
| Variables and functions      | `camelCase`             | `noiseLevel`, `fetchLocations()` |
| Constants                    | `UPPER_SNAKE_CASE`      | `API_BASE_URL`              |
| CSS class names              | `kebab-case`            | `location-card`             |

### API (`/api`, Express + Node.js)
| Item                         | Convention              | Example                     |
| ---------------------------- | ----------------------- | --------------------------- |
| Route, controller, service files | `camelCase.js`      | `locations.js`              |
| URL paths                    | lowercase, plural nouns, `kebab-case` | `/api/locations/:id/reviews` |
| JSON request/response fields | `camelCase`             | `noiseLevel`                |
| Environment variables        | `UPPER_SNAKE_CASE`      | `SUPABASE_URL`              |

### Database (`/supabase`)
| Item                         | Convention              | Example                     |
| ---------------------------- | ----------------------- | --------------------------- |
| Tables                       | plural `snake_case`     | `locations`, `reviews`      |
| Columns                      | `snake_case`            | `noise_level`, `created_at` |
| Primary keys                 | `id`                    | `id`                        |
| Foreign keys                 | `<singular_table>_id`   | `location_id`               |
| Migration files              | `<timestamp>_<description>.sql` | `20261101120000_create_locations.sql` |
| Seed files                   | `seed.sql` or `<description>_seed.sql` | `locations_seed.sql` |

### Docs (`/docs`)
- Main project documents use `UPPER-KEBAB-CASE.md` (for example, `PRD.md`, `TASK-LIST.md`, `WORKSPACE-RULES.md`).

---

## 3. Git Commit Guidelines

- Use the **Conventional Commits** format:

  ```
  <type>(<scope>): <short summary>
  ```

  - **type:** `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`
  - **scope:** `client`, `api`, `supabase`, `docs`, or a task ID such as `T-05.1`
  - **summary:** imperative mood, lowercase, no period, 72 characters or fewer

  Examples:
  ```
  feat(api): add noise level filter to locations endpoint (T-05.1)
  fix(client): show empty state when search has no results (T-01.3)
  docs: add task list for approved user stories
  ```

- Each commit is one logical change. Don't mix unrelated changes in a single commit.
- Put the task ID (`T-xx.x`) in the commit message when the commit implements a task.
- Never commit secrets, API keys, or `.env` files. Use `.env.example` to document required variables, with placeholder values only.
- Don't commit generated or ignored files (`node_modules/`, `dist/`, `build/`, `.DS_Store`).

---

## 4. Branching Strategy

- `main` is the stable branch. It must always contain working code.
- **Never push directly to `main`.** Every change goes through a pull request.
- Make each new branch from the latest `main`, using these prefixes:

  | Prefix      | Use for                         | Example                              |
  | ----------- | ------------------------------- | ------------------------------------ |
  | `feature/`  | New task from `TASK-LIST.md`    | `feature/T-05.1-noise-level-filter`  |
  | `fix/`      | Bug fixes                       | `fix/search-empty-results`           |
  | `docs/`     | Documentation-only changes      | `docs/update-prd`                    |
  | `chore/`    | Tooling, config, dependencies   | `chore/setup-eslint`                 |

- Branch names are lowercase `kebab-case` and include the task ID when there is one.
- Keep branches short-lived. Delete a branch after its pull request is merged.

---

## 5. Pull Request Process

1. Before opening a pull request, update your branch with the latest `main` and resolve any conflicts.
2. Open a pull request into `main` with:
   - **Title** in the same format as commit messages, for example `feat(client): add favorites section (T-10.3)`.
   - **Description** that includes:
     - The task ID(s) and user story(ies) addressed (for example, `T-10.3 / US-10`).
     - A short summary of what changed.
     - The task's acceptance criteria as a checklist, each item checked off once verified.
     - Steps for the reviewer to test the change locally.
     - Screenshots for any UI change.
3. Keep pull requests small and focused. Ideally one task per pull request.
4. A pull request can be merged only when:
   - At least **one** other team member has approved it.
   - All review comments are resolved.
   - All acceptance criteria for the task have been checked.
   - The app still runs locally (client with Vite, API with Node).
5. Use **squash and merge** so each pull request becomes one commit on `main`.

---

## 6. Code Reviews

### Reviewers check that:
- The change implements the linked task and meets its acceptance criteria.
- The change stays within scope: no rejected stories and no unrequested features.
- Naming follows Section 2.
- No secrets, credentials, or `.env` files are included.
- The code is readable, and any non-obvious logic has a short comment.
- Database changes are made through new migration files in `/supabase/`, not by editing migrations that have already been applied.

### Review etiquette:
- Review the code, not the person. Be specific and constructive.
- Label optional suggestions as `nit:` so they aren't mistaken for blocking changes.
- Respond to review requests within **one business day**.
- The author replies to or resolves every comment before merging.

---

## 7. Collaboration Rules

- **Source of truth:** The Analyst's Level 1 Specification and the approved User Stories List define the requirements. `docs/PRD.md` and `docs/TASK-LIST.md` are derived from them and must stay consistent with them.
- **Task ownership:** Assign yourself to a task before starting it, so two people don't work on the same task.
- **Communication:** Raise blockers and requirement questions early. Send questions about requirements to the Analyst.
- **Documentation:** Update the relevant file in `/docs/` (PRD, site map, OpenAPI, deployment notes) in the same pull request as the code change it describes.
- **Environment setup:** Each contributor keeps their own local `.env` files. Never share credentials in commits, pull requests, or issues.
- **Respect `main`:** If a merge breaks `main`, fixing it is the top priority for the person who merged it.
