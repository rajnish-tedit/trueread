# The Hub — Prototype Context

Single-file HTML prototype at `/home/user/trueread/index.html`.
Deployed via GitHub Pages from `main` branch at: https://rajnish-tedit.github.io/trueread/
Development branch: `claude/awesome-gauss-jmayva`

## Project
B2B SaaS dashboard ("The Hub") for multi-location roofing/sales businesses.
CRM-connected, role-based, single-page prototype — all code lives in `index.html`.

## Core Architecture
- Screen navigation: `goApp(id)` for app screens, `authGo(id)` for auth screens
- Modal system: `openModal(id)` / `closeModal(id)` / `closeModalBack(event)`
- Toast: `showToast(msg)`
- Roles: Owner / Branch Manager / Regional Manager — controlled by `applyRoleUI()`, `demoSwitchRole()`
- `enterApp()` — transitions auth → app shell
- Demo bar: slim top strip with role switcher buttons

## Onboarding Flow (current)
```
signup → OTP → company-setup → plan (pricing) → crm-pick → syncing → marketing-pick → enterApp()
```
After `enterApp()`:
1. Welcome email modal fires (new signups only, not manager joins)
2. After closing welcome modal → CRM sync progress overlay appears

## Screens Built
### Auth
- `scr-signin`, `scr-signup`, `scr-otp`
- `scr-company-setup` — Company name, Street, City, State (dropdown), ZIP, Phone. No company type (auto-detected by CRM).
- `scr-plan` — Pricing transparency screen (no location stepper). Monthly/Annual cards. Trial timeline (Day 1–30 free, Day 25 reminder, Day 31 choice). CRM auto-detect callout.
- `scr-crm-pick` — CRM connector selection
- `scr-marketing-pick` — Marketing tools
- `scr-syncing` — CRM sync animation
- `scr-branches` — Branch/manager invite (kept for demo bar only, NOT in new-user flow)
- `scr-invite-email`, `scr-join-accept`, `scr-join-profile`, `scr-invite-expired` — Manager invite flow

### App Screens
- `app-dashboard` — KPIs, branch comparison table, branch filter strip
- `app-contacts`, `app-r1`–`app-r5` — Reports
- `app-users` — Users & Seats table with functional Edit/Re-invite/Revoke buttons
- `app-billing` — Subscription & Billing
- `app-settings` — Settings with Branches & Managers tab
- `app-comms`, `app-audit`, `app-help`

## Key Modals
- `modal-welcome-email-modal` — Shows after signup. Dark hero header with "30 days — $0.00" badge, dynamic trial end date, next bill date, NJ compliance notice (no auto-charge), colour-coded timeline.
- `modal-edit-user-modal` — Edit user name/role/branch + Remove user
- `modal-plan-limit-modal` — Location limit reached
- `modal-upgrade-modal` — Upgrade prompt

## CRM Sync Overlay (`#sync-overlay`)
- Bottom-sheet overlay, fires after welcome modal is closed (new signups only)
- 4 animated streams: Contacts (~3,400), Deals (~820), Activities (~12,000), Locations
- Live log, overall % bar, ETA countdown
- Minimize to floating chip (`#sync-chip`), restore on click
- "Notify me when done" button
- Auto-dismisses 3s after 100% with toast

## Pricing Model (current)
- Base: $199/month (1 location included)
- Each additional location: $149/month
- Annual: $166/mo billed as $1,990/yr; extra location $1,490/yr
- Locations auto-detected by CRM after signup — NOT entered manually
- No card captured at signup → no auto-billing → trial ends/pauses on Day 31

## Users & Seats (`app-users`)
- Table rows: John Smith (Owner), Marcus Webb, Lisa Chen, Derek Osei (Sales Reps), Priya Patel (Branch Mgr), Kim Nguyen (pending)
- Edit button → `openEditUser()` modal (name, role, branch fields + Remove User)
- Re-invite → `resendUserInvite()` toast
- Revoke → `revokeUserInvite()` marks row Revoked, swaps to Re-invite only

## Role Views (demo bar)
- **Owner** (`applyOwnerRole`): all sidebar items, branch comparison table, all branch chips
- **Branch Manager** (`applyManagerRole`): branch-locked KPI header, hides billing/branches nav, hides branch strip
- **Regional Manager** (`applyRegionalRole`): shows comparison table, NJ+NY chips only, hides billing/branches nav

## Business Rules / Compliance
- No credit card at signup → NJ Consumer Fraud Act compliant → no auto-charge
- Welcome modal explicitly states: "no billing action without written authorization"
- Day 25: reminder email sent with exact price based on CRM-detected locations
- Day 31: account pauses if no payment method added — never silently charged

## Git Workflow
- Always develop on `claude/awesome-gauss-jmayva`
- On merge conflict: `git fetch origin main && git rebase origin/main && git push --force-with-lease`
- PR → squash merge → deploy auto-triggers on GitHub Pages
