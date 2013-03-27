# Fogjammer
Local Music Pooling

## Overview

Fogjammer will be a music player that runs on your Android phone and
shares music and ratings on the local network.  Imagine you arrive at
your friend's house and they have music playing on random shuffle.
The moment you walk in the door (and your Android device joins their
wifi network), your music is included in the shuffle.

Better still, you've rated some of your music (and your friend's music,
the last time you were there), and the stuff you and your friend like
comes up more often in the mix.  That terrible music your friend likes
won't come on until you leave.

This could also be used in public places.  Imagine a cafe that
constantly adjusts their playlist according to the likes and dislikes of
whoever happens to be there.  (They'd probably stick to their own music
though, in case some joker walks in with a collection of fart sounds
with appealing metadata.)

One benefit of this is that your device knows what's playing all the
time.  Want to know what's playing, just look at your phone.  Go ahead
and rate the song while you're at it, and maybe the one before that
too.  (Unlike audio song-id apps, you don't have to rush to catch it
while the song is playing.)


## Status

Current status: all talk.

The music sharing should be relatively straightforward using UPnP and
doing things a bit more automatically than other apps do.

Ratings are easy from a UI perspective, but I'm not sure yet how easy
it will be to pass that around via UPnP.

Better-than-random shuffle play should be a big improvement too.  There
are other players that let you rate things, but I haven't seen that data
used very well.  (You can exclude songs with fewer stars, but how about
just playing it less often?)  If we run a cloud server and let people
contribute their ratings, we can use that to make better predictions of
how you'd rate new music, which will help make community shuffle-play
work well.


## Rough Breakdown

 - UPnP media renderer (plays the music, can be remote-controlled)
 - UPnP media server (shares your music, perhaps also your ratings)
 - UPnP control point (let's you see/control what's playing; will also let you rate songs)
 - Playlist Manager (selects and queues music; not sure how this fits into the UPnP scheme.  It should provide read/write access to the queue, so it's more than just an automated control point.)
 - Buffer Manager? (We never want to start playing a song until we have a complete copy.  Does UPnP support that, or will we need to manage transfers ourselves and only play local media?)
 - ratings database
 - song metadata database  (titles are nice for displaying, but we'll want more than that for good playlist construction)

Possible starting points for the basic app:

 - [vanilla](https://github.com/kreed/vanilla)
 - [CyanogenMod-Music](https://github.com/adneal/CyanogenMod-Music)
 - [old Android music app?](https://github.com/KreN11/android_packages_apps_Music)

Peer discovery and music sharing will be done via UPnP, probably using one of these libraries:

 - [cling](http://4thline.org/projects/cling/)
 - [CyberLink](http://www.cybergarage.org/twiki/bin/view/Main/CyberLinkForJava)
 - [OhNet](http://www.openhome.org/wiki/OhNet)

Also worth noting:

 - [subsonic](http://www.subsonic.org/)

I'm hoping the metadata sections of UPnP can be extended.  People will
generally have rated only a small sample of music, and we'll want to
predict their opinion of other songs using more metadata than the
frequently-wrong text names of song, artist, and album.

For a minimally functional app, it'll be enough to play random songs
based on some function of everyone's ratings of that song, but really
we'll want to do at least some simple inference to fill in ratings
gaps.  Doing that in the cloud is simpler (as we can store a wealth of
metadata and perhaps have copies of everyone's ratings), but it would be
nice to support offline operation for when the network is unavailable,
expensive, or considered too nosy.

We'll need an ontology and representation for people and their ratings
data.  Can we pass this around via UPnP or do we have to set up yet
another communications channel?

## See Also

Here are some proprietary UPnP apps.  They'll be useful for testing and for feature ideas.

 - [BubbleUPnP](https://play.google.com/store/apps/details?id=com.bubblesoft.android.bubbleupnp)
 - [Andromote](https://play.google.com/store/apps/details?id=de.czesla.android.remote) (control point only)
 - [EML UPnP-AV Control Point](http://www.appbrain.com/app/eml-upnp-av-control-point/org.eml.upnp) (control point only)
 - [PlugPlayer](https://play.google.com/store/apps/details?id=com.plugplayer.plugplayer)
 - [Private Dancer](https://play.google.com/store/apps/details?id=com.abk.privatedancer) (uses cling)
 - [ShareMe](http://android.sygem.com/shareme/)
 - [HomeDia](https://play.google.com/store/apps/details?id=mobi.qiss.HomeDia)
 - [Skifta](https://play.google.com/store/apps/details?id=com.skifta.android.app)
 - [2Player](https://play.google.com/store/apps/details?id=com.twoplay.twoplayer)



## Random Notes

 - Sadly, we can't distinguish between headphones and speakers, so
   you'll have to manually tell the app when you're playing music that
   other local people might hear.  Perhaps solo mode vs party mode.  (If
   you have a dedicated devices hooked up to the speakers, you can leave
   it in party mode.)

 - There will need to be a manual override to say "This person on the
   network is on the other end of the house and I don't care what they
   like."  You'd only need that when the player is in party mode, but not
   everyone is at the party.

 - Using UPnP means that other players can make use of our music, and
   other control points can watch (and perhaps influence) what's
   playing, but it's not clear to me at this point what to do with music
   shared by other media servers, because it will almost certainly have
   nearly useless metadata.

 - This could opportunistically acquire and reshare Creative Commons
   licensed songs you come across that your player thinks you might like
   (or that you heard and rated highly).  This could even happen when no
   one is listening to anything.  The apps find each other, tell each
   other about the free music their owner likes, then do some metadata
   inference and guess whether it's worth copying what the other person
   has.

   
 - Option to sync ratings with cloud server.  We can probably make
   better predictions if we have everyone's ratings and a huge
   database of music metadata.  It will also help people who have more
   than one device.  We don't need this for version 1 though.

 - We can canonicalize metadata using [EchoPrint](http://echoprint.me/).
   Then we can use their song ids to communicate ratings, and use
   artist ids and their similarity functions in our shuffle player.

 - Someday we could use this for communal volume voting at a party.

 - We could extract your ratings from iTunes to jump start things, but
   does anyone use Android and iTunes?  (It would be nice to have a
   compatible iOS version of this eventually...)

