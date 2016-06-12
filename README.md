Some time ago, I started learning how modern Wi-Fi networks are built. As a security enthusiast, I was particularly interested in how their safety mechanisms can be broken and strengthened. In this post, I'm going to list problems that can't easily be solved yet and what their solutions could be if we created a new wireless network security protocol. NOTE: this post is not meant for tech-savvy people only - in every paragraph I'm going to try to explain the problem in layman's terms.

What's WPA2?
===

WPA2 is the latest network protocol that is supposed to secure your network connection from (possibly malicious) interference. It describes how your Wi-Fi access point should authenticate you in order to guard who can and who cannot enter your network. It was announced in 2004 in order to fix previous version of the protocol which became so insecure that it was pretty much the equivalent of having no protection at all.

What's wrong with WPA2?
===

Anyone can disconnect you
---

Here's a fun fact: if I'm within the range of your network, I can kick you out of it. I don't need a password or any special equipment: pretty much any average laptop with a tiny program installed is going to let me send a so-called DEAUTH packet to your AP and say something like "hey, just kick this person out of the network". If you're under this attack, which is called <a href="https://en.wikipedia.org/wiki/Wi-Fi_deauthentication_attack">DEAUTH attack</a>, it's going to look for you as if your network connection was weak or broken. You will be able to connect back and the attacker can then send the annoying signal again and again as much as he wants. There are no safeguards as of today (at least none that I know of) and since one can do it without sending a lot of information into the network, it's not trivial to detect and block the source of the disruption. Another fun fact: some hotels will try to do this kind of attack on you if you're not using their wireless network, just to force you to pay.

**How this could be solved?** That's pretty simple: don't accept DEAUTH packets from strangers. As long as one can't prove that he's a member of the network, he shouldn't be able to disconnect from it. After all, no session is established yet, so the command wouldn't really make much sense.

The password can be cracked offline
---

Apart from being able to disconnect you as many times as he wants, the attacker can also sniff your WiFi password if he manages to observe you connecting to the network. It's going to be encrypted though, but it shouldn't comfort you much; once the so-called WiFi handshake was sniffed, <a href="http://www.howtogeek.com/202441/your-wi-fi%E2%80%99s-wpa2-encryption-can-be-cracked-offline-here%E2%80%99s-how/">the attacker can just walk home and crack the password</a> within the access to your network. This means that he can easily try out millions of passwords without you knowing anything. At this rate, any simple password can be cracked in minutes and the more resistant ones - overnight. Think about it when choosing your WiFi password and read up on <a href="https://www.schneier.com/essays/archives/2014/02/choosing_a_secure_pa.html">how to pick one that takes ages to brute-force</a>.

**How this could be solved?** Apart from picking a good password (or <a href="http://world.std.com/~reinhold/diceware.html">a passphrase</a>), there are solutions that could help everyone. First of all, password checking should be made more computationally expensive. In other words, we should make it more difficult to check a password. On most networks, it wouldn't hurt if logging into the network for the first time took five more seconds and on a properly designed authentication standard, it would mean that the attacker would have to spend five seconds while trying out every single password. That means that instead of testing millions of passwords per second, testing million passwords would take <a href="https://www.wolframalpha.com/input/?i=five+million+seconds">almost two months</a>. With this amount of effort, this would be made both uneconomic to break passwords and future safe (at least for some time).

Another thing that could be done to prevent the attacker from cracking the password is to make it impossible break them without being within the Access Point's range. Contemporary cryptography provides tools that could solve this problem. Another benefit would be that we could clearly see the attacker trying all the possible passwords, which could serve as a warning to all the users. By the way, introducing some way for the AP to tell you "the network is being under attack!" is another thing that could be useful when securing your network.

Once you know the password, you can sniff traffic and spoof anyone
---

That's not all yet! Once the attacker cracked your password, him using your network should be least worrying to you. Anyone that is connected to the network can effectively <a href="http://security.stackexchange.com/a/8593/15648">read everyone's traffic and change it as he pleases</a>. Luckily this excludes HTTPS websites (at least to some degree), but it's still a huge problem when anyone can impersonate you and <a href="https://moxie.org/software/sslstrip/">redirect your encrypted bank connection to an unencrypted one</a>.

**How this could be solved?** The problem exists because WPA2 has a fatal cryptographic flaw which allows the derivation of the master key which is shared across all connected peers. This could be decided because of performance reasons, but I'm pretty sure that our computers and APs speeded up quite a lot since 2004 and even back then sacrificing security for performance (without a simple way to opt out) was a pretty bad idea.

From the user's perspective, unless you really know what you're doing, I advise you not to browse any sensitive websites (especially banking) over wireless and check in your AP's manual how to separate your Wi-Fi network from the wired one.

It won't let you secure a passwordless network (and the alternative is terrible).
---

Silly "terms of service" in your cafe can break your applications
---

OK, how could we fix things?
===

**TODO:** write this part.
