# Developer Feasibility Assessment

This section breaks down the entire build from a developer's perspective — what's straightforward, what's hard, and what might not be achievable within the proposed scope and timeline.

---

## Summary

| Category | Effort Level | Risk |
| --- | --- | --- |
| Public website (5–10 pages) | LOW | Low |
| Auth + billing integration | MEDIUM | Medium (Azure Entra complexity) |
| User portal UI (6–10 pages) | MEDIUM | Low |
| Single API integration (TheOddsAPI) | LOW-MEDIUM | Low |
| Three API integrations + normalisation | HIGH | High (event matching) |
| Calculation engine (core math) | MEDIUM | Medium (domain knowledge needed) |
| AI narration layer | MEDIUM | Medium (prompt engineering + validation) |
| Offer Library scraping | HIGH | High (fragile, legally grey) |
| Data pipeline (1-min cron) | MEDIUM | Medium (timeout constraints) |
| Admin panel | LOW-MEDIUM | Low |
| Email system | LOW | Low |

## Overall Feasibility

**Verdict: FEASIBLE, but the timeline is aggressive.**

The proposed 5–7 weeks is achievable only if:
- Development is full-time by an experienced developer
- Design iterations are minimal (designs approved quickly)
- The Offer Library launches with manual entry (not full scraping)
- Only one API (TheOddsAPI) is fully integrated at launch, with others added post-MVP

**Realistic timeline: 8–10 weeks** with all three APIs and scraping.

**Realistic timeline: 6–7 weeks** with one API and manual offer entry.
