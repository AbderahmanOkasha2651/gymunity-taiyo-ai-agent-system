# 🔗 Supabase Integration Plan

This document explains the planned integration between **Supabase** and the **TAIYO AI Agent System**.

The current Azure AI Foundry MVP was tested with sample data. The next step is to connect the agents to real GymUnity data through Supabase in a secure and organized way.

---

## 1. Why Supabase Is Needed

Azure AI Foundry handles the AI reasoning and agent orchestration, but it should not directly access the app database.

Supabase is needed because it already owns:

- User authentication
- Member, coach, seller, and admin roles
- Row-level security
- Live member data
- Nutrition data
- Workout data
- Store data
- Coach visibility permissions
- Payment and subscription data

The AI agents should receive only the data they need for the current request.

---

## 2. Planned Data Flow

The planned production flow is:

```text
Flutter App
→ Supabase Edge Function
→ Supabase Database / RPCs
→ Azure AI Foundry Orchestrator
→ Specialized Agents
→ Structured JSON Response
→ Supabase Edge Function
→ Flutter UI
```

Flutter should not call Azure directly.

This keeps Azure credentials, backend logic, and private user data away from the mobile app.

---

## 3. Role of Supabase Edge Functions

Supabase Edge Functions will act as the secure bridge between GymUnity and Azure AI Foundry.

Each Edge Function should:

- Verify the authenticated user
- Check the user role
- Validate permissions
- Build the correct context
- Call Azure AI Foundry when needed
- Return clean JSON to Flutter
- Log important AI requests and responses
- Handle errors safely

---

## 4. Planned Edge Functions

| Edge Function | Purpose |
| --- | --- |
| `taiyo-member-context` | Builds the member context needed for daily recommendations |
| `taiyo-daily-brief` | Sends member context to Azure and returns a daily brief |
| `taiyo-nutrition-context` | Builds nutrition-specific context |
| `taiyo-workout-planner` | Prepares workout planning context and calls Azure |
| `taiyo-coach-client-brief` | Builds a privacy-safe coach-client context |
| `taiyo-store-recommendations` | Builds store context and product catalog for recommendations |
| `taiyo-admin-ops-brief` | Builds admin operational context for payment and subscription issues |

---

## 5. Member Daily Brief Context

The first planned context builder is:

```text
taiyo-member-context
```

It should prepare the data needed for a daily member brief.

Example output:

```json
{
  "request_type": "daily_member_brief",
  "user_role": "member",
  "member_context": {
    "member_id": "uuid",
    "goal": "fat_loss",
    "fitness_level": "beginner",
    "injuries": [],
    "readiness": {
      "score": 4,
      "sleep_hours": 5,
      "energy_level": 4,
      "stress_level": 3,
      "soreness_level": 2
    },
    "last_workout": {
      "date": "2026-04-28",
      "focus": "lower body",
      "completed": true
    },
    "weekly_adherence": {
      "planned_sessions": 4,
      "completed_sessions": 2
    },
    "nutrition_status": {
      "protein_status": "low",
      "hydration_status": "low"
    }
  }
}
```

---

## 6. Supabase Data Mapping

### Member Context

| Context Field | Supabase Source |
| --- | --- |
| `member_id` | `auth.uid()` |
| `goal` | `member_profiles` / `nutrition_profiles` |
| `fitness_level` | `member_profiles` |
| `injuries` | `member_profiles` / readiness notes |
| `readiness.score` | `member_daily_readiness_logs` |
| `sleep_hours` | `member_daily_readiness_logs` |
| `last_workout` | `member_active_workout_sessions` / `workout_task_logs` |
| `weekly_adherence` | `workout_task_logs` / `workout_plan_tasks` |
| `nutrition_status` | `meal_logs` / `nutrition_targets` |
| `hydration_status` | `hydration_logs` |

---

### Nutrition Context

| Context Field | Supabase Source |
| --- | --- |
| `nutrition_profile` | `nutrition_profiles` |
| `active_target` | `nutrition_targets` |
| `meal_logs` | `meal_logs` |
| `hydration_logs` | `hydration_logs` |
| `checkins` | `nutrition_checkins` |
| `meal_plan` | `member_meal_plans` / `member_planned_meals` |

---

### Workout Planner Context

| Context Field | Supabase Source |
| --- | --- |
| `active_plan` | `workout_plans` |
| `plan_days` | `workout_plan_days` |
| `plan_tasks` | `workout_plan_tasks` |
| `task_logs` | `workout_task_logs` |
| `ai_plan_drafts` | `ai_plan_drafts` |
| `active_sessions` | `member_active_workout_sessions` |

---

### Coach Client Context

| Context Field | Supabase Source |
| --- | --- |
| `subscription_status` | `subscriptions` |
| `visibility_settings` | `coach_member_visibility_settings` |
| `visibility_audit` | `coach_member_visibility_audit` |
| `client_insight` | `get_coach_member_insight(...)` |
| `messages` | `coach_messages` |
| `weekly_checkins` | `weekly_checkins` |
| `coach_notes` | `coach_client_notes` |

---

### Store Recommendation Context

| Context Field | Supabase Source |
| --- | --- |
| `products` | `products` |
| `favorites` | `product_favorites` |
| `cart` | `store_carts` / `store_cart_items` |
| `orders` | `orders` / `order_items` |
| `recommendation_history` | `member_product_recommendations` |

---

### Admin Ops Context

| Context Field | Supabase Source |
| --- | --- |
| `payment_orders` | `coach_payment_orders` |
| `transactions` | `coach_payment_transactions` |
| `subscriptions` | `subscriptions` |
| `payouts` | `coach_payouts` |
| `payout_items` | `coach_payout_items` |
| `audit_events` | `admin_audit_events` |
| `dashboard_summary` | `admin_dashboard_summary()` |

---

## 7. Security Rules

The integration should follow these rules:

- Flutter must not store Azure keys
- Flutter must not call Azure directly
- Supabase Edge Functions should verify the user session
- Admin functions should require admin permissions
- Coach data should respect member visibility settings
- Product recommendations should only use real product catalog data
- Payment secrets should never be exposed to Azure or Flutter
- AI responses should not include private data unless the user has permission

---

## 8. Missing Context Handling

The system should return clear statuses when required data is missing.

Examples:

| Situation | Expected Status |
| --- | --- |
| Store recommendation without product catalog | `needs_catalog_context` |
| Coach brief without visibility permission | `needs_visibility_permission` |
| Serious safety symptoms | `blocked_for_safety` |
| Not enough live data for high confidence | `needs_live_context` or conservative fallback |

This makes the frontend easier to handle because Flutter can display the right UI state.

---

## 9. Planned Azure Connection

After the Supabase Edge Functions are ready, Azure AI Foundry can be connected using:

- OpenAPI Actions
- Secure backend calls from Supabase
- Environment variables for endpoints and credentials

The preferred flow is:

```text
Azure Agent
→ Supabase Edge Function
→ Supabase Database / RPC
```

Not:

```text
Azure Agent
→ Supabase Database directly
```

---

## 10. First Implementation Target

The first function to design and implement should be:

```text
taiyo-member-context
```

Then:

```text
taiyo-daily-brief
```

This allows the system to support the first real end-to-end AI feature:

```text
Member opens GymUnity
→ Supabase builds daily context
→ Azure generates daily brief
→ Flutter displays TAIYO Today Card
```

---

## 11. Current Status

Current phase:

```text
Supabase Context Builder Design
```

Completed before this phase:

- Azure AI Foundry agents created
- Orchestrator routing tested
- Knowledge grounding tested
- Safety behavior tested
- Store missing-catalog behavior tested
- Coach visibility-permission behavior tested
- Admin Ops risk behavior tested

Next work:

- Define final context schemas
- Map each schema field to tables and RPCs
- Build the first Supabase Edge Function
- Test with real or staged Supabase data
- Connect the function to Azure
