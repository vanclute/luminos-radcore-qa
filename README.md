# Luminos Health - RadCore API QA Assessment

## Overview

Your task is to QA the **RadCore API (v2.3)**, the fictional backend for a radiology information and PACS workstation used by hospital radiology departments.

Radiologists use RadCore to receive imaging study worklists, record measurements, author diagnostic reports, and route those reports through a review and sign-off workflow. These reports directly inform patient treatment decisions.

You are not expected to have any prior knowledge of radiology or medical imaging. Use your AI tools and any other research to orient yourself to whatever degree you find useful, that process is itself part of what we're evaluating.

---

## Getting Started

Clone this repository and do all your work inside the cloned folder:

```bash
git clone https://github.com/vanclute/luminos-radcore-qa.git
cd luminos-radcore-qa
```

Everything you create - test scripts, notes, plans, bug reports - goes inside this directory.

---

## Your Task

QA the RadCore API to whatever depth and breadth you choose, given your available time. Your deadline is provided separately by your interviewer.

The scope of this system is intentionally larger than anyone could fully cover in the time given. Make deliberate choices about where to focus. How you prioritize, and what you choose to build or document, is part of what we're evaluating.

**You decide what to deliver.** There is no prescribed output format. A test plan, a set of bug reports, an automated test suite, a combination - the choice is yours. Whatever you deliver, include a severity/priority assessment for your findings. A suggested scale:

- **Critical** - core workflow broken; end users cannot perform their job
- **High** - data integrity or security concern
- **Medium** - incorrect behaviour with a workaround available
- **Low** - cosmetic or minor

We are more interested in how you approached the problem than in any specific artifact or result.

---

## Ground Rules

**All work must be performed inside this project directory.** Plans, notes, test artifacts, automation code, bug reports - everything goes in this folder.

**The documentation is incomplete.** This is intentional and mirrors real working conditions at Tickblaze. When you encounter something unclear, undocumented, or apparently broken: form a hypothesis, document it, and move on. Getting blocked is not a reason to stop working. Progress despite ambiguity is part of what we are evaluating.

**Use any tools you like.** AI tools, testing frameworks, scripting languages - no restrictions. We use Claude Code internally and are happy to answer basic questions about it if you'd like to try it, but there is no requirement. Our QA stack is primarily Python; C# is our development language. Neither is required, but they are what we work with day to day.

---

## API Access

**Base URL:** `https://web-production-2c752.up.railway.app`

**Authentication:** All requests require an `X-API-Key` header. You have been issued two keys:

| Key | Role |
|-----|------|
| `rk-radcore-rad-[provided separately]` | Radiologist |
| `rk-radcore-adm-[provided separately]` | Admin |

Your actual key values are provided separately by your interviewer.

**Interactive docs:** `https://web-production-2c752.up.railway.app/docs`

Note: not all endpoints appear in the interactive docs. The API surface is larger than what is documented.

---

## Reference Documentation

- [`docs/requirements-spec.md`](docs/requirements-spec.md) - what RadCore is supposed to do
- [`docs/api-guide.md`](docs/api-guide.md) - supplementary API usage notes

---

## Submitting

At your deadline, you'll hear from your interviewer to schedule a short follow-up conversation about how it went.
