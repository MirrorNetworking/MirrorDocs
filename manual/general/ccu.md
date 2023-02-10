---
description: 'One of the most frequently asked questions: how many CCU can Mirror handle?'
---

# CCU

A similar question came up on the [Unity forums](https://forum.unity.com/threads/stress-test-using-unity-as-server.1126640/#post-7245842) a while ago, so I'll copy my answer here in case it's useful to anyone else.

![Our laggy 480 CCU test from 2019](../../.gitbook/assets/2021-06-17\_12-24-46@2x.png)

One of the MMOs [made with Mirror](https://github.com/vis2k/Mirror#made-with-mirror) had quite a lot of CCU when it launched, I think it was Inferna.\
They split the map into separate server instances, with around 200 CCU limit per map.\
I would have to look it up again, but I believe they achieved around 1000 CCU per world this way at times.\
\
There's also a [very old video](https://www.youtube.com/watch?v=mDCNff1S9ZU\&t=58s) of us trying 480 CCU worst case, all in one place like your video. It's laggy as hell, but the server survived just fine.\
\
Both Inferna and the 480 CCU video use old Mirror & Unity versions. We've had years of improvements since then, both for Mirror, Unity and server hardware.\
\
We still have plenty of optimizations to squeeze out of Mirror, with another CCU test planned afterwards around the end of this year.\
\
Keep in mind that your game's complexity is also a huge factor. A 3D physics movement MMO like WoW will be way harder to scale than a 2D click-to-move MMO. For an Indie developer, 2D is really worth considering. It's way cheaper, way easier to make and scales way better due to less complex physics/meshes etc.\
\
&#x20;At the end of the day, there's definitely a limit to what we can achieve in MonoBehaviour world.\
&#x20;For 1500 physics movement players like in WoW, you would definitely need DOTS or a server that does not run in Unity.\
\
&#x20;Imho Unity & MonoBehaviour is still a good option. Better to **release** even a 500 CCU MMO with 250 CCU per instance, than **never release** a 1500 CCU MMO.
