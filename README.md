# StructTech Client Portal

Authenticated portal for StructTech clients — portal.structtek.com

## What clients see
- **Roadmap** — their execution roadmap with live progress; they check off YOUR MOVE items
- **Your Systems** — status board of custom systems (PLANNED / IN BUILD / TRAINING / LIVE) with links
- **Requests** — two-way tickets with StructTech
- **Billing** — open invoices, future phase pricing, paid history (display-only)

## Auth model
- Supabase Auth (email/password), org-scoped via RLS
- `organizations` → businesses; `org_members` → users per business (multi-user native); `org_invites` → tokened invites
- Invite link: `portal.structtek.com/?invite=TOKEN` → signup → auto-joins the org

## ⚠ One-time Supabase setup
Dashboard → Authentication → Sign In / Up:
1. Email provider: **enabled**
2. **Disable "Confirm email"** for the smoothest invite flow (or configure SMTP + templates if you want confirmation)
3. Auth → URL Configuration → Site URL: `https://portal.structtek.com`

## Creating a client (until the OS Clients tab exists)
Run in Supabase SQL Editor:
```sql
-- 1. Create the org (link deal if exists)
insert into organizations (name, trade) values ('Brothers Metal Roofing', 'Roofing') returning id;

-- 2. Link their roadmap (use org id from step 1)
update client_roadmaps set org_id = 'ORG_ID' where token = 'THEIR_ROADMAP_TOKEN';

-- 3. Create the invite (returns the token)
insert into org_invites (org_id, email, role)
values ('ORG_ID', 'client@example.com', 'owner') returning token;
```
Send them: `https://portal.structtek.com/?invite=TOKEN`

Add systems / invoices the same way (`org_systems`, `org_invoices`) — or wait for the OS Clients tab (next build).

## Deploy
GitHub repo `structtech-portal` → Pages → custom domain `portal.structtek.com`
(Wix DNS: CNAME `portal` → `jacobaw1995.github.io`)

## Next phases (per portal roadmap)
1. **Clients tab in StructTech OS** — create orgs, invites, systems, invoices, answer tickets from the admin side (no SQL)
2. Wins/ROI tracker · weekly digest email (Make) · approvals · files · Stripe
