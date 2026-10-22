# Language Guide

Every document the skill writes follows these rules. Non-technical stakeholders and junior developers should understand each sentence on first read.

## Core Principles

1. **Clear over clever.** Use the plainest words that are still accurate.
2. **Short over long.** Say it once, in as few words as it takes.
3. **Concrete over vague.** Name the actual thing, number, or action.
4. **Business first, technical second.** Start with what happens and why it matters. Then give the technical detail.

## Sentences

- Under 25 words and one idea per sentence. Split longer ones.
- Active voice, naming who acts: "The checkout page calls `create_order`", not "`create_order` is called".
- Present tense: "This function creates a customer", not "will create".
- Open each section with one or two sentences a non-technical reader can follow; technical detail after.

## Words

- Use everyday words. Write "use" not "utilize", "help" not "facilitate", "start" not "initiate", "show" not "display functionality for".
- Define each technical term on first use, in a few words: "an API (a way for two programs to talk to each other)". Then add it to the Project Overview Glossary and use it freely.
- One name per thing everywhere. If the code says `Client` and the business says "customer", pick one and explain the link once.
- Code names in `backticks`: `customers` table, `create_order()`, `src/services/billing.py`.
- Exact numbers when the code gives them: "retries 3 times", not "a few times".

## Banned: Marketing and Filler

Don't use these words or anything like them. They add no information.

| Do not write | Why |
|---|---|
| seamless, robust, powerful, cutting-edge, state-of-the-art, best-in-class, world-class | Marketing claims, not facts |
| leverage, empower, streamline, unlock, harness, supercharge | Vague verbs; say what actually happens |
| comprehensive, holistic, end-to-end solution, ecosystem | Buzzwords with no clear meaning |
| simply, just, easily, obviously, clearly, of course | They make readers feel slow when something is not simple for them |
| very, really, quite, basically, essentially, actually | Filler that weakens the sentence |
| "It is important to note that", "In order to", "It should be mentioned that" | Wordy; cut straight to the point |

## Honesty

- State facts as facts. No "might", "should" or "probably" when the code is clear.
- When it isn't, say so with `⚠️ Needs confirmation`. Don't guess or hide the gap in vague wording.
- Describe the code; don't praise or criticize it. Known problems go in "Known Limitations", as plain facts.

## Structure

- Headings are plain labels: "Creating a Customer", not "The Magic of Customer Onboarding".
- Lists for steps, options or parallel items; numbered when ordered. Short paragraphs for explanations.
- Tables for repeated fields readers compare (table fields, file index).
- As short as possible while complete. One clear sentence beats three vague ones.

## Before and After

**Before:** "The `OrderService` leverages a robust, event-driven architecture to seamlessly orchestrate the end-to-end order lifecycle."

**After:** "`OrderService` creates orders, updates their status, and sends an event when an order is paid."

**Before:** "In order to facilitate customer onboarding, the system utilizes a comprehensive validation layer."

**After:** "Before creating a customer, the app checks that the email is valid and not already registered."

**Before:** "Data is persisted to the database and subsequently an email notification is dispatched."

**After:** "The app saves the order to the `orders` table, then emails the customer a receipt."

**Before:** "This function basically just handles the stuff related to invoices."

**After:** "This function creates a PDF invoice for a paid order and stores it in `invoices`."

## Final Language Check

- [ ] No sentence over 25 words.
- [ ] No banned words.
- [ ] Each section opens with a plain-language sentence.
- [ ] Each technical term is defined on first use.
- [ ] Each thing has one name throughout all three documents.
- [ ] No guessing; gaps are marked `⚠️ Needs confirmation`.
