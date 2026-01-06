---
description: Spatial Hash Interest Management
---

# Spatial Hashing

## **Spatial Hashing Interest Management**

{% hint style="warning" %}
For better performance try [Hex Spatial Hashing](hex-spatial-hashing.md) instead.
{% endhint %}

First things first: **"Spatial Hashing"** sounds complicated so that we network programmers can stroke our egos. The technique is actually pretty simple, and if you used uMMORPG before then you probably remember this as **"Grid Checker"**. We go with "Spatial Hashing" anyway since that's the industry term.

### Setting Up

Add the **Spatial Hashing Interest Management** component to the same object as your **Network Manager**:

![](<../../.gitbook/assets/image (68).png>)

### **How It Works**

Previously we **Vector3.Distance** checked each spawned entity against each connection. Instead, we put each spawned entity into a **Grid** and for each connection, we send all **8-neighbor** grid entries to it.  This is extremely fast. In early uMMORPG tests, it was 30x faster than distance checking. The algorithm is less complex, so it scales well to large amounts of entities.

![Source: https://www.dynetisgames.com/2017/04/05/interest-management-mog/](<../../.gitbook/assets/image (27).png>)

{% hint style="info" %}
Check out Mirror's **Benchmark** example. It uses Spatial Hashing and displays a runtime slider to let you play around with the visibility range.
{% endhint %}
