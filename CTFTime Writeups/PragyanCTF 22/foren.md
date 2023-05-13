---
title: Forensics
author: nxb1t
date: 2022-03-08
categories: [pragyan]
tags: [forensics]
---


## [CTF] Pragyan CTF - Forensics Writeup

Hy Friends , this time i played Pragyan CTF 2022 and my team scored 8th place. This CTF was pretty crazy. We solved all forensics challenges , Good Job @Vyshak and [@Paul](https://0xcyberpj.me/).

### Ye Olde Threat

---

![](https://i.imgur.com/TWH0aRe.png)

intel.txt have string `aHR0cHM6Ly9kcml2ZS5nb29nbGUuY29tL2ZpbGUvZC8xQUVremJqV1JDZFVwNEY3ZjJ3dExnSWQyd2RTRlkwUXE=`.

Convert this Base64 string to get a Gdrive URL.

It gives a zip file named intel.zip and inside it have a PNG image.

Tried to open the image but gave error , So it must be corrupted. Open it in hexeditor.

![](https://i.imgur.com/yQhVNiD.png)

We can see the PNG header is missing also IHDR isn’t correct , IDAT also named as Ida7.

![](https://i.imgur.com/uvJXsUg.png)

After fixing those we get a half-earth image.

![](https://i.imgur.com/pLEsIPb.png)

Check the image with pngcheck, `pngcheck -v Just_A_Plane_Image.png`.

![](https://i.imgur.com/OeZUis4.png)

Here the length of second IDAT is really weird, Its over the size of the png file itself.

Search the second IDAT and set the length value to `00 00 20 00` which is 8192 bytes , same as previous IDAT chunk.

![](https://i.imgur.com/RYcgSCp.png)

Now once again do pngcheck, this time it shows CRC error.

![](https://i.imgur.com/aKiyNUg.png)

Fix the CRC using [PCR tool](https://github.com/sherlly/PCRT), `python2 PCRT.py -v -i Just_A_Plane_Image.png` and open the output.png in stegsolve.

![](https://i.imgur.com/waA568V.png)

`p_ctf{K@nye_w@nt5_To_Buy_Th3_3n71r3_E4rth}`

### Frank’s Little Beauty

---

![](https://i.imgur.com/eJfrkJa.png)

First of all , dump password hashes to a file

`vol.py -f MemDump.DMP --profile=Win7SP1x64 hashdump > hashes`

We got Frank’s password hash : `Frank Reynolds:1000:aad3b435b51404eeaad3b435b51404ee:a88d1e18706d3aa676e01e5943d15911:::`.

Crack the hash `a88d1e18706d3aa676e01e5943d15911` using crackstation , the cracked password is `trolltoll`. This password will be needed later because as of Description , frank reuses password so this will come in handy to extract zips or like that.

The decription have frank is `lazy to type` , one of the crucial area in a memory dump is clipboard . Lets see whats inside the clipboard of this memory dump.

`vol.py -f MemDump.DMP --profile=Win7SP1x64 clipboard`

We got a pastebin link `https://pastebin.com/3Ecrm2DY`

flag 1/3 : `p_ctf{v0l4t1l1ty`

Now dig more to find remaining parts. Next area to check is process command lines.

`vol.py -f MemDump.DMP --profile=Win7SP1x64 cmdline > cmdlog`

> writing results to a file is always a good practice, so no need to rescan all time.

```
WinRAR.exe
pid:   2200
Command line : "C:\Program Files\WinRAR\WinRAR.exe"  e comp.rar
```

comp.rar is interesting , but we can’t get file from cmdline , To get files first we need to do a scan , then dump file using its Offset.

`vol -f MemDump.DMP --profile=Win7SP1x64 filescan > scanlog`

Found offset `0x000000003df4e450` for comp.rar , now dump the file from memory.

`vol -f MemDump.DMP --profile=Win7SP1x64 dumpfiles -D . -Q 0x000000003df4e450`

Its a password protected rar file , extract the file using `trolltoll` as password. It gives a image file which have 2nd part of the flag.

![](https://i.imgur.com/AyFPdOh.png)

Last flag must be related to the `Paddys` in description. He don’t remember `shortcut to Paddys`.

Search for `.lnk` in the scanlog. Assumption was correct , there exists `Paddys.lnk` shortcut file and its offset is `0x000000003e1891d0`.

`vol -f MemDump.DMP --profile=Win7SP1x64 dumpfiles -D . 0x000000003e1891d0`

Its a shortcut to `C:\Program Files\Microsoft Games\Minesweeper\sysinfo.txt`. Check the cmdlog to see if any process opened this file. Its currently opened by a notepad process.

```
notepad.exe
pid:   3016
Command line : "C:\Windows\system32\NOTEPAD.EXE" C:\Program Files\Microsoft Game
s\Minesweeper\sysinfo.txt
```

Tried to extract this file using offset , but that didn’t worked. Since its opened by Notepad dumping the notepad process should give its contents.

`vol -f MemDump.DMP --profile=Win7SP1x64 memdump -p 3016 -D .`

Notepad uses UTF-16 to store texts , so grep through the memdump of notepad.

`strings -e l 3016.dmp | less`

![](https://i.imgur.com/CTehsYB.png)

flag part 3/3 : `_r34d1ng_dump5_iasip}`

final flag : `p_ctf{v0l4t1l1ty_i5_v3ry_h4ndy_at_r34d1ng_dump5_iasip}`

### Marcel and the iPad

---

![](https://i.imgur.com/dNja05a.png)

This challenge is similar to [Bobby toes iPad](https://niekdang.wordpress.com/2020/07/31/ctflearn-solution-bobby-toes-ipad/amp/). Googling is really useful :).

Stegsolve the marcel.png.

![](https://i.imgur.com/FMHS9O6.png)

This is a one-time-pad message.

Message : `1h@ww_e0op$_0b_t0y`

Pad is at the Comment section of exif of the image.

Pad : `Steve_loves_apple`

Decrypt using [OTP tool](http://rumkin.com/tools/cipher/otp.php).

`p_ctf{1p@ds_j0ke$_0n_y0u}`

### Hidden and Protected

---

![](https://i.imgur.com/EOGbmQV.png)

The description don’t have much suspicious info , although the title is interesting.

Firstly do binwalk.

![](https://i.imgur.com/PCRc0nw.png)

We can see a zip file there, we can use foremost or binwalk itself to get that file.

`foremost image.png`

Inside the zip there is another image and its in JPG format , We have complied with first section of Title , that is Hidden.

So next part is protected, Since this is a JPG it should be a stegofile. Zip also have a text file , but didn’t bothered about it.

Using [stegseek](https://github.com/RickdeJager/stegseek) crack the stegofile.

`stegseek image2.jpg /usr/share/wordlists/rockyou.txt`

![](https://i.imgur.com/vpOTnXG.png)

### Zipped scandal

---

![](https://i.imgur.com/kbZXgVB.png)

We are given with a zip file named Aa.zip, extracting it gives Ab.zip and doing the same on it gives Ac.zip , so its like onion zipped many times.

But the pattern is `Aa, Ab, Ac` , first letter Uppercase and second letter Lowercase. Here is a way to extract the zip file.

```

import string

lower = string.ascii_lowercase

upper = string.ascii_uppercase

file = open("pattern.txt", "w")

for i in up:
    for j in low:
        file.write(i+j+"\n")
```

Run the script to make pattern wordlist , then `cat pattern.txt | while read line;do unzip $line.zip ;done`

It extracts all the zip files upto Zz.zip. Finally we are left with Checkpoint.zip. Extracting it gives an image and another zip.

Final.zip is password protected. Looking at exif of the image , we get a hash in comment section and GPS coordinates.

Cracking hash using crackstation gives `SHA256`. Next is to find the Location `36°20' 21.49" N, 96°48' 10.32" W`. Paste the location cords in Google Map.

![](https://i.imgur.com/tMtmfWZ.png)

The location is `Pawnee City Hall`.

Tried this as password to extract Final.zip , but no success. So connect SHA256 with this result , that is `Hash the location name in sha256`.

Thats the password , then it gives a black image.

![](https://i.imgur.com/msyNY4V.png)

Change its bit plane and it gives a QR with black background and white foreground. Its still not good , so invert the color to make it normal QR Code.

![](https://i.imgur.com/jWZNdsD.png)

This QR points to Gdrive link [zipped.zip](https://drive.google.com/file/d/1DM1HHXJ0gWg-r1Zm3VfGAryCHNhR_Pwt)

The zip includes an audio and a hint.txt , Audio sounds like morse code , so tried to decode it and it gave gibberish message.

Looking at the hint `"Fast as we can. Really put the pedal to the metal, you know! Bill and Ted did it."` , at first it didn’t made any sense.

After some googling , found out that its an excerpt from Ready Player One Movie , `Why can't we go backwards for once? Backwards, really fast. Fast as we can. Really, put the pedal to the metal, you know?`.

Now the hint makes much sense , reverse the audio and decode it again.

-   [Morse Code Audio Decoder](https://morsecode.world/international/decoder/audio-decoder-adaptive.html)
-   [Reverse Audio Online](https://mp3cut.net/reverse-audio)

![](https://i.imgur.com/N75joYL.png)

`p_ctf{ic3t0wnc0stsic3cl0wnhist0wncr0wn}`