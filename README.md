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
Usage: zik [--help|add <file>|menu|skip|play|pause|stop]
    Without any option, a dmenu prompt is opened to choose one.

    Options:
    --help      Print this message.
    add <file>  Add <file> to the queue.
    menu        Open dmenu to choose a file to add.
    skip        Go to next in queue.
    pause       Pause playback.
    resume      Resume playback.
    stop        Empty the queue and stop playing.
    reset       Remove the fifo file (use if something went wrong).
```

# How it works
One "main instance" of the script can exist at a time, it plays audio files from a queue materialized by a fifo/named pipe. By default it is created automatically as `/tmp/zik/fifo`. You can interact with the queue using the script itself, if the "main instance" isn't already running then it becomes it.
The skip/pause/resume/stop commands are sent to the "main instance" with the INT/USR1 or TSTP/CONT/TERM signals respectively. Again, you can use the script instead of sending kill commands manually.
For each song, the album cover art is extracted with `ffmpeg` and set as the wallpaper with `feh`. Then it is played by `ffplay` running with the `-nodisp` flag.


# TODO
- Chop file extension in selection menu
- Rewrite signal stuff with a command fifo running in parallel
- Move /tmp/zik to home dir
- Force empty the queue when starting (?)
- Make it more configurable : MENU_PROMPT, WALLPAPER, PLAYER(?) vars
- Clean the option parsing
- Query command(s) to get the time from ffplay stdout / the artist, title, metadata...
- volume control ?
- `ZIK_PATH` as env var / multiple paths `foo/bar:/home/bazz/music:another_dir/...`
- make it work with directories, adding ordered by track number
- blur the wallpaper so it's less ugly when resized from a square, see how [this guy](https://github.com/rstacruz/feh-blur-wallpaperu) does it with the gm program
- get the image from the web if not in the audio file by using ffmpeg to find the album name in the metadata
- cache images like that : `/tmp/zik/artist-album.jpg`
- add the artist and album to the search prompt (so it can be searched for more easily just in case)
- insert music to be played next : at the "wrong end" of the queue

