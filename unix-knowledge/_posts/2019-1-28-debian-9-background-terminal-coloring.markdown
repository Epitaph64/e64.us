---
layout: post
title:  "Debian 9 xfce4-terminal Background Color Customization for Differentiating Root Elevation"
date:   2019-01-28
categories: [debian, xfce4, coloring, sudo]
---
***
## Preface:

I have been recently developing software from a Debian 9 laptop workstation,
and have run into a user-issue where I was mistakingly entering commands into
a non-root terminal due to the very subtle style differences between the user
and root terminal coloring. Yes, I know this is a very bad thing! I realized
I could easily solve this cognitive issue however by simply restyling the root
terminal to use a different background color however I found the knowledge articles
online lacking and had to research briefly to come up with a solution.

***

## Steps taken to solve:

The config file for the xfce4-terminal (used by the Debian 9 distro) is located at
the following path for my system:

<pre class="outline">
~/.config/xfce4/terminal/terminalrc
</pre>

My user account already had one of these files created, and I had to create a new duplicate
under the root relative path at the same location.
I then modified that file's ColorBackground setting to a darkish gray color as such:

<pre class="outline">
ColorBackground=#555557575353
</pre>

I then had to update the /etc/sudoers file to grant sudo privilege
to my user account. However, I wanted to require the root password
still in order to spawn the root terminal. To do so, I added the following
two lines to the /etc/sudoers file:

<pre class="outline">
Defaults    rootpw
epitaph64 ALL=(ALL) ALL
</pre>

^ epitaph64 is the name of my user account on the Debian 9 laptop.

As a final step, I added the following alias to my ~/.bashrc file:

<pre class="outline">
alias rootterm="nohup sudo -bu root xfce4-terminal >/dev/null 2>&1"
</pre>

I arrived at this final command after researching online and consulting the man pages. The -b argument is used to
spawn the root terminal as a background process (using a trailing & as is standard
causes the password prompt to be inaccessible without a call to fg.) The -u
argument followed by "root" executes the command passed as the root user. The nohup command
causes the terminal prompt to be shown properly without requring a carriage return.
It is also used to suppress a warning about failing to connect to the session manager.
That warning is shown below:

<pre class="outline">
Failed to connect to session manager: Failed to connect to the session manager:
SESSION_MANAGER environment variable not defined
</pre>

I say warning because I could not find easily any documentation online which explained the downside
of not connecting a terminal to the session manager. However, I did find that
is required in order for X11R6 SM compliant programs to function properly (ones
that use the Inter-Client Exchange.) My guess is this is only relevant to a
distributed computing environment which my workstation is not.

Some relevant links regarding that can be found here:

<a href="https://linux.die.net/man/1/xfce4-session">https://linux.die.net/man/1/xfce4-session</a><br/><br/>
<a href="https://www.x.org/wiki/X11R6/#index9h3">https://www.x.org/wiki/X11R6/#index9h3</a>

If I run the xfce4-terminal command from the newly created root terminal,
I see the following error which has zero search results shown:

<pre class="outline">
** (xfce4-session:1939): CRITICAL **: polkit_unix_process_set_property: assertion 'val != -1' failed
gpg-agent[1952]: WARNING: "--write-env-file" is an obsolete option - it has no effect
gpg-agent[1952]: directory '/root/.gnupg' created
gpg-agent[1952]: directory '/root/.gnupg/private-keys-v1.d' created
gpg-agent[1953]: gpg-agent (GnuPG) 2.1.18 started
...
</pre>

(followed by some warnings and more errors. I then have to Ctrl-C the process which causes the terminal to give up on connecting to the session manager.)

I have yet to see any consequence of not connecting the root terminal to the session manager,
however I'm certain there must be some reason why it cannot connect (maybe the root terminal
exists somewhere lower in the ICE process flow or the root user is not meant to connect
to the session manager by design. Does anyone know?

***
## Additional sources used:

<a href="http://documentation.commvault.com/commvault/v11/article?p=1856.htm">http://documentation.commvault.com/commvault/v11/article?p=1856.htm</a><br/><br/>
<a href="https://superuser.com/questions/161593/how-do-i-make-sudo-ask-for-the-root-password">https://superuser.com/questions/161593/how-do-i-make-sudo-ask-for-the-root-password</a><br/><br/>
<a href="https://www.linuxquestions.org/questions/slackware-14/where-does-xfce-terminal-store-its-color-scheme-4175463770/">https://www.linuxquestions.org/questions/slackware-14/where-does-xfce-terminal-store-its-color-scheme-4175463770/</a><br/><br/>
<a href="https://stackoverflow.com/questions/10408816/how-do-i-use-the-nohup-command-without-getting-nohup-out">https://stackoverflow.com/questions/10408816/how-do-i-use-the-nohup-command-without-getting-nohup-out</a><br/>
