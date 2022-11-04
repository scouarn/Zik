# Zik Music Player
A simple shell script music player that does what I want my ideal music player to do.
It is meant to be run in the background and interacted with using a `dmenu` interface and `notify-send` alerts. The menu can be easily opened by binding the script to a key.
When a song starts playing, the album cover art is set as the wallpaper.

# Requirements
| Program  | Why               |
|----------|-------------------|
| `dmenu`  | Prompts           |
| `ffmpeg` | Extract art       |
| `feh`    | Set wallpaper     |
| `ffplay` | Play the audio    |

# Quick start
Prompt the options (see [Usage](#usage)) with `dmenu`:
```
zik
```

By default the `Menu` option finds (recursively) all audio files (`wav, mp3, flac, ogg`) in the `$HOME/Music` directory. This can be configured by changing variables in the script.


# Installation
Put it somewhere in your path. 

# Usage
```
Usage: zik [--help|add <file>|menu|skip|pause|resume|stop|reset]
    Without any option, a dmenu prompt is opened to choose one.

    Options:
    --help      Print this message.
    add <file>  Add the file to the queue.
    menu        Open a menu to choose a file to add..
    skip        Go to next in queue.
    pause       Pause playback.
    resume      Resume playback.
    stop        Empty the queue and stop playing.
    reset       Remove the fifo file (use if something went wrong)."
```

# How it works
The script can start a daemon (if it's not already running) that plays audio files from a queue materialized by a fifo/named pipe. By default it is created automatically as `$HOME/.zik/fifo`. You can interact with the queue using the script itself.
The skip/pause/resume commands work by sending the TERM/STOP/CONT signals respectively to the daemon's `ffplay` child. To stop the daemon, a TERM signal is sent which stops the audio playback cleanly. Again, you can use the script instead of sending kill commands manually.
For each song, the album cover art is extracted with `ffmpeg` or from the web and set as the wallpaper with `feh`. Then it is played by `ffplay` running with the `-nodisp` flag.
The images are cached in the `.zik/covers` directory.

# TODO
- refactor the cover finding stuff
- find the cover in the background
- "now playing" artist-album-title
- Chop file extension in selection menu
- Rewrite signal stuff with a command fifo running in parallel
- Force empty the queue when starting (?)
- Make it more configurable : MENU_PROMPT, WALLPAPER, PLAYER(?) vars
- Query command(s) to get the time from ffplay stdout / the artist, title, metadata...
- volume control ?
- `ZIK_PATH`(?) as env var / multiple paths `foo/bar:/home/bazz/music:another_dir/...`
- make it work with directories, adding ordered by track number
- blur the wallpaper so it's less ugly when resized from a square, see how [this guy](https://github.com/rstacruz/feh-blur-wallpaperu) does it with the gm program
- get the image from the web if not in the audio file by using ffmpeg to find the album name in the metadata
- add the artist and album to the search prompt (so it can be searched for more easily just in case)
- insert music to be played next : at the "wrong end" of the queue : cat into var, add the song, then readd everything back

