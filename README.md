## mknsd makes nsd configuration files

Instead of configuring full zone files, I created a shorter syntax for my
personal use. This covers everything I need right now, but it doesn't support
everything that DNS can provide.


## configuration

It requires that you setup a `$CWD/servers.txt` file that looks something like this:

```
a server1 111.111.111.111   2a11:1111:1:11::111:11
b server2 222.222.222.222
c server3 112.112.112.112   2a11:1111:1111:1111::1
- server4 221.221.221.221
```

Each line represents a server. If the first argument is '-', the server won't
be considered a nameserver. If it's anything else, it will be considered a
nameserver and the string will be used to create the subdomain
`<arg0>.ns.<domain>`, used in NS and glue records. The second argument is a
custom name for the server. Nameserver names must be sshable (`ssh server2`).
The third one is the ipv4 address and the fourth is an optional ipv6 address.

You can then specify a zone file in the `$CWD/zones/` directory. For example:

```
zone lifeonmars.pt. hugopeixoto.net.

alias @   server1
alias www server1

txt @ "v=spf1 mx -all"
```

`alias` records will be replaced with `A` and `AAAA` records with the ip
addresses specified in `servers.txt`.


## assumptions / how it works

The script creates a bunch of `nsd` configuration and zone files and uses
`rsync` to uplaod them to the nameservers defined in `servers.txt`. It then
restarts the `nsd` service.

There are a bunch of hardcoded TTLs and other numbers that I usually don't care
about.
