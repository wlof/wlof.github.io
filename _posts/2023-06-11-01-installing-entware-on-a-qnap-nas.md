---
layout: post
title:  "Installing Entware on a QNAP NAS"
date:   2023-06-11 15:44:47 -0700
categories: qnap
---
I recently upgraded my older Synology DS415play NAS to a shiny new [QNAP TS-664][qnap-ts-664] model. (I switched from Synology to QNAP because all the newer Synology models use AMD CPUs, and I preferred an Intel CPU for better [Plex compatibility][plex-nas-compatibility].)

Since I use the command line a lot, I wanted to be able to use many CLI utilities that are not available by default on QTS (QNAP's custom Linux OS). Some Google searches quickly revealed that [Entware][entware-wiki] was what I was needed. Their wiki even includes a page specifically for [QNAP NAS installs][entware-wiki-qnap-install], which also includes a link to [a thread on QNAP's own forums][entware-qnap-forum-thread].

I did run into some minor trouble with the install. The answer was in the forum thread, but finding the information you need in an old thread with years of posts can be frustrating, so I'm collecting it here. Hopefully it will be useful to somebody!

So here's how you install Entware on a modern QNAP NAS running a recent version of QTS (in my case, QTS 5.0.1.2376).

1. First, download the [package for standard installation][entware-std-qpkg]. Make sure to check the Entware wiki page, maybe a more recent version will have been published by the time you read this. (Although the current version, 1.03a, was released in 2018, so it looks like they're not releasing new versions very often!)

2. Then install the package manually, by logging into your NAS' web UI, opening the App Center, and clicking the "Install Manually" icon in the top-right menu bar. Select the `.qpkg` file you downloaded and proceed with the install.

3. Here's the part that gave me trouble. Entware only configures the root user, but on newer QTS versions, the root user is disabled. So you'll need to modify the `PATH` environment variable for your user account to be able to use the `opkg` package manager.
    
    After logging into your account, create or edit your `.profile` file with `vim ~/.profile` and add this line:
    
    {% highlight shell -%}
PATH=/opt/bin:/opt/sbin:$PATH
    {%- endhighlight %}

4. Log out and back in (or simply run `source ~/.profile`). `opkg` is now available, though you'll still need to use `sudo` (just like you would when using `apt` on a Debian-based Linux distribution).

    Run `sudo opkg update` to upate the list of packages, then `sudo opkg list` to see which packages are available. You can now install any of them with `sudo opkg install`. For example if you want to be able to use [zsh], simply install it with `sudo opkg install zsh`.

That's it! If you have other user accounts, you'll need to repeat step 3 for each of them so they can also use the binaries installed via `opkg`.

[qnap-ts-664]: https://www.qnap.com/en-us/product/ts-664
[plex-nas-compatibility]: https://docs.google.com/spreadsheets/d/1MfYoJkiwSqCXg8cm5-Ac4oOLPRtCkgUxU0jdj3tmMPc
[entware-wiki]: https://github.com/Entware/Entware/wiki
[entware-wiki-qnap-install]: https://github.com/Entware/Entware/wiki/Install-on-QNAP-NAS
[entware-qnap-forum-thread]: https://forum.qnap.com/viewtopic.php?f=351&t=139781
[entware-std-qpkg]: http://bin.entware.net/other/Entware_1.03a_std.qpkg
