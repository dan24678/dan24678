---
title: I Finally Got iPhone to Ubuntu VPN Working with OpenSwan
date: 2011-04-22T11:18:58+00:00
---
UPDATED 12/31/11: Added &#8220;dpd&#8221; items to ipsec.conf and lines to ip-down to enable the iPhone to quickly reconnect without needing to restart ipsec manually.

I am now able to network my iPhone to my Ubuntu desktop with VPN so I can play mp3s off my home NAS drive.

Here is a brief description of what I loaded in my config files to get this working.  I&#8217;m posting it online in the hope that it will be useful to some folks.

Here is a breakdown of my hardware:  Lucid Lynx Ubuntu desktop (internal IP is 192.168.1.10), iPhone 3GS, iomega NAS drive (internal IP is 192.168.1.80), Linksys E3000 router.

Networking stuff: I disabled the firewall on my ubuntu box while I was trying to get this working, then came back and loaded the firewall rules later.  I also ensured that my router is port forwarding TCP/UDP ports 500, 1701 and 4500 to my ubuntu box and VPN Passthrough is enabled.

First up, I installed Openswan 2.6.33 per the instructions in [this thread](http://ubuntuforums.org/archive/index.php/t-1678945.html).  Apparently, to get openswan and an iPhone to play nice, you need to use a custom compiled version.  Here are my config files (with my password and remote IP addresses modified, of course):

## /etc/ipsec.conf

<pre>config setup
 nat_traversal=yes
 #charonstart=yes
 #plutostart=yes

conn PSK
 authby=secret
 forceencaps=yes
 pfs=no
 auto=add
 keyingtries=3
 dpdtimeout=60
 dpdaction=clear
 rekey=no
 left=192.168.1.10
 leftnexthop=%defaultroute
 leftprotoport=17/1701
 right=%any
 rightprotoport=17/%any
 rightsubnet=vhost:%priv,%no
 dpddelay=10
 dpdtimeout=10
 dpdaction=clear

include /etc/ipsec.d/l2tp-psk.conf
</pre>

## /etc/ipsec.d/l2tp-psk.conf

<pre>conn L2TP-PSK-NAT
 rightsubnet=vhost:%priv
 also=L2TP-PSK-noNAT

conn L2TP-PSK-noNAT
 #
 # PreSharedSecret needs to be specified in /etc/ipsec.secrets as
 # YourIPAddress     %any: "sharedsecret"
 authby=secret
 pfs=no
 auto=add
 keyingtries=3
 # we cannot rekey for %any, let client rekey
 rekey=no
 # Set ikelifetime and keylife to same defaults windows has
 ikelifetime=8h
 keylife=1h
 # l2tp-over-ipsec is transport mode
 type=transport
 #
 left=192.168.1.10
 #
 # For updated Windows 2000/XP clients,
 # to support old clients as well, use leftprotoport=17/%any
 leftprotoport=17/1701
 #
 # The remote user.
 #
 right=%any
 # Using the magic port of "0" means "any one single port". This is
 # a work around required for Apple OSX clients that use a randomly
 # high port, but propose "0" instead of their port.
 rightprotoport=17/%any
 dpddelay=10
 dpdtimeout=10
 dpdaction=clear

conn passthrough-for-non-l2tp
 type=passthrough
 left=192.168.1.10
 leftnexthop=192.168.1.1
 right=0.0.0.0
 rightsubnet=0.0.0.0/0
 auto=route
</pre>

I am able to restart OpenSwan via **/etc/init.d/ipsec restart**.  Next up are my configs for xl2tpd which I start with **xl2tpd -c /etc/xl2tpd/xl2tpd.conf -s /etc/xl2tpd/l2tp-secrets -D**

## /etc/xl2tpd/xl2tpd.conf

<pre>[global]
debug network = yes
debug tunnel = yes
ipsec saref = no
listen-addr = 192.168.1.10

[lns default]
ip range = 10.42.42.17-10.42.42.31
local ip = 10.42.42.1
require chap = yes
refuse chap = no
refuse pap = no
require authentication = no
ppp debug = yes
pppoptfile = /etc/ppp/options.xl2tpd
length bit = yes</pre>

## /etc/ppp/options.xl2tpd

<pre>ipcp-accept-local
ipcp-accept-remote
#ms-dns 192.168.1.10
noccp
auth
crtscts
idle 1800
mtu 1410
mru 1410
defaultroute
debug
lock
proxyarp
connect-delay 5000
ipcp-accept-local




<h2>
  /etc/ppp/ip-down
</h2>
ADD THE FOLLOWING 3 LINES TO THE BOTTOM OF THE FILE:


<pre>dpddelay=10
dpdtimeout=10
dpdaction=clear</pre>


<p>
  And finally the config on my iPhone:
</p>


<p>
  I&#8217;m using the L2TP tab. I have my remote IP listed as the server; Account: dan; RSA SecurID: Off; password and secret both entered as my password; Send All Traffic: On; Proxy: Off.
</p>


<p>
  That&#8217;s about it.  Here&#8217;s some other links that proved useful while I was hashing my way through this:
</p>


<ul>
  <li>
    <a href="http://nielspeen.com/blog/2009/04/linux-l2tpipsec-with-iphone-and-mac-osx-clients/">http://nielspeen.com/blog/2009/04/linux-l2tpipsec-with-iphone-and-mac-osx-clients/</a>
  </li>
  
  
  <li>
    <a href="https://lists.openswan.org/pipermail/users/">OpenSwan Users Forum</a> for posting questions/help requests
  </li>
  
</ul>