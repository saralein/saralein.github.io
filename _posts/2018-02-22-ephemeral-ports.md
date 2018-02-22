---
layout: post
title:  "Ephemeral Ports"
date:   2018-02-22 10:00:00 -0600
categories: jekyll update
---

When I started my Java server, I read a lot of information about ports, protocols, etc. If you read about ports in particular, you quickly learn that there are 65,535 ports available for connections in TCP/IP.  After some additional reading, you might also find out that the first 1024 ports of these 65,535 are reserved for different services and protocols, leaving 64,511 available ports. If you are like me, you accept this knowledge and go about your business.

However, after a while, this assumption that you have 64,511 available ports might lead you into some trouble. My particular situation involved benchmarking my server with 20,000 requests.  Running `ab` with this number of requests, I consistently ran into issues somewhere about 16,000 requests before my test timed out.

<img src="/images/ab-test-failure.png" style="width: 75%; margin: auto; display: block;">

Each failure was in a small range around this 16,379 number. After some Googling, I stumbled upon ephemeral ports.

### What are ephemeral ports?

> e·phem·er·al [adjective] - lasting for a very short time

Ephemeral ports are short-lived ports for dynamic communication in TCP/IP.  If a client does not specify a particular port for use, it is considered dynamic communication. In such cases, a temporary port (our ephemeral port) is assigned from a designated range of port numbers by the machine's IP stack.

The catch with ephemeral ports is that this designated range is much lower than the 64,511 you may have imagined.  While the exact number varies depending on operating system, the range recommended by the [Internet Assigned Numbers Authority](https://www.iana.org/) is 49,152 to 65,535. OS X and newer Windows operating systems use this range as their default. Linux uses a range twice the size: 32,768 to 65,535.

So, OS X has 16,383 ephemeral ports.  Right in the range where my test fails above.

### Connection State

As they are used, ephemeral ports go through a number of stages: `SYN_SENT`, the initial state after a client connects to a server socket; `ESTABLISHED`, the state of a fully established connection; and so on until it finally enters `TIME_WAIT`. In normal circumstances, when a port is no longer needed, both sides of a connection are closed and each side must wait for the `TIME_WAIT` period to expire and clear up resources. This smaller range of available ports in combination with this TIME_WAIT means a possibility of running out of available ports, or port exhaustion.

### Range on OS X

If you are on OS X, you can see the ephemeral port range for yourself using the following command in your terminal:

`sysctl net.inet.ip.portrange.first net.inet.ip.portrange.last`

You should get the following default:

```
net.inet.ip.portrange.first: 49152
net.inet.ip.portrange.last: 65535
```

You'll find that the OS has 16,383 ephemeral ports available. If desired, you can increase the number of ephemeral ports using the following:

`sudo sysctl -w net.inet.ip.portrange.first=NUMBER`,

where `NUMBER` is a number below 49152.  For example, if you wanted the same range as on Linux, you'd use:

`sudo sysctl -w net.inet.ip.portrange.first=32768`
