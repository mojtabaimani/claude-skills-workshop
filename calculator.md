# Calculator

A simple, single-page calculator built with HTML, CSS, and JavaScript.

## How to Use
```mermaid
flowchart TB
    subgraph CC["Claude Code"]
        direction TB
    end

    subgraph IDE["Where it runs"]
        A1[Terminal / CLI]
        A2[Editor: VS Code, Cursor, JetBrains]
    end

    subgraph DEV["How it codes"]
        B1[Repo-wide context & multi-file edits]
        B2[Runs commands & tests in your environment]
        B3[Iterates: plan → implement → verify]
    end

    subgraph EXT["Extensibility"]
        C1[Skills — reusable playbooks]
        C2[MCP — tools & data sources]
        C3[Project rules & instructions]
    end

    subgraph WF["Team workflow"]
        D1[Git: read diffs, branch, commit, PRs]
        D2[Hooks — automate on events]
        D3[Permissions & safety boundaries]
    end

    CC --> IDE
    CC --> DEV
    CC --> EXT
    CC --> WF
```
Open `calculator.html` in any modern web browser. No build tools or dependencies required.

## Features

- **Basic math**: addition, subtraction, multiplication, division, and modulo
- **AC**: clears all input and resets the display
- **DEL**: removes the last entered character
- **Decimal support**: prevents duplicate dots in a single number
- **Chained operations**: continue calculating from the previous result
- **Negate (±)**: toggles the current number between positive and negative
- **Square (x²)**: squares the current value or evaluated expression
- **Keyboard input**: use your keyboard as an alternative to clicking buttons

## Keyboard Shortcuts

| Key | Action |
|-----|--------|
| `0-9` | Enter digits |
| `.` | Decimal point |
| `+` `-` `*` `/` `%` | Operators |
| `Enter` | Calculate result |
| `Backspace` | Delete last character |
| `Escape` | Clear all |
| `F9` | Negate (±) |
| `F2` | Square (x²) |
