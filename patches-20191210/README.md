
## Compilation fixes:

- 01_dreader-rlimit-fix.patch  
  Fix compilation problem with rlimit, include <resource.h>

- 02_diablo-gzfile-is-already-pointer.patch  
  The gzFile type used in zlib is already a pointer, and it
  should be used as 'gzFile', not '*gzFile'.

## Bug fixes:

- 03_dreader-getsockname-crash.patch  
  In some cases the getsockname() call fails and returns -1. dreaderd goes
  on processing the connection and coredumps in dns.c::DnsTest().
  This leaks filedescriptors, and it gets worse from there. Fix it
  by simply closing the filedescriptor and returning.

## Optional enhancements:

- 10_diablo-dr_lflags.patch  
  Add DR_LFLAGS (dreader LFLAGS) so that only dreaderd is linked with
  -lradius, -ldb etc

- 11_dreader-default-large-artno.patch  
  Enable large article numbers (>2147483647) by default.
  This doesn't conform to RFC3977 paragraph 6, which says
  "Article numbers MUST lie between 1 and 2,147,483,647, inclusive."
  but most clients support it anyway. However RFC3977 also says
  "Note that it is likely that the article number limit of 2,147,483,647
   will be increased by a future revision or extension to this
   specification."

- 12_dreader-vserver-multiple-interfaces-cidr.patch  
  This patch allows one to use multiple 'interface' statements
  in a vserver definition, like an IPv4 and an IPv6 one.
  Additionally, the address argument can be a CIDR address/mask expression.

- 13_dreader-maxconn-per-vserver.patch  
  Count 'maxconnperhost, maxconnpergroup, maxconnperuser only
  in the context of the current vserver. So if user with ip
  a.b.c.d connects to VSERVER1 that connection will not be
  counted in VSERVER2.

- 14_dreader-requiregroupcmd.patch  
  If in a readerdef 'requiregroupcmd' is set to 'yes', a client can
  only use STAT/ARTICLE/HEAD/BODY after a group (any group) has
  successfully been entered. Useful on textservers that are hammered by
  binary download clients for message-ids that are not available anyway.  
  This does not block POST/IHAVE at the moment.

- 15_dreader-vs-hostname-default.patch  
  If in a vserverdef the "hostname" attribute is set to "DEFAULT",
  the default hostname will be filled in instead of ""

- 16_dreader-maxagepolicy.patch  
  There is a readerdef setting called "maxagepolicy".
  It sets a limit to the age of the article that is retrieved by
  dreaderd from a backend server. The value is in seconds.
  This setting is undocumented and it looks like it was never
  implemented fully - but it's only a small patch to make it
  work. Note that this does not set a limit on HEAD commands
  or overview data (which is local).

- 17_dreader-ipv6-hosts.patch  
  Support IPv6 for hosts in dserver.hosts.

- 18_diablo-dsyncgroups-ipv6.patch  
  IPv6 support for dsyncgroups

- 19_dreader-modern-crypto.patch  
  If POST_CRYPTXTRACE is enabled for X-Trace header encryption, use AES256
  instead of DES (which doesn't even compile anymore....). In that case
  also use SHA512 instead of MD5 for NNTP-Posting-Host mangling.

- 20_dreader-passwd-longer-and-spaces.patch  
  Allow spaces in passwords. Also, password length was limited to 16
  characters. Up it to 64.

## XS4ALL local enhancements:

- 30_diablo-xs4all-config.patch  
  XS4ALL/Newszilla compile settings

- 31_dreader-rad-readerdef.patch  
  Make it possible to return dreader.access attributes in a radius reply

- 32_dreader-friendly-maxconn-msgs.patch  
  Change messages like "Your host connection limit reached" into
  "Too many connections from your host (max %d)", etc, because
  they were confusing users ("I thought there was no download limit!").

- 33_diablo-xclient.patch  
  Support for XCLIENT

- 34_dreader-banner-short-hostname.patch  
  Add '%H' to the variables available for the welcome banner. It
  expands to the short hostname (up to the first dot, no domain).

- 35_diablo-AND-in-hashfeed.patch  
  Support for AND in hashfeeds. You can now AND a list of hashmatches
  (seperated by comma's) with another hashmatch. The hashmatch to be ANDed
  in is specified by using  a '&' instead of a ','. If an offset is not
  specified, it is '0' for the comma-seperated list and 4 for the AND match.
   
  For example,
   
     hash=2/9,1/72,12-18/72&1-702/1521,85-168/836
   
  means:
   
     must match ( (2/9 OR 1/72 OR 12-18/72) AND 1-702/1521:4 ) OR ( 85-168/836 )
   
- 36_dreader-haproxy.patch  
  Support the HAPROXY protocol, so that haproxy can be used as a TLS offloader.
   
  If a readerdef is matched that contains 'haproxy yes', dreaderd
  will on connect read the first line and interpret it as
  a version 1 haproxy send-proxy line. The connection will then
  be restarted, and it will treat the IP address sent by haproxy
  as the source address of the connection. It will then match a
  different readerdef.
   
  Example, for haproxy connecting /from/ 127.0.0.1 /to/ dreaderd:
   
  access 127.0.0.1    rd-haproxy
  readerdef rd-haproxy
      read      yes
      haproxy   yes
  end
   
  Note that the destination-address sent by haproxy is ignored, it
  will stay the same. So if you have multiple vserverdefs based on
  "interface", make sure the haproxy session connects to the right
  IP address.
   
- 37_dreader-radius-remoteip.patch  
  Put the connecting remote IP address in the RAD_CALLING_STATION_ID
  attribute for RADIUS authentication requests

- 38_dreader-log-ignored-authinfo.patch  
  If "ignoreauthinfo" is set in  a dreaderd.access "readerdef" section,
  still log the username sent by the client, prefixed with a "!".

- 39_dreaderd-more-pipelining.patch  
  As long as any data is still pending for a client we do not accept new
  commands. This patch allows for up to 256K to be outstanding. Experimental,
  it doesn't seem to make much difference. Probably because a lot of data
  is already buffered in the kernel socket buffer.

## Experimental patches:

- 50_diablo-zalloc-debug.patch  
  Keep a shadow administration of mempools and memnodes, and dump usage
  to a file in /news/run on SIGUSR2. Helped a lot with debugging,
  but has a lot of overhead when enabled (compile-time setting).

