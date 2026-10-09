# The explanation's shape

Write for a senior engineer who doesn't know this area and has to start working in it. Drop any section that has nothing to say.

## Overview

One or two paragraphs: what this is, what it does, why it exists. A reader should be able to stop here and know whether to read on.

## Key concepts

The types, services and ideas the rest depends on. A short definition each, not a full list.

## How it works

The longest section. Walk the flow: what triggers it, what happens in order, where the data goes, where the decisions are. Prose, not pseudocode. Give file and function names, so the reader can open the code at the right spot; quote code only when a point needs it.

When something is complex, say why it's complex. When something is simple, don't pad it.

## Where things live

A short map of the files and folders someone needs to start working here.

## Gotchas

Behaviour that surprises, history that explains an odd shape, traps. Also the open questions: what nobody could trace.

## When it feeds a change

If the explanation grounds a design or a card, end with one line per constraint the change must respect: callers that can't break, state with a single owner, invariants that cross the boundary.
