## wsdl (Workshop DL)

Download Steam Workshop addons as compressed backups, with their preview images.

`wsdl` takes Steam Workshop IDs or item URLs, backs up each addon’s `.gma` file as a verified `.xz` archive, and saves its preview image. If the addon is already in your local Steam cache, it’s used directly. Otherwise, it’s downloaded with SteamCMD.

### Features

*   Accepts IDs or full URLs (`...?id=NNNNN`)
*   Uses the local Steam cache when possible, and SteamCMD otherwise
*   Prompts for Steam login only when SteamCMD requires it
*   Stores each backup as a `.gma.xz`, checked with `xz -t` before the uncompressed copy is removed
*   Saves the preview as JPEG by default, or AVIF with `-a`
*   Gets preview images from Steam’s public API, so sign-in-only pages still work
*   Offers to delete SteamCMD downloads, one item at a time, defaulting to No

### Usage

sh

```
wsdl 3842833548
wsdl 'https://steamcommunity.com/sharedfiles/filedetails/?id=3842833548'
wsdl -a -u myaccount 3842833548 1234567890
cat ids.txt | wsdl -j 8
```

Run `wsdl -h` for all options.

### Requirements

`curl`, `grep` (GNU, with `-P`), `xargs`, `file`, `xz`. Also `avifenc` for `-a`, and `steamcmd` for items not in the local cache. ImageMagick (`magick` or `convert`) is optional and converts non-JPEG previews to JPEG.

### Output

For each item, in `./ID/`:

```
ID/
├── ID.jpg              # preview (ID.avif with -a)
└── <name>.gma.xz       # verified backup
```
