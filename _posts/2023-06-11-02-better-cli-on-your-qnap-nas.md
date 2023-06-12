---
layout: post
title:  "Better CLI on your QNAP NAS"
date:   2023-06-11 16:21:13 -0700
categories: qnap
---
After [installing Entware]({% post_url 2023-06-11-01-installing-entware-on-a-qnap-nas %}), the next thing I did was configure my shell environment to provide a better CLI experience. The default shell is terribly basic!

Here's what I did:

1. I [installed Entware]({% post_url 2023-06-11-01-installing-entware-on-a-qnap-nas %}).

2. I installed a few packages: `sudo opkg install zsh curl git git-http coreutils-mkfifo tmux`.

3. I configured my account to use [Zsh][zsh] instead of the default `/bin/sh`. Normally you'd do this by using [`chsh`][chsh] or editing `/etc/passwd` directly to use `/opt/bin/zsh` as your new shell.

    However in this case I did it a bit differently for a couple of reasons:
    * in my experience with Synology NAS, OS upgrades tend to reset the shell in `/etc/passwd` to the default one (not sure if QNAP does the same, I guess we'll see)
    * if you mess with your Entware install and `/opt/bin/zsh` becomes unavailable, you may find yourself locked out of your account.

    Instead, I edited my `.profile` file with `vim ~/.profile` and added these lines at the end:

    {% highlight shell -%}
if [[ -x /opt/bin/zsh ]]; then
  export SHELL=/opt/bin/zsh
  exec /opt/bin/zsh
fi
    {%- endhighlight %}

This way, if `/opt/bin/zsh` is no longer available, you can stil log into your account -- it'll just fall back to the default `/bin/sh` shell.

4. Log out and back in, and configure Zsh with some basic settings.

The next steps are absolutely not specific to QNAP and are purely down to personal preference, but I'm listing them anyway:

5. Install [Oh My Zsh][oh-my-zsh] using the convenient [script][oh-my-zsh-install].

6. Install [Powerlevel10k][powerlevel10k] using the [installation instructions for Oh My Zsh][powerlevel10k-install-oh-my-zsh] (and install the [recommended font][powerlevel10k-recommended-font] for your terminal if you haven't already).

Now you can enjoy a modern, good looking CLI when ssh'ing into your NAS!

[zsh]: https://en.wikipedia.org/wiki/Z_shell
[chsh]: https://linux.die.net/man/1/chsh
[oh-my-zsh]: https://ohmyz.sh/
[oh-my-zsh-install]: https://ohmyz.sh/#install
[powerlevel10k]: https://github.com/romkatv/powerlevel10k
[powerlevel10k-install-oh-my-zsh]: https://github.com/romkatv/powerlevel10k#oh-my-zsh
[powerlevel10k-recommended-font]: https://github.com/romkatv/powerlevel10k#meslo-nerd-font-patched-for-powerlevel10k
