river
=====

A personal music server

What is River?
--------------

> When I was on Plan 9, everything was connected and uniform. Now everything
> isn't connected, just connected to the cloud, which isn't the same thing.

― [Rob Pike](http://usesthis.com/interviews/rob.pike/)

Streaming music services have rapidly become most people's preferred way of
listening to music. No local storage is required, and users never need to
synchronize all the discrete copies of their music collection, enabling people
to listen to more music more easily. However, there are disadvantages to the
streaming model: usually, users give up their ownership of the music; their
access is entirely dictated by the companies running the services, and they are
usually required to choose between paying a monthly fee, sitting through
intrusive advertisements, limiting their control of what music is played, or
all of the above. However, these issues aren't inherent to the streaming model.
Rather, they are inherent to the centralized, proprietary service model.

Up until now, I've continued to use the old-fashioned copy-and-sync method,
because I didn't want to give any money to the companies doing the nasty things
listed above. I prefer to own my music; I prefer to listen to whatever music I
can purchase, rather than only the music that record companies have chosen to
license to streaming services; I prefer to listen to music encoded using modern,
high-quality codecs (or at least the most optimized settings of poorer codecs);
finally, I prefer to avoid software that doesn't give me enough freedom.

I decided that I wanted a program that I could run on my home computer, that
I could access from my mobile devices, that would allow me to play my music
collection over the Internet. After doing some research on free software that
had this sort of functionality, I couldn't find anything that really matched
what I was looking for, so I decided I'd try to write the software myself.

Goals
-----
*   River should be a simple, straightforward program that streams users' music
    libraries.
*   River should integrate with existing music libraries. River should convert
    source files to streaming-friendly formats like Ogg Opus or MP3 on-the-fly,
    or stream them directly if the files are already in one of these formats.
    If on-the-fly conversion proves to be impractical, River should create
    streaming-friendly encodes of files, and synchronize additions, deletions,
    and metadata changes with the source library.
*   River should have a simple, extensible API for accessing and modifying the
    library. This API should make creating a frontend straightforward and easy.
*   River should include a reference browser-based frontend with a simple UI.
*   River should have strong, secure authentication, so that unauthorized users
    cannot access libraries.
*   River should be entirely free and open source software.
*   River should be easy to use for anyone. It will likely require a small
    amount of mandatory configuration (e.g. the location of the source library
    and the port to listen on), but it should have sane defaults. New
    users should be able to simply run the program and perform any configuration
    through the frontend UI. Configuration should also be possible by directly
    editing a configuration file. Configuration options should include:

    * The location of the source library
    * The port to listen on
    * The quality/compression level of streaming files

I'm using Go because:

*   It has good concurrency, which will be good for dealing with multiple
    simultaneous users.
*   The standard library has a very complete and useful HTTP package.
*   It can run C code natively, which will be necessary for transcoding and reading audio files using `libav*`.

Running
-------

### FFmpeg/LibAV

River calls `ffmpeg`/`avconv` and `ffprobe`/`avprobe` to transcode and read
audio files. If your operating system has a package manager, look for a package
called `ffmpeg` or `libav-tools` and install it. Otherwise, download an FFmpeg
build from [the official website](https://www.ffmpeg.org/download.html), and
either copy the `ffmpeg` and `ffprobe` executables to this directory, somewhere
in your system's PATH, or add the location of the executables to your system's
PATH. Windows and OS X releases should come with the executables bundled.

Hopefully, in the future, River will call `libav*` directly rather than running
executables.

### Configuration

River has the following options (listed here in the form
`option` [`default`]: description):

`library`: the music library to read from

`port` [`21313`]: the port to listen on

`config` [`config.json`]: the configuration file

All of River's options can be specified in the configuration file, which should
contain a JSON object with keys mapping to River options. Options specified via
flags override those specified via the configuration file.
