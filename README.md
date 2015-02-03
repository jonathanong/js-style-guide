
# JavaScript Style Guide

For a purely aesthetic style guide, 
I recommend using [standard](https://github.com/feross/standard).

## Organization

Whenever possible, split your code into multiple files and modules!
Each module should be defined:

- From high level to low level - when reading the code, developers should immediately understand how the module works, then continue reading to learn the implementation details.
- From public to private - when creating modules, the most important aspect is the `input -> output`. This should be almost immediately obviously.

Code in a single file should generally be structured in the following order:

1. Global imports - `import lib from 'lib'`
2. Relative imports - `import src from './src'`
3. Exports - `export default Main`
4. Variables - `var a = 1`
5. Main - `function Main() {}`
6. Prototype Properties - `Main.prototype.something = true`
7. Utilities - `function add(a, b) {return a + b}`

### Don't hoist variables!

There's no need for that!
It makes your code look messy and more difficult to understand.
It also demonstrates a lack of understanding of how JavaScript variables work.

### Avoid defining (anonymous) functions within functions

Doing so makes your code slower,
but refactoring anonymous functions into function declarations
makes your code easier to read.

```js
function sumArray(arr) {
  return arr.reduce(function (a, b) {
    return a + b
  }, 0)
}
```

should really be:

```js
function sumArray(arr) {
  return arr.reduce(sum, 0)
}

function sum(a, b) {
  return a + b
}
```

However, doing the same in the following example is unnecessary as a closure will always be required:

```js
function multiplyArray(x) {
  return arr.map(function (y) {
    return x * y
  })
}
```

### Move function declarations to the end of the closure and after the return statement

Name each function declaration something meaningful so that a developer knows what it does by its name.
Put all the implementation details within that function.

```js
function () {
  // do this
  // do that
  doTheNextThing()

  return 'something'

  function doTheNextThing() {

  }
}
```

## Performance

### Avoid using `Function.prototype.bind`

It's slow and people are inevitably going to complain!

### Avoid creating closures within functions

However, this is the lesser of other evils, such as `.bind()`.

### Minimize the number of variables within functions with closures

See this comment: https://github.com/jonathanong/style-guide/commit/ac8595b771d14e584e8b5e4a4b99ffac25aa29ae#commitcomment-7075319

### Move try/catches to separate, small functions

You should be writing small functions anyways, but if there are any try/catches in a function,
try to move it out into a separate function.

Note: in the future, this will not be relevant in v8

## Anti-Patterns

### Don't throw non-errors!

Strings are not errors! If `!(err instanceof Error)`, then it's not an error!
The primary reason for this is so that errors have stack traces!
Otherwise, where the hell did that error come from?

### Avoid using node.js event emitters

The fact that they create uncaught exceptions if you do not add listeners to the `error` event is a major problem for beginners in node.js.
In general, if your API requires events, then it may be too complicated.
Try making your API have a single input and single output.

### Avoid using streams

For binary data, streams are fine, but you should in general abstract them into simpler functions.
For example, https://github.com/expressjs/body-parser abstracts requests to return the body of a request.

Other than being a subclass of emitters, streams are very buggy in node.js and have many minor edge cases that may cause leaks if not handled properly.

My suggestion is to use many modules that abstract streams into `fn(stream) -> result` results.

### Don't use domains

When abstracting emitters and streams, there is very little need to use domains.
Plus, domains are about to be deprecated.

## Git diffs

When working with teams, you want to minimize git diffs so that you can properly assign blame.
When deciding on a JS style guide, git diffs is really the only thing no one can argue against.

### No trailing whitespace

Keep it consistent!

### Trailing line break

Many editors such as `atom` have this enabled by default.

### One var/const/let per line

Don't do any crazy whitespacing with commas!
One declaration per line makes diffs easy!

```js
let a = 1;
let b = 2;
```

### Define arrays and objects in multiple lines and with traililng commas

```js
var thingsILike = [
  'coffee',
  'cigarettes',
  'cocaine',
]

var stuff = {
  coffee: true,
  cigarettes: true,
  cocaine: true,
}
```

### Do not define functions in an array

Don't do this:

```js
var fns = [function () {

}, function () {

}]
```

Instead, use function declarations:

```js
parallel([fn1, fn2, fn3], callback)

function fn1() {}
function fn2() {}
function fn3() {}
```

Of course, use actually helpful function names. The same rule could apply to objects, but generally they aren't as difficult to read within objects.
