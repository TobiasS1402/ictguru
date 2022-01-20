---
id: 26
title: Cisco Cheatsheet
date: 2020-03-04T23:06:55+01:00
layout: page
---
Configuratie Modus:

<pre class="wp-block-code"><code class="">ENABLE
CONFIGURE TERMINAL</code></pre>

Configuratie opslaan:

<pre class="wp-block-code"><code class="">WR MEM</code></pre>

Hostname:

<pre class="wp-block-code"><code class="">HOSTNAME (NAME)</code></pre>

Message of today instellen:

<pre class="wp-block-code"><code class="">BANNER MOTD #Banner#</code></pre>

Interface configureren:

<pre class="wp-block-code"><code class="">INTERFACE (INTERFACE NAME)
IP ADDRESS (IP ADDRESS) (SUBNET MASK)
DESCRIPTION (VOORBEELD OMSCHRIJVING)
NO SHUT
END</code></pre>

DHCP configureren:

<pre class="wp-block-code"><code class="">IP DHCP POOL (NAME)
NETWORK (IP ADDRESS) (SUBNET MASK)
DEFAULT-ROUTER (ROUTER INTERFACE IP)
END</code></pre>

Inlog timeout:

<pre class="wp-block-code"><code class="">LOGIN BLOCK-FOR (TIMEOUT IN SEC.) ATTEMPTS (AANTAL POGINGEN) WITHIN (AANTAL SECONDEN)</code></pre>

Router ENCRYPTED password:

<pre class="wp-block-code"><code class="">SERVICE PASSWORD-ENCRYPTION
ENABLE SECRET (PASSWORD)</code></pre>

Console password:

<pre class="wp-block-code"><code class="">LINE CON 0
EXEC-TIMEOUT (MIN.) (SEC.)
PASSWORD (WACHTWOORD)
LOGIN
END</code></pre>

Auxiliary password:

<pre class="wp-block-code"><code class="">LINE AUX 0
EXEC-TIMEOUT (MIN.) (SEC.)
PASSWORD (WACHTWOORD)
LOGIN
END</code></pre>

SSH / Telnet password:

<pre class="wp-block-code"><code class="">LINE VTY 0 4
EXEC-TIMEOUT (MIN.) (SEC.)
PASSWORD (WACHTWOORD)
LOGIN
LOGGING SYNCHRONOUS
END</code></pre>

Static routing:

<pre class="wp-block-code"><code class="">IP ROUTING
IP ROUTE (DEST IP) (SUBNET MASK) (NEXT HOP)
EXIT</code></pre>

Routing Information Protocol:

<pre class="wp-block-code"><code class="">ROUTER RIP
VERSION (VERSION NUMBER)
NETWORK (CLIENT NETWORK)
NETWORK (ROUTER NETWORK)
END</code></pre>

OSPF routing:

<pre class="wp-block-code"><code class="">ROUTER OSPF (ID)
NETWORK (ROUTER NETWORK) (WILDCARD MASK) AREA 0
NETWORK (CLIENT NETWORK) (WILDCARD MASK) AREA 0</code></pre>
