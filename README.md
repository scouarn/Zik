# Zik Music Player
A simple shell script music player that does what I want my ideal music player
to do. It is meant to be run in the background and interacted with using a
`dmenu` interface and `notify-send` alerts. The menu can be easily opened by
binding the script to a key. When a song starts playing, the album cover art is
set as the wallpaper.

# Requirements
| Program   | Why                      |
|-----------|--------------------------|
| `dmenu`   | Prompts                  |
| `ffmpeg`  | Extract art and metadata |
| `ffprobe` | Video channel resolution |
| `ffplay`  | Playback audio           |
| `wget`    | Fetch images             |
| `feh`     | Set wallpaper            |

# Quick start
Prompt the options (see [Usage](#usage)) with `dmenu`:
```
zik
```

By default the `Menu` option finds (recursively) all audio files (`wav, mp3,
flac, ogg`) in the `$HOME/Music` directory. This can be configured by changing
variables in the script.


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

## Queue
The script can start a daemon (if it's not already running) to plays audio
files from a queue with `ffplay -nodisp`. The queue is materialized by a
fifo/named pipe which holds file names, by default it is created automatically
as `$HOME/.zik/fifo`. You can interact with the queue using the script itself.

## Controls
The skip/pause/resume commands work by sending the TERM/STOP/CONT signals
respectively to the daemon's `ffplay` child. To stop the daemon, a TERM signal
is sent which stops the audio playback cleanly. Again, you can use the script
instead of sending kill commands manually.

## Cover art
When playing a song the album cover art is fetched. First it looks in the cache
in `.zik/covers`.  Then if there's a large enough (`MIN_WIDTH` variable)
picture in the audio file it is extracted with `ffmpeg`.  Otherwize the
[MusicBrainz](https://musicbrainz.org/) and [Cover Art
Archive](http://coverartarchive.org/) APIs are used to find the image.  Finally
the wallpaper is set with `feh`

# TODO
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

