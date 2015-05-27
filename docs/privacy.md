### It's your data!

Your data should be yours. OwnTracks does all it can to ensure that,
and you decide where you store your data, how to process it, and for
how long you store it (if at all).

However, be advised, that if you're very concerned about privacy,
you probably shouldn't be using a smartphone at all. OwnTracks uses
your smartphone's location services to determine where it is. While
you decide whether you want to enable or disable this feature (check
your phone's documentation), there is no guarantee that the APIs used
by OwnTracks won't submit your data to the vendor's servers.

For example, it's quite possible that Apple or Google keep requests
used for reverse geo-coding your location; there is pretty much
nothing OwnTracks can do to avoid this.

As one of the developers writes:

> We could, with great sacrifices to code complexity, battery efficiency and
> performance code around all native services offered by the platform in order
> to use third party maps and geo coders. In that case Google might not receive
> your geocoder requests but another third party that sells it. We could also
> work around the Play Services by handling all the location acquiring the old
> way by querying the GPS/WIFI/Cell positioning ourselves.  I however doubt
> that these information isn't send to Google in that case too. The thing is,
> if you don't want Google or Apple to collect your position you have to
> disable location services in which case you don't have to bother installing
> the app anyway.

It's your choice.
