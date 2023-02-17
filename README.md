# bingewatch

*bingewatch* is a utility to automatically download the most recent videos from
any YouTube channel for offline viewing.

It is designed to be as simple as possible, wrapping `yt-dlp` for all the heavy
lifting and YouTube interaction.  What it adds is the ability to follow users
and channels in a straightforward, lightweight manner.

There is intentionally no database or interface.  Bingewatch just downloads new
videos and drops them into a directory per channel; that's it.  After that you
can do whatever you like with them.

## Setup

Bingewatch is not designed to be installed in the normal way.  It is a single,
self-contained Python script, intended to be directly copied into place and run.

The way I use this is to have an external USB drive containing the `binge`
script along with the configuration file and all my cached videos.  This drive
can then be plugged into any machine that has `yt-dlp` and `ffmpeg` available.
`binge` depends only on these two external commands and Python itself (which
must be available if `yt-dlp` is installed.)  Only libraries in the Python
standard library are used, so there's no need to install additional
dependencies.

## Use

### Adding channels

To add a channel for bingewatch to monitor, run:

```sh
$ ./binge add NAME URL
```

`NAME` is the name by which you want to refer to the channel.  Bingewatch will
create a subdirectory of this name in the current directory and store the
channel's videos and archive file there.

`URL` can be any of:

  - the URL of any YouTube video
  - the URL of a channel homepage
  - the URL of a user's homepage

All will have the same result; this allows you to just throw any video URL at
bingewatch and it will add the channel that the video is from.

If for some reason you already have the ID of the channel you're interested in
(a long string starting with `UC`) you can use that in place of the URL.

### Downloading

Just run `./binge` to download new videos.

Channels are processed in order; if you're short on time or space feel free to
edit `channels.txt` to prioritize those you're most interested in by moving
them higher up the file.

You can temporarily ignore a channel by commenting out its line with a leading
`#`

## Caveats

The YouTube channel RSS feeds only contain the most recent 15 videos.  If your
checking interval is longer than the time it takes the channel to post 15 new
videos, you could miss content.  In this case it's possible to use `yt-dlp`
directly in the channel directory; just make sure you use `--download-archive
seen.txt` to avoid duplication and update the archive file.

## Extras

[rclone](https://rclone.org) can be a convenient way to serve your cached
videos, allowing easy viewing on TVs, iPads, and other devices. 

To run a DLNA media server for your videos, try:

```sh
$ rclone serve dlna --read-only --dir-cache-time 0 --exclude '.*' --exclude '.*/' .
```

To make them available over HTTP:

```sh
$ rclone serve http --addr :8080 --read-only --dir-cache-time 0 --exclude '.*' --exclude '.*/' .
```
