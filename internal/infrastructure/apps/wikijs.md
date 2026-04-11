---
title: Wikijs - Setup
description: 
published: true
date: 2026-04-11T22:23:43.205Z
tags: 
editor: markdown
dateCreated: 2026-04-11T22:23:43.205Z
---

# Wiki.js — Installation & Configuration

**Path:** `internal/infrastructure/apps/wikijs`  
**Tags:** `wikijs`, `infrastructure`, `apps`, `docker`, `sso`, `git`

Wiki.js is our internal knowledge base. This doc covers installation, configuration, and ongoing maintenance. For how to *use* and *write* docs, see → [Documentation SOP](/en/internal/sop/documentation)

---

## Overview

| Item | Detail |
|---|---|
| Version | Wiki.js 2.x |
| Deployment | Docker + Docker Compose |
| Database | PostgreSQL 15 |
| Port | `8081` |
| Git sync | Enabled (backup + version control) |
| Auth | Microsoft Azure AD SSO |
| Timezone | `Asia/Kolkata` (IST) |
| Maintained by | IT Admin |

---

## Prerequisites

- Docker + Docker Compose installed on host
- Git 2.7.4 or later (already included in the Wiki.js Docker image)
- A dedicated Git repository for Wiki.js content (do not share with other projects)
- Microsoft Azure AD access (for SSO setup)
- Ports `8081` open on host firewall

---

## Installation

### 1. Create directory structure

```bash
mkdir -p /opt/wikijs
cd /opt/wikijs
```

### 2. Create `.env` file

```bash
nano .env
```

Paste and fill in:

```env
# Wiki.js Database
DB_NAME=wiki
DB_USER=wikijs
DB_PASS=your-strong-password-here

# App
WIKI_PORT=8081
TZ=Asia/Kolkata
```

> **⚠ Never commit `.env` to Git.** Add to `.gitignore` immediately.

### 3. Create `docker-compose.yml`

```yaml
services:
  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: ${DB_NAME}
      POSTGRES_PASSWORD: ${DB_PASS}
      POSTGRES_USER: ${DB_USER}
      TZ: ${TZ}
    logging:
      driver: none
    restart: unless-stopped
    volumes:
      - db-data:/var/lib/postgresql/data

  wiki:
    image: ghcr.io/requarks/wiki:2
    depends_on:
      - db
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: ${DB_USER}
      DB_PASS: ${DB_PASS}
      DB_NAME: ${DB_NAME}
      TZ: ${TZ}
    restart: unless-stopped
    ports:
      - "${WIKI_PORT}:3000"

volumes:
  db-data:
```

### 4. Start Wiki.js

```bash
docker compose up -d
```

### 5. First-time setup

1. Open browser → `http://your-server-ip:8081`
2. Complete the setup wizard — create admin account
3. Wiki.js is now running

---

## Git Integration

Wiki.js syncs all page content to a Git repository as `.md` files. This gives you version history, backup, and the ability to edit docs directly via Git/VS Code.

> **⚠ The Git repo must be dedicated to Wiki.js only — no subfolders or sharing with other projects.**

### Setup

1. Create a new empty repository in your Git provider (GitHub, GitLab, Gitea, etc.)
2. Generate an SSH key pair on the Wiki.js host — **no passphrase**, password-protected keys will not work:
   ```bash
   ssh-keygen -t ed25519 -f /opt/wikijs/wiki-git-key
   ```
3. Add the **public key** (`wiki-git-key.pub`) as a Deploy Key in your Git repo with **write access**
4. In Wiki.js → **Administration → Storage → Git**
5. Configure:
   - **Authentication type:** SSH
   - **Repository URL:** `git@github.com:yourorg/yourrepo.git`
   - **Branch:** `main`
   - **SSH Private Key path:** `/opt/wikijs/wiki-git-key`
   - **Verify SSL:** Yes
6. Click **Apply**

### Syncing existing content

If you created pages before enabling Git, force a sync:

Administration → Storage → Git → scroll to bottom → click **Add Untracked Changes** → then **Import Everything**

### How content is stored

Every Wiki.js page becomes a `.md` file in the repo, mirroring the path structure:

```
/siemens/licensing/flexlm  →  siemens/licensing/flexlm.md
/internal/helpdesk/vpn     →  internal/helpdesk/vpn.md
```

You can edit `.md` files directly and push — changes appear in Wiki.js automatically on the next sync interval.

---

## Microsoft SSO (Azure AD)

Allows team members to log in with their Microsoft 365 / company credentials.

### Part A — Wiki.js side (get Redirect URI first)

1. Go to **Administration → Authentication**
2. Click **Add Strategy → Azure Active Directory**
3. Copy the **Redirect URI** shown — you'll need it in Azure
4. Leave this page open

### Part B — Azure Portal

1. Go to [portal.azure.com](https://portal.azure.com)
2. Open **Azure Active Directory → App registrations → New registration**
3. Name it `Wiki.js`
4. Set **Redirect URI** to the value you copied from Wiki.js
5. Click **Register**
6. Copy the **Application (client) ID** — needed later
7. Click **Endpoints** at the top → copy the **OpenID Connect metadata document** URL:
   ```
   https://login.microsoftonline.com/YOUR_TENANT_ID/v2.0/.well-known/openid-configuration
   ```
   > **⚠ Make sure the Tenant ID in the URL is a long alphanumeric string — NOT the word "organizations".** If it shows "organizations", find your real Tenant ID under Azure AD → Properties → Directory ID and replace it manually.

8. Go to **Authentication** in the left nav:
   - Set **Logout URL** to `https://your-wiki-domain`
   - Check **ID tokens** under Implicit grant
   - Click Save

9. Go to **API permissions** → confirm **Microsoft Graph → User.Read** is listed
10. Optional but recommended: click **Grant admin consent** — prevents the consent popup for all users on first login

### Part C — Back to Wiki.js

1. Paste the **Identity Metadata Endpoint** (OpenID Connect URL from step 7)
2. Paste the **Client ID** (from step 6)
3. Enable **Self-registration** — or disable if you want to approve users manually
4. Set the **default group** for new users
5. Check the **Active** checkbox next to Azure Active Directory
6. Click **Apply**

### Fix IST timezone per user (optional)

Wiki.js sets timezone per user in the database. If timestamps show wrong time after login, run against PostgreSQL:

```sql
ALTER TABLE users ALTER COLUMN timezone SET DEFAULT 'Asia/Kolkata';
```

---

## Backup

The Docker volume `db-data` holds the PostgreSQL database. The Git repo holds all page content. Both need to be backed up.

| What | Where | Backup method |
|---|---|---|
| Page content | Git repository | Handled automatically by Git sync |
| Database | `db-data` Docker volume | Schedule `pg_dump` or volume snapshot |
| `.env` file | `/opt/wikijs/.env` | Store securely — **not in Git** |

### Database backup command

```bash
docker exec wikijs-db-1 pg_dump -U wikijs wiki > /opt/wikijs/backup/wiki-$(date +%Y%m%d).sql
```

Run this on a cron schedule or before any major changes.

---

## Useful Commands

```bash
# Start
docker compose up -d

# Stop
docker compose down

# View logs
docker compose logs -f wiki

# Restart wiki only (not DB)
docker compose restart wiki

# Update to latest image
docker compose pull && docker compose up -d
```

---

## Troubleshooting

**Wiki.js not accessible on port 8081**
- Check containers are running: `docker compose ps`
- Check host firewall allows port 8081
- Check logs: `docker compose logs wiki`

**Azure AD login fails with "not authorized"**
- Confirm the Tenant ID in the OpenID Connect URL is the real GUID, not "organizations"
- Confirm Redirect URI in Azure matches exactly what Wiki.js shows
- Check ID tokens is checked under Implicit grant in Azure

**Git sync not working**
- Confirm SSH private key path is correct and accessible inside the container
- Key must have **no passphrase**
- Deploy key must have **write access** on the repo
- Check logs: `docker compose logs wiki | grep STORAGE`

**Timestamps showing wrong time**
- Run the `ALTER TABLE` SQL command above to set default timezone to IST
- Or each user can set their own timezone in their profile settings

---

## Related Pages

- [Documentation SOP](/internal/sop/documentation)
- [Infrastructure](/internal/infrastructure)
- [Internal IT Home](/internal)