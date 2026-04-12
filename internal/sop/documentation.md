---
title: Documentation
description: 
published: true
date: 2026-04-12T00:26:38.255Z
tags: 
editor: markdown
dateCreated: 2026-04-11T23:22:56.625Z
---

# Documentation SOP

This is the rulebook for how our team creates and maintains documentation. Read this before writing your first page. It is short — follow it and the wiki stays useful. Ignore it and it becomes a mess nobody trusts.

## Why This Exists

Before this wiki, IT knowledge lived in one person's head. When that person is busy or unavailable, everyone is stuck. This wiki fixes that — but only if everyone writes docs the same way.

The goal is simple: **anyone on the team should be able to find what they need without asking.**


## Who Writes What

| Role | Responsible for |
|---|---|
| IT Admin | Infrastructure, apps, licensing, onboarding, all SOPs |
| Helpdesk | Troubleshooting guides, helpdesk procedures |
| Technical Sales | Customer install guides, Siemens product docs |

If something falls between two roles — write it anyway and tag the other person for review. A rough doc is better than no doc.

## The Golden Rules

**1. Write it while you do it**
The best time to document something is when you're setting it up or fixing it. Don't plan to "write it up later" — later never comes.

**2. One source of truth**
If something is documented in two places, one of them is wrong. Pick one location, write it there, link to it everywhere else.

**3. Done is better than perfect**
A rough doc with gaps is more useful than a perfect doc that doesn't exist yet. Use *Coming soon* or *TBD* for gaps. Others can fill them in.

**4. Update when things change**
If you change a configuration, update the doc the same day. A wrong doc is worse than no doc — it sends people down the wrong path.

**5. If you had to ask, write the answer down**
If you searched for something and couldn't find it, or had to ask a colleague — that's a missing doc. Write it after you get the answer.

---

## Where Things Live

Always check the structure before creating a new page. Put docs in the right place.

```
/internal/infrastructure/    ← Servers, network, apps (IT Admin)
/internal/helpdesk/          ← Troubleshooting, support procedures (Helpdesk)
/internal/onboarding/        ← New staff setup (IT Admin)
/internal/sop/               ← Rules and procedures (IT Admin)
/siemens/                    ← All Siemens product docs (IT + Sales, shared)
/siemens/licensing/          ← License server docs (shared, linked from products)
/customers/                  ← Customer deployment guides (Technical Sales)
```

**Shared resources** (things used by multiple products or teams) live in **one place** and everything else links to them. Never duplicate.

Example: FlexLM licensing is used by NX, Process Simulate, and TIA Portal. It lives at `/siemens/licensing/flexlm`. Each product page has one line linking there — not a copy of the licensing steps.


## Naming Pages

| Rule | Correct | Wrong |
|---|---|---|
| All lowercase | `common-dns-issue` | `Common-DNS-Issue` |
| Dashes not spaces | `tia-portal-install` | `tia portal install` |
| No file extensions | `vpn-setup` | `vpn-setup.md` |
| Short and clear | `flexlm-licensing` | `how-to-setup-the-flexlm-license-server-for-siemens` |

> Wiki.js treats `/internal` and `/Internal` as two different pages. Always lowercase.


## Page Length and Format

### Short doc — single procedure or troubleshooting guide

Use for: helpdesk fixes, quick how-tos, single-topic guides.

```markdown
# Title
One line — what this solves or when to use it.

## Symptoms / When to use this

## Steps
1. Step one
2. Step two

## Notes / Gotchas
```

### Medium doc — product, app, or service

Use for: Siemens products, internal apps, anything with multiple topics.

```markdown
# Product / App Name
One line description.

## Overview
## Requirements
## Installation
## Configuration
## Licensing
See → [FlexLM Licensing](/siemens/licensing/flexlm)

## Troubleshooting
## Related Pages
```

**When to split into child pages:** Only when a section becomes too long to maintain, or when different people own different sections. For an SMB like us, one page per product is almost always enough.


## Tags

Always add tags when creating a page. Tags help find content across different folder paths.

| Tag | When to use |
|---|---|
| `siemens` | Any Siemens product page |
| `install` | Installation procedures |
| `helpdesk` | Pages helpdesk staff regularly use |
| `customer` | Customer-facing procedures |
| `license` | Anything licensing related |
| `network` | Network and connectivity |
| `sop` | Standard operating procedures |
| `infrastructure` | Servers, apps, hosting |

Use multiple tags per page. A Siemens install page should have `siemens`, `install`, and possibly `helpdesk` if it's relevant to them.


## Landing Pages

Every folder level needs a landing page — a simple index page that links to its children.

When you create the first page in a new folder, immediately create the folder's landing page too. Example: after creating `/internal/helpdesk/network/common-dns-issue`, create `/internal/helpdesk/network` with links to all its child pages.

Without landing pages, breadcrumb links lead to dead ends.


## Using AI to Write Docs

We have AI prompt templates to help write docs faster. Use them — they are tuned for our team, structure, and environment.

See → [AI Doc Prompts](/internal/sop/ai-doc-prompts)

Paste the right prompt at the start of any AI chat, fill in the topic, and use the output as a starting draft. Always review before publishing — AI doesn't know your specific versions, hostnames, or environment details.

## Reviewing and Maintaining Docs

- **No formal review process** — we are too small for that. Use common sense.
- If you spot something wrong, fix it directly. Don't leave a comment and move on.
- If a doc is outdated but you don't have time to fix it, add a visible note at the top:
  ```
  > ⚠ This doc may be outdated. Last verified: [date]. Contact IT Admin to update.
  ```
- Every quarter, IT Admin does a quick pass to check for obviously stale pages.


## What Not to Do

- **Don't store docs outside the wiki** — no Word files on shared drives, no notes in Teams chat, no "I'll remember it"
- **Don't duplicate** — if it exists, link to it
- **Don't use vague titles** — `misc.md`, `notes.md`, `temp.md` are not page names
- **Don't leave empty pages** — if you create a page, put at least a stub in it


## Related Pages

- [Wiki.js Installation & Config](/internal/infrastructure/apps/wikijs)
- [AI Doc Prompts](/internal/sop/ai-doc-prompts)
- [Internal IT Home](/internal)