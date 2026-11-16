# Research notes

Notes collected while writing [README.md](README.md), November 2026. Most of this is community knowledge from forums and hobbyist wikis, not official documentation. Where sources disagree, I say so.

## Why some Wiis can read DVD-R and others can't

Early in development the Wii was going to play DVD movies, so the early drives kept a standard DVD read mode. Softmod loaders and drive-key modchips both use that mode to read burned discs.

Nintendo removed it in the D3-2 revision (around mid-2009). The D3-2 can't read burned DVD±R at all, and modchips don't fix it according to most posts. The D4 and the Wii U drive behave the same way. The only fixes are USB loading or a drive swap.

Drive history:

- **DMS, D2A.** Launch drives.
- **D2B.** Early 2007.
- **D2C, D2C2.** D2C broke every modchip of its time. D2C2 (May 2008) looks identical but needed updated chips.
- **D2E.** Late 2008, similar to D2C2.
- **D3 ("D2Nothing").** December 2008. Merged the D2x chip into the main controller and removed the debug port that modchips used. Still has DVD mode, so burned discs work.
- **D3-2.** Looks like D3, but the top of the big chip is blank. No DVD mode.
- **D4.** Later revision, also no DVD mode.

How to identify the drive:

- Open the console. The first line on the drive chip starts with `GC2` or `GC2R` and ends with the revision (D2A, D2B, ...).
- Approximate D3-2 serial cutoffs from AfterDawn: US/Canada LU7012xxxxx and LU3941xxxxx, EU LEF1128xxxxx, UK LEH2682xxxxx, AU LAH1167xxxxx. One GBAtemp post gave "Lah1162" for AU, so treat the numbers as rough.
- Any black, red or blue Wii, and every RVL-101 and RVL-201, has a D3-2 or newer. One forum post says a `C/RVL-...-1` label means a newer drive.
- wiidrives.com has a serial lookup and drive list.
- Repaired consoles may have a different drive than the serial suggests.

## Media and burning

- WiiBrew's DVDx page recommends DVD-R because its reflectivity is closest to a pressed DVD. Cheap brands can fail, and drives differ.
- DVD+R and DVD+RW need book type set to DVD-ROM for software loading. That requires a burner that supports bitsetting. With a modchip, DVD+R reportedly works without it.
- Avoid RW discs.
- Forum consensus on speed is 4x maximum. Several posts say 2x.
- Verbatim gets recommended most often.

## Software

- **wii.hacks.guide.** The current softmod guide. Exploits by console: Wilbrand (SD card) or str2hax (no SD) on a regular Wii, Bluebomb on Wii mini, a Wii U-side exploit for vWii, and ModMii for a guided Windows setup. HackMii installs the Homebrew Channel and BootMii, then Priiloader and d2x cIOS. The guide warns against cIOS packs like DARKCORP.
- **d2x cIOS.** Patched IOS that lets loaders access the DVD drive and USB.
- **USB Loader GX.** Main recommendation. Disc icon in the top right launches a burned disc.
- **NeoGamma R9.** Older disc loader. Works for older games, fails on many newer ones.
- **uLoader 5.1x.** Can read a burned disc from an external USB DVD drive, which gets around the D3-2 limit. Old and less maintained.
- **CleanRip.** Dumps your own Wii and GameCube discs to ISO.

## Sources

- [wii.hacks.guide](https://wii.hacks.guide/) and its [Get Started page](https://wii.hacks.guide/get-started)
- [WiiBrew: DVDx](http://wiibrew.org/wiki/DVDx)
- [ConsoleMods: Wii modchips](https://consolemods.org/wiki/Wii:Modchips)
- [List of Wii drivechips (fandom)](https://list.fandom.com/wiki/List_of_Wii_drivechips)
- [wiidrives.com how-to](https://www.wiidrives.com/howto)
- [AfterDawn: new black Wii drives can't read DVD-R](https://forums.afterdawn.com/threads/possible-that-new-black-wii-drives-can-not-read-dvd-r.713257/)
- [GBAtemp: Help burning Wii games on DVD+R](https://gbatemp.net/threads/help-burning-wii-games-on-dvd-r.424479/)
- [GBAtemp: How to create a DVD game Wii backup](https://gbatemp.net/threads/how-to-create-a-dvd-game-wii-backup.578992/)
- [GBAtemp: Copying a Wii disc to DVD-R for another Wii](https://gbatemp.net/threads/how-can-i-copy-a-wii-game-disc-onto-a-dvd-r-to-be-played-on-another-wii.666354/)
- [GBAtemp: Burning a Wii ISO for vWii](https://gbatemp.net/threads/how-to-burn-a-wii-iso-onto-a-dvd-and-play-it-on-your-vwii.665050/)
- [GBAtemp: Black Wii D3-2 replacement](https://gbatemp.net/threads/black-wii-d3-2-replacement.198265/)
- [GBAtemp: Wii with D3-2 drive](https://gbatemp.net/threads/wii-with-d3-2-drive.184571/)
- [GBAtemp: How can I check if my Wii can read DVD-R backups?](https://gbatemp.net/threads/how-can-i-check-if-my-wii-can-read-dvd-r-backups.527315/)
- [GBAtemp: Wii DVD support from Disc Channel](https://gbatemp.net/threads/wii-dvd-support-from-disc-channel.587206/page-4)
