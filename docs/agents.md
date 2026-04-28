# 🤖 TAIYO Agents Overview

This document explains the agents used in the **TAIYO AI Agent System** and the role of each one inside GymUnity.

TAIYO is designed as a multi-agent system. Instead of using one general chatbot for every task, the system separates responsibilities between specialized agents.

---

## 1. Agent Structure

The system is built around one main coordinator:

- **TAIYO Orchestrator Agent**

And a group of specialized agents:

- **TAIYO Member Fitness Agent**
- **TAIYO Nutrition Agent**
- **TAIYO Workout Planner Agent**
- **TAIYO Safety and Recovery Agent**
- **TAIYO Coach Copilot Agent**
- **TAIYO Store Recommendation Agent**
- **TAIYO Admin Ops Agent**

---

## 2. TAIYO Orchestrator Agent

The Orchestrator Agent is the main coordinator of the system.

Its role is to:

- Understand the request type
- Decide which specialized agent should handle the request
- Route the task to one or more agents
- Apply high-level safety and privacy rules
- Return one final structured JSON response

Example request types:

```text
daily_member_brief
workout_plan_draft
coach_client_brief
store_recommendation
admin_ops_brief
```

The Orchestrator does not replace the specialized agents. It manages them.

---

## 3. Specialized Agents

| Agent | Main Role |
| --- | --- |
| Member Fitness Agent | Generates daily fitness guidance based on member context |
| Nutrition Agent | Provides general nutrition and hydration guidance |
| Workout Planner Agent | Drafts workout plans and plan adjustments |
| Safety and Recovery Agent | Reviews safety risks, red flags, fatigue, and recovery needs |
| Coach Copilot Agent | Helps coaches understand client status with privacy checks |
| Store Recommendation Agent | Recommends products only from a valid product catalog |
| Admin Ops Agent | Explains payment, subscription, payout, and operational issues |

---

## 4. Member Fitness Agent

The Member Fitness Agent focuses on daily training guidance.

It can use context such as:

- Member goal
- Fitness level
- Readiness score
- Sleep
- Recent workouts
- Injuries or discomfort
- Weekly adherence
- Progress notes

Example output areas:

- Training decision
- Workout focus
- Risk level
- Motivation message
- Reason for recommendation

---

## 5. Nutrition Agent

The Nutrition Agent provides general nutrition support.

It can help with:

- Protein focus
- Hydration focus
- Meal consistency
- Calorie guidance
- Nutrition habits related to the member goal

The Nutrition Agent is not designed to diagnose medical conditions or create medical diets.

---

## 6. Workout Planner Agent

The Workout Planner Agent drafts workout structures.

It can help with:

- Weekly workout split
- Training focus by day
- Exercise planning logic
- Intensity guidance
- Progression notes
- Deload or recovery suggestions

The agent drafts plans only. Final activation into app data should be handled by Supabase backend logic.

---

## 7. Safety and Recovery Agent

The Safety and Recovery Agent is used to make the system more conservative when risk is present.

It checks for cases such as:

- Chest pain
- Dizziness
- Severe pain
- Fainting
- Breathing difficulty
- Serious injury
- Very low readiness
- Pain during movement

When serious symptoms are present, the system should avoid training recommendations and escalate the case safely.

---

## 8. Coach Copilot Agent

The Coach Copilot Agent supports coaches by summarizing client status.

It can help with:

- Client adherence
- Readiness trends
- Check-in summary
- Possible red flags
- Suggested coach action
- Suggested message draft

Privacy is important here. The agent should only work when the member has allowed the coach to access the relevant data.

If visibility permission is not confirmed, the system should return:

```text
needs_visibility_permission
```

---

## 9. Store Recommendation Agent

The Store Recommendation Agent suggests products based on member context and catalog availability.

It can consider:

- Member goal
- Training focus
- Nutrition gaps
- Product category
- Product availability

The agent should only recommend products from a provided catalog.

If no product catalog is provided, the system should return:

```text
needs_catalog_context
```

The agent should not invent products or make medical claims.

---

## 10. Admin Ops Agent

The Admin Ops Agent helps explain operational and payment-related issues.

It can support cases related to:

- Payment orders
- Paymob transactions
- Subscription status
- Coach payout items
- Missing coach/member threads
- Admin reconciliation actions

Example recommendation:

```text
admin_reconcile_payment_order
```

This agent should never expose payment secrets or sensitive backend credentials.

---

## 11. Routing Examples

| Request | Expected Agent Routing |
| --- | --- |
| Daily member brief | Member Fitness Agent + Nutrition Agent + Safety and Recovery Agent |
| Workout plan draft | Workout Planner Agent + Safety and Recovery Agent |
| Coach client brief | Coach Copilot Agent + Safety and Recovery Agent when needed |
| Store recommendation | Store Recommendation Agent |
| Admin payment issue | Admin Ops Agent |

---

## 12. Output Style

TAIYO is designed to return structured JSON responses instead of long unstructured text.

This helps the Flutter app render the result as UI cards, badges, summaries, and actions.

Example response areas:

- `status`
- `selected_agents`
- `risk_level`
- `result`
- `ui_cards`
- `next_backend_action`
- `confidence`

---

## 13. Current Status

The agents have been created and tested manually as part of the Azure AI Foundry MVP phase.

The next step is to connect the agent system to live Supabase context through secure Edge Functions.
