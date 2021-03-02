---
title: "Unbound as DNS Sinkhole"
date: 2019-02-26T09:30:21+00:00
draft: false
tags: ["unbound","DNS","sinkhole","FreeBSD"]
---

Inspired by the popular [pi-hole](https://pi-hole.net) project which sends advertisements and tracking scripts into oblivion, I decided to roll my own solution as an educational exercise. First place to look was at a DNS server, `dnsmasq` is a common choice for this kind of task, but I noticed that FreeBSD is shiped with the [Unbound](https://nlnetlabs.nl/projects/unbound/about/) DNS server installed by default. As I like to keep my system as clean as possible, I decided to use this one. Below is the config file used for the unbound DNS server, the important lines are the `local-zone:` lines. This tells the server to return `NXDOMAIN` which stands for non-existent domain. Also frequently seen on the internet is to return `127.0.0.1` instead of `NXDOMAIN`, the downside of this solution is that the client will make an additional request to `127.0.0.1` to find the data it was looking for. Using the `local-zone:` approach also  any subdomains are automatically blocked. Any non-blocked DNS queries are forwarded to upstream DNS servers in the `forward-zone`. 

```
# /etc/unbound/unbound.conf
server:
	# Listen on all interfaces
	interface: 0.0.0.0
	access-control: 192.168.0.1/24 allow
	statistics-cumulative: yes
	# Consumes more time, but will enable us to see the number of NXDOMAIN returns
	extended-statistics: yes
	# For local machines
	local-data: "pydev IN A 192.168.0.7"
	
	# These domains including subdomain are blocked
	local-zone: "criteo.com" always_nxdomain
	local-zone: "doubleclick.net" always_nxdomain # Google ad-network
	local-zone: "google-analytics.com" always_nxdomain
	
	forward-zone:
	name: "."
	forward-addr: 9.9.9.9 # quad9.net
	forward-addr: 8.8.8.8 # Google
	
	remote-control:
	control-enable: yes
	control-use-cert: no
```

## Stats
I love numbers and I was happy to see that it was easy to get all kinds of statistics from the Unbound DNS server using the command ` local-unbound-control stats`. In the unbound config file the extended stats were enabled, this allows us to see the number of times the server returned NXDOMAIN. An excerpt of some of the stats returned is given below.

```
num.query.opcode.QUERY=76998
total.num.cachehits=31708
total.num.cachemiss=45290
num.answer.rcode.NXDOMAIN=11699
```

Using a simple cron job these number are send to a database and visualised in Grafana, my favourite dashboard.
