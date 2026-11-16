# Making a Wii game disc from your own game

This is for a game **you build**, played on a **softmodded** Wii. It covers compiling your
game to a disc image, fakesigning it, burning it, and launching it. The background on why a
stock console won't touch it, and why you can't sign it for real, is in
[research-notes.md](research-notes.md).

The short version of the hard truth: there is no way to make a disc that boots on an
untouched Wii. Signing needs Nintendo's private key, which nobody has. The route below works
only because the softmod reinstalls the old "trucha" signature bug, so a *fakesigned* disc is
accepted. The softmod is the thing doing the heavy lifting, not the signature.

## Two walls that the softmod does not remove

1. **The drive has to physically read DVD-R.** Only pre-D3-2 drives do, which in practice
   means white Wiis made before about mid-2009. A D3-2 or newer drive (every black/red/blue
   Wii, RVL-101, RVL-201, Wii mini, Wii U) cannot read a burned disc no matter what software
   you run. If that's your console, skip the disc and load the same `.dol` from SD or USB
   instead. Same build, no burning.
2. **The console must already be softmodded with d2x cIOS.** That's the premise here. Follow
   [wii.hacks.guide](https://wii.hacks.guide/) if it isn't done yet. The cIOS is what carries
   the trucha bug that makes a fakesigned disc boot.

## Test in Dolphin first

Before you burn anything, run the composed `.iso` (built below) in
[Dolphin](https://dolphin-emu.org/). It loads your image directly, no burning and no console,
and it's the fastest way to catch a broken build. Burn only once it runs there.

## What you need

- Your game compiled to a `main.dol` (devkitPPC + libogc; `elf2dol` produces the `.dol`)
- [Wiimms ISO Tools](https://wit.wiimm.de/) (`wit`) to build and fakesign the disc image
- The open-source apploader template:
  [composing-template.zip](https://wit.wiimm.de/download/composing-template.zip)
- A softmodded Wii with d2x cIOS and USB Loader GX
- A Wii with a pre-D3-2 drive (see wall #1)
- Blank **DVD-R** (Verbatim or similar), and ImgBurn (Windows) or `growisofs` (Linux)

## Steps

### 1. Build your game to a `.dol`

Compile with devkitPPC and libogc. The output you want is `main.dol`. This is your whole game;
everything else below just wraps it in a disc the Wii understands.

### 2. Lay out the project directory

Unzip the composing template and drop your game in. The layout `wit` expects:

```
mygame/
  sys/
    main.dol        <- YOUR game's .dol goes here (replace the template's)
    apploader.img   <- open-source apploader, from the template, leave as-is
    boot.bin        <- from the template
    bi2.bin         <- from the template
  files/            <- any assets your game reads off the disc
  ticket.bin        <- template; wit rewrites and fakesigns it
  tmd.bin           <- template; wit rewrites and fakesigns it
  cert.bin          <- template cert chain
```

Put data files your game loads at runtime under `files/`.

### 3. Compose and fakesign the disc image

```
wit copy ./mygame ./mygame.iso --id HBDE --name "My Game" --region USA --ios 58
```

- `--id` is a 4-character game ID. The **4th letter is the region**: `E` = NTSC-U, `P` = PAL,
  `J` = Japan. Match it to `--region` or the console gets confused.
- `--ios` sets the IOS your game asks for. Use the IOS your game is built against (58 is a
  common choice with USB networking). The raw template defaults to something odd (IOS 21,
  near-Europe ID), so always set these two yourself.
- `wit` builds the content hash tree and **trucha-signs** the ticket and TMD for you.

Confirm it worked:

```
wit verify ./mygame.iso
```

A good build reports `TICKET & TMD are fake signed. Partition is encrypted.` and `+OK`. The
image comes out at single-layer size (about 4.5 GiB), padded, which is exactly what fits on a
DVD-R.

### 4. Burn to DVD-R

Burn `mygame.iso` **as a disc image**, not as a file on a data disc.

- ImgBurn: Mode > Write, pick the `.iso`, set speed to 4x or lower (2x is safer), Verify on.
- Linux: `growisofs -dvd-compat -speed=2 -Z /dev/sr0=mygame.iso`

### 5. Launch it

Put the disc in the softmodded Wii, open **USB Loader GX**, and click the disc/DVD icon (top
right). It reads and boots the disc through cIOS, whose trucha-patched check accepts your
fakesigned ticket and TMD. The normal Disc Channel will **not** boot it, because it uses the
stock, unpatched IOS.

## If it doesn't boot

- Runs in Dolphin but not on the Wii? Almost always the drive (wall #1) or the burn. Try
  slower speed and better media, and confirm the drive is pre-D3-2.
- Loader can't see the disc at all: check d2x cIOS is installed and USB Loader GX's loader IOS
  is set to the cIOS slot (usually 249).
- Wrong region or a black screen on boot: recheck `--id` 4th letter against `--region`, and
  that `--ios` points at an IOS the console actually has.

## Simpler alternative: run the `.dol` without a game disc

If you don't specifically need a disc that looks like a real Wii game, skip all of the above.
Put your build at `/apps/mygame/boot.dol` on an SD card or USB drive and launch it from the
Homebrew Channel. No fakesigning, no DVD-R, works on every softmodded Wii including D3-2 ones.
The disc is the hard way; this is how homebrew normally ships.
