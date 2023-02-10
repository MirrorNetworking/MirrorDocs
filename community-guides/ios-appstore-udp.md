# iOS AppStore UDP

Mirror's default kcp Transport uses UDP sockets.\
When submitting your App to the App Store review process for the first time, some people's apps get rejected because Apple's VPN can't seem to handle UDP.

It's not obvious to us why that is, but here are a few workarounds that people suggested:

* cooper: "Submit your App with a TCP transport like Telepathy. Once approved, switch to KCP again in the next update. Supposedly this works."
* Ninja: "The app reviews kept getting rejected until I essentially asked for manual intervention, and then it passed"
