# The progress element

When building web sites and web applications, we often need to provide essential
feedback to a user informing them of the progress of their request or task, be
it uploading a file, playing a video, or importing data.

HTML5 makes life easier for us by defining an element whose sole purpose is just
that: the `<progress>` element.

The [HTML5 specification][1] describes it best:

> The progress element represents the completion progress of a task.

The task itself is irrelevant, but displaying its progress is not.

## Attributes

There are four attributes that are specified for the `<progress>` element:
`max`, `value`, `position`, and `label`s. Before taking a look at each of theses
attributes in turn, here are a few important notes on them:

* If the `value` attribute exists, then the progress bar is considered to be 
**determinate** (i.e., it has exact limits). Otherwise it is considered to be 
**indeterminate**.
* If the `max` attribute is not included, the default acceptable range for the 
progress bar is between 0.0 and 1.0 inclusive.
* Authors are encouraged always to include the current value and the maximum 
value (if used) as inline text to the `<progress>` element so that the 
information is still visible to users of legacy browsers.

And now let’s take a closer look at the attributes themselves:

### Max

The `max` attribute is a floating point number that indicates how much work the
relevant task requires in total before it can be considered complete. The
default value is 1.0.

### Value

This is a floating point number that represents the current progress of the
relevant task. Its value must be greater than or equal to 0.0 and less than or
equal to 1.0 **or** the value of the `max` attribute if present.

### Position

This is a read-only attribute that returns the current position of the
`<progress>` element. This value is equal to `value` / `max` for a determinate
progress bar and -1 for an indeterminate progress bar (as the position can’t
accurately be determined).

### Labels

This is also a read-only attribute that returns a list of the `<progress>`
element’s `label`s (if there are any).

## Examples

Let’s now take a look at some examples of how the `<progress>` element might be
used, including how it might be updated via JavaScript.

### Simple Indeterminate Progress Bar

    <progress id='p'></progress>

Since we have no idea how long the task in hand will take, the browser will
simply display the progress bar until the task is complete and we either hide it
or replace it with a ‘task complete’ message.

### Simple Determinate Progress Bar

    <progress id='p' max='70'><span>0</span>%</progress>

And the JavaScript for updating it:

    var pBar = document.getElementById('p');
    var updateProgress = function(value) {
      pBar.value = value;
      pBar.getElementsByTagName('span')[0].innerHTML = Math.floor((100 / 70) * value);
    }

Note that here the progress bar can have a maximum value of 70, so calls to
`updateProgress` will have values between 0 and 70 and that the value is also
displayed within the `<progress>` element so that it is visible as a simple
percentage string for users of legacy browsers.

## Using `<progress>` with HTML5 Video

A good example of using the `<progress>` element is with an HTML5 video,
informing the user how much of the video has been played. This won’t be a full
example but will just show the necessary HTML and JavaScript.

Given a `<video>` element with an `id` of ‘v’:

    <progress id='p' max='100' value='0'><span>0</span>% played</progress>

Use this JavaScript:

    var video = document.getElementById('v');
    var pBar = document.getElementById('p');
    video.addEventListener('timeupdate', function() {
      var percent = Math.floor((100 / video.duration) * video.currentTime);
      pBar.value = percent;
      pBar.getElementsByTagName('span')[0].innerHTML = percent;
    }, false);

The JavaScript uses the `timeupdate` event, which is constantly triggered as the
video is being played. The value is converted into a percentage value using the
video’s `duration` (full length) and `currentTime` (where in the video it
currently is).

## Using `<progress>` with the File API

Another useful example is how the `<progress>` element might be used with the
[File API][2]. Again, this is not a complete example, assuming the existence of
a file input and upload button and `<progress>` element, and code for reading
the file. The [specification itself][3] has some good code examples of this.

    var p = document.getElementById('p');
    reader.addEventListener('loadstart', function(evt) {
      if (evt.lengthComputable) p.max = evt.total;
    }, false);
    reader.addEventListener('progress', function(evt) {
      if (evt.lengthComputable) p.value = evt.loaded;
    }, false);

Here the `loadstart` event is used to set the `max` attribute of the
`<progress>` element, and then the `progress` event is used to update the
`<progress>` element’s `value` attribute.

## Browser Support

The `<progress>` element is [fairly well-supported][4] in today’s major
browsers, although only in version 6+ of Safari (so not on Windows, as the last
version there was 5.1) and only in version 10+ of Internet Explorer.

## Styling

As is usual with such elements, browsers have their own unique styling for the
`<progress>`element. You can, however, override this to a certain extent using
simple CSS.

The default styling tends to consist of an element with rounded corners, a grey
background colour, and a green gradient for the actual progress marker.

![screenshot1][A screenshot of the progress element in Firefox]

The default styling for the progress element in Firefox 18.0.2.

Let’s say we want to style our progress bar for the HTML5 Doctor website, so
that it fits in with the design. We’ll remove the bevel and the gradients using
only solid colours. The code below will almost do that:

    progress {
      color: #0063a6;
      font-size: .6em;
      line-height: 1.5em;
      text-indent: .5em;
      width: 15em;
      height: 1.8em;
      border: 1px solid #0063a6;
      background: #fff;
    }

This, however, won’t change the colour of the progress bar itself, which will
remain a green colour. We can change this in Firefox, Internet Explorer 10,
Chrome, and Safari using a variety of methods which are outlined below.

### Firefox

All we need to use here is:

    progress::-moz-progress-bar { background: #0063a6; }

and Firefox will display the colour correctly.

![screenshot2][A screenshot of the progress bar after custom styling]

The progress bar as it appears in Firefox 18.0.2 after being styled.

### Chrome and Safari

For Chrome and Safari, we need to use `progress::-webkit-progress-value` to
specify the progress bar colour and `progress::-webkit-progress-bar` to specify
the background colour of the `<progress>` element itself:

    progress::-webkit-progress-bar { background: #fff; }
    progress::-webkit-progress-value { background: #0063a6; }

> **Update – 13th February 2013**

> Opera have just [announced their move to using WebKit as their rendering 
engine, so eventually the WebKit][5] specific styling rules will also work in 
Opera.

### Internet Explorer 10

Actually the CSS above will be fine to style the `<progress>` element in IE10,
but it’s worth noting that this browser interprets the `<progress>` element’s
color value as the colour of the progress bar.

I’ve uploaded some [working examples][6] of determinate and indeterminate
progress bars for you to see.

## Summary

The `<progress>` element may seem very similar to the [`<meter>` element][7] —
which was also introduced in HTML5 — but it can be seen as a more specific kind
of `<meter>` element that’s only used to measure progress of a task. It’s worth
bearing this in mind when deciding which one to use.

Since browser support is pretty good, it’s definitely worth using the
`<progress>` element where appropriate, but you should also ensure that you
provide textual fallback for legacy browsers as has been outlined above.

[1]: http://www.w3.org/TR/html5-author/the-progress-element.html
[2]: http://www.w3.org/TR/FileAPI/
[3]: http://www.w3.org/TR/FileAPI/
[4]: http://caniuse.com/#feat=progressmeter
[5]: http://business.opera.com/press/releases/general/opera-gears-up-at-300-million-users
[6]: http://html5doctor.com/demos/progress/progress-element.html
[7]: http://html5doctor.com/measure-up-with-the-meter-tag/

[A screenshot of the progress element in Firefox]: img/progress-element-firefox.jpg
[A screenshot of the progress bar after custom styling]: img/progress-element-styled.jpg