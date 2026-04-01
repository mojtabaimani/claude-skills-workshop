---
name: standup
description: Summarize git commits from the last 24 hours as a standup update
---

# Standup Skill

Generate a quick standup summary based on recent git activity. Follow these steps:

## Step 1: Fetch Recent Commits

Run `git log --since="24 hours ago" --oneline --no-merges` to get all commits from the last 24 hours.

If there are no commits, tell the user there's nothing to report and stop.

## Step 2: Summarize

Present the results as a short, clean standup update with:

- A heading: **What I did (last 24h)**
- A bulleted list where each item is a concise task description derived from the commit messages
- Group related commits into a single bullet when they clearly belong to the same task
- Use plain language, not raw commit messages (e.g., "Added square button to calculator" instead of "Add x² (square) button to calculator")
- Skip merge commits and trivial changes like whitespace fixes

## Step 3: Stats

End with a one-liner summary, e.g.: "3 tasks across 5 commits"

## Guidelines

- Keep it concise, this is a standup, not a changelog
- If commits span multiple projects or areas, group by area
- Do not include Co-Authored-By lines or other metadata in the output
