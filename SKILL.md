---
name: find-hottest-content
description: Define and measure the hottest, most popular, trending, or top content on websites and platforms using reproducible public signals. Use when a user asks for the hottest/most popular/trending/top/most-discussed articles, posts, videos, products, projects, papers, or pages on a site for a date, time range, category, or recent period, especially when "hottest" is ambiguous and the agent must choose or explain a ranking method.
---

# Find Hottest Content

## Core Principle

Turn "hottest" from a vague word into a stated, reproducible measurement. Do not present a subjective guess as an objective ranking.

Default definition when the user leaves it open:

```text
hottest = highest publicly visible engagement within the requested time window
```

Prefer signals that show user intent, not just exposure. Saves, favorites, comments, votes, reactions, reposts, and official rank usually matter more than page position alone.

## Workflow

1. **Resolve the scope**
   - Identify the site or platform.
   - Resolve the time window using the user's locale when known.
   - Identify content type: article, post, video, project, product, paper, issue, or page.
   - If the user says "yesterday", "today", or "recent", convert it to exact dates in the final answer.

2. **Choose the ranking source**
   Use the highest-trust available source:

```text
official API or official ranking page
> embedded page data / JSON-LD / app hydration data
> RSS, sitemap, archive pages, or search pages
> third-party rankings or search engine results
> subjective recommendation
```

3. **Collect candidate items**
   - Gather enough pages/API results to cover the full time window.
   - Filter by publish/release time, not crawl time or edit time.
   - Avoid private, paid, or authenticated content unless the user explicitly has access and asks for it.
   - Do not bypass access controls, rate limits, CAPTCHAs, or anti-abuse systems.

4. **Select a scoring formula**
   Use fields actually available from the source. Keep the formula simple and explain it.

Common formulas:

```text
official rank available:
score = official_rank_position

likes + saves available:
score = likes + saves * 2

votes + comments available:
score = votes + comments * 1.5

reactions + comments + shares available:
score = reactions + comments * 1.5 + shares * 2

only article list available, no engagement:
no objective ranking; report that public engagement data is unavailable
```

Treat comments carefully. Comments can reflect popularity, controversy, support requests, or debate. Use them as supporting context unless the task is specifically "most discussed".

5. **Rank and verify**
   - Sort candidates by the selected score.
   - Open or fetch the top result to confirm it is accessible and the title matches.
   - If the top result is deleted, private, redirected, or mismatched, inspect the next candidate.

6. **Report with caveats**
   Include:
   - exact time window
   - data source
   - scoring formula
   - top result link
   - runner-up table when useful
   - uncertainty or missing data

## Output Template

Use a compact answer like:

```text
I treated "hottest" as <formula/definition>, filtered to <exact date/time window>.

Top result: <title> - <url>

Signals: <likes>, <favorites>, <comments>, <score>

Notes: <data source and caveats>
```

If no objective engagement data exists:

```text
I could not find public engagement data for this site/time window, so I cannot make an objective "hottest" ranking. I can still give a clearly labeled subjective pick based on author prominence, topic relevance, homepage placement, and recency.
```

## Data Source Tactics

- Check obvious endpoints and pages first: `/api`, `/graphql`, `/feed`, `/rss`, `/sitemap.xml`, `/robots.txt`, archive pages, tag pages, and official trending pages.
- Inspect page HTML for embedded JSON such as `__NEXT_DATA__`, `window.__INITIAL_STATE__`, JSON-LD, or app hydration payloads.
- Prefer structured parsers or JSON APIs over brittle regex when practical.
- Use web search only when site-native sources are missing or blocked, and label the result as lower confidence.
- Respect `robots.txt` and terms of service. For public tools, avoid high-volume scraping and do not mirror content.

## Scoring Guidance

Choose weights based on intent cost:

```text
view/impression: weak signal
like/reaction: light signal
comment/reply: medium signal, can be controversy-heavy
favorite/bookmark/save: strong signal
share/repost: strong signal
official rank/editorial hot list: strong if the user accepts platform-defined popularity
```

Do not over-engineer weights. A transparent, defensible formula is better than a complicated one that looks precise but cannot be justified.

## Site Recipe: SSPAI / 少数派

Use this only when the target site is SSPAI.

Public article list:

```text
GET https://sspai.com/api/v1/articles?limit=200&offset=0
```

Useful fields:

```text
released_at
title
id
likes_count
favorites_count
comments_count
author.nickname
```

Recommended formula:

```text
score = likes_count + favorites_count * 2
```

Filter by `released_at` using UTC+8 day boundaries. Continue pagination with `offset += 200` until the oldest `released_at` is earlier than the target day start. Verify the winner at:

```text
https://sspai.com/post/<id>
```

Do not use `views_count`; it is often `0`. Do not depend on `/api/v1/articles/info/get?id=<id>` because it may require a JWT.

## Safety

Keep requests low-volume and task-scoped. Do not use this skill to bypass paywalls, authentication, rate limits, CAPTCHAs, or private APIs. Do not republish full copyrighted content; link and summarize instead.
