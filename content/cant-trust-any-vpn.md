+++
title = "Can't trust any VPN these days"
date = 2024-10-16

[taxonomies]
categories = ["Projects"]
+++

After Turkey banned Discord, I had to jump through some hoops, fix my VPN, and learn a bit about how DNS works.

<!-- more -->

<q>
On October 9th 2024, Discord was banned in Turkey due to various criminal allegations.<br>
This was an ongoing issue and Discord apparently refused to share user data (IP addresses and content) thus the government decided to block the whole thing altogether. I'm not here to talk about if this was the right thing to do or not or to drag you into politics, so feel free to <a href="https://www.reuters.com/technology/turkey-blocks-instant-messaging-platform-discord-2024-10-09/">read</a> around the internet if you're interested.
</q>

Today I'm here to share what I have learned while trying to... you know. Find a way to use Discord again. Surprisingly, this ban ended up being a positive experience for me.

<small>I hope the officials are ̶n̶o̶t̶ reading this.</small>

---

### **Timeline**

In the early morning hours of the day that our beloved platform, Discord, got its ass whooped, I was actually at the airport heading to Vienna for the [EuroRust](https://eurorust.eu) conference. Before my flight, I tried checking my messages to see if there was anything new - to my surprise, I was greeted with this:

```
----- Certificate i=0 (emailAddress=root@localhost.localdomain,
CN=localhost.localdomain,OU=IT,O=MyCompany,L=Seattle,ST=WA,C=US) -----
ERROR: No matching issuer found
Error downloading with electron net: net::ERR_CERT_AUTHORITY_INVALID
```

In other words:

```
discord.com uses an invalid security certificate.
The certificate is not trusted because it is self-signed.
Error code: MOZILLA_PKIX_ERROR_SELF_SIGNED_CERT
```

I thought:

> Ah, maybe the airport Wi-Fi is acting up. But wait... I'm already connected to the VPN. 🤔

So I tried using the Discord app on my phone and it worked as usual. Later on, I saw the news when I did a quick research but I didn't think much about it because I had a flight and a conference ahead. I could always look into it when I'm back home, right? After all, I had a VPN, so it should be fine, _right???_

Well, I came home last night and realized I can't use Discord at all, even with a VPN.

That's why I'm writing this blog post in the morning.

Pain.

---

### **VPN Setup**

<small>Not sponsored!</small>

You might be asking, "Hey Orhun, VPNs should work out of the box, what kind of a shitty setup do you have?" and you are absolutely right.

I have my own VPN (😎) - in other uncool words, I set up [OpenVPN](https://github.com/OpenVPN/openvpn) on a VPS and I have been using it for a while without any issues.

<q>Wow, setting that up must be a lot of work!</q>

No. In fact, I just ran the [`openvpn-install`](https://github.com/angristan/openvpn-install) script to set everything up:

```bash
curl -O https://raw.githubusercontent.com/angristan/openvpn-install/master/openvpn-install.sh
chmod +x openvpn-install.sh
./openvpn-install.sh
```

This gives you a configuration file such as `client.ovpn` which contains your server address, port and certificates and you can simply run this command to connect to the VPN on Linux:

```bash
openvpn --config $HOME/.openvpn/client.ovpn
```

I use the [Android app](https://play.google.com/store/apps/details?id=de.blinkt.openvpn&hl=en&pli=1) as well and everything works like a charm! At least that's what I thought until this blog post...

Here is the relevant part of my VPN configuration:

```ini
client
proto udp
explicit-exit-notify
remote <ip> 1194
dev tun
resolv-retry infinite
nobind
persist-key
persist-tun
remote-cert-tls server
verify-x509-name server name
auth SHA256
auth-nocache
cipher AES-128-GCM
tls-client
tls-version-min 1.2
tls-cipher TLS-ECDHE-ECDSA-WITH-AES-128-GCM-SHA256
ignore-unknown-option block-outside-dns
setenv opt block-outside-dns # Prevent Windows 10 DNS leak
verb 3
```

This is a vanilla configuration so I'm mostly using the defaults. See the [reference manual](https://openvpn.net/community-resources/reference-manual-for-openvpn-2-6/) for detailed information about these values.

---

### **Debugging**

The most mind-boggling part of the problem for me was that I was able to use Discord via OpenVPN on my phone but not on my computer. Whenever I try to launch the desktop app, it gets stuck at the loading screen with the error message I shared above. To limit the scope of the problem, I also tried the website app directly, which resulted in the certificate error.

<q>How does Discord know that I'm still in Turkey!?</q>

I searched around the internet and found out that nearly all the website restrictions in Turkey are done on the DNS level. I quickly brought out [`trippy`](https://github.com/fujiapple852/trippy) from my terminal toolbox to check the network hops that are happening while I'm connecting to Discord:

```bash
trip discord.com
```

![trippy](/trippy.jpg)

<small>I hope I'm not doxxing myself with that image. Maybe I'm just \*tripping\*... </small>

So from 9th hop to 11th, I can see that I am connecting to my OpenVPN server in Frankfurt. But after the 12th hop, it goes back to the Turkey DNS.

<q>What? What's the point of the VPN then lmao</q>

Okay, at that point I was clueless. I tried changing the DNS settings of OpenVPN (i.e. `dhcp-option DNS 1.1.1.1`) but it didn't work. After a couple of iterations with ChatGPT, it finally led me to the correct path.

It turns out, **my DNS was leaking**.

> The DNS leak is a security flaw that allows your DNS requests to be revealed to your ISP, and anyone else who might be snooping on the network.

I discovered a website called [DNSLeakTest](https://www.dnsleaktest.com/) which you can use to check that:

![DNSLeakTest](/dns-leak-test.png)

<q>Sheesh dats indeed leakin'</q>

So I was using my ISP's DNS instead of the VPN's DNS the whole time.

Using this VPN was just pointless. 💀

---

### **Solution**

Remember these lines from my config above?

```ini
ignore-unknown-option block-outside-dns
setenv opt block-outside-dns # Prevent Windows 10 DNS leak
```

Using `block-outside-dns` is also suggested by DNSLeakTest and it sounds like the correct option:

> Block DNS servers on other network adapters to prevent DNS leaks.  
> This option prevents any application from accessing TCP or UDP port 53 except one inside the tunnel.

<q>Well then, what's the problem?</q>

> It uses Windows Filtering Platform (WFP) and works on Windows Vista or later.

<q>Bruh... Windows Vista mentioned tho o7</q>

I'm on [Arch Linux](https://archlinux.org/) btw - and it has a very detailed wiki about OpenVPN, as always. It wasn't very hard to find the exact thing I needed, it's right there under the [DNS section](https://wiki.archlinux.org/title/OpenVPN#DNS).

In a nutshell:

- The DNS changes are not automatically applied by the OpenVPN client on Linux.
- You need to configure `up` and `down` scripts for managing the DNS updates.
- The recommended script is [`update-resolv-conf`](https://github.com/alfredopalhares/openvpn-update-resolv-conf), which modifies DNS settings when the VPN connects and restores them upon disconnection.
- That script consists of a bunch of arcane bash commands that I don't understand.

<q>So how is any of that relevant to DNS leaking?</q>

When the DNS changes aren't automatically applied when connecting to the VPN, the system will continue to use the default DNS settings thus your ISP's DNS. 💀

So the solution for me was to firstly install the following packages:

```bash
# install Openresolv for managing DNS settings
pacman -S openresolv

# install the script for updating DNS with OpenVPN
# (use your favorite AUR helper)
paru -S openvpn-update-resolv-conf-git
```

Then add the following lines to the OpenVPN configuration:

```ini
script-security 2 # Allow script execution
up /etc/openvpn/update-resolv-conf
down /etc/openvpn/update-resolv-conf
```

And run the VPN as usual:

```bash
openvpn --config $HOME/.openvpn/client.ovpn
```

Boom, no more leaks!

![DNSLeakTest](/dns-leak-test2.png)

![trippy](/trippy2.jpg)

It goes through Frankfurt as expected!

And I can use Discord again! 🎉

---

### **Extras**

#### `dnsleaktest-tui`

Since I'm a big fan of terminal tools, I wrote a small proof-of-concept TUI using [Rust](https://www.rust-lang.org/)/[Ratatui](https://ratatui.rs/) to check if my DNS is leaking. I also added a small traceroute feature to see the network hops.

Here is how it looks like without VPN:

![PoC](/dns-leak-test-poc.jpg)

And most importantly, I can observe that my DNS is leaking while I'm connected to the VPN:

![PoC](/dns-leak-test-poc2.jpg)

The code is based on [dnsleaktest](https://github.com/macvk/dnsleaktest) and I used [`trippy`](https://github.com/fujiapple852/trippy) as a library.

⭐ Here is the repository: <https://github.com/orhun/dnsleaktest-tui>

#### **i3 integration**

I don't like running this command every time I want to connect to the VPN:

```bash
openvpn --config $HOME/.openvpn/client.ovpn
```

So I wanted to add a keybinding to my [i3](https://i3wm.org/) configuration to enable/disable the VPN and show a notification on the status bar when it's connected/disconnected.

So...

- I switched to [a systemd service](https://community.openvpn.net/openvpn/wiki/Systemd) to manage the VPN.
- I created a simple script to start/stop the service.
- I added a keybinding to my `i3` configuration to run the script via `pkexec` (since it requires root privileges).
- I installed `lxsession-gtk3` package as the authentication agent.
- I updated my `i3status` configuration to show the VPN status.

⭐ [Here](https://github.com/orhun/dotfiles/commit/1d3cacc54342311d347d1b06a768311d81e5ec7f) is how I implemented it.

At the end of the day, I just press `Super + Shift + V` to connect/disconnect:

![lxpolkit](/lxpolkit.png)

And I can see the status ("V: UP")

![i3status](/i3status.png)

Cheers!
