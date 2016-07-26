# audiostreamsource.js

This is a tiny library to provide a streamed audio source for the WebAudio API cross browser.

Specifically at least up to iOS9 you can't stream audio to the WebAudio API >:(
In that case it's not streamed. Sucks to be Safari.

## Usage

```html
<script src="audiostreamsource.min.js"><script>
<script>
var context = new (window.AudioContext || window.webkitAudioContext)();
var streamSource = audioStreamSource.create({
  context: context,                    // a WebAudio context
  loop: true,                          // true or false if you want it to loop
  autoPlay: false,                     // true to autoplay (you don't want this. See below)
  crossOrigin: false,                  // true to try to get crossOrigin permission
});

streamSource.on('newSource', function(source) {
  streamSource.play();
  source.connect(context.destination);
});

streamSource.on('error', function(err) {
  // got an error, bad URL? cross origin permission error?
});

var src     = 'url-to-some.mp3';          // file to play
var lofiSrc = 'url-to-some-smaller.mp3';  // for shitty browsers like Safari on iOS

streamSource.setSource(src, lofiSrc);
</script>
```

## API

`audioStreamSource.create`, it takes options. After that you setup event listeners, `newSource`
and `error` using the `streamSource.on` function. You then call `streamSource.setSource` with
1 or 2 URLs. The first URL is the source to your audio. The second URL is an optional second source
for if you're on iOS since as of iOS9 iOS still does not support `createMediaElementSource`.

When the audio is ready to play you'll get a `newSource` event. At that point you can call
`streamSource.play` and connect the `source` node wherever you need it.

Because iOS does not support `createMediaElementSource` it can't stream audio into Web Audio.
That means (a) it can not start playing audio until the entire file is downloaded and (b) it
generally can not get access to cross origin audio data.

*   `streamSource.setSource`

    lets you set a new source.

*   `streamSource.getSource`

    Gets the source node for the stream. This will be null until the first `newSource`
    event arrives.

*   `streamSource.play()`

    starts playing the source

*   `streamSource.stop()`

    stops playing the source

*   `streamSource.isPlaying()`

    return true if the stream is playing.

### Options

The options passed into `audioStreamSource.create` are as follows

    context:      a WebAudio context (required)
    loop:         whether or not to loop. Default false
    autoPlay:     whether or not to start playing automatically. default false ([don't set to true](#autoplay-issues))
    crossOrigin:  whether or not to request crossOrigin permissions

## autoPlay issues

Mobile browsers can not autoplay audio. Audio must be started by user gesture. Because of this autoPlay can not work
on mobile period. You must ask the user to click or touch something.

Example:

```js
var isMobile = window.navigator.userAgent.match(/Android|iPhone|iPad|iPod|Windows Phone/i);
var context = new (window.AudioContext || window.webkitAudioContext)();
var clickToStartElem = document.getElementById("clickMe");  // some element to click

function startAudio() {
  streamSource.play();
  source.connect(context.destination);
}

var streamSource = audioStreamSource.create({
  context: context,                    // a WebAudio context
  loop: true,                          // true or false if you want it to loop
});

streamSource.on('newSource', function(source) {
  if (isMobile) {
    clickToStartElem.style.display = "block"; // make this element visible
    clickToStartElem.addEventListener('click', function() {
      clickToStartElem.style.display = "none"; // hide it
      startAudio();
    });
  } else {
    startAudio();
  }
});

streamSource.on('error', function(err) {
  // got an error, bad URL? cross origin permission error?
});

var src     = 'url-to-some.mp3';          // file to play
var lofiSrc = 'url-to-some-smaller.mp3';  // for shitty browsers like Safari on iOS

streamSource.setSource(src, lofiSrc);
```

## Example

[There's an example here](http://twgljs.org/examples/dynamic-buffers.html).

## License

MIT

