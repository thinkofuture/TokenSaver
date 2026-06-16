# React Native App — TokenSaver Example

> A lightweight agent-agnostic project context system for **FitTrack**, a fitness tracking app with Expo, Supabase, Zustand, and AI training plans.

---

## Generated Files

```
.tokensaver/
├── overview.md
├── architecture.md
├── codebase-map.md
├── conventions.md
├── decisions.md
├── gotchas.md
└── session-handoff.md

TOKENSAVER.md
```

---

## .tokensaver/overview.md

```markdown
# FitTrack — Overview

> Mobile fitness tracker with AI workout plans. iOS + Android via Expo.

**Last updated:** 2026-04-01

---

## What

Log workouts, track progress, get AI-generated training plans. 50K+ exercise database, Apple Health / Google Fit sync, social features.

## Tech Stack

| Layer | Tech |
|-------|------|
| Framework | Expo SDK 52 (React Native) |
| Language | TypeScript (strict) |
| Navigation | Expo Router (file-based) |
| State | Zustand (client) + TanStack Query (server) |
| Backend | Supabase (PostgreSQL, Auth, Edge Functions) |
| AI | OpenAI GPT-4o (training plan generation) |
| UI | NativeWind (Tailwind for RN) |
| Distribution | Expo EAS (App Store + Play Store) |

## Key URLs

- Supabase Dashboard: https://supabase.com/dashboard/project/fittrack
- EAS Dashboard: https://expo.dev/accounts/fittrack
- Figma: https://figma.com/file/fittrack-designs

## Related
- [[codebase-map]] — directory structure
- [[architecture]] — app architecture
```

---

## .tokensaver/codebase-map.md

```markdown
# FitTrack — Codebase Map

> Annotated directory tree. Where things live.

**Last updated:** 2026-04-01

---

```
fittrack/
├── app/                        # Expo Router (file-based)
│   ├── (auth)/                 # Login, register, onboarding
│   ├── (tabs)/                 # Home, Log, Progress, Profile
│   ├── workout/[id]/           # Workout detail
│   └── plan/[id]/              # Training plan detail
├── src/
│   ├── components/
│   │   ├── ui/                 # Primitives (Button, Input, Card)
│   │   ├── workout/            # Workout-specific components
│   │   └── charts/             # Victory Native wrappers
│   ├── features/               # Feature modules (screens + logic)
│   │   ├── auth/               # Auth flow
│   │   ├── workout-logging/    # Active workout editing
│   │   ├── training-plan/      # AI plan generation
│   │   └── social/             # Friends + feed
│   ├── stores/                 # Zustand (auth, active workout, settings)
│   ├── api/                    # TanStack Query + Supabase client
│   └── lib/                    # Utilities (exercise DB, calculations)
├── supabase/
│   ├── migrations/             # DB migrations
│   └── functions/              # Edge Functions (Deno)
└── tests/
    ├── unit/                   # Jest
    └── e2e/                    # Detox (critical flows only)
```

## Key Patterns

- **Feature-based organization.** Features own screens, components, hooks, and API.
- **Supabase as sole backend.** No custom server. Edge Functions for compute.
- **Zustand for UI state.** TanStack Query for server cache. Clear boundary.
- **No StyleSheet.create().** NativeWind exclusively.
- **FlatList for all scrollable content.** Never map() + ScrollView.

## Related
- [[conventions]] — coding patterns
- [[architecture]] — component design
```

---

## .tokensaver/decisions.md

```markdown
# FitTrack — Decisions

**Last updated:** 2026-04-01

---

## ADR-001: Expo Managed over Bare RN

**Date:** 2025-09-01 | **Status:** Accepted

**Context:** Need iOS + Android with 2 engineers.

**Decision:** Expo managed + EAS Build.

**Rationale:**
- OTA updates without App Store review
- EAS handles native compilation
- Config plugins for native modules
- One engineer covers both platforms

**Consequences:** Some native modules need config plugins. EAS queue times during peak.

---

## ADR-002: Supabase over Custom Backend

**Date:** 2025-09-15 | **Status:** Accepted

**Context:** Need auth, DB, storage, compute. No backend team.

**Decision:** Supabase for all backend infra.

**Rationale:**
- Built-in Apple/Google Sign-In
- PostgreSQL + RLS for data isolation
- Edge Functions for compute
- Open source — no lock-in

**Consequences:** RLS is sole security layer — must test thoroughly. Cold starts on Edge Functions.
```

---

## .tokensaver/gotchas.md

```markdown
# FitTrack — Gotchas

> Mobile-specific pitfalls.

**Last updated:** 2026-04-01

---

## Build

- **EAS Build needs explicit system image** for Android. `eas.json`: `image: ubuntu-22.04-jdk-17`.
- **Apple Sign-In needs paid dev account.** Mock `expo-apple-authentication` in dev.
- **Run `npx expo prebuild --clean`** after native dep changes or builds fail.

## State

- **Zustand persist can corrupt AsyncStorage** on crash. Validate shape after hydration.
- **TanStack Query stale time is 30s** for social feed. Manual refresh may not invalidate.

## Supabase

- **RLS must have integration tests.** Easy to get wrong. Test every table with role variations.
- **Anon key is bundled in the app.** RLS is the only security — never trust "key is secret."

## Performance

- **Victory Native (SVG) lags with >100 data points.** Aggregate on backend.
- **FlatList required for all lists.** ScrollView + map() freezes on long lists.

## Related
- [[architecture]] — overall design
```

---

## .tokensaver/session-handoff.md

```markdown
# FitTrack — Session Handoff

**Last updated:** 2026-04-01

---

## In Progress

- AI training plan generation: streaming implemented but needs error handling for OpenAI timeouts
- Apple Watch companion app: data model specified, implementation starting next sprint

## Next Steps

- Migrate TanStack Query to v5
- Add offline workout sync conflict resolution (currently last-write-wins)
- Implement push notification reminders for missed workouts

## Blockers

- Apple Health permissions for watch — waiting on App Review for HealthKit entitlement

## Last Session

Launched AI training plan generation via Supabase Edge Function + GPT-4o. Streaming responses working but need to add retry logic and better error UX for rate limits.
```

---

## Optional: AGENTS.md Adapter

If the project needs generic agent compatibility, add this thin adapter:

```markdown
# AGENTS.md

## TokenSaver
Read `TOKENSAVER.md` and `.tokensaver/` before scanning source code.
```

---

*This example shows a complete agent-agnostic context system for a React Native app. Mobile-specific concerns — native modules, EAS Build, offline, permissions — are captured concisely. `TOKENSAVER.md` is the universal entry point.*
