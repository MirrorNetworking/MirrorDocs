---
description: https://github.com/MirrorNetworking/LiteNetLibTransport/
---

# LiteNetLib Transport

**LiteNetLib based transport for Mirror.**

## Usage <a href="#usage" id="usage"></a>

1. Download the unity package from [Releases ](https://github.com/MirrorNetworking/LiteNetLibTransport/releases)and import it to your project (it does not contain Mirror)
2. Put `LiteNetLibTransport` component on gameobject with NetworkManager and assign it there

## Features <a href="#features" id="features"></a>

* UDP
* Built-in Network Discovery and UPnP
* Fully managed code
* Small CPU and RAM usage
* Small packet size overhead ( 1 byte for unreliable, 3 bytes for reliable packets )
* Different send mechanics
* Reliable with order
* Reliable without order
* Ordered but unreliable with duplication prevention
* Simple UDP packets without order and reliability
* Automatic small packets merging
* Automatic fragmentation of reliable packets
* Automatic MTU detection
* NTP time requests
* Packet loss and latency simulation
* IPv6 support (dual mode)
* Connection statisitcs (need DEBUG or STATS\_ENABLED flag)
* Multicasting (for discovering hosts in local network)

## IL2CPP Warning! <a href="#il2cpp-warning" id="il2cpp-warning"></a>

With IL2CPP, IPv6 is only supported on Unity 2018.3.6f1 and later because of this:\
&#x20;[Unity ChangeLog](https://unity3d.com/unity/whats-new/2018.3.6)

> IL2CPP: Added protocol support for IPv6 on Windows. (1099133)
>
> IL2CPP: Correctly indicate that IPv6 is not supported on non-IPv6 platforms. (1108823)

Also, socket Reuse Address option isn't available in IL2CPP.

## Credits <a href="#credits" id="credits"></a>

RevenantX - for [LiteNetLib](https://github.com/RevenantX/LiteNetLib/releases)\
&#x20;vis2k & Paul - for [Mirror](https://assetstore.unity.com/packages/tools/network/mirror-129321)\
&#x20;Coburn - for [Ignorance](https://github.com/SoftwareGuy/Ignorance) which i've used as an example\
&#x20;Dankrushen - for helping me find one small mistake which i couldn't find for two days\
&#x20;Lucas Ontivero - for [Open.Nat](https://github.com/lontivero/Open.NAT/releases), used for UPnP\
&#x20;shiena - for NetworkDiscoveryHUD
