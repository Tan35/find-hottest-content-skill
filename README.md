# Find Hottest Content Skill

A Codex skill for turning questions like "what was the hottest article yesterday?" into a reproducible research workflow.

It helps an agent:

- define what "hottest" means for a site or platform
- choose the best available data source
- build a simple scoring formula from public signals
- verify the top result
- explain uncertainty instead of pretending a subjective pick is objective

## Install

Use this repository as a Codex skill:

```text
https://github.com/Tan35/find-hottest-content-skill
```

If your tool uses an upload dialog, download and upload the packaged skill file from this repository:

```text
find-hottest-content.skill
```

That package keeps `SKILL.md` at the archive root. GitHub's default "Download ZIP" wraps files in a top-level folder, which some skill uploaders do not handle well.

## Example Prompts

```text
Use $find-hottest-content to find yesterday's hottest article on SSPAI.
```

```text
Use $find-hottest-content to identify the most discussed posts on a site this week.
```

## Notes

The skill is self-contained in `SKILL.md`. It does not require bundled scripts, so it works better with tools that only import the main skill file.
