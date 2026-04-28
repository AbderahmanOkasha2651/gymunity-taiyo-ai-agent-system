# 🗺️ TAIYO Project Roadmap

This roadmap shows the planned development phases for the **TAIYO AI Agent System** inside GymUnity.

The project is currently focused on building a practical AI architecture MVP first, then connecting it step by step to Supabase and Flutter.

---

## ✅ Phase 1 — Azure AI Foundry Agent System

**Status:** Completed as MVP

In this phase, the main goal was to build and test the AI agent layer inside Azure AI Foundry.

Completed work:

- Created the Azure AI Foundry project
- Configured the model deployment
- Created the TAIYO Orchestrator Agent
- Created specialized agents for fitness, nutrition, workout planning, safety, coach support, store recommendations, and admin operations
- Connected the specialized agents to the Orchestrator
- Added knowledge grounding
- Tested routing behavior
- Tested safety and privacy rules
- Tested structured JSON responses

---

## ✅ Phase 2 — Knowledge Grounding

**Status:** Completed as MVP

In this phase, static GymUnity rules were added to the agent system.

The knowledge base includes:

- Safety rules
- Training rules
- Nutrition guidance
- Workout planning rules
- Coach Copilot rules
- Store recommendation rules
- Admin Ops rules
- GymUnity app FAQ
- Orchestrator routing notes

This helps the agents follow consistent rules instead of relying only on the prompt.

---

## 🔄 Phase 3 — Supabase Context Builder Design

**Status:** Next phase

The next step is to design the live data layer.

The goal is to define what data each agent needs from Supabase and where that data should come from.

Planned context schemas:

- Member daily brief context
- Nutrition context
- Workout planner context
- Coach client context
- Store recommendation context
- Admin Ops context

Example mapping:

| Context Field | Supabase Source |
| --- | --- |
| Member goal | `member_profiles` |
| Readiness | `member_daily_readiness_logs` |
| Sleep | `member_daily_readiness_logs` |
| Workout history | `workout_task_logs` / `member_active_workout_sessions` |
| Nutrition logs | `meal_logs` |
| Hydration logs | `hydration_logs` |
| Coach visibility | `coach_member_visibility_settings` |
| Products | `products` |
| Payments | `coach_payment_orders` / `coach_payment_transactions` |
| Subscriptions | `subscriptions` |

---

## ⏭️ Phase 4 — Supabase Edge Functions

**Status:** Planned

After the context schemas are designed, Supabase Edge Functions will be created.

Planned functions:

- `taiyo-member-context`
- `taiyo-daily-brief`
- `taiyo-nutrition-context`
- `taiyo-workout-planner`
- `taiyo-coach-client-brief`
- `taiyo-store-recommendations`
- `taiyo-admin-ops-brief`

These functions will handle:

- Authentication
- Role checks
- Permission checks
- Context building
- Secure calls to Azure
- Logging
- Error handling

---

## ⏭️ Phase 5 — Azure Actions and OpenAPI Tools

**Status:** Planned

In this phase, Azure agents will be connected to backend tools.

The goal is to allow the Orchestrator to call Supabase Edge Functions when live context is needed.

Planned work:

- Create OpenAPI specs for Supabase Edge Functions
- Add actions/tools to Azure AI Foundry
- Test tool calls from the Azure Playground
- Handle missing context and tool failures safely

---

## ⏭️ Phase 6 — Flutter Integration

**Status:** Planned

In this phase, TAIYO responses will be displayed inside the GymUnity app.

Planned UI areas:

- TAIYO Today Card
- Daily Member Brief screen
- Nutrition Focus card
- Workout Adjustment card
- Coach Copilot client summary
- Store recommendation cards
- Admin Ops summary card

Flutter will call Supabase Edge Functions, not Azure directly.

---

## ⏭️ Phase 7 — Evaluation and Demo Preparation

**Status:** Planned

This phase is focused on preparing the system for demonstration and review.

Planned work:

- Add screenshots from Azure AI Foundry tests
- Create repeated test cases
- Track safety and routing behavior
- Prepare demo flow for graduation presentation
- Add sample JSON responses
- Document limitations and next improvements

---

## Current Focus

The current next step is:

```text
Design Supabase context schemas and map them to the existing GymUnity database tables and RPCs.
```

This will prepare the project for the first real backend integration between Supabase and Azure AI Foundry.
