# XS4ALL Diablo patches

I used to run the [diablo](http://www.openusenet.org/diablo/)
NNTP software on our newszilla usenet cluster at [XS4ALL](https://en.wikipedia.org/wiki/XS4ALL).

We were using the diablo-6-CUR-20170901-00 version from the
[development snapshots](http://www.openusenet.org/diablo/download/snapshots/)

## Debian init.d script

We run diablo on Debian/Linux systems, and we store the history
file on <i>ramfs</i> (note, not <i>tmpfs</i>, as that can get
swapped out, if that happens performance is worse than any other
disk-based filesystem. Ramfs cannot get swapped out.). This is the
/etc/init.d/diablo file that sets it all up:

- [diablo.init](../../raw/main/init-20181106/diablo.init).

Ofcourse there's a matching /etc/init.d/dreaderd file, which sources
a /etc/default/dreaderd file for config settings:

- [dreaderd.init](../../raw/main/init-20181106/dreaderd.init)
- [dreaderd.default](../../raw/main/init-20181106/dreaderd.default)

(<i>version init-20181106</i>)

## New set of /news/adm scripts
The sample administration script that ship with diablo are meant
to be customized for your local setup. Unfortunately they were
written in
[C shell](http://www.faqs.org/faqs/unix-faq/shell/csh-whynot/).
I did a rewrite in standard /bin/sh, which in most cases you do
not have to customize at all. They automatically detect if dreaderd
or diablo are running, and run only if needed. Just call the scripts
from news' crontab and you're all set.

- [the new /news/adm scripts](adm-20180207/)

(<i>version adm-20180207</i>)

## libradius-linux patch

We use `libradius-linux` on our Linux systems to get the Diablo radius support to work.
It's an (old, but working) port of the FreeBSD radius library.

- [Documentation](https://web.archive.org/web/20160719011052/http://www.portal-to-web.de:80/tacacs/libradius.php)
- [libradius-linux-20040827.tar.gz](https://web.archive.org/web/20060721021659/http://portal-to-web.de:80/tacacs/libradius-linux-20040827.tar.gz)

You're supposed to link it with this
[MD5 library](https://web.archive.org/web/20060721021644/http://portal-to-web.de:80/tacacs/libmd.tar.gz)
but I like to keep dependencies to a minimum so
I patched it to link with <i>libcrypto</i> from openssl instead
(which is almost certainly included with your linux distro).

- [patch for libradius](../../raw/main/libradius-linux-20040827/libradius-linux-20040827.patch)

(<i>version libradius-linux-20040827</i>)

## Diablo patches

Finally, these are the patches we use on top of diablo-6-CUR-20170901-00: 

- [patches-20191210](patches-20191210/)

