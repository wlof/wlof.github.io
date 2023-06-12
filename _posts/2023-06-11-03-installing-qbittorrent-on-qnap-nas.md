---
layout: post
title:  "Installing qBittorrent on a QNAP NAS"
date:   2023-06-11 23:11:30 -0700
categories: qnap
---
A brief guide for properly installing and configuring [qBittorrent][qbittorrent] on a QNAP NAS.

1. First, if you haven't already, choose a port for your BitTorrent traffic and configure your router to redirect this port to your NAS' internal IP address.

    The default port range for BitTorrent is 6881-6889, but some ISPs might block those. Per [IANA][iana], I recommend choosing a random port between 49152 and 65535.

2. Log into your QNAP NAS' web UI, open App Center, and install [Container Station][container-station] (you can find it under "QTS Essentials").

3. In a shared folder on your NAS, create 2 directories:
    1. One for your downloads, e.g. `/share/storage/downloads`
    2. Another to hold qBittorrent's configuration files, e.g. `/share/storage/qbittorrent`

4. Open Container Station. Go to the Images tab and click Pull. In the "Pull image" popup window, enter `linuxserver/qbittorrent` for "Image Name" amd `4.5.3` for "Image version". (You can use a more recent version tag if one is available, but I don't recommend using the `latest` tag, as pinning a specific version is preferable. This way you can control if and when you want to upgrade to a newer version. Some trackers also don't immediately allow newer versions.) Finally, click Pull.

5. Before we proceed to create the actual container, we need to jot a few values down:
    1. the UID and GID that will be used to own the downloaded files. I recommend using those of your admin user. That's likely going to be `1000` for the UID and `100` for the GID, but you can check that by logging into your admin account and running the `id` command.
    2. you also need to choose a port for qBittorrent's web UI. Unlike the port used for BitTorrent traffic, I strongly recommend that you *don't* set up port forwarding for this one. If you want to access the UI from outside your network, use a VPN solution like [Tailscale][tailscale].
    3. finally, this is optional, but you should also write down your local timezone as defined in the [tz database][tz-db-time-zones], e.g. `America/Los_Angeles`

6. We're now ready to create the Docker container for qBittorrent.
    1. In Container Station's Images tab, click the + icon (the tooltip reads "Create Container") in the `linuxserver/qbittorrent` row.
    2. In the "Create Container" popup, configure the CPU Limit and Memory Limit as you wish. I used 50% for CPU Limit and half the total memory for Memory Limit.
    3. Don't click Create just yet! Click Advanced Settings first.
    4. In the Environment tab, add the following 4 variables:
        1. `PUID` should be set to the UID, e.g. `1000`
        2. `PGID` should be set to the GID, e.g. `100`
        3. `WEBUI_PORT` should be set to the web UI port, e.g. `8008`
        4. `TZ` should be set to the tzdb time zone, e.g. `America/Los_Angeles`
    5. In the Network tab, make sure that NAT is selected as the Network Mode (it should be the default) and that the following 3 ports are configured:
        1. the web UI port (e.g. `8008`) for both Host and Container, with TCP protocol
        2. the torrent port (e.g. `54321`) for both Host and Container, with TCP protocol
        3. the torrent port again, with UDP protocol
    6. No need to change anything in the Device tab.
    7. In the Shared Folders tab, make the following changes:
        1. First, remove the volume with a `/config` mount point in the first section.
        2. Then in the "Volume from host" section, create the following two volumes:
            1. Host Path: `/share/storage/downloads` (or whatever directory you chose to store your downloads) with a Mount Point of `/downloads` (make sure it's `/downloads`, plural!). Leave the Write checkbox checked.
            2. Host Path: `/share/storage/qbittorrent` (or whatever directory you chose to store qBittorrent's configuration files) with a Mount Point of `/config`. Leave the Write checkbox checked.
    8. Finally, click Create.

7. After a few seconds, your new qBittorrent container should be up and running. Navigate to the web UI (there should be a convenient link in Container Station, e.g. http://qnap:8008) and log in with `admin` as the username and `adminadmin` as the password.

8. You're basically done! Before you start downloading all those Linux ISOs, I recommend you click the gear icon to open qBittorrent's settings and make a couple of changes:
    1. First, in the BitTorrent tab, uncheck Torrent Queuing so you can have more than 3 active torrents.
    2. Then, in the Web UI tab, change the password (and maybe the username too).

Happy torrenting!

[qbittorrent]: https://www.qbittorrent.org/
[iana]: http://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml
[container-station]: https://www.qnap.com/en/software/container-station
[tailscale]: https://tailscale.com/
[tz-db-time-zones]: https://en.wikipedia.org/wiki/List_of_tz_database_time_zones
