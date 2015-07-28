river
=====

A personal music server

About
-----

River is a personal music streaming server that is designed to be an alternative
to proprietary streaming services. Users set up the River server on the computer
where they store their music library, and stream their songs via client
programs. Clients can be built by using the small JSON API provided by the
server. Songs in the user's library can be streamed in the Ogg Opus or MP3
formats (note that [all major browsers support at least one of these
formats](https://en.wikipedia.org/wiki/HTML5_Audio)). When a stream is
requested, songs are automatically transcoded on-the-fly to the requested
format, if necessary.

River integrates with existing music libraries, so setting up River streams your
music without interfering or conflicting with other tools. The server is
designed to be simple to set up for anyone with basic knowledge of the command
line who is capable of configuring dynamic DNS (or owns a static global IP).

Building
--------

    go build

### FFmpeg/LibAV

River calls `ffmpeg`/`avconv` and `ffprobe`/`avprobe` to transcode and read
audio files. If your operating system has a package manager, look for a package
called `ffmpeg` or `libav-tools` and install it. Otherwise, download an FFmpeg
build from [the official website](https://www.ffmpeg.org/download.html), and
either copy the `ffmpeg` and `ffprobe` executables to this directory, somewhere
in your system's PATH, or add the location of the executables to your system's
PATH. Windows and OS X releases should come with the executables bundled.

Running
-------

    river -library=[path to library]

To connect to the server, you can use
[river-web](https://github.com/william-alexander/river-web), a browser-based
River client. An iOS client will probably be in the works soon.

If you'd like to build your own client, you can use the server's JSON API,
detailed [below](#api).

### Flags

`-library`: the library directory

`-port`: the port to listen on (defaults to 21313)

### API

Note that track and disc numbers should begin at `1`. Numbers lower than
`1` indicate that the field is missing or should be treated as such.

#### Get a list of songs in the library

    GET /songs

```http
HTTP/1.1 200 OK
Content-Type: application/json
```

```json
[
	{
		"id": "iswkgapo",
		"path": "Bob Dylan/Bringing It All Back Home/Mr. Tambourine Man.flac",
		"artist": "Bob Dylan",
		"album": "Bringing It All Back Home",
		"disc": 2,
		"track": 1,
		"title": "Mr. Tambourine Man"
	},
	{
		"id": "wybtohyc",
		"path": "Neutral Milk Hotel/In the Aeroplane over the Sea/Holland, 1945.flac",
		"artist": "Neutral Milk Hotel",
		"album": "In the Aeroplane over the Sea",
		"disc": 0,
		"track": 6,
		"title": "Holland, 1945"
	}
]
```

#### Reload the library (i.e. after adding music or editing tags)

    PUT /songs

```http
HTTP/1.1 200 OK
Content-Type: application/json
```

```json
[
	{
		"id": "iswkgapo",
		"path": "Bob Dylan/Bringing It All Back Home/Mr. Tambourine Man.flac",
		"artist": "Bob Dylan",
		"album": "Bringing It All Back Home",
		"disc": 2,
		"track": 1,
		"title": "Mr. Tambourine Man"
	},
	{
		"id": "ihnqqjce",
		"path": "Neutral Milk Hotel/Ferris Wheel on Fire/Home.flac",
		"artist": "Neutral Milk Hotel",
		"album": "Ferris Wheel on Fire",
		"disc": 0,
		"track": 3,
		"title": "Home"
	},
	{
		"id": "wybtohyc",
		"path": "Neutral Milk Hotel/In the Aeroplane over the Sea/Holland, 1945.flac",
		"artist": "Neutral Milk Hotel",
		"album": "In the Aeroplane over the Sea",
		"disc": 0,
		"track": 6,
		"title": "Holland, 1945"
	}
]
```

#### Get the stream for a song

##### Opus

    GET /songs/wybtohyc.opus

```http
HTTP/1.1 200 OK
Content-Type: audio/ogg
```

##### MP3

    GET /songs/wybtohyc.mp3

```http
HTTP/1.1 200 OK
Content-Type: audio/mpeg
```
