# Repository Knowledge Base

Generated for future reference from a full repository inventory and first-party source review.

## Latest Integration Note

- Zentra AI mobile chat now calls the RAG backend instead of returning simulated local responses. Evidence: `zentra-1.0-main/app/zentra-ai/chat.tsx`, `zentra-1.0-main/lib/ragApi.ts`.
- The RAG service now exposes a FastAPI server with `GET /health` and `POST /ask`. Evidence: `backend/rag/api.py`.
- The RAG LangChain QA chain is cached in process to avoid rebuilding the Pinecone/OpenAI chain on every request. Evidence: `backend/rag/src/query.py`.
- RAG API runtime dependencies now include FastAPI and Uvicorn. Evidence: `backend/rag/requirements.txt`.
- To run the RAG API locally: from `backend/rag`, install dependencies and start `uvicorn api:app --reload --host 0.0.0.0 --port 8001`.
- Mobile API base URL resolution: `zentra-1.0-main/lib/ragApi.ts` uses `EXPO_PUBLIC_RAG_API_BASE_URL` when provided, otherwise infers the Expo dev host and port `8001`, with Android emulator and localhost fallbacks.

## A. Repository Overview

- Project name: Zentra / Zentra AI plus OVIYA meal planner.
- High-level purpose: fitness and nutrition platform with mobile fitness tracking, Supabase-backed user data, form-correction UI, step tracking, meal planning, food analysis, and a separate AI meal-planning web/API stack.
- Primary tech stack: Expo Router + React Native + Supabase for mobile; React/Vite/Redux/Tailwind/shadcn for web; FastAPI/SQLAlchemy/OpenAI/Gemini/USDA for meal API; LangChain/Pinecone/OpenAI for RAG; TensorFlow/OpenCV/MediaPipe for bicep curl inference.
- Main architectural style: multiple adjacent apps rather than one integrated monolith. Mobile uses file-based routing and direct Supabase calls; meal web calls FastAPI; RAG is a CLI pipeline; bicep correction is a local script/prototype.
- Runtime/build system: Expo Router entry for mobile, Vite for web, Uvicorn/FastAPI for API, Python CLI for RAG and inference.
- Deployment shape: detectable only for web/API local dev and generic production hints; no CI/CD or container files found.

Evidence: `zentra-1.0-main/package.json`, `zentra-1.0-main/app.json`, `zentra-1.0-main/app/_layout.tsx`, `zentra-1.0-main/supabase/migrations/20251112124215_create_zentra_schema.sql`, `backend/mealgenerator/README.md`, `backend/mealgenerator/apps/web/package.json`, `backend/mealgenerator/apps/web/src/App.tsx`, `backend/mealgenerator/apps/api/app/main.py`, `backend/mealgenerator/apps/api/requirements.txt`, `backend/rag/main.py`, `backend/rag/requirements.txt`, `backend/Bicep_Curl_Done/infer.py`.

## B. File Manifest Summary

- Total files discovered: `45,163`.
- Files read/analyzed: approximately `147` first-party behavior/config/doc files.
- Intentionally ignored: approximately `45,016`.
- Unread due to access/tool limits: `0`.
- Note: `zentra-1.0-main/app/blog/[id].tsx` initially failed through normal PowerShell globbing because of bracket characters, then was successfully read with `-LiteralPath`.

### Coverage Table

| File/Directory | Type | Status | Purpose | Notes |
|---|---|---|---|---|
| `zentra-1.0-main/app/**` | Core mobile source | Read and analyzed | Expo Router screens/layouts for auth, onboarding, tabs, meals, steps, history, workout, form correction, blogs, AI chat | Several screens use mock/local state rather than persisted services |
| `zentra-1.0-main/components/**` | Core mobile UI | Read and analyzed | Shared button, picker, progress, day history components | Includes `PrimaryButton.tsx`, `ScrollPicker.tsx`, `CircularProgress.tsx`, `DayProgressBar.tsx`, `ProgressDots.tsx` |
| `zentra-1.0-main/lib/supabase.ts` | Integration | Read and analyzed | Supabase client setup | Requires Expo config/env Supabase URL and anon key |
| `zentra-1.0-main/supabase/migrations/**` | Database | Read and analyzed | Supabase schema, RLS, history tables | Main source of mobile persistence model |
| `zentra-1.0-main/assets/images/**` | Binary assets | Ignored intentionally | App icon/favicon | Binary/media; impact limited to visual branding |
| `backend/mealgenerator/apps/api/app/**` | Core API source | Read and analyzed | FastAPI routes, services, auth, database, AI integrations | Active API for OVIYA meal planner |
| `backend/mealgenerator/apps/api/.env`, `backend/mealgenerator/apps/api/app/.env` | Secrets/config | Read key names only | API env configuration | Secret values intentionally not recorded |
| `backend/mealgenerator/apps/web/src/pages/**` | Core web source | Read and analyzed | Web app pages and flows | Chat, calendar, recipes, auth, food analysis, shopping list |
| `backend/mealgenerator/apps/web/src/store/**` | State | Read and analyzed | Redux persisted auth/chat/calendar/meal state | Important persistence behavior |
| `backend/mealgenerator/apps/web/src/components/layout/**` | Web layout/auth | Read and analyzed | Navbar, protected routes, auth initializer, theme provider | Protected route depends on incomplete API auth validation |
| `backend/mealgenerator/apps/web/src/components/ui/**` | Generated UI wrappers | Ignored intentionally except `background-slider.tsx` | shadcn/Radix UI primitives | Exact primitive internals not restated; architecture unaffected |
| `backend/mealgenerator/apps/web/src/assets/**`, `backend/mealgenerator/apps/web/public/**` | Static assets | Ignored intentionally | Meal photos, favicon, placeholder SVG | Binary/media/static |
| `backend/mealgenerator/apps/web/package-lock.json`, `zentra-1.0-main/package-lock.json`, `backend/mealgenerator/apps/web/bun.lockb` | Generated lockfiles | Ignored intentionally | Resolved dependency graph | Package manifests were read for architecture; exact pinned tree not restated |
| `backend/rag/src/**`, `backend/rag/main.py` | Core RAG source | Read and analyzed | Ingest TXT/PDF, embed, store in Pinecone, query with GPT | Corpus files intentionally ignored |
| `backend/rag/data/txt/**` | Data corpus | Ignored intentionally | 5,576 transcript text files for RAG | Impact: domain corpus content not summarized individually |
| `backend/Bicep_Curl_Done/infer.py` | Core prototype source | Read and analyzed | Webcam bicep curl rep classifier | Uses local model/pickle assets |
| `backend/Bicep_Curl_Done/Try_2.keras`, `backend/Bicep_Curl_Done/reps2.pkl`, `backend/Bicep_Curl_Done/Bicep Curl Training Code.docx` | Model/training artifacts | Ignored intentionally | ML model, training data/doc | Binary/Office formats; inference code reveals usage |
| `**/node_modules/**`, `**/venv/**`, `**/.git/**`, `**/__pycache__/**` | Vendor/runtime/generated | Ignored intentionally | Installed deps, virtualenvs, Git metadata, bytecode | Not first-party behavior |

## C. Entry Points And Execution Flow

- Mobile entry is Expo Router via `expo-router/entry` in `zentra-1.0-main/package.json`; app bootstrap is `zentra-1.0-main/app/_layout.tsx`, which keeps the splash screen visible until a short readiness delay completes and registers stack routes.
- Mobile launch flow: `zentra-1.0-main/app/index.tsx` waits 3 seconds then routes to `/onboarding`; onboarding routes to `/auth`; auth uses Supabase and routes existing onboarded users to `/(tabs)` or new users to `/body-metrics`.
- Mobile authenticated runtime uses direct Supabase calls from screens rather than a repository/service layer. Evidence: `zentra-1.0-main/app/(tabs)/profile.tsx`, `zentra-1.0-main/app/steps.tsx`, `zentra-1.0-main/app/blogs.tsx`, `zentra-1.0-main/app/history/*.tsx`.
- Web entry is `backend/mealgenerator/apps/web/src/main.tsx`, mounting `App.tsx`; `App.tsx` wraps Redux Persist, AuthInitializer, React Query, ThemeProvider, BrowserRouter, Navbar, and routes.
- API entry is `backend/mealgenerator/apps/api/app/main.py`; startup initializes `MealPlanningAgent` and `FoodAnalysisService`, registers CORS, request logging, `/health`, `/`, and `/api/v1`.
- RAG entry is `backend/rag/main.py`, offering `1 -> Ingest Data` and `2 -> Ask Questions`.
- Bicep curl entry is `backend/Bicep_Curl_Done/infer.py`, opening webcam `0`, extracting MediaPipe landmarks, counting reps, and classifying via `Try_2.keras`.

## D. Architecture Map

- Mobile layer: Expo Router screens directly own UI, navigation, Supabase reads/writes, and local state. Shared concerns are minimal: theme in `zentra-1.0-main/constants/theme.ts`, Supabase client in `zentra-1.0-main/lib/supabase.ts`, UI helpers in `zentra-1.0-main/components/*`.
- Mobile persistence boundary: Supabase Postgres/RLS schema is defined in `zentra-1.0-main/supabase/migrations/*.sql`; screens do not use a central repository layer.
- Web layer: React pages call API endpoints and Redux slices store auth, user profile, chat, calendar, and meal plans. Evidence: `backend/mealgenerator/apps/web/src/App.tsx`, `backend/mealgenerator/apps/web/src/store/store.ts`, `backend/mealgenerator/apps/web/src/pages/Chat.tsx`.
- API layer: FastAPI route modules delegate to `core/*` AI/services and `domains/*` models/schemas/repos. Evidence: `backend/mealgenerator/apps/api/app/api/v1/*.py`, `backend/mealgenerator/apps/api/app/core/*.py`, `backend/mealgenerator/apps/api/app/domains/users/*`, `backend/mealgenerator/apps/api/app/domains/meals/*`.
- RAG layer: CLI pipeline has clear ingestion, splitting/embedding, Pinecone storage, and query chain modules. Evidence: `backend/rag/src/ingest.py`, `backend/rag/src/embed.py`, `backend/rag/src/store.py`, `backend/rag/src/query.py`.

## E. Feature / Domain Context

### Authentication

- Relevant files: `zentra-1.0-main/app/auth/index.tsx`, `zentra-1.0-main/lib/supabase.ts`, `backend/mealgenerator/apps/api/app/api/v1/auth.py`, `backend/mealgenerator/apps/api/app/core/security.py`, `backend/mealgenerator/apps/web/src/pages/Auth.tsx`.
- Mobile uses Supabase email/password sign-in, sign-up, and password reset.
- Web/API uses custom JWT with BCrypt and SQLAlchemy users.
- Edge cases: web `/auth/me` always returns 401; `/auth/refresh` is called by frontend but not implemented in router.

### Mobile Profile / Body Metrics

- Relevant files: `zentra-1.0-main/app/body-metrics/index.tsx`, `zentra-1.0-main/app/body-metrics/weight.tsx`, `zentra-1.0-main/app/(tabs)/profile.tsx`, `zentra-1.0-main/supabase/migrations/*.sql`.
- Stores height, weight, units, onboarding status, step goal; profile computes BMI client-side.

### Mobile Step Tracking

- Relevant files: `zentra-1.0-main/app/steps.tsx`, `zentra-1.0-main/app/history/steps.tsx`, `zentra-1.0-main/supabase/migrations/20251112124215_create_zentra_schema.sql`, `zentra-1.0-main/supabase/migrations/20251205220918_add_history_tables.sql`.
- Uses accelerometer magnitude thresholding, upserts daily `step_tracking`, and reads monthly `user_steps_history`.
- Edge case: daily tracking writes `step_tracking`, while history reads `user_steps_history`; no observed code syncs daily rows into monthly history.

### Mobile Workout / Form Correction

- Relevant files: `zentra-1.0-main/app/workout/index.tsx`, `zentra-1.0-main/app/form-correction/*.tsx`, `backend/Bicep_Curl_Done/infer.py`.
- Mobile form-correction live view is simulated UI; actual camera ML exists separately in Python and is not integrated into Expo.

### OVIYA Meal Planning

- Relevant files: `backend/mealgenerator/apps/web/src/pages/Chat.tsx`, `backend/mealgenerator/apps/api/app/api/v1/meal_planning.py`, `backend/mealgenerator/apps/api/app/core/meal_agent.py`.
- Takes user profile/preferences, asks OpenAI GPT-4o with USDA tool calls, expects strict JSON with `day1` and meal macros.
- Edge case: frontend calls `/generate-weekly`, but API only defines `/generate` and `/generate-daily`.

### Food Image Analysis

- Relevant files: `backend/mealgenerator/apps/web/src/pages/FoodAnalysis.tsx`, `backend/mealgenerator/apps/api/app/api/v1/food_analysis.py`, `backend/mealgenerator/apps/api/app/core/food_analysis.py`.
- Uploads image, validates MIME, sends image to Gemini, formats nutrition output.
- Edge case: BLIP model loading is commented out, so fallback BLIP path would fail if reached because processor/model remain `None`.

### Shopping List

- Relevant files: `backend/mealgenerator/apps/web/src/pages/Calendar.tsx`, `backend/mealgenerator/apps/web/src/pages/ShoppingList.tsx`, `backend/mealgenerator/apps/api/app/api/v1/shopping.py`, `backend/mealgenerator/apps/api/app/domains/meals/service.py`.
- Aggregates weekly meal data through OpenAI into fixed categories.

### Recipes

- Relevant files: `backend/mealgenerator/apps/api/app/api/v1/recipe.py`, `backend/mealgenerator/apps/api/app/core/generate_recipe.py`, `backend/mealgenerator/apps/api/app/core/recipe_utils.py`, `backend/mealgenerator/apps/web/src/pages/MealRecipe.tsx`, `backend/mealgenerator/apps/web/src/pages/RecipeDetails.tsx`, `backend/mealgenerator/apps/web/src/pages/MealDetails.tsx`.
- Generates raw recipe text via OpenAI and parses `INGREDIENTS:` / `INSTRUCTIONS:` into structured data.

### RAG Fitness Q&A

- Relevant files: `backend/rag/main.py`, `backend/rag/src/*.py`, `backend/rag/data/txt/**`.
- Ingests transcripts into Pinecone and answers concise questions using GPT-4o-mini with top-2 retrieval.

## F. Data Flow

- Mobile user data enters through Supabase auth/profile forms and sensor events. Validation is mostly client-side: email/password checks in `zentra-1.0-main/app/auth/index.tsx`, height/weight picker bounds in body metric screens, accelerometer thresholds in `zentra-1.0-main/app/steps.tsx`.
- Mobile persistence uses Supabase tables directly from screens; RLS policies protect user-owned rows. Evidence: `zentra-1.0-main/supabase/migrations/*.sql`.
- Web data enters through forms, chat input, calendar actions, file upload, and recipe/shopping list buttons. Evidence: `backend/mealgenerator/apps/web/src/pages/*.tsx`.
- API validation uses Pydantic schemas for auth, meal planning, food analysis response, shopping list payloads, users, and recipes. Evidence: `backend/mealgenerator/apps/api/app/api/v1/*.py`, `backend/mealgenerator/apps/api/app/domains/users/schemas.py`, `backend/mealgenerator/apps/api/app/domains/meals/schemas.py`.
- External APIs: OpenAI chat/completions in meal agent, shopping list, recipe; USDA FoodData Central in meal agent; Gemini in food analysis; Pinecone/OpenAI in RAG.
- Caching: no application cache detected. Redux Persist/localStorage persists web state; Supabase persists mobile data.

## G. API And Integration Context

- `GET /health`, `GET /`: FastAPI health/root. Evidence: `backend/mealgenerator/apps/api/app/main.py`.
- `POST /api/v1/auth/register`: creates SQLAlchemy user and hashes password. Evidence: `backend/mealgenerator/apps/api/app/api/v1/auth.py`, `backend/mealgenerator/apps/api/app/domains/users/service.py`.
- `POST /api/v1/auth/login`: returns JWT access/refresh tokens. Evidence: `backend/mealgenerator/apps/api/app/api/v1/auth.py`.
- `GET /api/v1/auth/me`: placeholder 401. Evidence: `backend/mealgenerator/apps/api/app/api/v1/auth.py`.
- `POST /api/v1/meal-planning/generate`, `/generate-daily`: daily meal plan generation. Evidence: `backend/mealgenerator/apps/api/app/api/v1/meal_planning.py`.
- `POST /api/v1/food-analysis/analyze-image`: multipart image analysis. Evidence: `backend/mealgenerator/apps/api/app/api/v1/food_analysis.py`.
- `POST /api/v1/shopping_list/generate`: OpenAI-generated categorized shopping list. Evidence: `backend/mealgenerator/apps/api/app/api/v1/shopping.py`, `backend/mealgenerator/apps/api/app/domains/meals/service.py`.
- `POST /api/v1/recipe/generate-recipe`: OpenAI recipe generation. Evidence: `backend/mealgenerator/apps/api/app/api/v1/recipe.py`.
- Error handling is mostly broad `except Exception` returning 500 or structured error data. Evidence: `backend/mealgenerator/apps/api/app/api/v1/meal_planning.py`, `backend/mealgenerator/apps/api/app/api/v1/food_analysis.py`, `backend/mealgenerator/apps/api/app/domains/meals/service.py`, `backend/mealgenerator/apps/api/app/core/generate_recipe.py`.

## H. Configuration And Environment

- Mobile env vars: `EXPO_PUBLIC_SUPABASE_URL`, `EXPO_PUBLIC_SUPABASE_ANON_KEY`; also supports `expoConfig.extra.supabaseUrl` and `supabaseAnonKey`. Evidence: `zentra-1.0-main/lib/supabase.ts`.
- API env vars: `SECRET_KEY`, `SQLALCHEMY_DATABASE_URI`, `USDA_API_KEY`, `OPENAI_API_KEY`; `GEMINI_API_KEY` has a default hardcoded value in config. Evidence: `backend/mealgenerator/apps/api/app/core/config.py`, API `.env` key names.
- Web env var: `VITE_API_BASE_URL`, falling back to current hostname port `8000`. Evidence: `backend/mealgenerator/apps/web/src/lib/utils.ts`.
- RAG env vars: `OPENAI_API_KEY`, `PINECONE_API_KEY`, `PINECONE_INDEX_NAME`, `PINECONE_REGION`. Evidence: `backend/rag/src/utils.py`, `backend/rag/.env` key names.
- Build/runtime assumptions: mobile uses Expo; web dev server host `::`, port `8080`; API docs at `/docs`; database expected to be PostgreSQL/Neon.

## I. Database / Storage Context

- Mobile Supabase schema includes `user_profiles`, `step_tracking`, `recipes`, `meal_plans`, `meal_plan_items`, `workout_logs`, `ai_chat_conversations`, `ai_chat_messages`, `blog_posts`, `saved_blogs`, plus history tables `user_logs_history`, `user_meal_history`, `user_steps_history`, `shopping_lists`. Evidence: `zentra-1.0-main/supabase/migrations/*.sql`.
- Mobile RLS policies restrict user-specific tables to `auth.uid() = user_id` or ownership through parent rows; recipes/blog posts are readable by authenticated users. Evidence: `zentra-1.0-main/supabase/migrations/20251112124215_create_zentra_schema.sql`.
- API database has only SQLAlchemy `users` model detected, with email, hashed password, profile/preferences, flags, and timestamps. Evidence: `backend/mealgenerator/apps/api/app/domains/users/models.py`.
- API table creation is manual through `create_db.py` and destructive recreation through `drop_and_create_db.py`; no Alembic migrations detected.
- RAG storage is Pinecone vector index, populated from TXT/PDF documents. Evidence: `backend/rag/src/store.py`.

## J. Authorization / Security Context

- Mobile auth: Supabase Auth sessions, persisted/auto-refreshed by Supabase client. Evidence: `zentra-1.0-main/lib/supabase.ts`, `zentra-1.0-main/app/auth/index.tsx`.
- Mobile authorization: Supabase RLS policies. Evidence: `zentra-1.0-main/supabase/migrations/*.sql`.
- Web/API auth: custom JWT access/refresh tokens signed with `SECRET_KEY`, BCrypt password hashing. Evidence: `backend/mealgenerator/apps/api/app/core/security.py`, `backend/mealgenerator/apps/api/app/domains/users/service.py`.
- API auth gap: `app/deps/auth.py` imports `verify_token` and calls `repo.get_by_id`, but neither exists in observed `core/security.py` or `domains/users/repo.py`; active `/auth/me` is a placeholder 401.
- Security-sensitive risks: permissive CORS with credentials and all origins; request middleware logs all headers; a Gemini key default is hardcoded in config.

## K. Testing Context

- No first-party test files, test directories, or test scripts were found in package/API manifests.
- Web lint script exists: `npm run lint`.
- Mobile lint/typecheck scripts exist: `expo lint`, `tsc --noEmit`.
- Missing/weak coverage areas: auth lifecycle, meal JSON parsing, weekly generation endpoint mismatch, Supabase history writes, food image non-food handling, RAG ingestion/query, and bicep curl inference.

Evidence: `zentra-1.0-main/package.json`, `backend/mealgenerator/apps/web/package.json`, `backend/mealgenerator/apps/api/requirements.txt`.

## L. DevOps / Delivery Context

- CI/CD workflows: none detected.
- Containers/infrastructure: no Dockerfile/compose/Terraform-like deployment files detected.
- Lint/type checks: mobile has `lint` and `typecheck`; web has ESLint config and `lint`.
- Deployment clues: FastAPI README suggests Uvicorn local and Gunicorn production; web build uses Vite; mobile build likely Expo but no EAS config detected.

Evidence: `backend/mealgenerator/README.md`, `backend/mealgenerator/apps/web/package.json`, `backend/mealgenerator/apps/web/eslint.config.js`, `backend/mealgenerator/apps/web/tsconfig*.json`, `zentra-1.0-main/package.json`.

## M. Risks / Code Smells / Gaps

- Web frontend calls API endpoints that are absent or placeholders: `/api/v1/auth/refresh`, `/api/v1/auth/me`, `/api/v1/meal-planning/generate-weekly`. Evidence: `backend/mealgenerator/apps/web/src/lib/auth.ts`, `backend/mealgenerator/apps/web/src/pages/Chat.tsx`, `backend/mealgenerator/apps/api/app/api/v1/auth.py`, `backend/mealgenerator/apps/api/app/api/v1/meal_planning.py`.
- Auth dependency is incomplete and likely unusable if wired. Evidence: `backend/mealgenerator/apps/api/app/deps/auth.py`, `backend/mealgenerator/apps/api/app/core/security.py`, `backend/mealgenerator/apps/api/app/domains/users/repo.py`.
- Hardcoded secret default and permissive CORS/logging are security risks. Evidence: `backend/mealgenerator/apps/api/app/core/config.py`, `backend/mealgenerator/apps/api/app/main.py`.
- Mobile app has Supabase tables for meal/chat/workout history but several screens use hardcoded/mock data or local-only state. Evidence: `zentra-1.0-main/app/meal-plan/*.tsx`, `zentra-1.0-main/app/zentra-ai/chat.tsx`, `zentra-1.0-main/app/workout/index.tsx`, `zentra-1.0-main/app/form-correction/live.tsx`.
- Step daily and history flows appear disconnected. Evidence: `zentra-1.0-main/app/steps.tsx` writes `step_tracking`; `zentra-1.0-main/app/history/steps.tsx` reads `user_steps_history`.
- Bicep curl Python inference is not integrated into mobile live form-correction UI. Evidence: `zentra-1.0-main/app/form-correction/live.tsx`, `backend/Bicep_Curl_Done/infer.py`.
- Food analysis BLIP fallback path is unsafe because model initialization is commented out. Evidence: `backend/mealgenerator/apps/api/app/core/food_analysis.py`.

## N. Unknowns And Required Follow-Ups

- Not confirmed whether Supabase env values are supplied outside `app.json`; `app.json` does not show `extra`.
- Not confirmed whether database has manual triggers/jobs to roll `step_tracking` into `user_steps_history`.
- Not confirmed whether the OVIYA API is meant to serve the Zentra mobile app; no direct mobile calls to the FastAPI API were observed.
- Not confirmed whether the RAG service is intended to power mobile Zentra AI; mobile chat currently simulates responses.
- Not confirmed whether ignored RAG transcript corpus contains licensing or content-quality constraints.

## O. Final Working Context

Zentra is a multi-app fitness/nutrition repo. `zentra-1.0-main` is an Expo Router React Native app using Supabase Auth and Supabase Postgres/RLS. Mobile routes live under `zentra-1.0-main/app`, shared UI under `zentra-1.0-main/components`, theme under `zentra-1.0-main/constants/theme.ts`, and Supabase client under `zentra-1.0-main/lib/supabase.ts`. The database schema and RLS policies are in `zentra-1.0-main/supabase/migrations/*.sql`. Mobile features include onboarding/auth, body metrics/profile/BMI, accelerometer step tracking, static/mock meal plan screens, workout logging, form-correction UI, blogs, history screens, and a simulated Zentra AI chat. Several mobile features are UI-only or partially persisted.

`backend/mealgenerator` is a separate OVIYA meal-planning full-stack app. The API is FastAPI in `backend/mealgenerator/apps/api/app`, with entry `main.py`, router `api/v1/router.py`, auth routes, meal planning, food analysis, shopping list, and recipe endpoints. It uses SQLAlchemy/PostgreSQL for users, OpenAI GPT-4o plus USDA FoodData Central for meal planning, OpenAI for recipes/shopping lists, and Gemini for image nutrition analysis. The web app is Vite React in `backend/mealgenerator/apps/web/src`, with Redux Persist state in `store`, route setup in `App.tsx`, auth in `pages/Auth.tsx` plus `lib/auth.ts`, meal chat in `pages/Chat.tsx`, calendar/shopping/recipe pages, and food analysis upload page.

`backend/rag` is a CLI LangChain/Pinecone/OpenAI RAG pipeline. `main.py` chooses ingestion or query; `src/ingest.py` loads PDFs/TXT corpus, `src/embed.py` chunks and embeds with `text-embedding-3-small`, `src/store.py` writes batches to Pinecone, and `src/query.py` retrieves top-2 chunks and answers with `gpt-4o-mini`.

`backend/Bicep_Curl_Done/infer.py` is a local webcam bicep curl classifier using OpenCV, MediaPipe pose landmarks, TensorFlow Keras model `Try_2.keras`, and `reps2.pkl` to determine sequence length. It counts reps based on elbow angle thresholds and classifies each rep as correct/incorrect. The mobile live form-correction screen is currently a placeholder camera UI and is not wired to this Python inference script.
