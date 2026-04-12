---
title: AI Prompt Snippets
description: 
published: true
date: 2026-04-12T00:23:48.921Z
tags: 
editor: markdown
dateCreated: 2026-04-12T00:20:19.501Z
---

# AI Doc Snippets for Wiki.js Documentation

Paste the relevant prompt at the start of any AI chat session before asking for help writing a doc.

---

## Short Doc Prompt
*Use for: helpdesk procedures, troubleshooting guides, single-topic how-tos*

```
You are helping me write internal IT documentation for our company wiki (Wiki.js).
We are an SMB in a fast-paced environment. Keep docs short, practical, and frustration-free for the reader.

Follow these rules:
- Format: Markdown only
- Length: Short and focused. One topic per doc. No fluff or corporate filler.
- Structure:
    # Title
    One line description of what this solves or when to use it.

    ## Symptoms / When to use this

    ## Steps
    (numbered steps only, no long explanations unless critical)

    ## Notes / Gotchas
    (optional — only include if there is a genuine catch or warning)

- No table of contents
- No introduction paragraphs
- No "In this document we will cover..." type sentences
- Use **bold** only to highlight critical warnings or key terms
- Use code blocks for any commands, paths, or config values

The doc I need help writing is about: [DESCRIBE YOUR TOPIC HERE]
```

---

## Medium Doc Prompt
*Use for: Siemens product pages, internal apps, anything with multiple subtopics*

```
You are helping me write internal IT documentation for our company wiki (Wiki.js).
We are an SMB and a Siemens reseller. We use the products we sell internally (CAD, virtual commissioning).
Our audience is: IT helpdesk, technical sales, and IT admin. All are technical but busy.

Follow these rules:
- Format: Markdown only
- Length: One page covers the whole product/topic. Do not suggest splitting into multiple pages unless a section is genuinely too large to maintain.
- Structure:
    # Product / App Name
    One line description.

    ## Overview
    (2-3 sentences max. What it is, who uses it, why we have it.)

    ## Requirements
    (hardware, OS, dependencies, network — only what is actually needed)

    ## Installation
    (numbered steps, be specific, include paths and versions where relevant)

    ## Configuration
    (post-install setup, key settings)

    ## Licensing
    (brief section — if it uses a shared license server, just say so and link to it:
    See → [FlexLM Licensing](/siemens/licensing/flexlm) )

    ## Troubleshooting
    (common issues only — symptom → cause → fix format)

    ## Related Pages
    (links to related wiki pages)

- Wiki.js auto-generates a TOC from headings so do not add a manual TOC
- No fluff, no "Introduction" section, no "In conclusion"
- Use **bold** for warnings and key terms only
- Use code blocks for commands, paths, registry keys, config values
- If a topic is shared across multiple products (e.g. licensing, AD, VPN), do not duplicate it — write a one-line reference and link instead

The doc I need help writing is about: [DESCRIBE YOUR TOPIC HERE]
Additional context: [ANY SPECIFIC VERSIONS, ENVIRONMENT DETAILS, OR KNOWN ISSUES]
```

---

## Quick Tips for Both Prompts

- Always fill in the **[DESCRIBE YOUR TOPIC HERE]** placeholder before sending
- For medium docs, the more context you give in **Additional context**, the less back-and-forth you'll need
- If the AI adds sections you don't need, just say "remove the X section, we don't need it"
- After the AI drafts the doc, ask: *"Is there anything in this doc that should be a link to a separate shared page instead?"*