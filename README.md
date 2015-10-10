# audiostreamsource.js

This is a tiny library to provide a streamed audio source for the WebAudio API cross browser.

Specifically at least up to iOS9 you can't stream audio to the WebAudio API >:(
In that case it's not streamed. Sucks to be Safari.

## Usage

    <script src="audiostreamsource.min.js"><script>
    <script>
    var context = new (window.AudioContext || window.webkitAudioContext)();
    var streamSource = audioStreamSource.create({
      context: context,                    // a WebAudio context
      loop: true,                          // true or false if you want it to loop
      src: 'url-to-some.mp3',              // file to play
      lofiSrc: 'url-to-some-smaller.mp3',  // for shitty browsers like Safari on iOS
    }, doSomething);

    funciton doSomething(err) {
      // do something with the streamSource

      streamSource.source.connect(context.destination);
      streamSource.play();
    }
    </script>

## API

There's one function, `audiostreamsource.create`, it takes an options and a callback.

The callback will be called with null as the first argument if there are no errors.

The created stream source has just 2 functions and 1 property

    streamSource.source  // a WebAudio source node
    streamSource.play(); // starts playing the source
    streamSource.stop(); // stops the source

### Options

The options passed into `audioStreamSource.create` are as follows

    context:   a WebAudio context (required)
    src:       the URL for an audio file (required)
    lofiSrc:   an *optional* URL for Safari. Because since it can't stream you might want something smaller
    loop:      whether or not to loop. Default false
    autoPlay:  whether or not to start playing automatically. default false

## Example

[]There's an example here](http://twgljs.org/examples/dynamic-buffers.html).

## License

MIT

