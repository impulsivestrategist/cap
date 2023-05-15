# cap 0.1 
> cap: a better scrot cli screencapture utility for macOS

Running `cap` will screenshot, auto-convert from .png to .jpg, scrub EXIF metadata from the .jpg, delete the original .png, and open your screenshots folder in Finder.

Default Mac screenshots have ridiculous file sizes (4-9 MB). This conversion drastically reduces file size (~75%).

This makes sharing to 4chan (max 4 MB upload), Discord (max 8 MB), and other places with size limits possible, with the added benefit of saving disk space.
There is no visual difference in image quality after conversion.

#### Dependencies:
Install `imagemagick` and `exiftool` via brew. These are free and open-source packages you'll find useful outside of the context of cap as well.
Remove '.txt' from this file and make it executable with 'chmod +x /path/to/cap'. Then move 'cap' to /usr/local/sbin


Your screenshots will no longer appear on your Desktop when using the default shortcut. To restore this, change the save location in the native Screenshot app, or run 'defaults write com.apple.screencapture location ~/Desktop'



```if [[ $1 == "--help" || $1 == "-h" ]]; then
    echo "Usage: $(basename "$0") [options]"
    echo ""
    echo "Options:"
    echo "  --help, -h     cap requires imagemagick and exiftool to convert your screenshots and remove their metadata respectively. Please run 'brew install imagemagick exiftool' for cap to function properly."
    exit 0
fi
now=$(date +"%Y.%m.%d-%H.%M.%S")

mkdir -p ~/Pictures/screenshots
defaults write com.apple.screencapture location ~/Pictures/screenshots
# killall SystemUIServer (You may need to run this or simply reboot but I haven't needed to. "Works on my machine.")

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
rm ~/Pictures/screenshots/*_original```


