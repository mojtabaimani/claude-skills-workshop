---
name: invoice
description: Generate a professional invoice for a client
---

# Invoice Skill

Generate a professional invoice. Follow these steps:

## Step 1: Gather Invoice Details

Use AskUserQuestion to ask the following (you may ask them together or in sequence):

1. **Client name** - Who is this invoice for?
2. **Hours worked** - How many hours did you work?
3. **Hourly rate** - What is your hourly rate (in USD)?

## Step 2: Generate the Invoice

Calculate the total and generate a clean, professional invoice in markdown format. Include:

- Invoice number (use format `INV-YYYYMMDD-001` based on today's date)
- Today's date as the invoice date
- Due date (30 days from today)
- Client name
- Line item with hours and rate
- Subtotal, tax line (marked as 0% by default), and total

## Step 3: Save the Invoice

Write the invoice to a file named `invoice-<client-name-lowercase>-YYYYMMDD.md` in the current directory.

## Invoice Template

Use this structure:

```
# INVOICE

**Invoice #:** INV-YYYYMMDD-001
**Date:** <today>
**Due Date:** <30 days from today>

---

**Bill To:**
<client name>

---

| Description          | Hours | Rate     | Amount   |
|----------------------|-------|----------|----------|
| Professional Services| X     | $XX.XX   | $XXX.XX  |

---

| | |
|------------|----------|
| **Subtotal** | $XXX.XX |
| **Tax (0%)**  | $0.00   |
| **Total**    | $XXX.XX |

---

**Payment Terms:** Net 30
```
