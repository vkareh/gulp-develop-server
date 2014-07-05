gulp-develop-server
====================

> run your node.js server and automatically restart with gulp.

[![Build Status](http://img.shields.io/travis/narirou/gulp-develop-server/master.svg?style=flat)](https://travis-ci.org/narirou/gulp-develop-server)
[![Npm Modules](http://img.shields.io/npm/v/gulp-develop-server.svg?style=flat)](https://www.npmjs.org/package/gulp-develop-server)
[![MIT Licensed](http://img.shields.io/badge/license-MIT-blue.svg?style=flat)](http://opensource.org/licenses/MIT)


gulp-develop-server is a development assistant for node.js server that runs
the process and automatically restarts it when a file is modified. 


installation
------------

```bash
npm install gulp-develop-server --save-dev
```



usage
-----

```javascript
var gulp   = require( 'gulp' ),
    server = require( 'gulp-develop-server' );

// run server
gulp.task( 'server:start', function() {
    server.listen( { path: 'app.js' } );
});

// restart server if app.js changed
gulp.task( 'server:restart', [ 'server:start' ], function() {
     gulp.watch( [ 'app.js' ], server.restart );
});
```



api
---

###server.listen( options[, callback] )

**options {Object}**  

- `path`  
    - type: {String}
    - exapmle: `'./your_app.js'`
    - Main application path. This option is required.

- `env`  
    - type: {Object}  
    - default: `{ NODE_ENV: 'development' }`  
    - example: `{ PORT: 3000, NODE_ENV: 'production' }`  
    - Server environment settings.  

- `execArgv`  
    - type: {Array}  
    - example: `[ '--harmony' ]`  
    - run node process with this options.  

- `delay`   
    - type: {Numeric}  
    - default: `600`  
    - If not receive an error after `options.delay` seconds, regard the server listening success.
    - This option needs to adjust according to your application's initialize time.

- `successMessage`  
    - type: {RegExp}
    - default: `/^server listening$/`  
    - If your application send a specific message by `process.send` method, regard the server listening success.

- `killSignal`  
    - type: {String}
    - default: `SIGTERM`

**callback( error )**  


###server.restart( [callback] ) / server.changed( [callback] )

**callback( error )**  


###server()

Stream server restart function.  
caution: If many files send to this stream, the server try to restart many times.  


###server.kill( [signal, callback] )

sending kill signal to the server process.  
**signal {String}**  
**callback( error )**  


###server.reset( [signal, callback] )

sending kill signal to the server process and reset the options to default.   
**signal {String}**  
**callback( error )**  



more examples
-------------

####with [gulp-livereload](https://github.com/vohof/gulp-livereload):

```javascript
var gulp       = require( 'gulp' ),
    server     = require( 'gulp-develop-server' ),
    livereload = require( 'gulp-livereload' );

gulp.task( 'server:start', function() {
    server.listen( { path: 'app.js' }, livereload.listen );
});

// If server scripts change, restart the server and then livereload.
gulp.task( 'server:restart', [ 'server:start' ], function() {
    function restart() {
        server.changed( function( error ) {
            if( ! error ) livereload.changed();
        });
    }
    gulp.watch( [ 'app.js', 'routes/**/*.js' ], restart );
});
```


####with Stream:

```javascript
var gulp       = require( 'gulp' ),
    changed    = require( 'gulp-changed' ),
    server     = require( 'gulp-develop-server' ),
    livereload = require( 'gulp-livereload' );

gulp.task( 'server:start', function() {
    server.listen( { path: 'app.js' }, livereload.listen );
});

gulp.task( 'server:restart', [ 'server:start' ], function() {
    gulp.src( [ './app.js' ], { read: false } )
        .pipe( changed( './' ) )
        .pipe( server() )
        .pipe( livereload() );
});
```
