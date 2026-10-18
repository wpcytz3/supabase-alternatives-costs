# Supabase Alternatives: Which Piece to Replace, What Each Option Actually Costs, and When Self-Hosting on a VPS Wins

Searches for Supabase alternatives usually start with the same two facts: free projects pause after one week without traffic, and the first paid tier is $25 a month with per-project compute billed on top. Supabase's own worked example prices a two-project Pro organization at $35 a month before a single user signs up — $25 for the plan, $10 per Micro compute instance, minus a $10 credit — and paid projects never scale back to zero. The compute bills 24 hours a day whether anyone visits your app or not.

There is no single swap that fixes this, because Supabase is five products sold as one: a Postgres database, authentication, file storage, realtime, and edge functions. The right alternative depends entirely on which of the five your bill is actually coming from. This guide covers the hosted options with current numbers, the auth-only shortcut that lets you keep your database untouched, and the route most comparisons skip — self-hosting the stack, or just Postgres, on a cheap cloud server, with the real monthly cost written out.

> **Quick picks**
>
> - **You only really used the Postgres database.** Keep Postgres, drop the rest: a small cloud server (from $5/mo) or managed PostgreSQL (from $15/mo) covers it. [👉 See Vultr's compute and managed Postgres pricing](https://www.vultr.com/pricing/?ref=7268283)
> - **You want a hosted, like-for-like bundle.** Appwrite is the closest open-source match; Firebase fits mobile-first apps.
> - **Your traffic is spiky and mostly idle.** Neon suspends compute after 5 minutes and bills nothing while it sleeps.
> - **Solo project, and SQLite is enough.** PocketBase, one binary, free.

## Why teams actually leave Supabase

Four specific things push teams off the platform, and none of them is the database itself.

**The free-tier pause.** Free projects pause after one week of inactivity. For a demo or a low-traffic internal tool, that means babysitting your own backend to keep it awake.

**The cost shape.** Pro starts at $25/month for the organization, but compute is billed separately for every project: Micro add-ons start at $10/month and climb to $110 for Large and $210 for XL. Egress beyond the included 250 GB costs $0.09 per GB, and monthly active users beyond 100,000 cost $0.00325 each.

**The startup program is gone.** Supabase quietly ended its startup credits program. As of September 2026 there's no application form, no published credit amount, and the startup-program page returns a 404. Perks sites are still republishing the old terms as if you could apply today — if an article written earlier this year promises Supabase startup credits, that part is expired.

**Self-hosting has real conditions.** Supabase is open source and officially self-hostable via Docker Compose, but its own documentation flags that branching, managed backups, point-in-time recovery, and the Management API are unavailable when you self-host, and support is community-only.

## Work out which of the five products you're replacing

Open your Supabase usage page before shopping. Most teams discover they use two of the five products at most, and that answer decides everything else.

| If your bill comes from | What to swap in |
| --- | --- |
| The Postgres database only | Serverless Postgres (Neon), dedicated clusters (PlanetScale), or managed PostgreSQL on your own cloud |
| Authentication only | Auth0, MojoAuth — or a third-party JWT issuer while keeping Supabase Postgres (explained below) |
| File storage only | Cloudflare R2 or any S3-compatible object storage |
| All five at once | Firebase, Appwrite, Nhost — or self-host the full stack |
| Nothing specific | Tune your RLS policies and slow queries before paying for a migration |

## The hosted alternatives, with the numbers that matter

Seven hosted options cover most of the space. The table is the short version, with free tiers and entry prices as listed on each vendor's own pricing pages in early October 2026. Prices move often, so confirm before you commit.

| Option | Replaces | Free tier | Entry paid price | Main catch |
| --- | --- | --- | --- | --- |
| Firebase | Full bundle | Firestore 1 GiB stored, 50K reads/day, 20K writes/day; Auth 50K MAU; 5 GB storage | Pay as you go (Blaze) | Document model, so joins become app code; the Postgres path (Data Connect) starts at $9.37/mo after a 3-month trial |
| Appwrite | Full bundle | 75K MAU, 2 GB storage, 5 GB bandwidth, 2 projects | Cloud Pro from $25/mo; dedicated DB compute from $10/mo | Free projects pause after a week idle — the exact behavior you may be fleeing; document database, not Postgres |
| Nhost | Full bundle on Postgres + GraphQL | Starter (free) | Pro from $25/mo | Hasura permissions layer to learn on top of Postgres |
| Neon | Postgres only | 100 projects, 0.5 GB storage and 100 CU-hours each per month, 5 GB egress | $0.106/CU-hour + $0.35/GB-month (Launch) | Database only; exceeding a free limit suspends compute for the rest of the month |
| PlanetScale | Postgres only, dedicated | None | $5/mo | No free tier; bring your own auth and storage |
| Convex | Data + functions + realtime, TypeScript-native | Yes | Professional $25/dev/mo | Not Postgres; you write every authorization check by hand |
| PocketBase | Full bundle, self-hosted | Free (MIT license) | Free — you pay only for a server | SQLite, single node, still pre-1.0 with breaking changes between releases |

A few of these deserve a sentence more.

**Firebase** is the most common full-bundle move, and the shift that matters is the data model: relational queries become application logic. Its Data Connect product narrows that gap if you specifically want Postgres on Google's stack.

**Appwrite** was designed from the ground up for self-hosting and exposes auth, databases, storage, and functions the way Supabase does. Just know its cloud free tier also pauses projects after a week idle, so the pause problem follows you unless you self-host.

**Neon** is the closest thing to a drop-in database replacement: scale-to-zero after 5 minutes, Git-style branching with 10 branches per project included (then $1.50 per branch-month), and autoscaling up to 16 CU on paid plans. Databricks acquired Neon in May 2025 for a reported $1 billion, so its roadmap now sits inside a much larger company.

**PlanetScale** sells dedicated clusters with millisecond billing prorated daily — the pick if noisy-neighbor latency has burned you before. Postgres and MySQL are both available.

The edges of the list, briefly: Turso (SQLite-compatible libSQL) suits local-first or database-per-tenant designs; AWS Amplify fits teams already standardized on AWS, with DynamoDB behind AppSync and pay-as-you-go pricing; Directus is for when you already have a SQL database and only need an API, an admin UI, and roles on top.

### You may not need to migrate at all

If your actual complaint is Supabase's auth pricing or features, there's a shortcut most people miss: Supabase officially supports third-party auth issuers, including Auth0, Clerk, Firebase Auth, AWS Cognito, and WorkOS. You keep Supabase Postgres, your row-level security policies, Storage, and Realtime, and change only who signs the JWT. Migration cost drops to near zero.

If you do want auth-only alternatives: Auth0's free plan covers 25,000 MAU with unlimited social connections, but MFA sits behind paid plans. MojoAuth covers 25,000 MAU with email OTP, magic links, and social login; passkeys and MFA live on its Business Pro tier.

## The route most comparisons skip: self-hosting Supabase on your own server

Supabase itself is free software, and self-hosting the real thing is a legitimate alternative — with eyes open. The full stack is roughly a dozen services: Postgres, GoTrue, PostgREST, Realtime, Storage, Kong, and Studio among them. Budget about 4 GB of RAM and real upgrade work. The officially documented path is Docker Compose, and you can start from a server image with Docker preinstalled.

What you give up is the managed half of the product: no branching, no managed backups, no point-in-time recovery, no Management API, community-only support. That makes two decisions matter more than any other — the server you pick, and who owns backups.

A provider like Vultr sells exactly the pieces this setup needs, billed hourly with a monthly cap, across 32 data center locations worldwide. Vultr's full catalog also includes bare metal, Kubernetes, and load balancers; the rows below are the ones that map to a Supabase stack.

| Vultr product | What it covers | Entry configuration | Monthly price | Deploy |
| --- | --- | --- | --- | --- |
| Cloud Compute — Regular | Docker host for self-hosted Supabase, or Postgres + your own app | 1 vCPU, 1 GB RAM, 25 GB SSD, 1 TB transfer | $5 | [ Deploy Cloud Compute](https://www.vultr.com/pricing/?ref=7268283#cloud-compute) |
| Cloud Compute — Regular, 4 GB | The realistic floor for the full ~12-service stack | 2 vCPU, 4 GB RAM, 80 GB SSD, 3 TB transfer | $20 | [ Check the 4 GB tier](https://www.vultr.com/pricing/?ref=7268283#cloud-compute) |
| Cloud Compute — High Performance, 4 GB | Same footprint on NVMe with faster cores | 2 vCPU, 4 GB RAM, 80 GB NVMe | $24 | [ Check High Performance](https://www.vultr.com/pricing/?ref=7268283#cloud-compute) |
| Managed Databases for PostgreSQL | Postgres without the ops: automated backups and maintenance handled | 1 vCPU, 1 GB RAM, 25 GB storage | $15 | [ See managed Postgres plans](https://www.vultr.com/pricing/?ref=7268283#managed-databases) |
| Object Storage | S3-compatible file storage and offsite backup target | 250 GB storage, 1 TB bandwidth | $5 | [ See Object Storage](https://www.vultr.com/pricing/?ref=7268283#object-storage) |

### What a self-hosted stack on Vultr actually costs

The realistic build for the full Supabase stack is the 4 GB tier — $20/month on the Regular line, $24 on High Performance if you want NVMe and faster cores — plus $5/month of Object Storage as an offsite target for database dumps. Call it **$25/month, fixed**. No per-project compute meter, no MAU meter, no egress surprise. And because instances bill hourly, destroying a server stops the meter entirely — there's no 24/7 compute charge for a project nobody visits. [👉 Check Vultr's current 4 GB compute pricing](https://www.vultr.com/pricing/?ref=7268283#cloud-compute)

Set that against a two-project Supabase Pro organization at $35/month plus whatever the meters add, and the fixed bill holds up. The honest trade is your time: upgrades, server hardening, and actually testing a restore. If you lean on auth, storage, and realtime together, those are three subsystems you'd now be maintaining yourself — which is exactly why running the official stack via Docker Compose on the $20 box beats hand-assembling pieces one by one.

For backup insurance, Vultr charges $0.05/GB per month for snapshots, or 20% on top of the instance price for automatic daily backups. Either option is cheaper than discovering your backup strategy was a screenshot.

### When managed Postgres on Vultr beats running it yourself

If the database is the only piece you need and you'd rather not patch Postgres at 2 a.m., Vultr's Managed Databases for PostgreSQL starts at $15/month for 1 vCPU, 1 GB RAM, and 25 GB of storage, with automated backups and maintenance handled for you. Pair it with a $5–10 compute instance for your app, and you've rebuilt the "Postgres behind an API I wrote anyway" setup on a flat monthly bill. [👉 See Vultr's managed PostgreSQL plans](https://www.vultr.com/pricing/?ref=7268283#managed-databases)

What you don't get is Studio, the auto-generated API layer, and the SDK ergonomics. If those were why you picked Supabase in the first place, self-host the full stack or stay on Pro rather than rebuilding three services by hand.

## A decision rule you can actually use

Match the plan to your usage pattern rather than to the brand you're leaving:

- **Traffic is spiky and idle-heavy.** Neon's 5-minute scale-to-zero, or a Vultr instance you destroy between phases — hourly billing means a stopped server stops charging.
- **Steady traffic, and you use auth + storage + realtime heavily.** Staying on Supabase Pro is the least-work option. Appwrite if you want an open-source exit path.
- **You only used the database behind your own API.** A $5–20/month server with Postgres, or $15/month managed Postgres. Swap auth for a library you control.
- **Solo project, and SQLite is enough.** PocketBase in one binary, and you're done in an evening.
- **Mobile-first with offline sync.** Firebase's document model is built for exactly this.
- **Predictable performance or compliance matters.** PlanetScale's dedicated clusters or a larger managed Postgres tier, not shared compute.

## FAQ

**Is self-hosting Supabase actually cheaper?**
Sometimes, and only on a fixed-bill basis. A 4 GB server plus storage runs about $25/month flat, which matches Supabase Pro's floor — the savings come from the meters (per-project compute, egress, MAU) that stop applying. Your ops time is the offset, and the stack is about a dozen services. If you'd spend $25 anyway and nobody on your team can own backups, staying put is a rational answer.

**Can I keep Supabase's Postgres and replace only the auth?**
Yes. Supabase officially supports third-party JWT issuers including Auth0, Clerk, Firebase Auth, AWS Cognito, and WorkOS. Your Postgres, RLS policies, Storage, and Realtime stay untouched — you only change who signs the token.

**Does Supabase still have a startup credits program?**
No. As of September 2026 there's no application form, no published amount, and the program page returns a 404. Older blog posts and perks sites still list it; treat those as expired.

**What's the cheapest way to keep just Postgres?**
For experiments, a serverless free tier like Neon's works well. For anything always-on, a $5/month VPS running Postgres, or managed Postgres from $15/month if nobody on your team owns the backup job.

**How much RAM does self-hosted Supabase need?**
About 4 GB for the full stack of roughly a dozen services. If you're only running Postgres plus your own application, 1 GB is workable for small projects.

## Before you click deploy

Three checks, in order. Open your Supabase usage page and write down which products show non-zero usage — that list is your real migration scope, and for most teams it's shorter than they assumed. Decide who owns backups in the new setup, because a database without a tested restore is a liability with extra steps. Then match the plan to that scope: one piece, swap only it; three pieces, take a bundle or run the real stack yourself on a $20 box. [👉 Open Vultr's pricing page and match a plan to your usage](https://bit.ly/VulTR)
