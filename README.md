<!--

@license Apache-2.0

Copyright (c) 2018 The Stdlib Authors.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

-->


<details>
  <summary>
    About stdlib...
  </summary>
  <p>We believe in a future in which the web is a preferred environment for numerical computation. To help realize this future, we've built stdlib. stdlib is a standard library, with an emphasis on numerical and scientific computation, written in JavaScript (and C) for execution in browsers and in Node.js.</p>
  <p>The library is fully decomposable, being architected in such a way that you can swap out and mix and match APIs and functionality to cater to your exact preferences and use cases.</p>
  <p>When you use stdlib, you can be absolutely certain that you are using the most thorough, rigorous, well-written, studied, documented, tested, measured, and high-quality code out there.</p>
  <p>To join us in bringing numerical computing to the web, get started by checking us out on <a href="https://github.com/stdlib-js/stdlib">GitHub</a>, and please consider <a href="https://opencollective.com/stdlib">financially supporting stdlib</a>. We greatly appreciate your continued support!</p>
</details>

# Inspect Stream

[![NPM version][npm-image]][npm-url] [![Build Status][test-image]][test-url] [![Coverage Status][coverage-image]][coverage-url] <!-- [![dependencies][dependencies-image]][dependencies-url] -->

> [Transform stream][transform-stream] for inspecting streamed data.

<section class="installation">

## Installation

```bash
npm install @stdlib/streams-node-inspect
```

Alternatively,

-   To load the package in a website via a `script` tag without installation and bundlers, use the [ES Module][es-module] available on the [`esm`][esm-url] branch (see [README][esm-readme]).
-   If you are using Deno, visit the [`deno`][deno-url] branch (see [README][deno-readme] for usage intructions).
-   For use in Observable, or in browser/node environments, use the [Universal Module Definition (UMD)][umd] build available on the [`umd`][umd-url] branch (see [README][umd-readme]).

The [branches.md][branches-url] file summarizes the available branches and displays a diagram illustrating their relationships.

To view installation and usage instructions specific to each branch build, be sure to explicitly navigate to the respective README files on each branch, as linked to above.

</section>

<section class="usage">

## Usage

```javascript
var inspectStream = require( '@stdlib/streams-node-inspect' );
```

<a name="inspect-stream"></a>

#### inspectStream( \[options,] clbk )

Creates a [transform stream][transform-stream] for inspecting streamed data.

```javascript
function log( chunk, idx ) {
    console.log( 'index: %d', idx );
    console.log( chunk );
}

var stream = inspectStream( log );

stream.write( 'a' );
stream.write( 'b' );
stream.write( 'c' );

stream.end();
/* =>
'index: 0'
'a'
'index: 1'
'b'
'index: 2'
'c'
*/
```

The function accepts the following `options`:

-   **objectMode**: specifies whether a [stream][stream] should operate in [objectMode][object-mode]. Default: `false`.
-   **highWaterMark**: specifies the `Buffer` level at which `write()` calls start returning `false`.
-   **allowHalfOpen**: specifies whether a [stream][stream] should remain open even if one side ends. Default: `false`.
-   **readableObjectMode**: specifies whether the readable side should be in [objectMode][object-mode]. Default: `false`.

To set [stream][stream] `options`,

```javascript
function log( chunk, idx ) {
    console.log( 'index: %d', idx );
    console.log( chunk );
}

var opts = {
    'objectMode': true,
    'highWaterMark': 64,
    'allowHalfOpen': true,
    'readableObjectMode': false // overridden by `objectMode` option when `objectMode=true`
};

var stream = inspectStream( opts, log );
```

#### inspectStream.factory( \[options] )

Returns a `function` for creating [streams][transform-stream] which are identically configured according to provided `options`.

```javascript
var opts = {
    'objectMode': true,
    'highWaterMark': 64
};

var factory = inspectStream.factory( opts );
```

This method accepts the same `options` as [`inspectStream()`](#inspect-stream).

##### factory( clbk )

Creates a [transform stream][transform-stream] for inspecting streamed data.

```javascript
function log( chunk, idx ) {
    console.log( 'index: %d', idx );
    console.log( chunk );
}

var factory = inspectStream.factory();

// Create 10 identically configured streams...
var streams = [];
var i;
for ( i = 0; i < 10; i++ ) {
    streams.push( factory( log ) );
}
```

#### inspectStream.objectMode( \[options,] clbk )

This method is a convenience function to create [streams][stream] which **always** operate in [objectMode][object-mode].

<!-- eslint-disable object-curly-newline -->

```javascript
function log( chunk, idx ) {
    console.log( 'index: %d', idx );
    console.log( chunk );
}

var stream = inspectStream.objectMode( log );

stream.write( { 'value': 'a' } );
stream.write( { 'value': 'b' } );
stream.write( { 'value': 'c' } );

stream.end();
/* =>
'index: 0'
{'value': 'a'}
'index: 1'
{'value': 'b'}
'index: 2'
{'value': 'c'}
*/
```

This method accepts the same `options` as [`inspectStream()`](#inspect-stream); however, the method will **always** override the [objectMode][object-mode] option in `options`.

</section>

<!-- /.usage -->

<section class="examples">

## Examples

<!-- eslint no-undef: "error" -->

```javascript
var parseJSON = require( '@stdlib/utils-parse-json' );
var stdout = require( '@stdlib/streams-node-stdout' );
var transformFactory = require( '@stdlib/streams-node-transform' ).factory;
var inspect = require( '@stdlib/streams-node-inspect' ).objectMode;

function parse( chunk, enc, clbk ) {
    clbk( null, parseJSON( chunk ) );
}

function pluck( chunk, enc, clbk ) {
    clbk( null, chunk.value );
}

function square( chunk, enc, clbk ) {
    var v = +chunk;
    clbk( null, v*v );
}

function toStr( chunk, enc, clbk ) {
    clbk( null, chunk.toString() );
}

function join( chunk, enc, clbk ) {
    clbk( null, chunk+'\n' );
}

function logger( name ) {
    return log;

    function log( chunk, idx ) {
        console.log( 'name: %s', name );
        console.log( 'index: %d', idx );
        console.log( chunk );
    }
}

// Create a factory for generating streams running in `objectMode`:
var tStream = transformFactory({
    'objectMode': true
});

// Create streams for each transform:
var s1 = tStream( parse );
var i1 = inspect( logger( 'parse' ) );
var s2 = tStream( pluck );
var i2 = inspect( logger( 'pluck' ) );
var s3 = tStream( square );
var i3 = inspect( logger( 'square' ) );
var s4 = tStream( toStr );
var i4 = inspect( logger( 'toString' ) );
var s5 = tStream( join );
var i5 = inspect( logger( 'join' ) );

// Create the pipeline:
s1.pipe( i1 )
    .pipe( s2 )
    .pipe( i2 )
    .pipe( s3 )
    .pipe( i3 )
    .pipe( s4 )
    .pipe( i4 )
    .pipe( s5 )
    .pipe( i5 )
    .pipe( stdout );

// Write data to the pipeline...
var v;
var i;
for ( i = 0; i < 100; i++ ) {
    v = '{"value":'+i+'}';
    s1.write( v, 'utf8' );
}
s1.end();
```

</section>

<!-- /.examples -->

<!-- Section for related `stdlib` packages. Do not manually edit this section, as it is automatically populated. -->

<section class="related">

* * *

## See Also

-   <span class="package-name">[`@stdlib/streams-node/debug`][@stdlib/streams/node/debug]</span><span class="delimiter">: </span><span class="description">transform stream for debugging stream pipelines.</span>

</section>

<!-- /.related -->

<!-- Section for all links. Make sure to keep an empty line after the `section` element and another before the `/section` close. -->


<section class="main-repo" >

* * *

## Notice

This package is part of [stdlib][stdlib], a standard library for JavaScript and Node.js, with an emphasis on numerical and scientific computing. The library provides a collection of robust, high performance libraries for mathematics, statistics, streams, utilities, and more.

For more information on the project, filing bug reports and feature requests, and guidance on how to develop [stdlib][stdlib], see the main project [repository][stdlib].

#### Community

[![Chat][chat-image]][chat-url]

---

## License

See [LICENSE][stdlib-license].


## Copyright

Copyright &copy; 2016-2024. The Stdlib [Authors][stdlib-authors].

</section>

<!-- /.stdlib -->

<!-- Section for all links. Make sure to keep an empty line after the `section` element and another before the `/section` close. -->

<section class="links">

[npm-image]: http://img.shields.io/npm/v/@stdlib/streams-node-inspect.svg
[npm-url]: https://npmjs.org/package/@stdlib/streams-node-inspect

[test-image]: https://github.com/stdlib-js/streams-node-inspect/actions/workflows/test.yml/badge.svg?branch=v0.2.1
[test-url]: https://github.com/stdlib-js/streams-node-inspect/actions/workflows/test.yml?query=branch:v0.2.1

[coverage-image]: https://img.shields.io/codecov/c/github/stdlib-js/streams-node-inspect/main.svg
[coverage-url]: https://codecov.io/github/stdlib-js/streams-node-inspect?branch=main

<!--

[dependencies-image]: https://img.shields.io/david/stdlib-js/streams-node-inspect.svg
[dependencies-url]: https://david-dm.org/stdlib-js/streams-node-inspect/main

-->

[chat-image]: https://img.shields.io/gitter/room/stdlib-js/stdlib.svg
[chat-url]: https://app.gitter.im/#/room/#stdlib-js_stdlib:gitter.im

[stdlib]: https://github.com/stdlib-js/stdlib

[stdlib-authors]: https://github.com/stdlib-js/stdlib/graphs/contributors

[umd]: https://github.com/umdjs/umd
[es-module]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules

[deno-url]: https://github.com/stdlib-js/streams-node-inspect/tree/deno
[deno-readme]: https://github.com/stdlib-js/streams-node-inspect/blob/deno/README.md
[umd-url]: https://github.com/stdlib-js/streams-node-inspect/tree/umd
[umd-readme]: https://github.com/stdlib-js/streams-node-inspect/blob/umd/README.md
[esm-url]: https://github.com/stdlib-js/streams-node-inspect/tree/esm
[esm-readme]: https://github.com/stdlib-js/streams-node-inspect/blob/esm/README.md
[branches-url]: https://github.com/stdlib-js/streams-node-inspect/blob/main/branches.md

[stdlib-license]: https://raw.githubusercontent.com/stdlib-js/streams-node-inspect/main/LICENSE

[stream]: https://nodejs.org/api/stream.html

[object-mode]: https://nodejs.org/api/stream.html#stream_object_mode

[transform-stream]: https://nodejs.org/api/stream.html

<!-- <related-links> -->

[@stdlib/streams/node/debug]: https://github.com/stdlib-js/streams-node-debug

<!-- </related-links> -->

</section>

<!-- /.links -->
