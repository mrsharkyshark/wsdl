wsdl

wsdl (Workshop DL) is a lightweight POSIX shell script for downloading and backing up Steam Workshop addons.

It backs up .gma files, compresses them with XZ, verifies archives, and downloads preview images in JPEG or AVIF format.

Features
Backs up .gma files from the local Steam cache.
Downloads missing addons using SteamCMD.
Supports anonymous and authenticated downloads.
Compresses and verifies backups with XZ.
Downloads Workshop preview images.
Supports JPEG and AVIF formats.
Parallel processing and automatic retries.
Optional SteamCMD download cleanup.
Requirements
curl
GNU grep (with -P support)
xargs
file
xz
SteamCMD (only when downloading missing local files)

Optional:

avifenc (for AVIF conversion)
ImageMagick (magick or convert) for image conversion.
Usage
sh
./wsdl [options] [Workshop ID or URL ...]


You can also provide IDs through standard input:

sh
cat ids.txt | ./wsdl

Examples

Download and back up an addon:

sh
./wsdl 3842833548


Using a Workshop URL:

sh
./wsdl 'https://steamcommunity.com/sharedfiles/filedetails/?id=3842833548'


Save the preview as AVIF:

sh
./wsdl -a 3842833548


Use multiple parallel workers:

sh
./wsdl -j 8 3842833548 1234567890


Specify a Steam account:

sh
./wsdl -u myaccount 3842833548

Options
Option	Description
-u USER	Steam account username
-c PATH	SteamCMD binary path
-d DIR	SteamCMD download directory
-w DIR	Steam Workshop cache directory
-j JOBS	Number of parallel workers
-r RETRIES	Download and compression retries
-q QUALITY	AVIF quality (0–100)
-a	Save preview images as AVIF
-s SPEED	AVIF encoding speed (0–10)
-h	Show help
Output

Each Workshop item gets its own directory:

text
3842833548/
├── addon.gma.xz
└── 3842833548.jpg


With -a:

text
3842833548/
├── addon.gma.xz
└── 3842833548.avif


Archives are verified before the original uncompressed backup is removed.

SteamCMD Authentication

Downloads are attempted anonymously first.

If authentication is required, wsdl prompts for your Steam username and lets SteamCMD handle your password and Steam Guard verification.

SteamCMD may save an authentication token for future sessions.

Notes
Existing backups and preview images are skipped.
Local Steam Workshop cache files are never deleted.
SteamCMD downloads can optionally be deleted after a verified backup.
Failed archive verification preserves the original .gma file.
License

See LICENSE for license information.
