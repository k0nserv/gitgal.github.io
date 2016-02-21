---
layout: post
title: "A look on protocols"
date: 2016-02-21 09:52:45 +0100
categories: software protocols
---

HEADER
---

Today I am going to have a look on protocols and some Erlang.

I am writing this post while doing the research / implementation so that the reader(you now and me later) can follow my chain of thougth as I think that is really intressting.

Lets go!

[ARP](http://www.faqs.org/rfcs/rfc826.html)
---

Appearently ARP is a method of converting protocol addresses (IP adresses) to local network adresses (Ethernet address).

Here are the definitions:

> Define the following for referring to the values put in the TYPE field of the Ethernet packet header:
> ether_type$XEROX_PUP,<br />
> ether_type$DOD_INTERNET,<br />
> ether_type$CHAOS, <br />
> and a new one:<br />
> ether_type$ADDRESS_RESOLUTION.  <br />
> Also define the following values (to be discussed later):<br />
> ares_op$REQUEST (= 1, high byte transmitted first) and<br />
> ares_op$REPLY   (= 2), <br />
> and<br />
> ares_hrd$Ethernet (= 1).<br />

So ether_type seems to refrence what ethernet protocol is being used, ares_op defines if the incomming packet was a request or a reply and ares_hrd is what hardware it is being used.

Ok I think I got that.

Now the next part is the packet format here there are some question mark's if you ask me.

>  Ethernet transmission layer (not necessarily accessible to the user):<br />
>  48.bit: Ethernet address of destination<br />
>  48.bit: Ethernet address of sender<br />
>  16.bit: Protocol type = ether_type$ADDRESS_RESOLUTION<br />
>  Ethernet packet data:<br />
>  16.bit: (ar$hrd) Hardware address space (e.g., Ethernet, Packet Radio Net.)<br />
>  16.bit: (ar$pro) Protocol address space.  For Ethernet hardware, this is from the set of type fields ether_typ$protocol.<br />
>  8.bit: (ar$hln) byte length of each hardware address<br />
>  8.bit: (ar$pln) byte length of each protocol address<br />
>  16.bit: (ar$op)  opcode (ares_op$REQUEST | ares_op$REPLY)<br />
>  nbytes: (ar$sha) Hardware address of sender of this packet, n from the ar$hln field.<br />
>  mbytes: (ar$spa) Protocol address of sender of this packet, m from the ar$pln field.<br />
>  nbytes: (ar$tha) Hardware address of target of this packet (if known).<br />
>  mbytes: (ar$tpa) Protocol address of target.<br />

Like what is the relationship between 16.bit ar$hrd and ares_hrd if there is any except that they are both reprecenting hardware in some form.<br />
Or perhaps they have 16.bit to be able to support more types of hardware then is defined.

> The Address Resolution module then sets the ar$hrd field to ares_hrd$Ethernet

Ok, think that answered my question.

After reading the example at the end and getting a aha moment when realising that what you use ARP for is to broadcast $REQUEST and then see if you get a $REPLY to where the computer is located.

Great! Done with that for know.

[IP](http://www.ietf.org/rfc/rfc791.txt)
---

Seems to provide abstraction between networks(ARP).

First thougth reading about fragmentation is "this is complex as fuck" the general concept is easy just break the package in N smaller parts copy the header over those parts and send the parts.

> The first fragment will have the fragment offset
> zero, and the last fragment will have the more-fragments flag reset
> to zero.

If you say so...<br />
Why is there not a definition like NFB = 64 bit I have to scan the massive text blob to find that.

Whatever moving on to Gateways.

Ok working as a channel between local net's got it.

### FINALLY SPECIFICATIONS!!!

<pre>
  0                   1                   2                   3   
  0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |Version|  IHL  |Type of Service|          Total Length         |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |         Identification        |Flags|      Fragment Offset    |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |  Time to Live |    Protocol   |         Header Checksum       |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |                       Source Address                          |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |                    Destination Address                        |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |                    Options                    |    Padding    |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
</pre>

Ok 32 bit in each part.

IHL = Internet Header Length.

Type of service breakdown.<br />
0 - 2: Precenence.<br />
3: Delay. (0: Normal | 1: high)<br />
4: Throughout. (0: Normal | 1: high)<br />
5: Relibility. (0: Normal | 1: high)<br />
6-7: Not used.<br />

> Precedence<br />
> <br />
>   111 - Network Control<br />
>   110 - Internetwork Control<br />
>   101 - CRITIC/ECP<br />
>   100 - Flash Override<br />
>   011 - Flash<br />
>   010 - Immediate<br />
>   001 - Priority<br />
>   000 - Routine<br />

Flags decides if the packet can be fragmented and if it is last fragment.

At this point my head is full and I have to come back to this topic some other day.

Going to have some fun with UDP sockets instead.
