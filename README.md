# cap 0.1 
>cap: a better scrot cli screencapture utility for macOS

Running `cap` will screenshot, auto-convert from .png to .jpg, scrub EXIF metadata from the .jpg, delete the original .png, and open your screenshots folder in Finder.

Default Mac screenshots have ridiculous file sizes (4-9 MB). This conversion drastically reduces file size (~75%).

This makes sharing to 4chan (max 4 MB upload), Discord (max 8 MB), and other places with size limits possible, with the added benefit of saving disk space.
There is no visual difference in image quality after conversion.

https://github.com/impulsivestrategist/cap/assets/72365890/5c16c6d0-367b-4e0b-96ee-7d9f60b7db16

### Warnings
* Your screenshots will no longer appear on your Desktop when using the default shortcut. To restore this, change the save location in the native Screenshot app, or run `defaults write com.apple.screencapture location ~/Desktop`. 
* Your original screenshots are simply moved to the trash, not deleted. If you take a lot of screenshots, I'd advise ticking the 'Remove items from the Trash after 30 days' box in Finder preferences.

### Dependencies
Install `imagemagick` and `exiftool` via brew. These are free and open-source packages you'll find useful outside of the context of cap as well. Eventually I'll have this in a tap and you'll be able to just run 'brew install cap' and it will automatically install these dependencies. But for now, copy the script below and save it as 'cap'. Make it executable with `chmod +x /path/to/cap`. Then move 'cap' to /usr/local/sbin. `mv /path/to/cap /usr/local/sbin`

```
if [[ $1 == "--help" || $1 == "-h" ]]; then
    echo "Usage: $(basename "$0") [options]"
    echo ""
    echo "Options:"
    echo "  --help, -h     cap requires imagemagick and exiftool to convert your screenshots and remove their metadata respectively. Please run 'brew install imagemagick exiftool' for cap to function properly."
    exit 0
fi
now=$(date +"%Y.%m.%d-%H.%M.%S")
mkdir -p ~/Pictures/screenshots
defaults write com.apple.screencapture location ~/Pictures/screenshots
#killall SystemUIServer (You may need to run this or simply reboot but I haven't needed to. "Works on my machine.")
printf 'capturing screen in 3... '
sleep 1
printf '2... '
sleep 1
printf '1... '
sleep 1
echo ''

screencapture ~/Pictures/screenshots/${now}.png
echo 'screen captured in ~/Pictures/screenshots/'
echo 'opening ~/Pictures/screenshots/...'
open ~/Pictures/screenshots/
convert ~/Pictures/screenshots/*.png -set filename:base "%[basename]" ~/Pictures/screenshots/"%[filename:base].jpg"
mv ~/Pictures/screenshots/*.png ~/.Trash/
exiftool -q -q -all= ~/Pictures/screenshots/*
rm ~/Pictures/screenshots/*_original
```

