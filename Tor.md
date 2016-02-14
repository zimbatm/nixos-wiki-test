---
title: Tor
permalink: /Tor/
---

How Tor is set up by default in NixOS
=====================================

By default Tor in NixOS provides 2 different SOCKS proxies.

9050 is a "slow" SOCKS port which can be used for email, git and pretty much any other protocol but HTTP(S) since a new circuit will be created for each destination IP. This is a safe default which complicates identity corellation attacks, although isn't sufficient to completely thwart them.

9063 is the "fast" SOCKS port suitable for a browser. A new circuit is established every 10 minutes.

torsocks, torsocks-faster vs tsocks difference
----------------------------------------------

Both torsocks and tsocks wrappers route traffic via Tor.

However, neither of the wrappers reliably prevent an application from establishing connections outside of Tor network, but merely ensure that non-malicious code has its direct connection attempts routed via Tor. Some applications, such as those using KDE KIO framework, don't make direct connections and instead use kdeinit4 to spawn worker processes, rendering the wrappers useless.

For more security, you need to run an application inside a virtual machine and configure its networking to be routed via Tor only.

torsocks is a bit more secure because it blackholes UDP traffic and private IP traffic, such as LAN traffic.

You should use torsocks where possible. Use torsocks-faster/fast port/Privoxy for HTTP(due to performance) and protocols which break if used from several IPs. Examples: ICQ login requires contacting different servers and checks that it is done from the same IP; FTP transfers are performed via separate connection which must originate from the same IP.

tsocks is the weakest wrapper which is nevertheless necessary if your application needs to make local connections or makes DNS queries in a way not handled by torsocks. Example: Kopete's XMPP plugin only works with tsocks and obviously leaks DNS queries.

tsocks as a "guard" wrapper
---------------------------

Some applications have native support for HTTP/SOCKS proxies, and it is tempting to use it. However, it isn't unheard of for proxy support to have bugs or for application plugins to ignore proxy settings or settings getting lost. Using torsocks wrapper can be more reliable.

An alternative approach is use both tsocks and built-in proxy support. This way, if the application's proxy support fails, the connection is likely to be caught by tsocks and if you run the application without tsocks by mistake, the connections are still likely to be proxied.

Privoxy
-------

By default, privoxy is enabled if you enable Tor client functionality and configured to route to the fast SOCKS port.

KDE and Tor
-----------

In KDE, proxy server configuration is set for all applications at once. You should set the SOCKS proxy to Tor's default 9050 port, and set HTTP proxy to Privoxy(port 8118). Without Privoxy, KDE apps using either KHTML or WebKit KPart such as Konqueror, Rekonq, KTorrent, Akregator would become nearly unusable and cause excessive load to the Tor network.

Another possibility is to run "tsocks kdeinit4", which would cause kdeinit4 to respawn in a wrapped state. All KDE applications started after this, will be wrapped with tsocks.

### Kopete

Kopete makes direct connections and ignores KDE settings. Kopete torification dependins on what plugins you use. XMPP requires tsocks. ICQ requires torsocks-faster.

### KDE PIM

KMail respects KDE-wide proxy settings, and the "safe" SOCKS port offers good isolation between your mailboxes.

How to see what's going on, and work around bans
------------------------------------------------

Vidalia is convenient for troubleshooting and changing circuits should your current exit node be banned by a particular site. Vidalia needs an active control port:

      services.tor.controlPort = 9051;

Vidalia allows you to change Tor configuration on the fly, which you should avoid doing and instead use NixOS config to control your Tor instance. The only useful features are "new Identity" and "network map".

If you do change something else by accident, you will have to manually edit Vidalia's configuration file ~/.vidalia/vidalia.conf and replace all "Changed = true" with "Changed = false", otherwise Vidalia and NixOS will fight for the control of the Tor service process.