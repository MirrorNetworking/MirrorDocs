---
description: Mischa's unusual, yet convenient remote desktop hosting method.
---

# Hosting with a Remote Desktop

Please refer to our "[Pragmatic Hosting Guide](pragmatic-hosting-guide.md)" for a modern, best practices hosting method.\
This guide is about my unusual Remote Desktop hosting approach, without SSH, SCP, Terminal, etc.

{% hint style="info" %}
This is definitely not best practice, and not light on resources.\
However, it is very easy to use :smile:.
{% endhint %}

Here is a preview of the final result, this is a Google Cloud VM instance, running Debian with a Desktop Environment, which I log into via Google Remote Desktop from my Browser:

<figure><img src="../.gitbook/assets/image (65).png" alt=""><figcaption><p>A Google Cloud VM instance running with a desktop environment.</p></figcaption></figure>

This allows for the same workflow as my local machine. I can easily navigate the file system, check resource usage, check on long running tasks, download files with Firefox etc. without worrying about terminal commands.

Ease of use & convenience are more important to me than a few hundred megabytes of storage.\
But again, this is not best practice.

### Installation Instructions

{% hint style="info" %}
This guide specifically uses **Google Cloud**, **Debian**, **XFCE** and **Google Remote Desktop**,\
because this combination was the easiest & most reliable. Feel free to try other methods.
{% endhint %}

This method is based on Google Cloud's official [Guide for using Google Remote Desktop](https://cloud.google.com/solutions/chrome-desktop-remote-on-compute-engine) on their VM instances. However, with a few recommendations which I figured out over time:

* Use at least **15 GB** of disk space.
* Use **Debian**. Ubuntu did not work for this method.
* A few install **errors are expected**, which is why Google has --fix-broken commands in there.
* Use **XFCE** as desktop environment. Cinnamon did not work for this.
* It's useful to also install **Firefox**, as mentioned in their guide.

After following the Guide, you should be able to log in with [Google Remote Desktop](https://remotedesktop.google.com/access/).

### Extra: File Transfers

Google Remote Desktop's interface has a file transfer feature, which is not that convenient to use.

I found it easier to use any of the popular cloud file sync tools.\
[Mega](https://mega.nz/) works great for this, because of official Linux support as well as end-to-end encryption.\
\
First install it on your local machine with the official download, and create a folder with a test file.\
\
Afterwards install it on the cloud server, with the following steps:

```
  hostnamectl   // to see your debian version
  // next: use firefox to download the Mega client for Debian 10
  sudo apt install /home/USERNAME/Downloads/FILENAME.deb
  open via applications->system->megasync
```

### Summary

We've learned how to install a desktop environment on our cloud server, how to log into it via Google Remote Desktop, and how to transfer files to it.

I hope this makes life easier for some of you who aren't that fond of the terminal.\
We use this method for our Discord Bot (Alan), and a few other tools.\
You can easily use this for a few game servers as well.\
\
But remember, our [Pragmatic Hosting Guide](pragmatic-hosting-guide.md) is better for serious games.
