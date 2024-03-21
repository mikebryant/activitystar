# p2p protocol

Check out an [example layout](example) - what the earthstart data structure might look like (Not including encryption, capabilities etc)

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

### Security Levels

Some ideas on what security levels might look like for an individual user

#### High security

- Grant public access to `/capabilities`. This is the cap in your bio, the one you hand out as a link, etc
- Your `/followers` contains a list of the public capability of each of the people you follow
- Your `/following` contains a list of the public capabilitie of each of the people who follow you
- Grant public access to /public
- Grant your followers access to /followers
- Grant individual DM recipients access to individual DM paths
- Allows people to infer the total count of followers & DM recipients you have (because of the issued capabilities)
- Requires you & a follower or DM recipient to be online and have the other as a peer to exchange any data

#### Medium security

In addition to the above:
- Grant the relays you're on access to /followers
- (?) Grant your followers access to /direct
- Grant the relays you're on access to /direct
- Allows the relays to see how many & when followers and DM posts happen
- Allows your followers to see how many/when DMs etc

This feels like it maps to the state of SSB?

#### Low security

In addition to the above:
- Grant public access to your entire subspace.
- Allows everyone to read your public posts
- Allows everyone to know how many & when your followers posts are
- Allows everyone to know how many, to how many people & when your DMs are
- Doesn't allow people to read the contents of followers / DMs
- Best usability and reach of your stuff. Potentially allows DM communication across many unknown hops

## FAQ

**Q: Does activitystreams / activitypub constrain this?**

**A:** Somewhat. Take the types of activities - if you want other people to understand them, then yes, you need to stick with the kinds their software understands. If we made up the protocol from scratch, none of this would be shared. By reusing the definitions of Follow, Notes etc, we can get a lot of the basics for free, in ways other people may already understand. This doesn't stop us making our own types of activites up though - other people will ignore them, but that's fine. And this doesn't need to be standardised up front - anyone can make up a new kind and start using it.
