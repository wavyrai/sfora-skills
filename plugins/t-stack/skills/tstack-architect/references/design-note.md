# The design note

One page, appended to the card as `## Design (role, date)` or kept in a doc linked from the card. Sentence-case headings. Replace each note in italics with the real content, and drop a section only when it truly has nothing to say.

## Problem

*One paragraph: what we're doing, and what makes the shape not obvious. Name the constraints the grounding found: types we must work with, callers we can't break, rules that cross our boundary.*

## Usage

*Written first. Two or three places in the caller's own code that would use it, each showing the import line, the call and the value it gets back. The shape below follows from this.*

## Shape

*The data structures first, then the signatures and how data flows through them. Say which rules the types enforce, where validation happens, and what the design deliberately doesn't do. Say what the public surface hides and why it is no bigger than it must be.*

## The choice

*Which shape won, and why. One line on each shape that lost and the reason. If you ran tstack-arena, its synthesis note goes here: the base, what was grafted from which candidate, what was dropped.*

## Trade-offs we accept

*One line each: "we accept X to get Y". Name anything a later reader might take for a mistake.*

## Deviations

*Filled in while building. Each accepted change to the sketch, who accepted it (the lead, the PM, the owner's decision on the card), and what it changed in the usage or the signatures. Local changes that leave the contract intact get one line saying why.*

## Open questions

*Questions for the owner or the PM, phrased as questions. A question that is truly the owner's call becomes one ask (see tstack-decide).*

## First step

*One sentence: the first thing to build.*
