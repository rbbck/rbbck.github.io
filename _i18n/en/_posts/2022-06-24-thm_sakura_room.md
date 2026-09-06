---
layout: post
title: "TryHackMe Sakura Room"
category: writeup
lang: en
ref: sakura_room-thm
---

![](/assets/Pasted image 20220627235902.png)

# First steps
This room is designed to test a variety of **OSINT**[^1] techniques, we are going to experience an example of **investigation** that aims to **find some identifiers** and other **pieces of information** in order to catch a cybercriminal.

>NOTE: All answers can be obtained via passive OSINT techniques, DO NOT attempt any active techniques such as reaching out to account owners, password resets, etc to solve these challenges.

We are also warned that no **active OSINT** will be required. See below **some** examples to distinguish:

| Passive OSINT                                          | Active OSINT                                                 |
| ------------------------------------------------------ | ----------------------------------------------------------- |
| No direct contact                                 | Contact may occur                         |
| Passive scanning, more hidden                            | More aggressive and intrusive posture, higher risk of detection                                     |
| Gathering available information hosted on third parties | Social engineering, e.g. sending a malicious link to the victim |

---

# First flag
>What username does the attacker go by?

We are asked for the criminal's **username**. In addition, an overview is provided:
>The [OSINT Dojo](https://www.osintdojo.com) was the victim of a cyberattack and during forensic analysis, an image left behind by the hacker was found. Perhaps it contains some clues that would allow us to point a direction?

![](/assets/Pasted image 20220628011354.png)

With this we have our starting point. In an image there can be a lot of hidden information, either because [someone hid it](https://en.wikipedia.org/wiki/Steganography) there, or, as in our case, because someone **forgot to check its own track**. The ***Exchangeable image file format*** or simply **EXIF** is a **standard for storing information in digital photography**. In short, it is a set of information about (and contained in) an image, such as its **resolution**, **modification date**, **camera manufacturer**, etc. In some cases it may even contain **geolocation** data. 

We will use the [ExifTool] tool (https://exiftool.org/), which is used to read, write and edit **metadata**.

```
exiftool sakurapwnedletter.svg
ExifTool Version Number         : 12.42  
File Name                       : sakurapwnedletter.svg  
Directory                       : .  
File Size                       : 850 kB  
File Modification Date/Time     : 2022:06:28 01:28:26-03:00  
File Access Date/Time           : 2022:06:28 01:28:26-03:00  
File Inode Change Date/Time     : 2022:06:28 01:28:26-03:00  
File Permissions                : -rw-r--r--  
File Type                       : SVG  
File Type Extension             : svg  
MIME Type                       : image/svg+xml  
Xmlns                           : http://www.w3.org/2000/svg  
Image Width                     : 116.29175mm  
Image Height                    : 174.61578mm  
View Box                        : 0 0 116.29175 174.61578  
SVG Version                     : 1.1  
ID                              : svg8  
Version                         : 0.92.5 (2060ec1f9f, 2020-04-08)  
Docname                         : pwnedletter.svg  
Export-filename                 : /home/SakuraSnowAngelAiko/Desktop/pwnedletter.png  
Export-xdpi                     : 96  
Export-ydpi                     : 96  
Metadata ID                     : metadata5  
Work Format                     : image/svg+xml  
Work Type                       : http://purl.org/dc/dcmitype/StillImage  
Work Title                      :
```

We have numerous outputs here, but let's focus on a specific one. **Export-filename** takes as its value the path of the file at the time of export: 
```
Export-filename                 : /home/SakuraSnowAngelAiko/Desktop/pwnedletter.png
```
And with that we find out the username of our attacker. 

---

# Second flag
>What is the full email address used by the attacker?

For the attacker's **email**, it is either valid to use an automated tool or to go directly to the best known websites and social networks and enter the username we found before, i.e. a **manual approach**.

A **very good tool** is [Sherlock](https://github.com/sherlock-project/sherlock), written in **Python**, that provided a username, the software will go after it through locations on the internet. 

Example usage:

```
python3 sherlock.py --verbose SakuraSnowAngelAiko

[*] Checking username SakuraSnowAngelAiko on:  
  
  
[+] [5058ms] GitHub: https://www.github.com/SakuraSnowAngelAiko  
[+] [11563ms] Reddit: https://www.reddit.com/user/SakuraSnowAngelAiko
[...]
```

One of the outputs is his **GitHub**, which is of great interest to us:

![](/assets/Pasted image 20220628232728.png)

These are the **fixed repositories** of our attacker:

![](/assets/Pasted image 20220628233553.png)

A special highlight is the **PGP**[^2] repository, where the attacker shares his **public key**[^3].

![](/assets/Pasted image 20220629014758.png)

If we download the key, run [gnu](https://gnupg.org/) **without providing any parameters**, passing the **publickey we downloaded**, the program will **print** the key: 

	wget https://github.com/sakurasnowangelaiko/PGP/blob/main/publickey
	gpg publickey
	
	gpg: WARNING: no command supplied.  Trying to guess what you mean ...  
	pub   rsa3072 2021-01-23 [SC] [expires: 2023-01-22]  
	     A6519F273BF88E9126B0F4C5ECDD0FD294110450  
	uid           SakuraSnowAngel83@protonmail.com  
	sub   rsa3072 2021-01-23 [E] [expires: 2023-01-22]

Revealing the **email of our cyberpirate**:

	uid           SakuraSnowAngel83@protonmail.com  

---

# Third flag
>What is the attacker's full real name?

That was easy peasy! Putting the **nickname** of GitHub into Google, **our first result already gives us the answer**, **it's name**:

![](/assets/Pasted image 20220629030444.png)

![](/assets/Pasted image 20220629030620.png)

---

# Fourth flag
>What cryptocurrency does the attacker own a cryptocurrency wallet for?

Our goal now is to find out what **cryptocurrency** Aiko exchanges. Do you remember the repositories of our software engineer?

If you notice, the fixed ones add up to only **five**, when in reality he has **nine**:

![](/assets/Pasted image 20220629032530.png)

One of them being called "ETH", an acronym for "[Ethereum](https://ethereum.org/en/)", one of the most famous **cryptos**:

![](/assets/Pasted image 20220629032858.png)

Entering **ETH** into TryHackMe, the answer was accepted, however, other repositories may indicate that it would also use **Litecoin** and **Bitcoin**.

---

# Fifth flag
>What is the attacker's cryptocurrency wallet address?

To find the **wallet address** of the hacker, we will need to go back in time.
But don't worry, we won't need the DeLorean!

Take a look at the **ETH** repository page:

![](/assets/Pasted image 20220629033224.png)

You notice that **two commits** have been made. Let's see what has changed? By clicking on the little clock we see:

![](/assets/Pasted image 20220629033630.png)

And when you click to see the commit "Update miningscript":

![](/assets/Pasted image 20220629033717.png)

That's right! The **address of it's digital wallet** being hidden.

---

# Sixth flag
>What mining pool did the attacker receive payments from on January 23, 2021 UTC?

Inserting our previous **flag** into [Etherscan](https://etherscan.io/):

![](/assets/Pasted image 20220629034349.png)

We found out that the wallet does in fact exist:

![](/assets/Pasted image 20220629034431.png)

Now we just need to go to the **transactions** and look for **who made payments** to this destination **on January 23, 2021** as the flag calls for:

![](/assets/Pasted image 20220629034918.png)
We have unraveled yet another one! The sender of this transaction was "**Ethermine**".

---

# Seventh flag
>What other cryptocurrency did the attacker exchange with using their cryptocurrency wallet?

What other **cryptocurrency** did our target use?
Still on the transactions page we find this answer:

![](/assets/Pasted image 20220629040200.png)

**Tether**! A **cryptocurrency** that aims to have a 1:1 quotation ratio with the **US dollar**

# Eighth flag
>What is the attacker's current Twitter handle? 

To find the Twitter *username* of our attacker, the following information is provided:

>Just as we thought, the cybercriminal is fully aware that we are gathering information about them after their attack. They were even so brazen as to message the OSINT Dojo on Twitter and taunt us for our efforts. The Twitter account which they used appears to use a different username than what we were previously tracking, maybe there is some additional information we can locate to get an idea of where they are heading to next?

![](https://raw.githubusercontent.com/OsintDojo/public/main/taunt.png)

Searching this new username in any search engine should return the account
**@SakuraLoverAiko**

In that account, there is a tweet saying "Hi! I am **@AikoAbe3!**"

![](/assets/Pasted image 20220630212419.png)

---

# Ninth flag
>What is the URL for the location where the attacker saved their WiFi  SSIDs and passwords?

This flag I admit I found unexpected but interesting.
By analyzing the last Twitter account we found, we are able to find a **not at all suspicious** tweet:

![](/assets/Pasted image 20220630212522.png)

Okay dokey. So we know that our attacker (for some reason) **posted their WiFi access passwords somewhere**.
Another point that makes it a lot easier for us is that the **ID of the post is complete**. All that's left is to know **where** this list was hosted.

Right after this tweet, Aiko replies:

![](/assets/Pasted image 20220630213801.png)

The tweet quotes "**Dark Web**" and "**Deep Paste**" (in capital letters, it looks comical). So we know that our site is on the [Onion network](https://www.torproject.org/). 
The question here becomes now **HOW** to find this address, considering that to **index** pages on these alternative networks always requires some effort.

If you find (for instance, on Google or DuckDuckGo) any site that lists famous .onion addresses, finding DeepPaste's address becomes easy ([the v3 address](https://support.torproject.org/onionservices/v2-deprecation/)).


>Deep Paste may be offline at the time you are working on this room. For this reason, TryHackMe provides the address in the hint

![](/assets/Pasted image 20220701192938.png)

Once you find it, just enter the ID of the post in the search field and the page will show up. In it some **SSIDs** and some **passwords** have been stored.

![](/assets/Pasted image 20220701193518.png)

---

# Tenth flag

>What is the BSSID for the attacker's Home WiFi?

To find the BSSID[^4], you will need an account at [wigle](https://wigle.net/) to use the advanced search function.

With a simple search, we only get a sense of the geolocation of the BSSID, not its identifier:

![](/assets/Pasted image 20220703005717.png)

Now with advanced search:

![](/assets/Pasted image 20220703010734.png)

![](/assets/Pasted image 20220703010722.png)

---

# Tenth flag

>What airport is closest to the location the attacker shared a photo from prior to getting on their flight?

Taking a look at his Twitter, we found this:

![](/assets/Pasted image 20220701194313.png)

So far, I admit not being good at **IMINT**[^5], I got caught up in this part, which motivated me to want to do the [Searchlight - IMINT room](https://tryhackme.com/room/searchlightosint), learn and explore more of this area. I didn't know any tools either, so I had to rely on the help of others. However, I will try to illustrate it in a way that we can all **learn!**.

In the image you can see an **obelisk** faaar awaay in the center of the image:

![](/assets/Pasted image 20220703012352.png)

I imagine that for those living at US it is something very identifiable just by looking at it, but for me it was impossible. I spent about half an hour looking for places near the coast of Japan, still not having made the connection that our invader was traveling not through Japanese lands, but in **another country**. 

The obelisk is the **Washington Monument**:

![](/assets/Pasted image 20220703012933.png)

Then we look for airports that are close to the monument:

![](/assets/Pasted image 20220703020423.png)

The closest one is Ronald Reagan Washington National Airport. The format of this flag is three characters, so the one used here will be the airport code.

![](/assets/Pasted image 20220703020833.png)

---

# Eleventh flag

>What airport did the attacker have their last layover in?

![](/assets/Pasted image 20220703021109.png)

Reverse image search:

![](/assets/Pasted image 20220703021723.png)

We see already in the second result "**Tokyo International Airport [Haneda]**".

And then just go after the 3-digit code:

![](/assets/Pasted image 20220703022808.png)

---

#  Twelfth flag 
>What lake can be seen in the map shared by the attacker as they were on their final flight home?

Also from Twitter:

![](/assets/Pasted image 20220703022955.png)

Just use a map and go for it, it is quite easy to find the place in Japan if you orient yourself by the islands to the northwest. We can easily get this one, since it is right under our noses:

![](/assets/Pasted image 20220703023300.png)

![](/assets/Pasted image 20220703023357.png)

![](/assets/Pasted image 20220703023333.png)

# Thirteenth flag 
> What city does the attacker likely consider "home"?

**Two** ways are possible to getting this flag:
- See in **which city** that **BSSID we put in the Wigle earlier** is in
- Use the **BSSID** of **Aiko's city WiFi** from the Deep Paste

---

# Footnotes

[^1]: Open Source Intelligence
[^2]: Pretty Good Privacy
[^3]:  Read about [public-key criptography](https://en.wikipedia.org/wiki/Public-key_cryptography).
[^4]: Basic Service Set Identifier; It usually refers to the MAC address, that is, a unique identifier of an adapter or an access point. Or it will be a derivation of it. Reference: https:/[/www.quora.com/What-is-the-difference-between-SSID-BSSID-and-MAC-addresses](https://www.quora.com/What-is-the-difference-between-SSID-BSSID-and-MAC-addresses)
[^5]: [Imagery Intelligence](https://en.wikipedia.org/wiki/Imagery_intelligence)