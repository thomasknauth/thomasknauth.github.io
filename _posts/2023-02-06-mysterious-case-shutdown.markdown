---
layout: post
title:  "The Mysterious Case of /shutdown"
date:   2023-02-06 15:33:12 +0100
categories: linux
---

Once upon a time, there was a machine which would throw a kernel panic on every reboot.

```
...
[  OK  ] Finished Reboot.
[  OK  ] Reached target Reboot.
/shutdown: error while loading shared libraries: libsystemd-shared-245.so: cannot open shared object file: No such file or directory
[604555.4104131 Kernel panic - not syncing: Attempted to kill init! exitcode=0x00007100
...
```

The exact circumstance at how the machine got into this state are unknown (partial upgrade?; simply having a broken version of the software in question  which we will discover at the end of this story?). In any case, it was annoying enough to investigate, since we sometimes need to reboot the machine remotely.

Of course, the first thing that made our head scratch was what  exactly this /shutdown binary was. Tring to find it on the local file system did not reveal anything. Also, there referenced shared library was nowhere to be found, but only a more recent version (libsystemd-shared-249.so).

The first thing I tried was to copy the missing shared library (libsystemd-shared-245.so) from some other server. This did not solve the problem, but changed the behavior: now the machine would complain about another missing dependency, namely, libsystemd-shared-249.so the one which actualy did already exist - makes total sense.

Somehow, this is related to systemd and missing dependencies. We had lots of theories and the details of all the things we tried are murky. At some point, I tried to rename /lib/systemd/systemd-shutdown on a hunch to see if that had any effect. It did! The machine would now complain about a missing systemd-shutdown binary.

Along the way in all this stabbing in the dark I stumbled upon [dracut](http://www.kernel.org/pub/linux/utils/boot/dracut/dracut.html): "... an event driven initramfs infrastructure". While I was aware that there is an initial in-memory file system involved at boot, it turns out something similar is used on [the way out too](https://mirrors.edge.kernel.org/pub/linux/utils/boot/dracut/dracut.html#_dracut_on_shutdown).

Essentially, there exist some systemd targets that are executed when the machine goes down. Those targets assemble an in-memory file system with a bunch of things. Just for fun, I did `touch /run/initramfs/shutdown` to see if that had any effect. It did. No more complains about missing binaries or libraries - the machine would reboot without any errors. Somehow, we are getting closer.

At this point I became interested in what exactly systemd-shutdown does. One `apt-get source systemd` later, I was looking at the source. Then it immediately became clear, what /shutdown likely is, [since the source references it in a couple of places](https://github.com/systemd/systemd/blob/bbcce4f8d7cb213e30e8538144e66cabc245fbc4/src/shutdown/shutdown.c#L548).

Studying the code for systemd-shutdown also explains why creating an empty /run/initramfs/shutdown file made the error disappear. At some point in the systemd-shutdown code, the /run/initramfs/shutdown file is probed for execute permissions. The file I created fails this check, leading the variable `can_initrd` to evaluate to false. As a result, The code path trying to execute /shutdown is skipped.

```
can_initrd = !in_container && !in_initrd() && access("/run/initramfs/shutdown", X_OK) == 0;
```

The last missing piece to this story is `finalrd`. This shell script is responsible to actually assemble the contents of /run/initramfs when shutting down the machine. Since it is just a shell script, it is easily inspected. Among the things it tries to do is figure out the runtime dependencies for some binaries - systemd-shutdown among them. And this is where things went south.

[The bugtracker for finalrd](https://git.launchpad.net/finalrd/commit/finalrd.sh?id=f203066af54204e452c4c8a531ff02d6c2893dcb) had the following commit message, which pretty much sounds like the problem we ran into.

```
Improve library detection in finalrd.sh.

I was accidentally testing my glibc 2.34 snapshot in a live session and
noticed that rebooting failed. On digging this turned out to be because
the dynamic linker path with my snapshot did not match the regexp in
finalrd.sh any more. Rather than playing with regexp games, let's just
use a more reliable way to get the dependencies of a binary.
```

So, instead of using some brittle regex trying to figure out the dependencies, they now invoke the dynamic linker and parse its output for better results. Sounds legit.

At this point, I am not sure how I ultimately "fixed" the system. At the time, the Ubuntu repositories did not have the updated version of finalrd yet, I think. So, maybe I copied it manually or just stuck to `touch`ing /run/initramfs/shutdown at boot time. In any case, it was an interesting "rabbit hole" to disappear into for a bit.
