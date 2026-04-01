---
name: add-button
description: Add a new button to the calculator app with custom function and styling
---

# Calculator Button Skill

Add a new button to the calculator. Follow these steps:

## Step 1: Gather Button Details

Use AskUserQuestion to ask all of the following:

1. **Button label** - What text should appear on the button? (e.g., "√", "^", "±")
2. **Description** - What does this button do? (e.g., "Calculates the square root of the current value")
3. **Function** - What should happen when clicked? Describe the math or logic. (e.g., "Take the square root of the current number on display")
4. **Color** - What color should the button be?

## Step 2: Read the Current Calculator

Read `calculator.html` to understand the current layout, button structure, and JavaScript logic.

## Step 3: Implement the Button

Edit `calculator.html` to add the new button:

1. Add the button element in the appropriate position in the `.buttons` grid
2. If the chosen color does not match an existing button class, add a new CSS class for it
3. Add the JavaScript function that implements the described behavior
4. Make sure the new button integrates cleanly with the existing calculator logic (uses the display, updates expression/result correctly)
5. If the button has a keyboard shortcut that makes sense, add it to the keydown event listener

## Step 4: Update the Guide

Read `calculator.md` and update it to include:
- The new button in the Features section
- A keyboard shortcut entry if one was added

## Step 5: Write Social Media Posts

### LinkedIn Post

Write a short, professional LinkedIn post about the new button and save it to `./linkedin/<button-label-lowercase>.md`. The post should:
- Be 3-5 sentences
- Mention what was built and why it's useful
- Sound authentic, not overly promotional
- End with a relevant hashtag or two

### X (Twitter) Post

Write a short tweet about the new button and save it to `./X/<button-label-lowercase>.md`. The post should:
- Be under 280 characters
- Be concise and engaging
- Include 1-2 relevant hashtags

## Step 6: Commit

Stage `calculator.html`, `calculator.md`, and the new posts in `./linkedin/` and `./X/`, then create a git commit with a message like:

```
Add <button label> button to calculator

<Short description of what the button does>
```
