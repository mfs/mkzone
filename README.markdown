## mkzone

mkzone generates bind zone files from yaml files.

The easiest way to learn how to use it is to look at the example yaml files.
There are three in total covering a domain "example.com" and both IPv4 and IPv6
reverse zone files.

### Forward zones

These are fairly straight forward:

    origin: example.com.
    ttl: 1h

    soa:
        ns: ns.example.com.
        user: root.example.com.

    records:
        - type: NS
          value: ns.example.com.

        - type: MX
          priority: 10
          value: mx.example.com.

        - type: A
          host: ns
          value: 203.0.113.1

        - type: AAAA
          host: ns
          value: 2001:db8::1

        - type: A
          host: mx
          value: 203.0.113.2

        - type: AAAA
          host: mx
          value: 2001:db8::2

        - type: A
          host: '@'
          value: 203.0.113.3

        - type: AAAA
          host: '@'
          value: 2001:db8::3

        - type: CNAME
          host: www
          value: example.com.

This example shows all the current record types supported at the moment. Once
catch is that @ symbols need to be quoted. Could possibly avoid this somehow.

### Reverse zones

Reverse zones are indicated by `type: reverse`:

    origin: 203.0.113.0/24
    ttl: 1h
    type: reverse

    soa:
        ns: ns.example.com.
        user: root.example.com.

    records:
        - type: NS
          value: ns.example.com.

        - type: PTR
          host: ns.example.com.
          value: 203.0.113.1

        - type: PTR
          host: mx.example.com.
          value: 203.0.113.2

        - type: PTR
          host: example.com.
          value: 203.0.113.3

Reverse zones are processed a little differently. The origin needs to be in CIDR
format. That is include the netmask. mkzone uses this to calculate the correct
origin and host entries. Run `./mkzone examples/reverse.ip4.yml` and you will
get the idea.

### Requirements

mkzone is written in Python 3. It also uses the `ipaddress` module which is only
included in Python 3.3.2+ and only on a provisional basis. If your distro
doesn't package Python 3.3.2+ try [pyenv](https://github.com/yyuu/pyenv) or
something similar.
