# postcss-px-to-viewport [![NPM version](https://badge.fury.io/js/postcss-px-to-viewport.svg)](http://badge.fury.io/js/postcss-px-to-viewport)

A plugin for [PostCSS](https://github.com/ai/postcss) that generates viewport units (vw, vh, vmin, vmax) from pixel units.

## Install
```
$ npm install postcss-px-to-viewport --save-dev
```

## Usage

If your project involves a fixed width, this script will help to convert pixels into viewport units.

### Input/Output

```css
// input

.class {
  margin: -10px .5vh;
  padding: 5vmin 9.5px 1px;
  border: 3px solid black;
  border-bottom-width: 1px;
  font-size: 14px;
  line-height: 20px;
}

.class2 {
  border: 1px solid black;
  margin-bottom: 1px;
  font-size: 20px;
  line-height: 30px;
}

@media (min-width: 750px) {
  .class3 {
    font-size: 16px;
    line-height: 22px;
  }
}

// output

.class {
  margin: -3.125vw .5vh;
  padding: 5vmin 2.96875vw 1px;
  border: 0.9375vw solid black;
  border-bottom-width: 1px;
  font-size: 4.375vw;
  line-height: 6.25vw;
}

.class2 {
  border: 1px solid black;
  margin-bottom: 1px;
  font-size: 6.25vw;
  line-height: 9.375vw;
}

@media (min-width: 234.375vw) {
  .class3 {
    font-size: 5vw;
    line-height: 6.875vw;
  }
}
```

### Example

```js
'use strict';

var fs = require('fs');
var postcss = require('postcss');
var pxToViewport = require('..');
var css = fs.readFileSync('main.css', 'utf8');
var options = {
    replace: false
};
var processedCss = postcss(pxToViewport(options)).process(css).css;

fs.writeFile('main-viewport.css', processedCss, function (err) {
  if (err) {
    throw err;
  }
  console.log('File with viewport units written.');
});
```

### Options

Default:
```js
{
  unitToConvert: 'px',
  viewportWidth: 320,
  viewportHeight: 568, // not now used; TODO: need for different units and math for different properties
  unitPrecision: 5,
  propList: ['*'],
  viewportUnit: 'vw',
  fontViewportUnit: 'vw',  // vmin is more suitable.
  selectorBlackList: [],
  minPixelValue: 1,
  mediaQuery: false,
  exclude: [] // ignore some files
}
```
- `unitToConvert` (String) unit to convert, by default, it is px.
- `viewportWidth` (Number) The width of the viewport.
- `viewportHeight` (Number) The height of the viewport.
- `unitPrecision` (Number) The decimal numbers to allow the vw units to grow to.
- `propList` (Array) The properties that can change from px to vw.
  - Values need to be exact matches.
  - Use wildcard * to enable all properties. Example: ['*']
  - Use * at the start or end of a word. (['*position*'] will match background-position-y)
  - Use ! to not match a property. Example: ['*', '!letter-spacing']
  - Combine the "not" prefix with the other prefixes. Example: ['*', '!font*']
- `viewportUnit` (String) Expected units.
- `fontViewportUnit` (String) Expected units for font.
- `selectorBlackList` (Array) The selectors to ignore and leave as px.
    - If value is string, it checks to see if selector contains the string.
        - `['body']` will match `.body-class`
    - If value is regexp, it checks to see if the selector matches the regexp.
        - `[/^body$/]` will match `body` but not `.body`
- `minPixelValue` (Number) Set the minimum pixel value to replace.
- `mediaQuery` (Boolean) Allow px to be converted in media queries.
- `exclude` (Array or Regexp) Ignore some files like 'node_modules'
    - If value is regexp, will ignore the matches files.
    - If value is array, the elements of the array are regexp.

### Use with gulp-postcss
add to your gulp config:
```js
var gulp = require('gulp');
var postcss = require('gulp-postcss');
var pxtoviewport = require('postcss-px-to-viewport');

gulp.task('css', function () {

    var processors = [
        pxtoviewport({
            viewportWidth: 320,
            viewportUnit: 'vmin'
        })
    ];

    return gulp.src(['build/css/**/*.css'])
        .pipe(postcss(processors))
        .pipe(gulp.dest('build/css'));
});
```
### Use with Postcss configuration file
add to postcss.config.js
```js
module.exports = {
  plugins: {
    ...
    'postcss-px-to-viewport': {
      // options
    }
  }
}
```
