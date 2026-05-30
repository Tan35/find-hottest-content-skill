# Find Hottest Content Skill

A specialized skill for turning vague questions like "what was the hottest article yesterday?" into a reproducible, data-driven research workflow.

It transforms an AI agent from a "subjective guesser" into a "precise researcher" by providing:

- **Rigorous Definitions**: Converts "hottest" into measurable signals (engagement, intent, time-decay).
- **Anti-Bot Tactics**: Specialized workflows to bypass data "walled gardens" using Archive pages, Sitemap analysis, and hidden endpoints.
- **Deep Data Extraction**: Advanced techniques like **Console Injection** to extract clean data from `window.__APOLLO_STATE__` or `__NEXT_DATA__` hydration payloads.
- **Platform Recipes**: Built-in recipes for complex platforms like **Medium**, **Reddit**, and **SSPAI**.
- **Transparent Reporting**: A standardized output format that includes scoring formulas and data sources.

## Install

Use this repository as a Manus/Codex skill:

```text
https://github.com/Tan35/find-hottest-content-skill
```

## Core Workflow

1. **Scope Resolution**: Lock down site, time window, and content type.
2. **Source Selection**: Prioritize Official APIs > Embedded JSON > RSS/Archives > Search.
3. **Data Extraction**: Use the most robust method (REST, GraphQL, or Browser Console).
4. **Scoring**: Apply a defensible formula (e.g., `score = likes + saves * 2`).
5. **Verification**: Confirm accessibility and report with full transparency.

## Example Prompts

```text
/find-hottest-content Find yesterday's hottest articles on Medium.
```

```text
/find-hottest-content Identify the most discussed posts on Reddit r/technology this week.
```

## Notes

The skill is self-contained in `SKILL.md`. It is designed to be highly token-efficient while providing advanced procedural knowledge for navigating the modern web's data restrictions.
