+++
date = "2015-09-27"
title = "Protocol Demo"
category = "internals"
[menu.main]
  name = "Protocol Demo"
  weight = 2
  identifier = "internals-nats-protocol-demo"
  parent = "internals"
+++

# NATS Protocol Demo

The virtues of the NATS protocol manifest when you experience how easy it is to use NATS. Because the NATS protocol is text-based, you can use NATS across virtually any platform or language. In the following demo we use [Telnet](https://en.wikipedia.org/wiki/Telnet).

On the wire you can publish and subscribe using a simple [set of protocol commands](/documentation/internals/nats-protocol/).

## Instructions

**1. Open a terminal session.**

You'll use this terminal as the subscriber.

**2. Connect to NATS.**

```
telnet demo.nats.io 4222
```

Expected result:

```
$ telnet demo.nats.io 4222
Trying 107.170.221.32...
Connected to demo.nats.io.
Escape character is '^]'.
INFO {"server_id":"ad29ea9cbb16f2865c177bbd4db446ca","version":"0.6.8","go":"go1.5.1","host":"0.0.0.0","port":4222,"auth_required":false,"ssl_required":false,"max_payload":1048576}
```

**3. Run the subscriber.**

On the wildcard subject `foo.*` with message size limit of `90` bytes.

```
sub foo.* 90
```

Subscriber result: `+OK` indicating successful interest registration.

```
sub foo.* 90
+OK
```

**4. Open a second terminal.**

You'll use terminal this is the publisher.

**5. Connect to NATS.**

```
telnet demo.nats.io 4222
```

Expected result:

```
$ telnet demo.nats.io 4222
Trying 107.170.221.32...
Connected to demo.nats.io.
Escape character is '^]'.
INFO {"server_id":"ad29ea9cbb16f2865c177bbd4db446ca","version":"0.6.8","go":"go1.5.1","host":"0.0.0.0","port":4222,"auth_required":false,"ssl_required":false,"max_payload":1048576}
```

**6. Publish a message.**

The message includes the command (`pub`), subject (`foo.bar`), and length of the payload (`5`). Press enter and provide the payload (`hello`).

```
pub foo.bar 5
hello
```

Publisher result: `+OK` indicating message publication.

```
pub foo.bar 5
hello
+OK
```

Subscriber result: `MSG` + subject name + max payload size + actual payload size + message payload `hello`.

```
sub foo.* 90
+OK
MSG foo.bar 90 5
hello
```

**7. Publish another message with reply box.**

```
pub foo.bar optional-reply-box 5
hello
+OK
```

Subscriber result: `MSG` indicating message receipt.

```
MSG foo.bar 90 optional-reply-box 5
hello
```

**8. Unsubscribe from interest in the subject.**

You can use the `UNSUB` command to unsubscribe from a message.

Run the subscriber to unsubscribe:

```
unsub foo.* 90
```

Subscriber result: `+OK` indicating successful disinterest.

```
unsub foo.* 90
+OK
```

**9. Reconnect to server and subscribe.**

```
telnet demo.nats.io 4222
```

```
sub foo.* 90
```

**10. Explore the ping/pong interval.**

You may have noticed that your clients receive `ping` requests from the server. If your client is not active, or does not respond to the server pings within the ping/pong interval, the server disconnects the client. The err message is `-ERR 'Stale Connection'`.

You can send a `ping` request to the serve and receive a `PONG` reply. For example:

```
$ telnet demo.nats.io 4222
Trying 107.170.221.32...
Connected to demo.nats.io.
Escape character is '^]'.
INFO {"server_id":"ad29ea9cbb16f2865c177bbd4db446ca","version":"0.6.8","go":"go1.5.1","host":"0.0.0.0","port":4222,"auth_required":false,"ssl_required":false,"max_payload":1048576}

ping
PONG
```
