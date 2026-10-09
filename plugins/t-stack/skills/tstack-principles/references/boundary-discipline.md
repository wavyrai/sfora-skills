# Boundary discipline

**The rule:** validate, narrow and handle errors where data enters the system: a request, a CLI argument, a config file, an env var, a webhook, a third-party API. Inside, trust the types and keep the logic in plain functions. The framework glue stays thin.

## When it applies

Writing a route, a Convex function, a CLI command, a webhook handler or a config loader; and reviewing any check that sits deep in the call chain.

## How it looks in the t-stack

- **Parse at the edge.** Turn the raw request or row into a typed value once, at the boundary. Code past that point doesn't re-check it.
- **Trust only what the boundary can prove.** A header a client can set is user input, whatever its name. Proof of origin has to come from something the client can't forge, like a shared secret the proxy adds.
- **Don't leak the boundary's shapes inward.** Wire types, storage rows and framework objects stay at the edge; the rest of the code sees domain types.
- **Keep business logic testable without the framework:** pure functions in, results out, with a thin handler that calls them.
- **The reviewer reads the boundary code by hand:** authorization, SSRF (redirects, DNS rebinding), secrets and privacy. Tests alone can't prove "no redirect was followed".

## The incident that taught it

A rate limit keyed on a client-address header that any client could set, so the limit was spoofable. The fix was at the boundary: the address is trusted only when the request carries a secret shared with the proxy, which proves it came through the proxy.

## What to do

1. For each input, ask: is this crossing a boundary right now? If yes, validate here. If no, don't re-validate.
2. For each value you trust, name what proves it. A name or a convention isn't proof.
3. Move logic out of the handler into a function you can test alone.
