# FloppoScope

**See what's on the disk.** FloppoScope is a single-file, browser-based analyzer for floppy disk images. Open an image (or a whole folder of them) to inspect the boot record, allocation tables, directory tree and raw sectors, without mounting anything.

Everything runs locally in your browser. Nothing is uploaded, and your images are never modified.

**Try it:** https://0ldstaph.github.io/FloppoScope/

Or download `index.html` and open it in any modern browser. No server, install or build step is needed.

## Features

* **Disk layout map.** Every sector is drawn on a per-side disk view, with zoom, pan, box zoom and single-side mode. Select a sector to see its bytes in a hex view and its cylinder/head/sector position. Flux images show sectors at their real positions. Unreadable sectors are drawn hatched red and checksum errors solid red, and "Show sectors" dims everything but errors or unmapped data.
<p align="center">
  <img src="assets/disk_layout.png">
</p>

![Disk layout zoom in](assets/disk_layout_zoom_in.png)
![Disk geometry](assets/disk_geometry_hfs.png)

* **Image library.** Open a folder to scan many images at once, with filtering, sorting, export and a library report.

![Image folder analyzed](assets/image_folder_analyzed.png)

* **Filesystem browsing.** Browse the file tree, preview files (text, common and retro images such as PCX/TGA/TIFF/IFF/ICO/PICT, PDF, RTF, and ZIP/TAR/GZ/BZ2/XZ/LZMA/Z/ARC/LZH/StuffIt archives), inspect FAT cluster chains and the boot-code inspector, and export selected files as a ZIP. Archives behave like folders (archives over 32 MiB open on first click instead), so they appear in the folder tree and file list right away, and nested archives open the same way, and you can tick any files or folders (or the archive itself) to export them with the usual Export button, or use Download file to get the archive as it is.
  
![Filesystem explorer](assets/filesystem_explorer_hfs.png)

* **Bulk conversion.** Convert images to IMG, IMD, HFE, Extended CPC DSK or Apple Disk Copy 4.2 (DC42), export VZ200/VZ300 disks as VZ DSK, or extract all files into one folder per disk. Output goes to ZIP or straight to a folder, with an optional manifest and SHA-256 checksums.
<p align="center">
  <img src="assets/bulk_export.png" alt="Bulk conversion">
</p>

* **Reports.** Export an analysis report for the open image or for a whole library.
<p align="center">
  <img src="assets/analysis_report.png" alt="Analysis report">
</p>
  
* **Flux and container decoding.** Reads KryoFlux streams, SCP, A2R, HFE, IMD, TD0, WOZ, NIB, G64, Amstrad CPC / Extended CPC DSK and similar containers, and rebuilds sector images from them, including a copy-protection and read-error scan. MSA, ATR, XFD, 2IMG, DDI (DiskDupe), IMZ (zipped IMA) and Disk Copy 4.2 wrappers are unwrapped automatically. DSK, IMD and KryoFlux files get a one-line summary in the image library.
<p align="center">
  <img src="assets/disk_image_found_issues.png" alt="Found issues">
</p>

* **Unmapped and deleted data.** Finds non-blank sectors that no live file or folder owns, separates deleted-file remnants from other unmapped data, and lets you step through them and export them.

## Supported formats


| Area                    | Details                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Filesystems             | FAT12/16/32 (PC, Atari ST, MSX-DOS), Amiga OFS/FFS, Apple DOS 3.3 and ProDOS, Apple Lisa PREBOOT, Macintosh MFS/HFS, Commodore (D64/D71/D81), Atari DOS 2, ZX Spectrum TR-DOS, BBC Micro DFS, Thomson TO/MO DOS, CP/M (Kaypro, 8-inch, Sharp MZ-800), SunOS Bar/Tar, SCO UNIX System V, NeXTSTEP UFS, DEC Files-11 ODS-1 (RSX-11), GenRad 2620, Sharp MZ Disk BASIC, Sharp MZF/MZT tape files, VTech Laser / Dick Smith VZ200/VZ300 DOS |
| Image and flux files    | .img .ima .imz .dsk .dvz .bin .d64 .d71 .d81 .adf .atr .xfd .st .msa .ddi .po .do .2mg .2img .hdv .trd .ssd .dsd .msx .mdi .woz .a2r .nib .g64 .scp .raw .hfe .imd .td0 .fd .dc42 .dc .image .mzf .mzt                                                                                                                                                                                                                                  |
| Recognised, not decoded | .ipf .dms .fdi                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Preview: images         | PCX, Targa, TIFF (strips or tiles, planar or chunky; uncompressed, PackBits, LZW, Deflate, JPEG), IFF ILBM/PBM, ICO/CUR, Mac PICT v2 (bitmap and QuickTime-JPEG pictures; vector drawing is skipped), SVG, PDF and RTF (text only)                                                                                                                                                                                                      |
| Preview: archives       | ZIP, TAR, GZIP, bzip2, XZ, LZMA, compress (.Z), also as tar.\* ; ARC; LHA/LZH (-lh0- to -lh7-); StuffIt classic)                                                                                                                                                                                                                                                                                                                        |
| Preview: not decoded    | ZIP Zstandard/PPMd and encrypted entries; ARC methods 5-7, 10, 11; LHA -lh1-/-lh2-/-lh3-/-lzs-; StuffIt LZAH, FixedHuffman, Installer, method 13/14, Arsenic (15) and StuffIt 5 archives; CCITT fax, old-style JPEG and JPEG 2000 TIFFs; progressive JPEG inside TIFF/PICT. These are listed and open as hex with an explanation.                                                                                                       |

Support depends on what the image actually contains, so some images may only be partly decoded. The unmapped-data scan applies to FAT sector images only. Sectors filled with the `-=[BAD SECTOR]=-` marker are shown as unreadable.