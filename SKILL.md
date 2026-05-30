---
name: "find-hottest-content"
description: "Define and rank the hottest, most popular, trending, top, or most-discussed content on websites and platforms using reproducible public signals."
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
   - **Anti-Bot Tactics**: If standard `curl` or `fetch` fails (403/401), use the browser to visit the page. If the page uses a "Login Wall" for data, look for "Archive" or "Latest" pages which are often less restricted.

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

## Data Source Tactics

- **Official Endpoints**: Check `/api`, `/graphql`, `/feed`, `/rss`, `/sitemap.xml`, `/robots.txt`.
- **Archive Pages**: Many platforms (like Medium) have hidden `/archive/YYYY/MM/DD` pages that bypass SPA hydration issues and show historical data with engagement.
- **Console Injection**: For modern SPA sites (React/Next.js/Apollo), use `browser_console_exec` to extract data from `window.__NEXT_DATA__` or `window.__APOLLO_STATE__`. This is often the only way to get "clean" engagement numbers.
- **Search Pages**: Use the site's own `/search` with "Latest" or "Top" filters if the homepage is too personalized.

## Site Recipe: Medium

Medium locks engagement (claps) behind a login wall on many pages, but Archive pages are often public.

1. **Path**: `https://medium.com/tag/{tag_name}/archive/{YYYY}/{MM}/{DD}`
2. **Scraping**: The archive page is Server-Side Rendered (SSR). Claps and response counts are visible in the HTML text.
3. **Alternative**: If on a tag page, extract from `window.__APOLLO_STATE__` using console injection.
4. **Formula**: `score = claps + responses * 2`

## Site Recipe: Reddit

Reddit blocks standard API requests for many agents. Use the browser.

1. **Path**: `https://www.reddit.com/r/{subreddit}/top/?t=day` (or `r/all`)
2. **Extraction**: Reddit uses Web Components (`shreddit-post`). Use `browser_console_exec` to grab attributes:
   ```javascript
   Array.from(document.querySelectorAll('shreddit-post')).map(p => ({
     title: p.querySelector('[id^="post-title-"]')?.innerText,
     score: parseInt(p.getAttribute('score')),
     comments: parseInt(p.getAttribute('comment-count')),
     ts: p.getAttribute('created-timestamp')
   }))
   ```
3. **Filtering**: Always filter by `created-timestamp` to ensure the post belongs to the target calendar day.
4. **Formula**: `score = score (upvotes) + comments * 1.5`

## Site Recipe: SSPAI / 少数派

Public article list: `GET https://sspai.com/api/v1/articles?limit=200&offset=0`
Formula: `score = likes_count + favorites_count * 2`

## Safety

Keep requests low-volume and task-scoped. Do not use this skill to bypass paywalls, authentication, rate limits, CAPTCHAs, or private APIs. Do not republish full copyrighted content; link and summarize instead.
