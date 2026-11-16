# Burning a Wii game to DVD

A short guide to making a burned DVD that boots on a Wii. It assumes you're backing up discs you own. Sources and the longer notes are in [research-notes.md](research-notes.md).

## Check your drive first

Whether your Wii can read a burned disc depends on the DVD drive inside it. Software can't change this.

| Drive | Reads burned DVD-R? |
|---|---|
| DMS, D2A, D2B, D2C, D2C2, D2E | Yes, after softmod |
| D3 (early, Dec 2008) | Yes, after softmod |
| D3-2, D4 (mid-2009 onward) | No |
| Wii U (vWii), Wii mini | No |

Rough ways to tell:

- Black, red and blue Wiis, and the later RVL-101 and RVL-201 models, have D3-2 or newer drives. They won't read burned discs.
- White launch-era Wiis (RVL-001, 2006 to 2008) usually have a readable drive. Approximate serial cutoffs for D3-2 are LU7012xxxxx / LU3941xxxxx (US), LEF1128xxxxx (EU), LEH2682xxxxx (UK) and LAH1167xxxxx (AU). Serials below those are probably fine.
- A Wii could have had its drive swapped during a repair, so the serial isn't proof. The test below is.

If you have a D3-2 or newer, skip discs and load games from a USB drive with USB Loader GX. You can also swap in an older drive (D2B or D2C are easy to find), because every Wii drive fits every Wii.

## What you need

- A Wii with a readable drive (see above)
- An SD card, FAT32
- Blank **DVD-R** discs, Verbatim or another good brand. Single-layer covers most games. A few dual-layer games (Smash Bros. Brawl, Xenoblade, Metroid Prime Trilogy) need DL discs, and burned DL discs are hit or miss on the Wii, so USB is the safer route for those.
- A PC with a DVD burner and ImgBurn (Windows) or `growisofs` (Linux)

## Steps

1. **Softmod the Wii.** Follow [wii.hacks.guide](https://wii.hacks.guide/). It installs the Homebrew Channel, BootMii and Priiloader, has you make a NAND backup, and installs d2x cIOS. Don't skip the NAND backup.
2. **Install a loader.** Put USB Loader GX on the SD card (`/apps/usbloader_gx/`). WiiFlow also works.
3. **Dump your game.** Run CleanRip from the Homebrew Channel with the original disc in the drive. It writes a `.iso` to SD or USB. Don't burn `.wbfs` or `.rvz` files. Convert them back to `.iso` first with Dolphin or wit.
4. **Burn the ISO.**
   - ImgBurn: Mode > Write, pick the ISO, set write speed to 4x or lower (2x is better), and turn on Verify.
   - Linux: `growisofs -dvd-compat -speed=2 -Z /dev/sr0=game.iso`
   - Burn the ISO as an image. Don't add it as a file to a data disc.
   - If you're stuck with DVD+R, set the book type to DVD-ROM (ImgBurn has a book type button in the lower right of the write screen). Your burner has to support bitsetting for this to work.
5. **Play it.** Put the burned disc in the Wii, open USB Loader GX, and click the disc icon in the top right. The Disc Channel won't boot burned discs on its own.

## If it doesn't boot

- Make sure d2x cIOS installed. If USB Loader GX can't load the disc from the disc button, nothing else will, and the drive is the likely problem.
- Burn again at a slower speed on better media.
- Old loaders like NeoGamma work for older games but fail on many newer ones. Stick with USB Loader GX.

Honest recommendation: unless you specifically want discs, USB loading is simpler. It works on every Wii, loads faster and doesn't wear out the drive.
