# APT Persist

## NOTE: You probably should never run this script.

I should preface this with the fact that I wrote this with the intention
of using it for the 2019 Cyberforce Competition as a red team member. The
idea being a means to not only install several backdoors, but to also ensure
that it will continue to exist by installing itself in various users' crontabs.
When I tested this on a virtual machine, it installed just fine and worked like
a charm. However when I left it alone for a couple of hours the VM was pinning
the CPU at a load of over 300! You probably shouldn't use this code as a whole,
but some of the tricks I used in this script were extremely helpful! Definitely
good tricks here, like the ability to fudge a file's creation date with the
touch command.

Anyway, with that said here's the original Readme:

This script is a sneaky little script designed to be run after obtaining
a root shell on a box. It is self-containing, and all that needs to be
uploaded is persist.sh. You can then invoke it with:

```bash
target:~# bash persist.sh
```

You can add a -q flag to suppress output.

Upon executing, it will perform the following:

1. Finds the location of the nologin file represented in /etc/passwd (usually /usr/sbin/nologin)
1. Makes a copy of nologin to /var/tmp/nl.b
1. Writes a SUID shell back door in place of nologin
1. Sets root ownership of nologin, sets perms to 4777
1. Adds nologin to list of known shells in /etc/shells
1. Adds included SSH pubkey to the authorized_keys file to the man, backup, daemon, and bin users
1. Creates a stripped copy of itself into the following places:
   1. /etc/tmux.cf
   1. /etc/modprobe.d/systemctrl.conf
   1. /media/cdrom1/debian.iso
   1. /usr/local/lib/python3.9
1. Installs crontabs for the aforementioned users (man,backup,daemon,bin) to execute this script again every minute.
1. Updates all the timestamps of new files to an older date so new files aren't picked up in a find -mtime sweep

The SUID shell is just a little C blurb, specifically:

```c
int main(void)
{
    setuid(0);
    setgid(0);
    system("/bin/bash");
}
```

# TODO: Make this more betterer

Could probably stand to create functions instead of repeating myself, but hey it works.

Edit: no it really doesn't
