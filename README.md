# adhosts - Generate ad-blocking hosts file from multiple sources

This minimal tool builds a composite host file, containing entries mapping
domain names to IP addresses, from multiple sources, such as ad-blocking,
anti-tracking and other lists.

The resulting file can be used with eg. `dnsmasq` to provide ad-blocking via DNS
for an entire network, or it can be installed on single hosts.

This script condenses much of the functionality of eg. PiHole into a form that
is easily integratable into existing systems, without introducing lots of new
software. Thus, this tool is mostly geared towards system administrators and
people already familiar with their infrastructure.

## Setup

The `config` file contains all configurable parameters, which are

* `SRC_HOSTS`: A file containing lines of links to hosts-style (ip-blank-domain) blocklists.
* `SRC_DOMAIN`: A file containing lines of links to domain blocklists.
* `OUT_DOMAINS`: The output file containing the final list of blocked domains.
* `OUT_HOSTS`: The output hosts-style blocklist.
* `ADSERVER`: The host to redirect ads to. This can be used to approximately count the number of
blocked ads (see below).

To add a local blocklist, in either domain or hosts-style, use a `file://` URL.

## Blocking on the local host

To apply the generated blocklist on a single computer, copy it to `/etc/hosts`.
Take care that there may be pre-existing entries in there, which may be destroyed
by simply overwriting the existing file. A reasonable solution may be including the
existing entries via a `file://` URL.

## Blocking for the whole network

To block ads for an entire network, you will need to configure that networks resolver
to prefer entries from an external hosts file. Most resolvers will also read the local
`/etc/hosts` and prefer that to external responses, to copying the generated hosts-style
file to `/etc/hosts` on the resolver should work.

For `dnsmasq`, the following configuration option will read an additional hosts-style file
into the resolver:

```
addn-hosts=/path/to/ad.hosts
```

### Counting blocked ads

When redirecting the blocked ads to a host on the local network via the `ADSERVER` configuration
variable, setting up a web server such as `lighttpd` and having it count the hits will provide
an approximation of the number of ads blocked.

Note that most ad networks (sensibly) use HTTPS now, so the count may be off until you
provide HTTPS on the diversion server (which may be complicated by having to also serve a valid
certificate for the requested domains).
