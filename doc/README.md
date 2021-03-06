## API

### Opening and creating graphic images

#### gd.create(width, height, callback)

Create a new image in memory of specified size and calls a callback that can have an argument being instance of `gd.Image`. This is a paletted image, thus has a white background and a limited color range of at most 256 colors.

#### gd.createSync(width, height)
Synchronous version of the above, returns an object of type `gd.Image`.

#### gd.createTrueColor(width, height, callback)
This is a true color image, and thus has a black background and a palette of millions of colors.

```javascript
var gd = require('node-gd');

gd.createTrueColor(100, 100, function(error, img) {
  if (error) throw error;
  img.saveJpeg('./test.jpg', 100, function(error) {
    if (error) throw error;
  });
});
```

#### gd.createTrueColorSync(width, height)

Synchronous version of the above, returns an object of type `gd.Image`.

#### gd.openJpeg(path[, callback])
Open a JPG file. If no callback is supplied, it will return an instance of `gd.Image`. In case of a supplied callback, it will receive two parameters: `error` and an instance of `gd.Image`. Uses `fs.readFile()` and `gd.createFromJpegPtr()` to load the image asynchronously.

```javascript
var gd = require('node-gd');

// open Jpeg image
gd.openJpeg('/path/to/file.jpg', function(error, image) {
  if (error) {
    throw error;
  }

  // save it as a PNG image
  image.savePng('/path/to/file.png', 0, function(error) {
    if (error) {
      throw error;
    }
  });
});
```

#### gd.createFromJpeg(path)
Synchronous version of `gd.openJpeg()`.

#### gd.createFromJpegPtr(data)
Load an image from a resource which is either a `String` or a `Buffer`.

#### gd.openPng(path[, callback])
Open a PNG file. The callback, if supplied, gets `error` and `image` as parameter. The image is an instance of `gd.Image`.

#### gd.createFromPng(path)
Synchronous version of `gd.openPng()`.

#### gd.createFromPngPtr(data)
Load an image from a resource.

#### gd.openGif(path[, callback])
Open a GIF file asynchronously.

#### gd.createFromGif(path)
Synchronous version of `gd.openGif()`.

#### gd.createFromGifPtr(data)

#### gd.openGd2(path[, callback])

#### gd.createFromGd2(path)

#### gd.createFromGd2Ptr(data)

#### gd.openGd2Part(path, srcX, srcY, width, height[, callback])

#### gd.createFromGd2Part(path, srcX, srcY, width, height)

#### gd.createFromGd2PartPtr(data, srcX, srcY, width, height)

#### gd.openWBMP(path[, callback])
Open a WBMP image file. WBMP stands for Wireless Application Protocol Bitmap Format, a monochrome graphics file format.

#### gd.createFromWBMP(path[, callback])

#### gd.createFromWBMPPtr(data)

#### gd.openBmp(path[, callback])
Open a BMP bitmap image file.

#### gd.createFromBmp(path)

#### gd.createFromBmpPtr(data)

#### gd.openFile(path[, callback])
GD will try to find out of what type the supplied image is, and will open it likewise. This is a quite new convenience method for if you don't exactly know what the type is that you will try to open.

#### gd.createFromFile(path)
A synchronous version of the above.

#### gd.trueColor(red, green, blue)
Returns an integer representation of the supplied color values.

```javascript
var gd = require('node-gd');

var red = gd.trueColor(255, 0, 0); // 16711680
```
Alternatively, you can use the hex notation for color values for parameters for GD likewise:
```javascript
var red = 0xff0000; // red
```

#### gd.trueColorAlpha(red, green, blue, alpha)
Returns an integer representation of the supplied `rgba` values. Be aware that in libgd the value for `alpha` ranges from `0` to `127`. From its source code:

```c++
[the alpha channel] occupies only the
LOWER 7 BITS of the MSB (most significant
bit). This very small loss of alpha channel
resolution allows gd 2.x to keep backwards
compatibility (...)
```

Example:

```javascript
var gd = require('node-gd');

var transparentRed = gd.trueColorAlpha(255, 0, 0, 63); // 1073676288
```

#### gd.getGDVersion()
Will return a string representing the version of your currently installed GD version. Outputs something like `2.1.1`

## Manipulating graphic images

#### gd.Image#destroy()
Free up allocated memory for image data.

### Drawing

#### gd.Image#setPixel(x, y, color)
Set the color of a certain pixel. Use for color either `0xff00ff` or `gd.trueColor(255, 0, 255)`.

#### gd.Image#line(x1, y1, x2, y2, color)
Draw a line from a certain point to a next point.

#### gd.Image#dashedLine(x1, y1, x2, y2, color)
Draw a dashed line from a certain point to a next point.

#### gd.Image#polygon(array, color)
Draw a closed polygon. The first parameter shoud be an `Array` of `Object`s containing an x and y property.

```javascript
// draw a red triangle on a black background
var gd = require('node-gd');
var img = gd.createTrueColorSync(100, 100);

var points = [
  { x: 10, y: 10 },
  { x: 70, y: 50 },
  { x: 30, y: 90 }
];

img.polygon(points, 0xff0000);
img.bmp('test.bmp', 0);
```

#### gd.Image#openPolygon(array, color)
Same as the above but start and end point will not be connected with a line.

#### gd.Image#filledPolygon(array, color)
Same as the above but color value will be used to fill the polygon.

#### gd.Image#rectangle(x1, y1, x2, y2, color)
Create a rectangle. The x and y values indicate upper left and lower right corner respectively.

```javascript
var gd = require('node-gd');
var img = gd.createSync(300, 200);
var color0 = img.colorAllocate(255,255,255);
var color1 = img.colorAllocate(255,0,255);
img.rectangle(20, 20, 280, 180, color1);
img.saveFile('./test.png');
```

#### gd.Image#filledRectangle(x1, y1, x2, y2, color)
Create a filled rectangle i.e. a solid bar.

```javascript
var gd = require('node-gd');
var img = gd.createSync(300, 200);
var color0 = img.colorAllocate(255,255,255);
var color1 = img.colorAllocate(255,0,255);
img.filledRectangle(20, 20, 280, 180, color1);
img.saveFile('./test.png');
```

#### gd.Image#arc(cx, cy, width, height, begin, end, color)
Draw an arc. `cx` and `cy` denote the center of the arc. The `begin` and `end` parameters are in degrees, from 0 to 360.

```javascript
var gd = require('node-gd');
var img = gd.createSync(300, 200);
var color0 = img.colorAllocate(255, 255, 255);
var color1 = img.colorAllocate(255, 0, 255);
var color2 = img.colorAllocate(0, 255, 255);

img.arc(150, 100, 200, 160, 0, 270, color1);
img.arc(150, 100, 120, 120, 90, 0, color2);

img.saveFile('./test.png');
```

#### gd.Image#filledArc(cx, cy, width, height, begin, end, color)
Draw a filled arc.

#### gd.Image#ellipse(cx, cy, width, height, color)
Draw an ellipse.

#### gd.Image#filledEllipse(cx, cy, width, height, color)
Draw a filled ellipse.

#### gd.Image#fillToBorder(x, y, border, color)
Fill a drawn form up until its border.

```javascript
var gd = require('node-gd');
var img = gd.createSync(300, 200);
var color0 = img.colorAllocate(255, 255, 255);
var color1 = img.colorAllocate(255, 0, 255);
var color2 = img.colorAllocate(0, 255, 255);

img.ellipse(150, 100, 160, 160, color2);
img.fillToBorder(150,100, 2, color1);

img.saveFile('./test.png');
```

#### gd.Image#fill(x, y, color)

#### gd.Image#setAntiAliased(color)

#### gd.Image#setAntiAliasedDontBlend(color, dontblend)

#### gd.Image#setBrush(image)

#### gd.Image#setTile(image)

#### gd.Image#setStyle(array)

#### gd.Image#setThickness(thickness)
Set the thickness for lines.

```javascript
var gd = require('node-gd');
var img = gd.createSync(300, 200);
var color0 = img.colorAllocate(255, 255, 255);
var color1 = img.colorAllocate(255, 0, 255);

img.setThickness(30);
img.line(0, 0, 300, 200, color1);
img.line(0, 200, 300, 0, color1);

img.saveFile('./test.png');
```

#### gd.Image#alphaBlending(blending)
Turn on (`1`) or off (`0`) alphablending for what is being drawn next. This switch can be switched on or off during a drawing process in order to let some layer or drawn form blend or not with former drawn elements.

A newly created truecolor image for example has a black background. If you want to start out with a transparent canvas, you will first need to draw a non-blending fully transparent rectangle the size of that image. For example:

```javascript
var gd = require('node-gd');
var img = gd.createTrueColor(100, 100);

// turn off alpha blending
// you don't want your first filled rectangle to blend
// with the basic black background!
img.alphaBlending(0);

// you *do* want to save the full alpha channel
// i.e. I assume you want your image to be really transparent
img.saveAlpha(1);

// allocate any color as long as it's transparent!
// i.e. an alpha value of 127
var color = gd.trueColorAlpha(255, 255, 255, 127);
img.filledRectangle(0, 0, 100, 100, color);

// from here on you can add forms, texts or new images
// turn alpha blending on or off if you want the drawn
// elements to blend with other layers.
// img.alphaBlending(1);

img.savePng('output.png', 0, function(err) {
  if(err) {
    throw err;
  }
});

img.destroy();
```

#### gd.Image#saveAlpha(saveFlag)
When set to `1`, the alpha information will be saved as a separate channel within the image. If turned off (`0`), all transparency will be handled within the image. Compare these two examples to see the difference:
```javascript
var gd = require('node-gd');
var img = gd.createTrueColorSync(100, 100);
// save the full alpha channel
// the image will be transparent to e.g. a browser's background color
img.saveAlpha(1);
img.alphaBlending(0);

var color = gd.trueColorAlpha(255, 255, 255, 127);
img.filledRectangle(0, 0, 100, 100, color);

img.alphaBlending(1);

var red = gd.trueColorAlpha(255, 0, 0, 64);
var blue = gd.trueColorAlpha(0, 0, 255, 64);

img.filledRectangle(0, 0, 80, 80, red);
img.filledRectangle(20, 20, 100, 100, blue);

img.savePng('output.png', 0, function(err) {
  if(err) {
    throw err;
  }
});

img.destroy();
```

And with `saveAlpha` turned off

```javascript
var gd = require('node-gd');
var img = gd.createTrueColorSync(100, 100);
// the image will have a white background
// but the red and blue squares will blend
// with each other and the white background
img.saveAlpha(0);
img.alphaBlending(0);

var color = gd.trueColor(255, 255, 255);
img.filledRectangle(0, 0, 100, 100, color);

img.alphaBlending(1);

var red = gd.trueColorAlpha(255, 0, 0, 64);
var blue = gd.trueColorAlpha(0, 0, 255, 64);

img.filledRectangle(0, 0, 80, 80, red);
img.filledRectangle(20, 20, 100, 100, blue);

img.savePng('output2.png', 0, function(err) {
  if(err) {
    throw err;
  }
});

img.destroy();
```


#### gd.Image#setClip(x1, y1, x2, y2)
Set the clipping area.

#### gd.Image#getClip()
Returns an object containing the coordinates of the clipping area.

#### gd.Image#setResolution(res_x, res_y)
Unsigned integers should be used for `res_x` and `res_y`.

### Query image information

#### gd.Image#Alpha(color)
Get the alpha value of a color.

#### gd.Image#getPixel(x, y)
For paletted images, this will return the palette index of the color for that pixel.

#### gd.Image#getTrueColorPixel(x, y)
This will return the color integer representation.

```javascript
var gd = require('node-gd');
var img = gd.createTrueColorSync(300, 200);
var color = img.colorAllocate(255, 0, 255);

img.setThickness(30);
img.line(0, 0, 300, 200, color);
img.line(0, 200, 300, 0, color);

// will output ff00ff
console.log(img.getTrueColorPixel(50, 50).toString(16));

img.saveFile('./test.png');
```

#### gd.Image#imageColorAt(x, y)
This is the implementation of the PHP-GD specific method imagecolorat.

#### gd.Image#getBoundsSafe(x, y)
Returns `0` if either x or y are out of the bounds of the image canvas, or `1` when within the bounds.

### Font and text

#### gd.Image#stringFTBBox(color, font, size, angle, x, y, string)
`BBox` in this function name refers to the _bounding box_ of the supplied `string` parameter when it would be rendered on the image. This might be handy in case you want to check wether the text fits the image bounds. The return value of this function is an array of 8 elements, weirdly ordered: [xll, yll, xlr, ylr, xur, yur, xul, yul]. In ascii-art:
```
   xul           xur
yul ._____________. yur
    | Lorem ipsum |
    | dolor sit   |
yll ._____________. ylr
   xll           xlr

legend:
xul = x upper left
ylr = y lower right
```

The font color can be allocated with `img.colorAllocate(r, g, b)`. The `font` parameter should be an absolute path to a `ttf` font file.

#### gd.Image#stringFT(color, font, size, angle, x, y, string, boundingbox)
`FT` refers to FreeType. Add text to the image. When the `boundingbox` parameter is set to be `true`, the return of this function is identical to `gd.Image#stringFTBBox`.

### Color handling

#### gd.Image#colorAllocate(r, g, b)
Allocate a color in the color table.

#### gd.Image#colorAllocateAlpha(r, g, b, a)
Allocate a color in the color table with transparency value.

#### gd.Image#colorClosest(r, g, b)

#### gd.Image#colorClosestAlpha(r, g, b, a)

#### gd.Image#colorClosestHWB(r, g, b)

#### gd.Image#colorExact(r, g, b)

#### gd.Image#colorResolve(r, g, b)

#### gd.Image#colorResolveAlpha(r, g, b, a)

#### gd.Image#red(r)

#### gd.Image#green(g)

#### gd.Image#blue(b)

#### gd.Image#getTransparent()

#### gd.Image#colorDeallocate(color)

### Color Manipulation

#### gd.Image#colorTransparent(color)
Specifies a color index (if a palette image) or an RGB color (if a truecolor image) which should be considered 100% transparent. For truecolor images, this is ignored if an alpha channel is being saved. Use `gd.Image#saveAlpha(0)` to turn off the saving of a full alpha channel in a truecolor image.

#### gd.Image#colorReplace(fromColor, toColor)
Replace one color with another. This returns the amount of changes pixels.

#### gd.Image#colorReplaceThreshold(fromColor, toColor, threshold)
Replace one color with another. The `threshold` will broaden the range of replaced colors and is a decimal `Number` between `0` and `100`.

#### gd.Image#colorReplaceArray(fromColors, toColors)
Replace colors in `fromColors` array to `toColors` array. The arrays should be of equal length.

### Effects

#### gd.Image#grayscale()
Remove all color from an image and create a grayscaled image. Only available from libgd2 version 2.1.x.

#### gd.Image#gaussianBlur()
Apply gaussian blur. Can by applied multiple times to an image to get more blur.

#### gd.Image#negate()
Invert the colors of the image.

#### gd.Image#brightness(brightness)
Supply an integer between `0` and `100`.

#### gd.Image#contrast(contrast)
The value for contrast is a bit weird. A value of `100` wil return a complete grey image, with gray being exactly `rgb(127, 127, 127)`. For best results, the range applied should be between `-900` and `1100` (so actually `100 + 1000` and `100 - 1000`).

#### gd.Image#selectiveBlur()

#### gd.Image#emboss()

#### gd.Image#flipHorizontal()
Flip the image on the canvas over the vertical axis. This means that what's at the right side of the images will be reflected to the left side: pixels move only horizontally.

#### gd.Image#flipVertical()
Flip the image on the canvas over the horizontal axis, the oposite of the above.

#### gd.Image#flipBoth()
Flip the image on the canvas over both axes.

#### gd.Image#crop(x, y, width, height)
Crop the supplied image from a certain point to a certain size. Will return a new instance of `gd.Image`. Negative numbers, i.e. when going out of the image bounds, can result in images with black parts.

#### gd.Image#cropAuto(mode)
The mode parameter is a value from 0 to 4, so there are 5 modes: `0`: default: 4 corners or background, `1`: crop using the transparent color, `2`: crop black borders, `3`: crop white borders, `4`: crop using colors of the 4 corners.

#### gd.Image#cropThreshold(color, threshold)
Crop by color, with a threshold. Use for color an integer and for threshold a number.

#### gd.Image#sharpen(pct)
Does not work on non-true color images. A value lower than 0 for `pct` is ignored.

#### gd.Image#createPaletteFromTrueColor(ditherFlag, colorsWanted)
Create and return a new palette based image instance of gd.Image. The `ditherFlag` is either `0` or `1` (a `Number`, not a `Boolean`), `colorsWanted` a value between 1 and 256.

#### gd.Image#trueColorToPalette(ditherFlag, colorsWanted)
Modify the current image into a palette based image. The `ditherFlag` is either `0` or `1` (a `Number`, not a `Boolean`), `colorsWanted` a value between 1 and 256.

#### gd.Image#paletteToTrueColor()
Modify the current image, if it is a palette based image, into a true color image. From libgd version `2.1.0` this returns either `0` or `1`, in version `2.0.26` it is void.

#### gd.Image#colorMatch(gd.Image)
The parameter should be a palette based image, which will be modified and which can be saved afterwards. The return value is a `Number`. This method tries to better match the colors from the palette based image to those of its true color original.

```javascript
var gd = require('node-gd');

// open a true color image
gd.openFile('/path/to/image.jpg', function(err, trueColor) {

  // create a new palette based image
  var palette = trueColor.createPaletteFromTrueColor(1, 128);

  // match colors to enhance the result of the palette image
  trueColor.colorMatch(palette);

  // save the palette based image
  palette.saveFile('/path/to/result.gif', function(err) {
    if (err) {
      throw err;
    }
  });
});
```

#### gd.Image#gifAnimBegin(anim, useGlobalColorMap, loops)
Create an animated GIF.

* `anim` is a `String` referencing a filename to be created, like `./anim.gif`
*  `useGlobalColorMap` a value being `-1`, `0` or `1` which determines if the colorMap of the current image will be used as the global colorMap for the whole animation. `-1` indicates `do default`.
* The value for `loops` can be `-1` for no looping, `0` means infinite looping, any other value the amount of loops the animation should run.


#### gd.Image#gifAnimAdd(anim, localColorMap, leftOffset, topOffset, delay, disposal, prevFrame)


* Add current image to the specified `anim`.
* `localColorMap` is a flag indicating wether GD should use this image's colorMap
* `leftOffset` and `topOffset` indicate the offset of this frame
* the `delay` is the delay before next frame (in 1/100 sec)
* `disposal` defines how this frame is handled when the next frame is loads. Usually this value should be set to `0`, quote from gd's source: _meaning that the pixels changed by this frame should remain on the display when the next frame begins to render_
* `prevFrame` should refer to the previous frame. If the current image is the first frame, supply `null`.


#### gd.Image#gifAnimEnd(anim)
Write and close the GIF animation. A complete working example could look like this:

```javascript
var gd = require('node-gd');
var anim = './anim.gif';

// create first frame
var firstFrame = gd.createSync(200,200);

// allocate some colors
var whiteBackground = firstFrame.colorAllocate(255, 255, 255);
var pink = firstFrame.colorAllocate(255, 0, 255);

// create first frame and draw an ellipse
firstFrame.ellipse(100, -50, 100, 100, pink);
// start animation
firstFrame.gifAnimBegin(anim, 1, -1);
firstFrame.gifAnimAdd(anim, 0, 0, 0, 5, 1, null);

var totalFrames = [];
for(var i = 0; i < 30; i++) {
  totalFrames.push(i);
}

totalFrames.forEach(function(i, idx, arr) {
  var frame = gd.createSync(200, 200);
  arr[idx] = frame;
  frame.ellipse(100, (i * 10 - 40), 100, 100, pink);
  var lastFrame = i === 0 ? firstFrame : arr[i - 1];
  frame.gifAnimAdd(anim, 0, 0, 0, 5, 1, lastFrame);
  frame.destroy();
});

firstFrame.gifAnimEnd(anim);
firstFrame.destroy();
```
### Copying and resizing

#### gd.Image#copy(dest, dx, dy, sx, sy, width, height)
Copy an image onto a destination image: `dest`. You'll have to save the destination image to see the resulting image. This method returns _the image on which it is called_ in order to be able to chain methods.

```javascript
var gd = require('node-gd');
var output = '../output/image-watermark.png';

var watermark = gd.createFromPng('../input/watermark.png');
var input = gd.createFromPng('../input/input.png');

watermark.alphaBlending(1);
watermark.saveAlpha(1);

// copy watermark onto input, i.e. onto the destination
watermark.copy(input, 0, 0, 0, 0, 100, 100);

// save the destination
input.savePng(output, 0, function(error) {
  if (error) throw error;
});
```

Example using chaining:

```javascript
var gd = require('node-gd');
var output = '../output/image-watermark.png';
var output2 = '../output/image-watermark2.png';

var watermark = gd.createFromPng('../input/watermark.png');
var input = gd.createFromPng('../input/input.png');
var input2 = gd.createFromPng('../input/input2.png');

watermark.alphaBlending(1)
.saveAlpha(1)
.copy(input, 0, 0, 0, 0, 100, 100)
.contrast(-900)
.copy(input2, 0, 0, 0, 0, 100, 100)

input.savePng(output, 0, function(error) {
  if (error) throw error;
});

input2.savePng(output2, 0, function(error) {
  if (error) throw error;
});
```


#### gd.Image#copyResized(dest, dx, dy, sx, sy, dw, dh, sw, sh)

#### gd.Image#copyResampled(dest, dx, dy, sx, sy, dw, dh, sw, sh)

#### gd.Image#copyRotated(dest, dx, dy, sx, sy, sw, sh, angle)

#### gd.Image#copyMerge(dest, dx, dy, sx, sy, width, height, pct)

#### gd.Image#copyMergeGray(dest, dx, dy, sx, sy, width, height pct)

#### gd.Image#paletteCopy(dest)

#### gd.Image#squareToCircle(radius)

### Misc

#### gd.Image#compare(image)

### Saving graphic images
The functions `gd.Image#savePng`, `gd.Image#saveJpeg`, `gd.Image#saveGif`, etc. are convenience functions which will be processed asynchronously when a callback is supplied. All of the following have a counterpart like `gd.Image#png` and `gd.Image#pngPtr` which write to disk synchronously or store the image data in a memory pointer respectively. `gd.Image#jpeg` will return the instance of `gd.Image`, `gd.Image#jpgPtr` will return the newly created image data.

```javascript
var gd = require('node-gd');
gd.openPng('/path/to/input.png', function(err, img) {
  if (err) {
    throw err;
  }

  // create jpg pointer from png
  var jpgImageData = img.jpegPtr(0); // jpeg quality 0
  // create gif pointer from png
  var gifImageData = img.gifPtr();

  // create instance of gd.Image() for jpg file
  var jpgImage = gd.createFromJpegPtr(jpgImageData);
  jpgImage.saveFile('./test01.jpg', function(err) {
    if (err) throw err;
  });

  // create instance of gd.Image() for gif file
  var gifImage = gd.createFromGifPtr(gifImageData);
  gifImage.saveFile('./test01.gif', function(err) {
    if (err) throw err;
  });

  img.destroy();
});
```
The above example shows how to create a JPEG and GIF file from a PNG file.

#### gd.Image#savePng(path, level[, callback])
Save image data as a PNG file. The callback will receive an error object as a parameter, only if an error occurred. When a callback is supplied, the image will be written asynchronously by `fs.writeFile()`, using `gd.Image#pngPtr()` to first write it to memory in the given format. `level` can be value between `0` and `9` and refers to a zlib compression level. A level of `-1` will let libpng12 decide what the default is.

#### gd.Image#saveJpeg(path, quality[, callback])
Save image data as a JPEG file. The callback will receive an error object as a parameter. Quality can be a `Number` between `0` and `100`.

#### gd.Image#saveGif(path[, callback])
Save image data as a GIF file.

#### gd.Image#saveGd(path[, callback])
Save image data as a GD file.

#### gd.Image#saveGd2(path, chunkSize[, format][, callback])
Save image data as a GD2 file.

#### gd.Image#saveWBMP(path, foreground[, callback])
Save image as a 2 color WBMP file. `foreground` is an integer value or `0x000000` value which defines the dark color of the image. All other colors will be changed into white.

#### gd.Image#saveBmp(path, compression[, callback])
Only available from GD version 2.1.1. The compression parameter is eiterh `0` for no compression and `1` for compression. This value only affects paletted images.

#### gd.Image#saveTiff(path[, callback])
Currently, opening TIFF files with GD has some errors, but saving image data as TIFF works fine. Therefore, there is no `gd.openTiff()` at this moment. Only available from GD version 2.1.1.

#### gd.Image#saveFile(path[, callback])
Lets GD decide in which format the image should be stored to disk, based on the supplied file name extension. Only available from GD version 2.1.1. This is not really asynchronous, since it uses a GD implementation of writing to disk, instead of a node / libuv based one.

### Image properties
Any instance of `gd.Image()` has a basic set of instance properties accessible as read only values.

```javascript
var gd = require('node-gd');
var img = gd.creatTrueColor(100,100);
console.log(img);
```

Will yield to something like:

```javascript
{
  colorsTotal: 0,
  interlace: true,
  height: 100,
  width: 100,
  trueColor: 1
}
```

#### gd.Image#colorsTotal
For paletted images, returns the amount of colors in the palette.

#### gd.Image#interlace
`Boolean` value for if the image is interlaced or not. This property can also be set. When set to `true` for Jpeg images, GD will save it as a progressive Jpeg image.

```javascript
var gd = require('node-gd');
var img = gd.createTrueColorSync(100,100);
img.interlace = true; // set interlace to true

// will save jpeg as progressive jpeg image.
img.saveJpeg('/path/to/output.jpg', 100, function(err) {
  if (err) throw err;
});
```

#### gd.Image#height
Returns the height of the image as `Number`.

#### gd.Image#width
Returns the width of the image as `Number`.

#### gd.Image#trueColor
Returns nonzero if the image is a truecolor image, zero for a palette image.

## libgd2 version information
Be aware that since `node-gd` version 0.3.x libgd2 version 2.1.x is mostly supported. `node-gd` version 0.2.x is backed at best with libgd2 version 2.0.x. Run `gdlib-config --version` to check the version of libgd2 on your system. `node-gd` should build successfully for both libgd2 version 2.0.x as wel as for 2.1.x. The main difference is that some functions will not be available. These include:

* `toGrayscale()`
* `emboss()`
* `gaussianBlur()`
* `negate()`
* `brightness()`
* `contrast()`
* `selectiveBlur()`
* `openBmp()`
* `openFile()`
* `createFromBmp()`
* `createFromBmpPtr()`
* `createFromFile()`
* `saveBmp()`
* `saveTiff()`
* `saveImage()`

Another way to check the installed GD version on your system:

```javascript
var gd = require('node-gd');

// as of node-gd 0.4.x
var version = gd.getGDVersion();
console.log(version); // 2.0.1 or 2.1.1 or the like
```

## Test

```bash
$ npm test
```
The `test/output/` directory contains the resulting images of the test script. The tests use, in some cases, a GNU Freefont font, which is licensed under the GNU General Public License v3.


## Related

* [Original author's repo](https://github.com/taggon/node-gd)
* [node-canvas](https://github.com/LearnBoost/node-canvas) uses libcairo to emulate browser HTML5 Canvas' image manipulation abilities within Node.js

