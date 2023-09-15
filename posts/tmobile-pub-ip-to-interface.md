---
title: Assign T-mobile public IP to own server (FreeBSD)
date: 2023-08-31T21:16:34+0200
draft: false
tags: [vlan, FreeBSD, networking]
---

With the recent (forced) transition from ADSL to fibre optic broadband internet, an interesting oppertunity arose. It enabled assigning the public ip address from the provider easily to a network interface of my private home server. The fibre optic cable entering our house is fed to a media converer which has a UTP connection. The default set-up from the installer attaches the media converter directly to the supplied T-Mobile router. For home networking enthousiasts it is also possible to connect the media converter directly to your home server. But why is this cool?! Here are two reasons:

* Avoids the need to encapsulate IPsec traffic within UDP packets.
* You can finally make use of an IPv6 tunnel broker. (Shame on you T-mobile/Odido for not supporting IPv6 yet).

This brief guide will show you how this is done in FreeBSD. Without further ado, this is how it is done:

```
vlans_em2="tmob"
create_args_tmob="vlan 300"
ifconfig_em2="up"
ifconfig_tmob="SYNCDHCP"
```

For those wo did not realise it yet, these settings should be added to `/etc/rc.conf`.

The media converter is directly attached to the `em2` network interface using a UTP cable. T-mobile uses a VLAN tag of 300 for their network. The `SYNCDHCP` option force the startup script to wait for the `dhclient` program to finish. A server with just one network interface would not be very useful in this situation, so I've added a second network interface to my server which will be connected to a switch and is for local traffic. In this case the server must take care of NAT, which whas previously done by the T-Mobile router. The relevant lines from the `pf.conf` file are:

```
ext_if="tmob"
nat on $ext_if from 192.168.0.0/24 to any -> ($ext_if)
```
