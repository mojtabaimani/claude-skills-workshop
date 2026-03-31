---
name: news
description: Get the latest news and updates on any topic from your preferred sources
---

# News Update Skill

Fetch and summarize the latest news on a topic the user cares about. Follow these steps:

## Step 1: Ask the Topic

Use AskUserQuestion to ask: "What topic do you want the latest updates on?"

Provide relevant options such as:
- **Technology** - AI, software, gadgets, startups
- **Programming** - Languages, frameworks, developer tools
- **World News** - Global events, politics, economy
- **Science** - Research breakthroughs, space, health

The user can also type their own topic.

## Step 2: Ask Preferred Source

Use AskUserQuestion to ask: "Do you have a preferred source for updates?"

Suggest 2-3 sources relevant to the topic the user chose. For example:
- If **Technology**: suggest TechCrunch, The Verge, Ars Technica
- If **Programming**: suggest Hacker News, dev.to, The GitHub Blog
- If **World News**: suggest BBC, Reuters, AP News
- If **Science**: suggest Nature, Science Daily, NASA

Always include an option: **"No preference"** with description "Find updates from top sources automatically".

## Step 3: Ask How Many Updates

Use AskUserQuestion to ask: "How many recent updates do you want?"

Options:
- **3** - Just the highlights
- **5** - A solid overview
- **10** - Comprehensive list

## Step 4: Fetch and Present Updates

Use WebSearch to find the latest updates based on the topic, source preference, and count.

- If the user chose a specific source, search with that source name included in the query
- If the user chose "No preference", search broadly and pick results from reputable sources

Present the results as a numbered list. For each item include:
- **Headline** (as a link if URL is available)
- **Source name** and date
- **One-sentence summary**

## Guidelines

- Always use the current date context to find the most recent updates
- If a preferred source returns too few results, supplement from other reputable sources and note it
- Keep summaries concise, one sentence each
- End with a "Sources" section listing all URLs used
