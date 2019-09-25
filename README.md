## Prioritizing traffic with prio

[PF](https://www.freebsd.org/cgi/man.cgi?query=pf&apropos=0&sektion=0&manpath=FreeBSD+12.0-RELEASE+and+Ports&arch=default&format=html) processes packets on a first-come-first-serve basis. This may not be optimal for high traffic applications. It may be in our best interest to filter certain packets before others. This we can do with the `set prio` option, which prioritizes packets according to a number range of 0 thru 7. Zero is the slowest, and seven is the fastest. The `set prio` option is enabled by default with a priority of 3. When we use it we are effectively overriding the default value.

Increase the priority of inbound SSH traffic:
```
pass in proto tcp to port { 22 } set prio 4
```

Give the maximum priority to web traffic:
```
pass proto tcp to port { 80 443 } set prio 7
```

Lower the priority for *name* and *time* services:
```
pass out proto tcp to port { 53 123 } set prio 2
pass out proto udp to port { 53 123 } set prio 2
```

Prioritize web traffic to 6, and *ACK* and *lowdelay TOS* packets to 7:
```
pass proto tcp to port { 80 443 } set prio (6,7)
```

The last example demonstrates the *tuple option*. The first value prioritizes regular packets, and the second value prioritizes *ACK* and/or *lowdelay TOS* packets. By default, ACK and lowdelay packets have to wait in line with everyone else, which can potentially bottleneck the filtering process. The tuple option can move these packets to the front of the line where they can be processed faster.



