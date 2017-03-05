Call for WPA3 - what's wrong with WPA2 security and how to fix it
===

Some time ago, I started learning how modern Wi-Fi networks are built. As a security enthusiast, I was particularly interested in how their safety mechanisms can be broken and strengthened. In this post, I'm going to list problems that can't easily be solved yet and what their solutions could be if we created a new wireless network security protocol. NOTE: this post is not meant for tech-savvy people only - in every paragraph I'm going to try to explain the problem in layman's terms.

What's WPA2?
===

WPA2 is the latest network protocol that is supposed to secure your network connection from (possibly malicious) interference. It describes how your Wi-Fi access point should authenticate you in order to guard who can and who cannot enter your network. It was announced in 2004 in order to fix previous version of the protocol which became so insecure that it was pretty much the equivalent of having no protection at all.

What's wrong with WPA2?
===

Anyone can disconnect you
---

Here's a fun fact: if I'm within the range of your network, I can kick you out of it. I don't need a password or any special equipment: pretty much any average laptop with a tiny program installed is going to let me send a so-called DEAUTH packet to your AP and say something like "hey, just kick this person out of the network". If you're under this attack, which is called <a href="https://en.wikipedia.org/wiki/Wi-Fi_deauthentication_attack">DEAUTH attack</a>, it's going to look to you as if your network connection was weak or broken. You will be able to connect back and the attacker can then send the annoying signal again and again as much as he wants. There are no safeguards as of today (at least none that I know of) and since one can do it without sending a lot of information into the network, it's not trivial to detect and block the source of the disruption. Another fun fact: some hotels will try to do this kind of attack on you if you're not using their wireless network, just to force you to pay.

**How could this be solved?** That's pretty simple: don't accept DEAUTH packets from strangers. As long as one can't prove that he's a member of the network, he shouldn't be able to disconnect from it. After all, no session is established yet, so the command wouldn't really make much sense.  This is also known as [802.11w](https://en.wikipedia.org/wiki/IEEE_802.11w-2009).

The password can be cracked offline
---

Apart from being able to disconnect you as many times as he wants, the attacker can also sniff your WiFi password if he manages to observe you connecting to the network. It's going to be encrypted though, but it shouldn't comfort you much; once the so-called WiFi handshake was sniffed, <a href="http://www.howtogeek.com/202441/your-wi-fi%E2%80%99s-wpa2-encryption-can-be-cracked-offline-here%E2%80%99s-how/">the attacker can just walk home and crack the password</a> within the access to your network. This means that he can easily try out millions of passwords without you knowing anything. At this rate, any simple password can be cracked in minutes and the more resistant ones - overnight. Think about it when choosing your WiFi password and read up on <a href="https://www.schneier.com/essays/archives/2014/02/choosing_a_secure_pa.html">how to pick one that takes ages to brute-force</a>.

**How could this be solved?** Apart from picking a good password (or <a href="http://world.std.com/~reinhold/diceware.html">a passphrase</a>), there are solutions that could help everyone. First of all, password checking should be made more computationally expensive. In other words, we should make it more difficult to check a password. On most networks, it wouldn't hurt if logging into the network for the first time took five more seconds and on a properly designed authentication standard, it would mean that the attacker would have to spend five seconds while trying out every single password. That means that instead of testing millions of passwords per second, testing million passwords would take <a href="https://www.wolframalpha.com/input/?i=five+million+seconds">almost two months</a>. With this amount of effort, this would be made both uneconomic to break passwords and future safe (at least for some time). This can be achieved using key derivation functions like <a href="https://en.wikipedia.org/wiki/PBKDF2">PBKDF2</a> or <a href="https://en.wikipedia.org/wiki/Scrypt">Scrypt</a>. Also, minimum password length requirements could be increased from 8 to 12 characters, this would make a thousand times more difficult to crack the simplest numeric password allowed.

Another thing that could be done to prevent the attacker from cracking the password is to make it impossible break them without being within the Access Point's range. Contemporary cryptography provides tools that could solve this problem. Another benefit would be that we could clearly see the attacker trying all the possible passwords, which could serve as a warning to all the users. By the way, introducing some way for the AP to tell you "the network is being under attack!" is another thing that could be useful when securing your network.

Once you know the password, you can sniff traffic and spoof anyone
---

That's not all yet! Once the attacker cracked your password, him using your network should be least worrying to you. Anyone that is connected to the network can effectively <a href="http://security.stackexchange.com/a/8593/15648">read everyone's traffic and change it as he pleases</a>. Luckily this excludes HTTPS websites (at least to some degree), but it's still a huge problem when anyone can impersonate you and <a href="https://moxie.org/software/sslstrip/">redirect your encrypted bank connection to an unencrypted one</a>.

**How could this be solved?** The problem exists because WPA2 has a fatal cryptographic flaw which allows the derivation of the master key which is shared across all connected peers. This could be decided because of performance reasons, but I'm pretty sure that our computers and APs speeded up quite a lot since 2004 and even back then sacrificing security for performance (without a simple way to opt out) was a pretty bad idea. This problem could be solved by using <a href="https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange">Diffie–Hellman key exchange (DH)</a>.

From the user's perspective, unless you really know what you're doing, I advise you not to browse any sensitive websites (especially banking) over wireless and check in your AP's manual how to separate your Wi-Fi network from the wired one.


It won't let you secure a passwordless network (and the alternative is terrible).
---

One more thing I'd expect from a secure Wi-Fi protocol is to make "open" Wi-Fi networks more secure. As of today, the only choice is between a semi-secure wireless network and one that isn't protected by a password. If you're for example running a cafe and would like to attract more customers with serving access to the internet for free, you either have to make users enter a long password (which would protect them from attackers that don't know the password) **or** set up a completely unprotected network so that customers don't need to enter the password. This isn't good choice.

**How could this be solved?** The new WPA security standard could support the "passwordless" mode that requires no authentication, but keeps an encrypted channel of communication so that the anonymous user can identify himself and make traffic only readable by the access point. In other words, we need something that works in a way similar to SSL/TLS, but connection-wide: require no authentication, but protect the user from having their traffic sniffed or spoofed in any way.

Silly "terms of service" in your cafe can break your applications and expose you to risk
---

Ever seen this "please accept terms of service" or a login screen when trying to open a website after connecting to a Wi-Fi network? Or even worse, did you ever get this "this connection is not trusted" screen, clicked "continue anyway" and see this kind of "welcome" website? Well, this is what terrible engineering looks like when there are no proper tools to solve a problem. Back in the time when Wi-Fi was invented, nobody thought that the network would have any need to contact you regarding terms of service or anything like this. Because of that, engineers decided that it's a good idea to capture the user's traffic and send him a note. Some of the Access Points will even try to perform the Man-In-The-Middle (MITM) attack to make you see this screen if you're trying to visit an encrypted website. From the psychological aspect of security, this is completely unacceptable: if you're making users used to the fact that an access point can modify the displayed website, he's losing one of the ways to tell whether he's under attack. The attacker can have an easier way to perform <a href="https://en.wikipedia.org/wiki/Social_engineering">social engineering</a> on a target, which obviously is bad.

**How could this be solved?** Introduce a way for APs to send messages to the user and let them know that without the acceptable of terms of service, he can't browse the internet. The same channel could be used to notify the user that the network is under attack. I expect that this will also be abused by people trying to smuggle advertisements over this channel, so the user should have a way to mute those notifications on a per-network basis. This channel could be used also to inform the Client if the network has internet access (telling the Client to keep cellular data active while on this network) and to inform if the network is a mobile hotspot with metered bandwidth limitations (telling the Client to treat this network as a cellular data connection).

What else?
---

I don't really know **all** of WPA2 features and I'm pretty sure that there are more things that are broken about WPA2 that could be fixed. If you know any, I invite you to open a pull request on Github and let me know about it.

OK, how could we fix things for everyone?
===

Option one: getting Wi-Fi alliance to fix their protocol
---

I believe that the first step should be to list the things that are broken about WPA2 and ask <a href="https://wi-fi.org">Wi-Fi alliance</a> to fix those. Here's what they told me when I contacted them:

> Thank you for contacting Wi-Fi Alliance.
> 
> In regards to your question about work on security developments, there is a member task group that is working to advance Wi-Fi security enhancements. You can find all the current work groups on the Wi-Fi Alliance website at http://www.wi-fi.org/who-we-are/current-work-areas.
> 
> In order to participate in discussion and work groups, companies must become a member of Wi-Fi Alliance.
> 
> Best Regards,
> Wi-Fi Alliance Staff
> ref:_00D407XSp._50033vj08w:ref

Option two: hack up unofficial WPA3 without the help of Wi-Fi alliance
---

Free Software community has a wide range of networking software that enables manipulation of Wi-Fi traffic. While some of it can be used for nefarious purposes, we could as well use it to sketch up a prototype of WPA3 and push for it to get adopted. If you're interested, I encourage you to contact the discussion boards for projects related to Wi-Fi manipulation and see if they're interested in this. Some of the projects that are related include: ScaPy, WPA supplicant, OpenWRT. There's definitely more of them so if you know them, let me know!

Final notes
===

I release this document under the WTFPL license and encourage everyone to edit this document and participate in the discussion about it. In order to change anything in this document, click <a href="https://github.com/d33tah/call-for-wpa3/edit/master/README.md">here</a>.

Signed,
Jacek "d33tah" Wielemborek
