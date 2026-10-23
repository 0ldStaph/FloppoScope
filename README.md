# FloppoScope

**See what's on the disk.** FloppoScope is a single-file, browser-based analyzer for floppy disk images. Open an image (or a whole folder of them) to inspect the boot record, allocation tables, directory tree and raw sectors, without mounting anything.

Everything runs locally in your browser. Nothing is uploaded, and your images are never modified.

**Try it:** https://0ldstaph.github.io/FloppoScope/

Or download `index.html` and open it in any modern browser. No server, install or build step is needed.

## Features

* **Disk layout map.** Every sector is drawn on a per-side disk view, with zoom, pan, box zoom and single-side mode. Select a sector to see its bytes in a hex view and its cylinder/head/sector position.
<p align="center">
  <img src="assets/disk_layout_only.png">
</p>

![Disk layout zoom in](assets/disk_layout_zoom_in.png)
![Disk geometry](assets/disk_geometry.png)

* **Image library.** Open a folder to scan many images at once, with filtering, sorting, export and a library report.

![Image folder analyzed](assets/image_folder_analyzed.png)

* **Filesystem browsing.** Browse the file tree, preview files, inspect FAT cluster chains and the boot-code inspector, and export selected files as a ZIP.
  
![Filesystem explorer](assets/filesystem_explorer.png)

* **Bulk conversion.** Convert images to IMG, IMD, HFE or Extended CPC DSK, export VZ200/VZ300 disks as VZ DSK, or extract all files into one folder per disk. Output goes to ZIP or straight to a folder, with an optional manifest and SHA-256 checksums.
<p align="center">
  <img src="assets/bulk_export.png" alt="Bulk conversion">
</p>

* **Reports.** Export an analysis report for the open image or for a whole library.
<p align="center">
  <img src="assets/analysis_report.png" alt="Analysis report">
</p>
  
* **Flux and container decoding.** Reads KryoFlux streams, SCP, HFE, IMD, TD0, WOZ, NIB, G64, Amstrad CPC / Extended CPC DSK and similar containers, and rebuilds sector images from them. DSK, IMD and KryoFlux files get a one-line summary in the image library.
<p align="center">
  <img src="assets/disk_image_found_issues.png" alt="Found issues">
</p>

* **VZ200/VZ300 disks.** Reads VTech Laser 200/210/310 and Dick Smith VZ200/VZ300 DOS disk images (.dsk, .dvz), checks every sector checksum, and can write them back out as VZ DSK files. See [VZ200/VZ300 disk images](#vz200vz300-disk-images) below.
* **Unmapped and deleted data.** Finds non-blank sectors that no live file or folder owns, separates deleted-file remnants from other unmapped data, and lets you step through them and export them.


## Supported formats

| Area | Details |
| --- | --- |
| Filesystems | FAT12/16/32 (PC), Amiga OFS/FFS, Apple DOS 3.3 and ProDOS, Macintosh, Commodore (D64 and relatives), Atari DOS, ZX Spectrum TR-DOS, BBC Micro DFS, SysV and SCO UNIX, NeXTSTEP UFS, DEC Files-11 ODS-1 (RSX-11), Sharp MZ Disk BASIC, Sharp MZ-800 Personal CP/M-80, Sharp MZF/MZT tape files, VTech Laser 200/210/310 and Dick Smith VZ200/VZ300 DOS |
| Image and flux files | .img .ima .dsk .bin .d64 .d71 .d81 .adf .atr .xfd .st .msa .po .do .2mg .hdv .trd .ssd .dsd .msx .mdi .woz .nib .g64 .dms .ipf .hfe .scp .raw .imd .td0 .fdi .mzf .mzt .dvz |

Support depends on what the image actually contains, so some images may only be partly decoded. The unmapped-data scan applies to FAT sector images only.

## VZ200/VZ300 disk images

VZ DOS disks are 40 tracks of 16 sectors on one side. Each sector holds 128 bytes: 126 bytes of data plus a 2-byte link to the next sector of the file. Sectors are written with a 3:1 interleave, and track 0 holds the directory and the sector map.

**Reading.** FloppoScope accepts these images in the layouts found in the wild and rebuilds a flat track/sector image from them:

* 98,560 bytes: 2,464-byte tracks, with no gap after each track. This is the early layout.
* 99,184 and 99,185 bytes: 2,480-byte tracks, where each track has padding after its 16 sectors.
* Files that were damaged in transfer. Sectors with a bad checksum are retried with extra CR bytes removed, which repairs images that went through a text-mode copy (for example 98,627-byte .DVZ files). Files whose sector links were overwritten are read in directory order instead.

Every sector checksum is checked, and anything missing or failing is listed in the found-issues report.

**Writing.** Use the VZ DSK options in the bulk export dialog, or the buttons under the loaded-disk summary. Both layouts are rebuilt from the decoded sectors:

| Option | Size | Layout |
| --- | --- | --- |
| VZ DSK (corrected) | 99,185 bytes | 2,480-byte tracks, 5 and 6 sync bytes around the ID field, as in freshly formatted disks |
| VZ DSK (early) | 98,560 bytes | 2,464-byte tracks, 6 and 5 sync bytes, no padding |

Things to know before you use the exported files:

* There is no single agreed VZ DSK layout. The VZEM (early and late) and DSVZ200/WINVZ300 emulators each wrote slightly different files, and the gap lengths inside a real image can differ from sector to sector. The two options above are the only ones FloppoScope writes.
* Exports are built from the decoded sector contents, so gap bytes of a damaged or hand-edited image are not copied. A 99,184-byte source therefore exports as 99,185 bytes. The sector data is identical.
* Missing sectors are written as zeros, and sectors with a bad checksum are written with a freshly calculated one. The export note lists how many were affected.
* Converting between the two layouts is lossless for the sector data, but test the result in the emulator or drive you plan to use.
