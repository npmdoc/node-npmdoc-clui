# api documentation for  [clui (v0.3.1)](https://github.com/nathanpeck/clui)  [![npm package](https://img.shields.io/npm/v/npmdoc-clui.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-clui) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-clui.svg)](https://travis-ci.org/npmdoc/node-npmdoc-clui)
#### A Node.js toolkit for drawing nice command line tables, gauges, spinners, and sparklines.

[![NPM](https://nodei.co/npm/clui.png?downloads=true)](https://www.npmjs.com/package/clui)

[![apidoc](https://npmdoc.github.io/node-npmdoc-clui/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-clui_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-clui/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-clui/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-clui/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Nathan Peck",
        "email": "nathan@storydesk.com"
    },
    "bugs": {
        "url": "https://github.com/nathanpeck/clui/issues"
    },
    "dependencies": {
        "cli-color": "0.3.2"
    },
    "description": "A Node.js toolkit for drawing nice command line tables, gauges, spinners, and sparklines.",
    "devDependencies": {},
    "directories": {},
    "dist": {
        "shasum": "013d082ce86ddbf060b86d3927f89ab8c33bf423",
        "tarball": "https://registry.npmjs.org/clui/-/clui-0.3.1.tgz"
    },
    "gitHead": "f319d50ae02867dba70f33e441a5904ea1a65f04",
    "homepage": "https://github.com/nathanpeck/clui",
    "keywords": [
        "command line interface",
        "CLI",
        "sparkline",
        "progress bar",
        "spinner",
        "gauge",
        "line",
        "console",
        "buffer"
    ],
    "licenses": [
        {
            "type": "MIT",
            "url": "https://raw.githubusercontent.com/nathanpeck/clui/master/LICENSE"
        }
    ],
    "main": "./lib/clui",
    "maintainers": [
        {
            "name": "nathanpeck",
            "email": "nathan@storydesk.com"
        }
    ],
    "name": "clui",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git://github.com/nathanpeck/clui.git"
    },
    "scripts": {},
    "version": "0.3.1"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module clui](#apidoc.module.clui)
1.  [function <span class="apidocSignatureSpan">clui.</span>Gauge (value, maxValue, width, dangerZone, suffix)](#apidoc.element.clui.Gauge)
1.  [function <span class="apidocSignatureSpan">clui.</span>Line (defaultBuffer)](#apidoc.element.clui.Line)
1.  [function <span class="apidocSignatureSpan">clui.</span>LineBuffer (userOptions)](#apidoc.element.clui.LineBuffer)
1.  [function <span class="apidocSignatureSpan">clui.</span>Progress (width)](#apidoc.element.clui.Progress)
1.  [function <span class="apidocSignatureSpan">clui.</span>Sparkline (points, suffix)](#apidoc.element.clui.Sparkline)
1.  [function <span class="apidocSignatureSpan">clui.</span>Spinner (message)](#apidoc.element.clui.Spinner)
1.  [function <span class="apidocSignatureSpan">clui.</span>Table ()](#apidoc.element.clui.Table)



# <a name="apidoc.module.clui"></a>[module clui](#apidoc.module.clui)

#### <a name="apidoc.element.clui.Gauge"></a>[function <span class="apidocSignatureSpan">clui.</span>Gauge (value, maxValue, width, dangerZone, suffix)](#apidoc.element.clui.Gauge)
- description and source-code
```javascript
Gauge = function (value, maxValue, width, dangerZone, suffix) {
  if(maxValue === 0)
  {
    return '[]';
  }
  else
  {
    var barLength = Math.ceil(value/maxValue*width);
    if(barLength > width)
      barLength = width;

    var barColor = clc.green;
    if(value > dangerZone)
      barColor = clc.red;

    return '['+
      barColor(Array(barLength).join("|")) +  //The filled portion
      Array(width+1-barLength).join("-") +    //The empty portion
    '] ' + clc.blackBright(suffix);
  }
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.clui.Line"></a>[function <span class="apidocSignatureSpan">clui.</span>Line (defaultBuffer)](#apidoc.element.clui.Line)
- description and source-code
```javascript
Line = function (defaultBuffer) {
  var lineContent = "";
  var self = this;
  self.defaultBuffer = defaultBuffer;

  // Put text in the line
  this.text = function (text, styles) {
    for(var styleNumber in styles)
    {
      text = styles[styleNumber](text);
    }
    lineContent += text;
    return self;
  };

  // Put padding in the line.
  this.padding = function (width, styles) {
    var padding = Array(width+1).join(" ");
    for(var styleNumber in styles)
    {
      padding = styles[styleNumber](padding);
    }
    lineContent += padding;
    return self;
  };

  // Put padding in the line.
  this.column = function (text, columnWidth, textStyles) {
    var textWidth = ansiTrim(text).length;

    if(textWidth > columnWidth)
    {
      self.text(text.slice(0, columnWidth), textStyles);
    }
    else if(textWidth < columnWidth)
    {
      self.text(text, textStyles)
          .padding(columnWidth - textWidth);
    }
    else
    {
      self.text(text, textStyles);
    }
    return self;
  };

  // Fill the rest of the width of the line with space.
  this.fill = function (styles) {
    var fillWidth = process.stdout.columns-ansiTrim(lineContent).length;
    if(fillWidth > 0)
      self.padding(fillWidth, styles);
    return self;
  };

  // Store a line in a line buffer to be output later.
  this.store = function (buffer) {
    if(typeof buffer == 'undefined')
    {
      if(typeof self.defaultBuffer == 'undefined')
        process.stderr.write('Attempt to store a line in a line buffer, without providing a line buffer to store that line in.');
      else
        self.defaultBuffer.addLine(self);
    }
    else
    {
      buffer.addLine(self);
    }
    return self;
  };

  // Output a line directly to the screen.
  this.output = function () {
    process.stdout.write(lineContent+"\n");
    return self;
  };

  // Return the contents
  this.contents = function () {
    return lineContent;
  };
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.clui.LineBuffer"></a>[function <span class="apidocSignatureSpan">clui.</span>LineBuffer (userOptions)](#apidoc.element.clui.LineBuffer)
- description and source-code
```javascript
LineBuffer = function (userOptions) {
  var self = this;
  self.lines = [];

  //Merge the user defined settings (if there are any) with the default settings.
  var defaultOptions = {
    x: 0,
    y: 0,
    width: 'console',
    height: 'console',
    scroll: 0
  };

  if(typeof userOptions == 'undefined')
    self.userOptions = defaultOptions;
  else
  {
    self.userOptions = {
      x: pick(userOptions.x, defaultOptions.x),
      y: pick(userOptions.y, defaultOptions.y),
      width: pick(userOptions.width, defaultOptions.width),
      height: pick(userOptions.height, defaultOptions.height),
      scroll: pick(userOptions.scroll, defaultOptions.scroll)
    };
  }

  this.height = function ()
  {
    if(self.userOptions.height == 'console')
      return process.stdout.rows;
    else
      return self.userOptions.height;
  };

  this.width = function ()
  {
    if(self.userOptions.width == 'console')
      return process.stdout.columns;
    else
      return self.userOptions.width;
  };

  // Push a line of content into the buffer.
  this.addLine = function (lineObject) {
    self.lines.push(lineObject);
    return self;
  };

  // See if the buffer has enough content to fill the vertical space, if not fill the vertical space
  // with the designated fill line.
  this.fill = function (fillLine) {
    var fillHeight = self.height()-self.lines.length;
    if(fillHeight > 0)
    {
      for(var i = 0; i < fillHeight; i++)
      {
        self.addLine(fillLine);
      }
    }
    return self;
  };

  // Output a buffer full of lines.
  this.output = function () {
    // First grab a subset of the lines depending on the scroll location and the height of the buffer.
    var outputLines;
    var sliceEnd;
    var outputHeight = self.height();
    if(self.userOptions.scroll > self.lines.length)
      return;

    if(self.lines.length - self.userOptions.scroll > outputHeight)
      outputLines = self.lines.slice(self.userOptions.scroll, self.userOptions.scroll + outputHeight);
    else
      outputLines = self.lines.slice(self.userOptions.scroll);

    // First move the cursor to the location where we want the buffer to draw.
    var currentY = self.userOptions.y;
    outputLines.forEach(function (line) {
      process.stdout.write(clc.moveTo(self.userOptions.x, currentY));
      line.output();
      currentY++;
    });
  };
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.clui.Progress"></a>[function <span class="apidocSignatureSpan">clui.</span>Progress (width)](#apidoc.element.clui.Progress)
- description and source-code
```javascript
Progress = function (width) {
  var currentValue = 0;
  var maxValue = 0;
  var self = this;

  this.update = function (currentValue, maxValue) {
    if(maxValue === 0)
    {
      return '[]';
    }
    else
    {
      var barLength = Math.ceil(currentValue / maxValue * width);
      if(barLength > width)
        barLength = width;

      return '['+
        clc.green(Array(barLength).join("|")) +  //The filled portion
        Array(width + 1 - barLength).join("-") +    //The empty portion
      '] ' + clc.blackBright(Math.ceil(currentValue / maxValue * 100) + '%');
    }
  };
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.clui.Sparkline"></a>[function <span class="apidocSignatureSpan">clui.</span>Sparkline (points, suffix)](#apidoc.element.clui.Sparkline)
- description and source-code
```javascript
Sparkline = function (points, suffix) {
  if(typeof suffix == 'undefined')
    suffix = '';

  var max = Math.max.apply(Math, points);

  var scaledSequence = points.map(function (thisPoint) {
    if(max === 0)
      return [0, 0];
    else if(thisPoint === 0)
      return [0, 0];
    else
      return [
        Math.ceil(thisPoint / max * (sparklineSymbols.length-1)),
        thisPoint
      ];
  });

  var sparklineGraph = '';
  var alreadyDrawnMax = false;
  scaledSequence.forEach(function (symbolNumber) {
    if(symbolNumber[1] == max & !alreadyDrawnMax)
    {
      sparklineGraph += clc.green(sparklineSymbols[symbolNumber[0]]);
      alreadyDrawnMax = true;
    }
    else
      sparklineGraph += sparklineSymbols[symbolNumber[0]];
  });

  return sparklineGraph + '  ' + clc.blackBright(points[points.length-1] + suffix + ' (') + clc.green(max + suffix) + clc.blackBright
(')');
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.clui.Spinner"></a>[function <span class="apidocSignatureSpan">clui.</span>Spinner (message)](#apidoc.element.clui.Spinner)
- description and source-code
```javascript
Spinner = function (message) {
  var spinnerMessage = message;

  this.start = function () {
    var self = this;
    var spinner = 'win32' == process.platform ? ['|','/','-','\\'] : ['◜','◠','◝','◞','◡','◟'];

    function play(arr, interval) {
      var len = arr.length, i = 0;
      interval = interval || 100;

      var drawTick = function () {
        var str = arr[i++ % len];
        process.stdout.write('\u001b[0G' + str + '\u001b[90m' + spinnerMessage + '\u001b[0m');
      };

      self.timer = setInterval(drawTick, interval);
    }

    var frames = spinner.map(function(c) {
      return sprintf('  \u001b[96m%s ', c);
    });

    play(frames, 70);
  };

  this.message = function (message) {
    spinnerMessage = message;
  };

  this.stop = function () {
    process.stdout.write('\u001b[0G\u001b[2K');
    clearInterval(this.timer);
  };
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.clui.Table"></a>[function <span class="apidocSignatureSpan">clui.</span>Table ()](#apidoc.element.clui.Table)
- description and source-code
```javascript
Table = function () {
  var self = this;
  var tableContent = '';

  // Adds a new table row to the output
  self.tr = function () {
    return self;
  };

  // Adds a new table cell to the output
  self.td = function (cellContent, cellWidth) {
    return self;
  };

  // Draw this table to the screen
  self.output = function () {
    return self;
  };

  return self;
}
```
- example usage
```shell
n/a
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
