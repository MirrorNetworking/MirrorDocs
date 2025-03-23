# iOS AppStore

**iOS AppStore UDP**

Mirror's default kcp Transport uses UDP sockets.\
When submitting your App to the App Store review process for the first time, some people's apps get rejected because Apple's VPN can't seem to handle UDP.

It's not obvious to us why that is, but here are a few workarounds that people suggested:

* cooper: "Submit your App with a TCP transport like Telepathy. Once approved, switch to KCP again in the next update. Supposedly this works."
* Ninja: "The app reviews kept getting rejected until I essentially asked for manual intervention, and then it passed"
* Use multiplexer for apple release, for example kcp as main and telepathy as backup transport.

**iOS AppStore LAN Broadcasting**

Follow the Xcode instructions to get the multicast networking permission from Apple.

Add it to the app provisioning, then add the capability to the app itself, making sure you've properly added the entitlement to your app in Xcode.

Latest versions may need this: Add NSLocalNetworkUsageDescription to the info.plist.

\
Note: If LAN Broadcasting does not work, try another address, for example, change 0.0.0.0 to 255.255.255.255  (remember to rebuild after changing)

This should get Network Discovery features working, Kudos to overmatch-iman, Sylvain and other Discord users for reporting back the working steps.

