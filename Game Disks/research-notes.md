# Research notes

Background for [README.md](README.md). Collected November 2026. Much of this is community
knowledge from forums and hobbyist wikis, not official documentation. Where sources disagree I
say so. The signing section is the important part, because it decides what's possible.

## Why you can't sign a disc for a stock Wii

Wii disc authentication is a four-level RSA chain of trust:

1. **Root** key pair (RSA-4096). The public half is burned into every console. The private
   half never left Nintendo and was never cracked or leaked.
2. Root signs a **CA** certificate (RSA-2048).
3. CA signs the working certs: an **XS** cert (signs tickets) and a **CP** cert (signs TMDs).
4. Each game's **ticket** and **TMD** are signed by those certs.

A disc carries the content, the ticket, the TMD, and the XS/CP certs so the console can verify
the chain. The content is encrypted with a per-disc **title key** (AES), which is itself
wrapped with the **common key**. The common key is symmetric and leaked years ago, so
encrypting content correctly is not the problem. The RSA signatures are.

To sign your own disc you'd need a Nintendo private key. Recovering one from the public key
means factoring a 2048-bit modulus, which is infeasible. More data does not help:

- **Collecting more games** gives you more public certs (verify-only) and more signatures bound
  to other games' bytes. No signing key is ever on a disc.
- **Dumping keys from consoles** gives per-console ECC private keys. Each was signed *by*
  Nintendo; you can't run a signature backwards to recover the signer's key.
- **Reusing another game's signature** (e.g. Mario Kart's) fails because a signature is bound
  to the exact bytes it covers. Change the TMD to list your content and the signature no longer
  matches; keep the signature valid and the disc must stay byte-identical to Mario Kart.

The one console signing key ever recovered was Sony's PS3 ECDSA key, and only because Sony
reused the same nonce on every signature. That's an ECDSA implementation blunder; Nintendo's
RSA scheme doesn't have that weakness. Sources:
[WiiBrew: Signing bug](https://wiibrew.org/wiki/Signing_bug),
[fail0verflow 25c3 "Wii Fail"](https://marcan.st/uploads/25c3_console_hacking/).

## What "fakesign" actually is

The homebrew scene never got the private key. Instead it found a verification bug: Nintendo
compared signature hashes with `strncmp`, which stops at the first null byte. Craft a hash that
collides in the first byte and the check passes with a bogus signature. That's the **trucha
bug**. It's fixed in retail IOS (IOS30/IOS37 era onward), so fakesigned discs are rejected by
an up-to-date console. The scene reinstalls the buggy check as a **cIOS** (e.g. slot 249 via
d2x). That reinstall is the softmod, and it's the only reason a fakesigned disc boots.
Source: [ConsoleMods: The Signing Bug](https://consolemods.org/wiki/Wii:The_Signing_Bug).

## What happens when you insert a disc

1. System Menu reads the disc header and confirms the Wii magic word.
2. It reads the TMD for the required IOS and reloads into it. The **ES** module in that IOS
   does the crypto.
3. ES verifies the cert chain (XS/CP against CA, CA against the burned-in Root), then the
   ticket signature and the TMD signature.
4. On pass, ES unwraps the title key with the common key.
5. The apploader loads content, each block checked against the TMD's hash tree and decrypted
   with the title key.
6. `main.dol` runs.

A homemade disc dies at step 3 on stock IOS (forged signature rejected), or earlier at the
drive if it can't read DVD-R. A cIOS swaps ES's real check for the trucha version, so step 3
waves the fakesign through.

## Building the disc (verified on this box)

`wit` (Wiimms ISO Tools) composes a Wii disc image from an extracted file system and fakesigns
it. Verified with wit v3.05a using the open-source apploader template:

```
wit copy ./mygame ./mygame.iso --id HBDE --name "My Game" --region USA --ios 58
wit verify ./mygame.iso
# -> TICKET & TMD are fake signed. Partition is encrypted.   +OK
```

Required project layout: `sys/main.dol`, `sys/apploader.img`, `sys/boot.bin`, `sys/bi2.bin`,
`files/`, plus `ticket.bin` / `tmd.bin` / `cert.bin` (wit rewrites and fakesigns the ticket and
TMD). `sys/bi2.bin` is optional since wit v3.05a. Output is a single-layer-sized (~4.5 GiB)
encrypted, fakesigned image.

The template is the open-source apploader by bushing (GC-Linux derived), bundled as
[composing-template.zip](https://wit.wiimm.de/download/composing-template.zip). Original post:
[hackmii.com open-source apploader / ISO template](http://hackmii.com/2008/08/open-source-apploader-iso-template/).
marcan's note there: "this is just a base image that can be used to run homebrew from DVD."

## Drive compatibility (the hardware wall)

Early Wiis kept a DVD read mode from a cancelled DVD-video feature; softmod loaders and
modchips use it to read burned discs. Nintendo removed it in the D3-2 revision (mid-2009).

| Drive | Reads burned DVD-R after softmod? |
|---|---|
| DMS, D2A, D2B, D2C, D2C2, D2E, early D3 (Dec 2008) | Yes |
| D3-2, D4 | No |
| Wii mini, Wii U (vWii) | No |

Telling them apart: the drive chip's first line ends in the revision (`D2A`, `D2B`, ...). D3-2
looks like D3 but the top of the big chip is blank. Approximate D3-2 serial cutoffs (AfterDawn,
treat as rough): US LU7012xxxxx / LU3941xxxxx, EU LEF1128xxxxx, UK LEH2682xxxxx, AU
LAH1167xxxxx. Black/red/blue Wiis and all RVL-101/201 are D3-2 or newer. A repaired console may
have a swapped drive, so the real test is burning one disc and trying it.

If the drive can't read DVD-R, load the same `.dol` from SD/USB instead. Every Wii drive fits
every Wii, so an older drive swap is also possible.

## Alternative disc routes

- **Homebrew Disc Loader** (WiiBrew): burn a plain ISO9660 data DVD with `boot.dol` files in
  per-app folders, launch the loader from HBC, it reads them. Needs DVDx and a DVD-R-capable
  drive. Simpler than a real Wii disc but it's a data disc, not a game disc.
- **libdi + libiso9660** (in libogc): let your own app read data files off a burned ISO9660 DVD
  at runtime (mount `dvd:/`). Needs AHBPROT or DVDx. Useful if your game streams assets from
  the disc rather than booting from it.

## Sources

- [wii.hacks.guide](https://wii.hacks.guide/) (softmod + d2x cIOS)
- [WiiBrew: Signing bug](https://wiibrew.org/wiki/Signing_bug)
- [WiiBrew: Wii system flaws](https://wiibrew.org/wiki/Wii_system_flaws)
- [ConsoleMods: The Signing Bug](https://consolemods.org/wiki/Wii:The_Signing_Bug)
- [fail0verflow 25c3: Console Hacking 2008, Wii Fail](https://marcan.st/uploads/25c3_console_hacking/)
- [Wiimms ISO Tools: composing](https://wit.wiimm.de/info/composing.html)
- [hackmii.com: open-source apploader / ISO template](http://hackmii.com/2008/08/open-source-apploader-iso-template/)
- [WiiBrew: Homebrew Disc Loader](https://wiibrew.org/wiki/Homebrew_Disc_Loader)
- [WiiBrew: libiso9660](https://wiibrew.org/wiki/Libiso9660)
- [libogc libdi README](https://github.com/devkitPro/libogc/blob/master/libdi/README)
- [AfterDawn: new black Wii drives can't read DVD-R](https://forums.afterdawn.com/threads/possible-that-new-black-wii-drives-can-not-read-dvd-r.713257/)
