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

## Capabilities

So, we have a data format, but how do people see it?

Grant capabilities to them!

- The client app will need to grant capabilities to other identities using the [capability](https://github.com/earthstar-project/application-formats/pull/6) spec.
- So the first cap is the cap for seeing capabilities, then further caps after that
- If you're using one/many relays you'll also want them to have capabilities for everything to sync it, though not necessarily keys
  - You want the relay to sync your follower-only messages and DMs, but not decrypt them
  - Depending on your level of trust, you might let anyone download everything by granting a public cap, e.g. like SSB. Would mean people can see when you post, but not to who
- It's likely you'll want to grant public access to `/capabilities`, so that can be synced everywhere, and let people pick up the relevant caps for other things
- Also you'll probably want this for your `/actor`, and `/public/...` paths - like in Mastodon, public is public, anyone can see without auth etc.
- Of course how do people get the initial capabilitiy even if it's signed to public?
  - Publish it in your `/actor`?
  - Ponder, should your list of followers/following also include their public caps to aid discovery? Not sure how else you'd be able to do the follower of follower thing like SSB

## Federation

Principles:
- This should be interoperable with the wider fediverse
- Consent should be respected in line with the current fediverse semantics

Theory:
- We'll call a server that's running both earthstar and activitypub protocols a bridge node
- From the earthstar side:
  - You have to send a `StartFederation` activity to the bridge node's identity
  - You _SHOULD_ only be federated with one server at a time (to avoid message duplication in the fediverse)
  - From this timestamp on, any activities it sees will be processed the same way they would normally - forwarded on to other servers in the `to` etc
  - It'll rewrite any identities into a form suitable for http semantics, e.g. mentions etc
    - If it's seen a `StartFederation` for some other identity, it'll use that server's address
    - If it's not they'll be left as an earthstar address?
  - TODO: `StopFederation`, stops forwarding, leaves identity active
    - A future `StartFederation` to another server should have the original one post a final `moved` message - or should the client need to do this BEFORE StopFederation? unsure
    - `DeleteFederation` to trash all hosted content?
- This is not a _full_ bridge - it will act the way any other fediverse server works
  - If it sees a `Follow` it'll send the follow request to the destination
  - If they Reject, forwards that back into earthstar
  - If they accept, it'll start publishing their activities into it's local subspace.
    - To keep the convention with the way the fediverse works, these activities _MUST_ only be accessible by identities federated with this bridge, e.g. caps and keys
  - The owner of the bridge is responsible for moderation in the same way a fediverse server owner is, e.g. you'll get defederated if you let spam through etc.

Summing up:
- No-one else on the fediverse should see this as any different to a Mastodon instance, for example
- They'll only see a given earthstar identity coming from one place
- Messages they send will only be sent to the people in their followers, or other people on the same instance(bridge), in line with toot visibility

Caveats:
- Not all earthstar stuff will make it to the fediverse - opt-in by design
- Obviously a bad actor can copy and forward stuff around - but they can do that with activitypub too
- Private messages between earthstar and fediverse participants will be vulnerable to snooping by the bridge owner - same as with a fediverse server
