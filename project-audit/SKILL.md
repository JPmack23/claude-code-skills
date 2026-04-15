# Project Audit — 1Team Deal Hub Admin

> **Trigger phrases:** "audit my code", "run a full review", "check my code", "security check", "code quality check", "review this", "run audit"

## Stack

- **Frontend:** React 18 + TypeScript + Vite + Tailwind CSS + shadcn/ui
- **State/Data:** TanStack React Query + Supabase JS client
- **Rich Text:** TipTap editor
- **Charts:** Recharts
- **Backend:** Supabase (Auth, Postgres, Edge Functions, Storage)
- **Email:** Resend (OTP, transactional)
- **Deployment:** Vercel (admin), Expo/EAS (mobile)

---

## Audit Phases

Run each phase in order. For each phase, scan the relevant directories and produce findings.

### Phase 1 — Security (Critical)

**1.1 OWASP Top 10 Check**
- Scan `src/` for XSS vulnerabilities (especially in components using `dangerouslySetInnerHTML` or DOMPurify)
- Check for injection risks in any dynamic SQL or user-input handling
- Review authentication flows in `src/` and `supabase/functions/`

**1.2 Hardcoded Secrets**
- Search ALL files for patterns: API keys, tokens, passwords, connection strings
- Check `.env`, `.env.local`, `.env.production` files
- Scan `supabase/functions/` for hardcoded credentials
- Check `src/integrations/` for exposed keys (anon key is OK, service role key is NOT)
- Verify `.gitignore` excludes sensitive files

**1.3 Authentication & Authorization**
- Review Supabase auth configuration in `src/integrations/supabase/`
- Check RLS (Row Level Security) policies in `supabase/migrations/`
- Review edge function auth patterns in `supabase/functions/`
- Check for missing auth guards on protected routes
- Verify OTP/email auth flow security

**1.4 Database Security**
- Review all SQL migrations for injection vulnerabilities
- Check RLS policies are applied to all tables with sensitive data
- Verify edge functions use proper authorization before database operations
- Check for overly permissive policies (e.g., allowing any authenticated user to modify any row)

### Phase 2 — Code Quality

**2.1 DRY / KISS / YAGNI**
- Flag duplicated logic across components (especially in `src/pages/` and `src/components/`)
- Flag over-engineered abstractions that add complexity without clear benefit
- Flag unused code paths, feature flags, or dead branches

**2.2 Dead Code Detection**
- Find unused exports in `src/lib/`, `src/hooks/`, `src/components/`
- Find unused imports across all `.tsx` and `.ts` files
- Find functions/components that are defined but never referenced

**2.3 Complexity Check**
- Flag functions over 50 lines
- Flag deeply nested logic (3+ levels of nesting)
- Flag components with too many props (10+) or too many state variables (8+)

**2.4 Dependency Audit**
- Check `package.json` for outdated dependencies
- Flag any dependencies with known vulnerabilities (check npm audit output)
- Flag unused dependencies (declared but never imported)
- Flag risky or unmaintained packages

### Phase 3 — Architecture

**3.1 Layer Boundaries**
- Check if business logic is leaking into UI components (should be in hooks/lib)
- Check if database queries are happening directly in components (should be in hooks)
- Verify Supabase client usage is properly abstracted

**3.2 API Contract Validation**
- Check edge functions for input validation (using Zod or manual checks)
- Verify API responses have consistent shape
- Check for proper error handling in API calls from frontend

**3.3 Environment Configuration**
- Verify env vars are accessed consistently (via import.meta.env or a config module)
- Check that no env vars are used without fallbacks in production-critical paths
- Verify Supabase URL and keys are properly configured

### Phase 4 — Performance

**4.1 Query Efficiency**
- Check for N+1 query patterns in React Query hooks
- Look for missing `.select()` on Supabase queries (fetching all columns unnecessarily)
- Check for missing pagination on list queries
- Review any `.rpc()` calls for efficiency

**4.2 Bundle Size**
- Check for large imports that could be lazy-loaded
- Flag any full-library imports where tree-shaking is available (e.g., importing all of lodash)
- Check for unnecessary dependencies that bloat the bundle

**4.3 React Performance**
- Flag missing `key` props in lists
- Flag expensive computations in render without `useMemo`
- Check for unnecessary re-renders from inline object/function creation in JSX props
- Flag missing `useCallback` on event handlers passed to memoized children

### Phase 5 — Test Coverage

**5.1 Critical Path Coverage**
- Check if auth flows have tests
- Check if API endpoints (edge functions) have tests
- Check if data mutation flows (create/update/delete deals) have tests

**5.2 Test Gaps**
- Identify untested edge functions
- Identify untested pages/components that handle user input
- Flag any error handling paths that are untested

**5.3 E2E Tests**
- Check `scripts/e2e-test.mjs` for coverage completeness
- Flag any API endpoints not covered by the e2e suite

---

## Output Format

Produce the report in this exact structure:

```
# Code Audit Report — deal-hub-admin
**Date:** [current date]
**Auditor:** Claude Code

## Summary

| Phase | Status | Issues |
|-------|--------|--------|
| Security | 🔴/🟡/🟢 | X critical, Y warnings |
| Code Quality | 🔴/🟡/🟢 | X critical, Y warnings |
| Architecture | 🔴/🟡/🟢 | X critical, Y warnings |
| Performance | 🔴/🟡/🟢 | X critical, Y warnings |
| Test Coverage | 🔴/🟡/🟢 | X critical, Y warnings |

## Critical Issues (Must Fix)
[Numbered list with file paths and line numbers]

## Recommended Fixes (Should Fix Soon)
[Numbered list with file paths and line numbers]

## Nice to Have (Low Priority)
[Numbered list]

## What's Working Well
[Bullet list of positives to reinforce]
```

---

## Important Notes

- Always scan `supabase/functions/` — these are Deno edge functions, not Node.js
- The `_shared/` directory in edge functions contains shared utilities — review these carefully as they affect all functions
- Check `supabase/migrations/` for RLS policies — these are the primary authorization layer
- The anon key in `src/integrations/supabase/client.ts` is intentionally public — do NOT flag it
- Service role keys should NEVER appear in frontend code — flag immediately if found
- Import scripts in `scripts/` use the service role key — this is expected but verify they're in `.gitignore`
