---
published: true
layout: post
title: 'Allow remote connections to postgres database'
date: 2016-10-21
categories: postgres
---
### Configure hba file

It's easy enough. Just edit your pg_hba.conf file following way (replace PGSQL_VERSION with yours):

	sudo nano /etc/postgresql/PGSQL_VERSION/main/pg_hba.conf

Now add ip-adress you want to get access to:

	host    all     all     X.X.X.X/32         md5

N.B.: To allow connection from all ip addresses change the line to:

	host    all     all     X.X.X.X/32         md5

Be aware of [CIDR notation](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) if you want to provide a range of ip adresses.

After that you need to enable remote connections via postgresql.conf file:

	sudo nano /etc/postgresql/PGSQL_VERSION/main/postgresql.conf


Just replace "localhost" with "*" in this line:

	listen_addresses = '*'

_That's all!_ By the way, if you troubled with firewall settings, you would add iptables rule to allow connections on 5432 port.
