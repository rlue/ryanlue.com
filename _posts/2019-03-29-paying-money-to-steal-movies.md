---
title: Paying Money to Steal Movies & Music on the Internet
teaser: Using Plex, Sonarr, Radarr, and more for piracy in 2019.
category: guides
tags: [multimedia, streaming, piracy, bittorrent]
---

This article will provide a high level overview of the infrastructure
required to start building your own collection of pirated media
without leaving your browser, and stream it anywhere.
It will not cover _how_ to set this all up,
but should help you understand what is possible,
what applications can be used to make it work, and how they all fit together.

Setting this up yourself will require:

* familiarity with the UNIX command line & `ssh`;
* knowledge of how basic web applications are configured, run, and accessed; and
* a decent helping of independent research.

> #### ⚠️ Warning: Non-free software ahead

## Preface

If you were a teen in the early 2000s,
you probably remember stealing movies and music on the Internet,
moving from Napster to Limewire, then Limewire to KaZaA,
always chasing the next P2P platform the RIAA hadn’t caught onto yet.
But these days, if you’re still stealing stuff on BitTorrent,
it’s easy to feel a little behind the times:
While Katie & Josh are off Netflix-and-chilling,
you’re sitting there with your thumb up your ass waiting for a download to finish
before moving your laptop over to your TV (if you can even find a place to put it)
so you can make sparks with an HDMI cable like the caveman jackass you are.

Piracy took off because it offered
such a massive improvement in convenience over CDs and DVDs
that no one gave a shit about the risk of a lawsuit.
Netflix and Spotify have done likewise,
offering such a massive improvement in convenience over piracy
that no one gives a shit about the nominal subscription fees.
(Goes to show it was never so much about stealing as it was about price.)

But what we’ve gained in access, we’ve lost in ownership:
$5 may get you more music than you could dream of for 30 days,
but cancel your subscription and the tap turns right off.
And what we’ve gained in discovery, we’ve lost in heritage:
[Netflix offers just thirty titles from the 1970s][],
and surely fewer the further back you go.

So if ownership or heritage matter to you,
you are left with no choice but to curate your own personal media library,
in which case piracy is still your best last refuge.[^1]
Can you do it with the same on-demand convenience of streaming media services?

...nope. I’ll admit right out of the gate that it’s still far from perfect.
For many people out there, the problems I’ve encountered might be dealbreakers.
But my setup now is better than it was,
and probably quite a bit closer to a modern streaming experience
than you might have thought.

A couple of the most important pieces of the puzzle
are commercial services (read: not free).
If you’re willing to cough up a little dough
to step up your piracy game, read on.

## Step 1: Rent a Seedbox (€5/mo. and up)

* **What’s a seedbox?**

  Just a server that you can download torrents to.

* **How is that different from a regular hosted server?**

  It’s not, really, except that
  server configurations, customer service, and privacy mechanisms
  are all targeted toward torrenting:
  lots of storage, no data transfer limits,
  step-by-step guides for setting stuff up,
  payment accepted via Bitcoin, etc.

* **What provider should I go with?**

  Check out the recommendations on [/r/seedboxes][].

* **Why can’t I just use my own computer instead?**

  You could—but since the goal is to stream to any device,
  it’d need to be on 24/7 and connected to ethernet,
  and you’d have to set up port forwarding on your router
  to expose your computer to the public Internet.
  Seeding torrents back to the swarm would also eat your bandwidth.

  But if you’re okay with all that, knock yourself out.

## Step 2: Download Torrents to It

### The Old Way

Traditionally, your procedure for downloading stuff probably looked like this:

1. Search on the Pirate Bay
2. If it’s not out yet, forget about it for a couple weeks, then return to Step 1.
3. Add the torrent to Transmission (or whatever client you use)
4. Wait for it to finish leeching
5. _Maybe_ wait for it to finish seeding
6. _Maybe_ rename the files
7. _Maybe_ move the files to an appropriate folder

And if you skip any of the maybes, it’s generally because you’ve decided it’s
not worth the trouble, or because you’re gonna get around to it eventually,
one of these days, swear to god.

But you’re paying for a seedbox now, so you can do better.

### The New Way

Say hello to [Sonarr][]:

<video width="100%" controls>
  <source src="/i/piracy-sonarr.webm" type="video/webm" />
  <source src="/i/piracy-sonarr.mp4" type="video/mp4" />
</video>

Sonarr is a web application that will handle all seven steps above,
more or less automatically.
Install and run it on your seedbox,
and the service will appear at the URL it’s configured for
(_e.g.,_ https://your-seedbox-provider.com:24601/username/sonarr).

Sonarr doesn’t replace any of your existing software tools.
It just streamlines and automates a bunch of work you’d otherwise have to do yourself.
Give it a list of places to search for torrents (“indexers”),
and it will return a single list of aggregated results.[^2]
Give it a way to talk to Transmission (or any other “download client”),
and it will handle adding the torrents you select.
Tell it how you like your files organized,
and it will wait ’til the torrent’s finished
before moving the files to the appropriate folder,
renaming them to whatever format you’d like,
and notifying your media player that it’s time to refresh library metadata.

With Sonarr, you can even add media _before it comes out,_
and it’ll watch for new torrents and fetch one automatically when it’s released.

This means that Sonarr manages its own database of your media library,
separate from what torrents you have in Transmission
and what files you have on your hard drive.
This takes some getting used to,
but it’s where the power and utility of Sonarr really lie.

One tiny catch: Sonarr is only for TV shows. For movies, there’s [Radarr][]; for
music, [Lidarr][].[^3] Triple the setup, triple the fun!

## Step 3: Stream Movies/Music off of It (Freemium)

Now that you’ve got a system for _collecting_ your media,
it’s time to _enjoy_ it. For that, I use Plex:

<video width="100%" controls>
  <source src="/i/piracy-plex.webm" type="video/webm" />
  <source src="/i/piracy-plex.mp4" type="video/mp4" />
</video>

Plex is a commercial streaming service where it’s your job to run the server.
With it, you can stream videos and music directly from your seedbox to your
browser, phone, Chromecast, AppleTV, PlayStation, and [much more][].

Bear in mind that as a commercial service, if Plex goes under,
the party’s over and you’ll have to find another solution.
Unlike Sonarr, where the app is served directly off of the seedbox,
Plex only uses your hardware to serve the content,
and uses _their_ hardware to serve the client app.

Set it up on your seedbox, create an account at <https://plex.tv>, and go to town.
At the time of this writing, it’s free to use,
but $5/mo. will buy you multi-user accounts and [a bunch of other features][].

On a sufficiently powerful machine (that includes most seedboxes),
it will transcode videos on the fly, adapting the quality to cope with bandwidth limitations.
The UI is still a generation or so behind Netflix & Spotify,
but leaps and bounds ahead of anything else I’ve seen.

Fair warning: it’s not without its flaws.
I hate to say it, but I’ve been using it for about a month now,
and I’ve run into a few very irritating issues:
Sometimes, I’ll be playing an album on my iPhone,
and it’ll play three seconds of one song before skipping to the next track.
Sometimes, the playback meter will keep moving, but the audio will just stop entirely.
Or I’ll be streaming a TV show to my Chromecast, and it’ll lag and stutter
and won’t actually degrade the quality of the video to keep playback smooth.
Some issues only seem to happen on the mobile client; others happen everywhere.

For the moment, I’m prepared to chalk it up to the geographical limitations of
streaming from a seedbox in Europe to a device in Asia,
and am planning on setting up my own server at home
in the hopes that that will fix it.

## Wrapping up

That’s about all there is to it. To recap:

1. Rent a seedbox;
2. build your library with Sonarr/Radarr/Lidarr;
3. stream your library with Plex.

(Or just stick with Netflix and Spotify. It’s your life.)

Thanks for reading!

---

[^1]:

    Before you mistake me for a cheapskate or a leech,
    let me state my unequivocal support for commerce in art.
    Culture thrives when creators are paid to make beautiful things,
    and there is no better system for getting artists paid today
    (and often no better way to discover them)
    than streaming media services like Netflix and Spotify.
    Piracy and legitimate consumption are not mutually exclusive;
    if you like the goodies, feed the machine.

[^2]:

    Now would be a good time to clarify that
    Sonarr requires a good bit of configuration to know where to look for torrents,
    and that’s a whole other rabbit hole.
    Strictly speaking, Sonarr was not made for searching for torrents;
    it was made for monitoring releases on [usenet newsgroups][].

    In order for it to check for new releases,
    it has to be configured to follow some kind of feed
    (or _indexer,_ as it’s called in Sonarr)—traditionally a newsgroup,
    or a [private tracker’s][] RSS feed.

    In order for it to search across an entire BitTorrent tracker
    (like the Pirate Bay), you need [Jackett][].

    Since Sonarr is built to watch for releases and grab them automatically,
    you have to be careful about the quality restrictions you set
    (or select your downloads manually),
    or else you might end up accidentally downloading a super-high-quality
    25GB Blu-ray release on a private tracker and end up demolishing your ratio.
    (That’s private tracker lingo; if you don’t get it, don’t worry about it.)

[^3]:

    Why are they split into three different programs?
    Sonarr came first, and was highly specialized
    for managing a collection of TV shows.
    The other two are “unofficial” forks maintained by other people,
    who wanted the same kind of tool for movies and music.

    (In other words, because without FOSS,
    we wouldn’t even have Radarr or Lidarr to begin with.)

[Netflix offers just thirty titles from the 1970s]: https://www.pastemagazine.com/articles/2017/04/the-best-70s-movies-on-netflix.html
[/r/seedboxes]: https://www.reddit.com/r/seedboxes
[Sonarr]: https://github.com/Sonarr/Sonarr
[Radarr]: https://github.com/Radarr/Radarr
[Lidarr]: https://github.com/lidarr/Lidarr
[much more]: https://www.plex.tv/apps-devices/
[a bunch of other features]: https://www.plex.tv/plex-pass/
[usenet newsgroups]: https://reddit.com/r/usenet
[private tracker’s]: https://reddit.com/r/trackers
[Jackett]: https://github.com/Jackett/Jackett
[hard links]: https://stackoverflow.com/a/185903
