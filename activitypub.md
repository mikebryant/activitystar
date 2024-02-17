# ActivityPub

This covers the adaption from https://www.w3.org/TR/activitypub/ @ https://www.w3.org/TR/2018/REC-activitypub-20180123/

# 3 Objects

Objects are all available via earthstar, and so are validated at a lower level, before they can be added to the store.

Clients _SHOULD_ validate that the `id` property

## 3.1 Object Identifiers

All objects have the following properties:

*id*: The object's unique global identifier, which will always be the Earthstar path of the object in the format `earthstar://<namespace>/<subspace>/activitystar/1.0/...`

## 3.2 Retrieving objects

Objects aren't retrieved - they're available if they've been synced to the local client, or not.

Objects are serialized payloads at their earthstar path in the `application/ld+json; profile="https://www.w3.org/ns/activitystreams"` format.

## 3.3 The source property

As per original spec

# 4 Actors

## 4.1 Actor objects

Earthstar location: `/<namespace>/<author-subspace>/activitystar/1.0/<actor-id>/actor`

A given author may have one, or multiple actors. These can be thought of as separate feeds

Attribute differences:

Example:

```
{
  "@context": ["https://www.w3.org/ns/activitystreams",
               {"@language": "ja"}],
  "type": "Person",
  "id": "earthstar://<namespace>/<subspace>/activitystar/1.0/default/actor",
  "following": "earthstar://<namespace>/<subspace>/activitystar/1.0/default/following.json",
  "followers": "earthstar://<namespace>/<subspace>/activitystar/1.0/default/followers.json",
  # "liked": # Not used
  # "inbox": # Not used, there's no definition on an inbox in our p2p semantics
  "outbox": "earthstar://<namespace>/<subspace>/activitystar/1.0/default/outbox/",
  "preferredUsername": "kenzoishii",
  "name": "石井健蔵",
  "summary": "この方はただの例です",
  "icon": [
    "earthstar://<namespace>/<subspace>/activitystar/1.0/default/icon.jpg"
  ]
}
```

# 5.1 Outbox

An earthstar URI ending in a / should be interpreted as a prefix, and for the outbox will then follow these conventions, following the Mastodon publishing levels:

- `outbox/public/.../<timestamp>`
- `outbox/followers-only/.../<timestamp>`
- `outbox/direct/<shared-id>/.../<timestamp>`

Notes:
- `/.../` Can have any path structure, depending on the writer's needs. readers should use any/all paths under here.
- One pattern might be `outbox/public/yyyy/mm/dd`, allowing for rotating encryption keys etc for private follower only posts etc
- payloads should be encrypted (TODO: spec here, use more general spec?).

The more interesting one is the `direct` outbox:
- `<shared-id>` is computed as `HKDF-Expand(scalarmult(my_sk, their_pk), "activitystar-direct", 32)`. This allows readers to discover which of the many subfeeds here is theirs.
- You will disclose how many messages and when you send to someone, but not who, or the contents


# 5.2 Inbox

This is not used in our p2p context. Instead clients will dynamically construct appropriate shared channels under the outbox prefix

# 5.3 Followers Collection

This should be a serialized Collection. Readers should probably not trust this, and compute their own view from all of the feeds they see

# 5.4 Following Collection

This should be a serialized collection.

# 5.7 Likes Collection

Not used - readers should compute their own view from the feeds they see

# 5.8 Shares Collection

Not used

# 6 Client to Server Interactions

These are all out of scope - it's assumed that anyone using this spec will be using a p2p client.
