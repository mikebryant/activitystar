# activitystar
An adaption of ActivityPub to p2p, using Earthstar

## Status

General musings at present...

## Why?

Why use activities / activitypub as a base?
- It offers the potential for some neat bridging, where you can run a relay that speaks both activitypub and earthstar, and anyone who opts in can have their feeds published on activitypub, or conversely can follow people in the fediverse.
- It means less reinventing the wheel - don't need to think about what a Note means, use the existing definitions.

## Naming?

ACTIVITYpub + earthSTAR

Naming is hard. Tell me if you have a better idea :D

## How does this compare to Mastodon timelines?

### Home timeline

Much the same - posts from anyone you directly follow

### Local / Community

This is where it differs - I anticipate the existence of ssb-style `pub` servers / earthstar relays.

Clients would follow a pub, and the pub follows back (pub owner does the moderation here, like a Mastodon server)

So these posts would all show up in Federated too - but you could have tabs in the client for each of the pubs you follow.

### Federated

All the public posts that you've synced.
