---
name: freelance-gig-radar
version: 0.1.0
description: >
  Automatically searches and summarizes open freelance gigs and Web3 bounties
  in the user's target niches (full-stack React/Firebase development and
  cinematography/VFX video editing), filters to currently-open and relevant
  postings, and returns a curated digest. Read-only: it queries sources and
  reports, never claims or applies to gigs on the user's behalf.
activation:
  keywords:
    - "gig radar"
    - "freelance gigs"
    - "job radar"
    - "bounty radar"
    - "freelance digest"
    - "find bounties"
    - "fresh gigs"
  patterns:
    - "(?i)(freelance|gig|bounty|contract).*(radar|digest|scan|watch|find|track)"
    - "(?i)show me (open )?(freelance|gigs|bounties)"
    - "(?i)find (me )?(react|firebase|vfx|video editing) (freelance|gig|bounty)"
  tags:
    - "freelance"
    - "web3"
    - "bounties"
    - "cryptography-work"
    - "offchain-jobs"
  max_context_tokens: 4000
requires:
  tools:
    - search_issues_pull_requests
    - nearai.web_search
  skills:
    - llm-council
---

# Freelance-Gig-Radar

Automatically fetches and curates open freelance gigs and Web3 bounties in the
user's niches: **full-stack development (React / Firebase)** and
**cinematography (VFX / video editing)**. Each run is a read-only pipeline that
queries live sources, filters to open/relevant postings, and returns a concise,
non-fabricated digest. It is typically driven by a daily cron routine but also
runs on demand.

## Inputs

| Source / tool | What to pull |
|---|---|
| `search_issues_pull_requests` (GitHub) | Open issues with `label:good-first-issue`, `label:help-wanted`, `label:bounty` and keywords `React`, `Firebase`, `React Native` for dev gigs. |
| `nearai.web_search` | Web3/NEAR bounty platforms, active freelance boards (Upwork, Freelancer, web3.career), VFX/video-editing freelance listings, and verified current salary anchors. |
| `llm-council` (guidance) | Cross-reference sources so a gig is only surfaced when it appears in a live feed AND passes the open/active + relevance filter — no fabrication. |

Optional enrichment: verified per-hour salary anchors (e.g. React freelance
≈ $54.94/hr, freelance VFX ≈ $47.71/hr) only to score/rank quality when cited
to a source.

## Generation flow

1. **Query sources** for each target keyword bucket:
   - Web3/NEAR bounties via GitHub issues API (labels `bounty`, `good-first-issue`, `help-wanted`).
   - Dev gigs (React/Firebase) via GitHub issues + web search of freelance/boards.
   - VFX / video editing via web search of vetted boards (include AZA-style niche boards as available).
2. **Filter.** Drop expired or irrelevant postings. Keep items that are (a) clearly relevant to full-stack dev (React/Firebase) OR cinematography/VFX editing, and (b) currently open/active. Prefer explicit paid bounties over free good-first-issues when ranking for "side income" relevance.
3. **Cross-reference.** A gig is surfaced only if it appears in a live feed and passes the open/active + relevance filter. If a source is stale or unreachable, say so rather than guessing.
4. **Format.** One clean entry per gig: platform/source, title, one-line description, relevance keywords, direct link. Group into sections: **Web3 Bounties**, **Full-Stack / Freelance Dev (React/Firebase)**, **VFX / Video Editing**.
5. **Report** the curated digest as the final reply. If nothing open is found in a section, report that explicitly (no fabricated sources).

## Output format

A concise digest:

- **Date**: `<run date>`
- **Web3 Bounties**: up to N entries (source, title, one-line, link). Optionally reward amounts when explicit in the source.
- **Full-Stack / Freelance Dev (React/Firebase)**: entries, grouped, with links.
- **VFX / Video Editing**: entries, grouped, with links. If empty, explicitly "No currently-open, verifiable posting matched."
- **No-fabrication note**: any source that returned nothing is reported as empty, never invented.

## Hard rules

1. **Read-only.** Never claim, apply to, or act on a gig on the user's behalf; only discover and report.
2. **No fabrication.** Every entry must trace to a live, currently-open source. Never invent listings or bounties.
3. **Evidence-based filtering.** A posting is only included if it is open/active and relevant to the target niches; drop unsupported or expired items.
4. **Prefer paid bounties** when reporting side-income opportunities; note reward amounts only when explicitly stated in the source.
5. **Timestamped.** Each report carries its run date; do not reuse stale results as fresh.

## Trigger

Primary: a daily cron routine (e.g. `0 9 * * *` UTC) that runs this pipeline as its task and returns the digest. Secondary: on-demand activation via the keywords/patterns above.

## Setup required

No credentials needed beyond the host's existing web search and GitHub access. No permanent storage; the skill is stateless and re-queries fresh sources each run.