# Terminology (always on)

Fork this file. It is deliberately near-empty, because the value is entirely in the specific
vocabulary of *your* organization, and inherited vocabulary is worse than none.

## Why this exists at all

A document corpus that calls one thing three names is not searchable, and the cost compounds
silently — nobody notices the search that returned nothing, they just rewrite the document
that already existed.

## What belongs here

- **The word you use and the words you do not**, with the reason. "Client, not customer" is
  worth more with "because our contracts and our support tooling both say client" attached.
- **The distinctions that get collapsed.** Two products that people conflate. Two lifecycle
  states that are not the same state. These are the highest-value entries — they prevent a
  category of error, not just an inconsistent word.
- **The exceptions.** Every terminology rule has them: product names, industry-standard
  phrases, verbatim quotations, code identifiers. Write them down or the rule will be applied
  where it should not be, and someone will "correct" a quotation.

## What does not belong here

Style preferences. Whether to use the Oxford comma is not a terminology rule, it is a taste,
and putting it here spends always-on context budget on nothing.

## Example shape

```markdown
1. **"Client" not "customer"** — including compounds.
   *Exceptions:* product names, "customer relationship management", verbatim quotes,
   code identifiers.

2. **Product A is not Product B.** They integrate; they are not interchangeable.
   Confusing them in a client-facing document is a credibility failure.
```
