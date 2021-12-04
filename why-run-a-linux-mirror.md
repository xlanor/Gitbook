# Why run a Linux mirror?

For the majority of Linux users, there's no real need to run a linux mirror. There's usually one or two major hosts in almost every country/region, and running a mirror requires money, both on electricity, as well as hardware to run a linux mirror.

I initially set up my linux mirror for personal usage. I wanted to try something new, I had about 5tb of storage free on my NAS that I wasn't using, and I was already running my hardware 24/7, anyway.

However, at that time, arch, which is my main distro that I utilise on a daily basis, was having mirror sync issues locally, and I could not find a few packages that I needed. In addition, I was trying a few new OSes such as EndeavourOS (which was the sucessor after the Antergos project shut down), and I couldn't find a reliable regional mirror.

So I set up my own mirror.

I do not have the same redundancy that other mirrors in Singapore have - I don't run my mirror on a UPS, which makes it susceptible to power trips, I don't have a core switch or a powerful router that can serve a ton of requests (which I don't expect to get, hence I'm not mirroring crazy popular distros like fedora or debian).

However, if you do set up the local mirrors with failover - there are many of us in Singapore who are running mirrors which may be unlisted, in the event that the main mirrors (either 0x or NUS in this case) is out of sync, it is highly likely that one of us has a mirror that's still up and in sync with an upstream mirror (since we mostly use different upstreams).&#x20;

If you do want some obscure packages that would not be feasible for the main mirrors in Singapore to host due to the low traffic, you can ususally reach out to one of the smaller mirrors lying around and there's a high chance that we can host it if we have sufficient space for it.

Here's some other smallers mirrors in Singapore that are ran by my friends which you may not have heard of:

* [aktkn.sg](https://mirror.aktkn.sg) - ran by Kenny (Arch, Parrot, kali, centOS, deb)
* [kst.asia](http://mirror.kst.asia) - ran by Likang (MX, Rocky, Mint, Ubnt, Arch)
* [soonkeat.sg](http://mirror.soonkeat.sg) - ran by Soon Keat. (Alma, ubnt, deb, kali)

