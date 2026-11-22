# wsdl

Back up Steam Workshop addons as verified `.xz` archives, with their preview images.

`wsdl` takes Steam Workshop IDs or item URLs. For each one, it finds the addon's `.gma` file, either in your local Steam cache or by downloading it with SteamCMD. It then compresses the file into a checked archive and saves the preview image. The default game is Garry's Mod (app ID `4000`), and you can pick another with `-g`.

## Features

- Accepts Workshop IDs or full URLs (`...?id=NNNNNNNNNN`)
- Uses the local Steam cache first, and SteamCMD only when needed
- Prompts for Steam login only when SteamCMD requires it
- Archives are named `ID_YYYYMMDD.gma.xz`, using the item's Steam update date
- Each archive is checked with `xz -t` before the uncompressed copy is removed
- Preview images are saved as JPEG by default, or AVIF with `-a`
- Previews come from Steam's public item-details API, so sign-in-only pages still work
- SteamCMD downloads are deleted once their archive is verified (use `-k` to keep them)
- Parallel processing, retries, and resumable runs (finished items are skipped)

## Requirements

- `curl`, `grep` (GNU, with `-P` support), `xargs`, `file`, `xz`
- `avifenc` (only with `-a`)
- `steamcmd` (only for items not already in the local Steam cache)
- ImageMagick (`magick` or `convert`, optional): converts non-JPEG previews to JPEG, and reads formats `avifenc` can't

## Installation

```sh
chmod +x wsdl
sudo mv wsdl /usr/local/bin/wsdl
```

Or run it from the folder you downloaded it to, as `./wsdl`.

## Usage

```sh
wsdl 1234567890
wsdl 'https://steamcommunity.com/sharedfiles/filedetails/?id=1234567890'
wsdl -o ./backups 1234567890 2345678901
cat ids.txt | wsdl -o ./backups -j 8
wsdl -g 730 -u your_steam_user 3456789012
```

Quote URLs in the shell, because `?` and `&` have special meanings there.

Run `wsdl -h` for the full option list.

## Options

| Option | Description | Default |
| --- | --- | --- |
| `-o DIR` | Output directory | current directory |
| `-f` | Put all files directly in the output directory, not one folder per ID | off |
| `-g APPID` | Steam app ID of the game | `4000` (Garry's Mod) |
| `-u USER` | Steam account for SteamCMD | try anonymous, then prompt if login is required |
| `-a` | Save the preview as AVIF (`ID.avif`) instead of JPEG | off |
| `-k` | Keep SteamCMD downloads after archiving | off |
| `-j JOBS` | Parallel jobs for archive and image work | `4` |
| `-r RETRIES` | Attempts for downloads and compression | `3` |
| `-q QUALITY` | AVIF quality, 0-100 (with `-a`) | `52` |
| `-s SPEED` | AVIF speed, 0 (slow) to 10 (fast) (with `-a`) | `6` |
| `-c PATH` | `steamcmd` binary | `steamcmd` |
| `-d DIR` | SteamCMD download directory | `~/.local/share/wsdl/steamcmd` |
| `-w DIR` | Steam client Workshop cache for the app | `~/.local/share/Steam/steamapps/workshop/content/APPID` |
| `-h`, `--help` | Show help | |

## Output

By default, each item gets its own folder:

```
1234567890/
├── 1234567890.jpg
└── 1234567890_20260305.gma.xz
```

With `-f`, all files go into the output folder directly:

```
backups/
├── 1234567890.jpg
└── 1234567890_20260305.gma.xz
```

The archive date is the item's last update on Steam, in UTC.

## How it works

For each item:

1. If the `.gma` is already in the local Steam cache, or in the SteamCMD folder, it's backed up. Otherwise, the item is downloaded with SteamCMD.
2. The backup is compressed to `ID_YYYYMMDD.gma.xz` and checked with `xz -t`.
3. If the check passes, the uncompressed backup is removed. If it fails, the backup is kept.
4. The preview image is downloaded as `ID.jpg` (or `ID.avif` with `-a`).
5. If the archive is verified, the SteamCMD download is deleted (unless `-k` is set).

Steam client files and existing archives are never modified or deleted.

## SteamCMD login

Items are first tried anonymously. If SteamCMD reports that login is required, `wsdl` asks for your Steam username, and SteamCMD then prompts for your password and any Steam Guard code. Passwords are entered into SteamCMD directly and never pass through `wsdl`. Logging in saves a token, so later runs usually don't ask again.

Downloading Workshop items requires an account that owns the game. If a download fails with `Failure` while logged in, check that the account owns the game and that the item is public. Steam can also refuse downloads temporarily, so retrying later often helps.

## Notes

- Preview images come from Steam's public API. If that fails, the script falls back to the item page's `og:image` tag.
- Each item's `.gma` is archived from a copy, so the Steam client's own files are left unchanged.
- Run `wsdl` only on Workshop content you have the right to download and store.

## License

Specify a license before publishing.
