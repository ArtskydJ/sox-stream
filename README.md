sox-stream
==========

A stream-friendly wrapper around [SoX](http://sox.sourceforge.net/). Transcode audio streams easily!

why
===

The other implementations I found were klunky to use; this has an extremely easy-to-use streaming interface.

examples
========

Lower volume:
```js
var Sox = require('sox-stream')
var fs  = require('fs')

var src = fs.createReadStream('loud.flac')
var sox = Sox({ volume: 0.8 }, {})
var dst = fs.createWriteStream('quiet.flac')
src.pipe(sox).pipe(dst)
```

Transcode:
```js
var Sox = require('sox-stream')
var fs  = require('fs')

var src = fs.createReadStream('song.ogg')
var sox = Sox({
	bits: 16,
	rate: 44100,
	channels: 2,
	type: 'wav'
})
var dst = fs.createWriteStream('transcoded.wav')
src.pipe(sox).pipe(dst)

sox.on('error', function (err) {
	console.log('oh no! ' + err.message)
})
```

#sox([soxInputOpts], soxOutputOpts, [soxPath])

- `soxInputOpts` is an object, and is optional. These options will be used to interpret the incoming stream.
- `soxOutputOpts` is an object, and is required. You must pass the `type` parameter in. These options will be used to format the outgoing stream.
- `soxPath` is a string of the path to SoX. Optional, defaults to `'sox'`, which works if the SoX binary is in your path. E.g. `'C:\Program Files\Sox\sox.exe'`.

Returns a transform (a.k.a. through) stream. The stream also emits 'error' events when there is an error.

(If you don't know how to use streams, I recommend reading the [stream handbook][stream-handbook].)

###sox features that are not supported
- **effects** - Might support if there is demand for it. Create an issue if you *literally* can't live without this feature. If you're feeling generous, you could make a pull request.
- **read from local file** - Just do: `fs.createReadStream('src.ogg').pipe(sox).pipe(dst)`.
- **write to local file** - Just do: `src.pipe(sox).pipe(fs.createWriteStream('dst.wav'))`.

#options

The common options are listed below.

The rest of the options are listed in [options.md][more-opts].

###input and output:

If you use these options on `soxInputOpts`, they will be used to interpret the incoming stream.

Most likely you will want to use these on `soxOutputOpts`. Then they will be used to format the outgoing stream.

- [`b` or `bits`][bitdepth-arg], **number**, bit depth. E.g. `16`. (Not applicable to complex encodings such as MP3 or GSM.)
- [`c` or `channels`][channel-arg], **number**, number of channels. E.g. `2` for stereo.
- [`r` or `rate`][samplerate-arg], **number**, sample rate. E.g. `44100`.
- [`t` or `type`][type-arg], **string**, file type. E.g. `'wav'`. This property is required by `soxOutputOpts`, and is suggested for `soxInputOpts`.

###input-only:

- `v` or `volume`, **floating point number**, volume adjustment. E.g. `0.8` would make the output stream 80% its original volume (which would be slightly quieter), while `1.0` would preserve the volume.

###output-only

- `C` or `compression`, **integer** or **float**, usage depends on output `type`. See [SoX format docs](http://sox.sourceforge.net/soxformat.html) for more information.

#install

Install [SoX 14.4.1a][sox-latest]. Then install this package with npm: 

```
npm install sox-stream
```

To run the tests, you must clone the [git repository](https://github.com/ArtskydJ/sox-stream). You must also put SoX  in your `PATH`. Then run:

```
npm test
```

#codec support

###FLAC

- **Problem:** FLAC was disabled accidentally in 14.4.1 (SourceForge default). [[Stack Overflow](http://stackoverflow.com/questions/23382500/how-to-install-flac-support-flac-libraries-to-sox-in-windows/25755799)]
- **Solution:** Install [SoX 14.4.1a][sox-latest].

###MP3

- **Problem:** MP3 is [proprietary](https://en.wikipedia.org/wiki/LAME#Patents_and_legal_issues). It's really lame and makes me mad.
- **Solution:** Get the [Fraunhofer Society](https://en.wikipedia.org/wiki/Fraunhofer_Society#Notable_projects) to release MP3 under an open source license. Or, more realistically; compile the [LAME](http://lame.sourceforge.net/) encoder, and/or the [MAD](http://www.underbit.com/products/mad) decoder.
- **Links:**
	- [Windows (Precompiled)](https://github.com/EaterOfCode/sux/tree/master/win_libs)
	- [Windows (How-To)](http://www.codeproject.com/Articles/33901/Compiling-SOX-with-Lame-and-Libmad-for-Windows)
	- [Ubuntu (How-To)](http://superuser.com/questions/421153/how-to-add-a-mp3-handler-to-sox)
	- [Ubuntu (How-To) 2](http://eggblog.invertedegg.com/?p=19)
	- [CentOS (How-To)](http://techblog.netwater.com/?p=4)

#license

[VOL](http://veryopenlicense.com)

[sox-latest]: http://sourceforge.net/projects/sox/files/sox/14.4.1/sox-14.4.1a-win32.exe/download
[bitdepth-arg]: https://en.wikipedia.org/wiki/Audio_bit_depth
[channel-arg]: https://en.wikipedia.org/wiki/Audio_channel
[samplerate-arg]: https://en.wikipedia.org/wiki/Sampling_(signal_processing)#Sampling_rate
[type-arg]: https://en.wikipedia.org/wiki/Audio_file_format
[stream-handbook]: https://github.com/substack/stream-handbook
[more-opts]: https://github.com/ArtskydJ/sox-stream/blob/master/options.md
