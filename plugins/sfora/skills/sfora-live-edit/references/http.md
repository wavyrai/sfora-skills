# The same thing over HTTP

For agents without the sfora CLI. The base URL is `https://www.sfora.ai`, and every request carries your key as `Authorization: Bearer <api-key>`. Add `X-Sfora-Client: <client>` (for example `claude-code`) to have your writes say which client sent them. Doc paths are the CLI's paths with `/v1/fs` in front.

## Who's here

```http
GET /v1/presence
Authorization: Bearer <api-key>
```

This answers `{ ttlSeconds, member, documents: [{ title, path, url, here: [{ name, type, kind, block }] }] }`. Add `?member=<name|id|self>` to ask about one member. It is a read only, so asking never puts you in a doc.

## The blocks

```http
GET /v1/fs/projects/hq/docs/launch-plan.md?view=blocks
Authorization: Bearer <api-key>
```

Each block has `id`, `type`, `lines`, `text` and `writable`.

## Claim a block, and keep it claimed

```http
POST /v1/fs/projects/hq/docs/launch-plan.md/_presence?kind=editing&block=<block-id>
Authorization: Bearer <api-key>
```

This answers `{ document, present, block, blockResolved, here }`, where `here` is everyone in the doc (`name`, `type`, `kind`, `blockId`). Send the same request every 30 seconds while you work. A claim lasts 90 seconds after the last one. `blockResolved: false` means the id names no block (it changed), and you're in the doc with no block, so read the blocks again and claim the new id. The request is all-or-nothing: a beat without `block` clears your block. `kind` defaults to `editing`. Posts and board cards answer 422, because they have no avatar stack.

## Write one block

```http
PUT /v1/fs/projects/hq/docs/launch-plan.md?block=<block-id>
Authorization: Bearer <api-key>
Content-Type: text/markdown

A paragraph the agent rewrote.
```

The body is the block's markdown only. The answer carries `changed` (true or false) and `blockIds` (how many of the doc's earlier block ids were kept, moved or orphaned). The write also puts you in the doc as editing, on the block you wrote. An empty body answers 422.

A 409 means the id no longer names a block, and nothing was written:

```text
{ "error": "conflict", "message": "…", "block": "<block-id>", "blocks": [{ "id": "…", "line": 5, "preview": "…" }] }
```

Read the block's new text, merge your change in, and PUT to the new id. See `collisions.md`.

## Leave

```http
POST /v1/fs/projects/hq/docs/launch-plan.md/_presence?leave
Authorization: Bearer <api-key>
```

This answers `present: false` and removes you from the avatar stack straight away. If you just stop beating, you disappear by yourself after 90 seconds.

## A whole-doc write, guarded

A whole-doc `PUT` with no `?block=` replaces every block. Only do it when nobody is in the doc. To fail instead of overwriting a change you haven't seen, send the revision you read. A `GET` of the doc returns it in the `ETag` and `X-Sfora-Revision` headers. Send it back as `If-Match: "<revision>"` or `?expectedRevision=<revision>`, and the PUT answers 409 if anything in the doc was saved since.
