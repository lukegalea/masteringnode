
# Globals

 As we have learnt node's module system discourages the use of globals, however node provides a few important globals for us to use. The first and most important is the `process` global which exposes process manipulation such as signalling, exiting, the process id (pid), and more. Other globals help drive to be similar to other familiar JavaScript environments such as the browser, by providing a `console` object.

## console

The `console` object contains several methods which are used to output information to _stdout_ or _stderr_. Let's take a look at what each method does.

### console.log()

The most frequently used console method is `console.log()` which simply writes to _stdout_ with a line feed (`\n`). Currently aliased as `console.info()`.

    console.log('wahoo');
	// => wahoo

    console.log({ foo: 'bar' });
	// => [object Object]

### console.error()

Identical to `console.log()`, however it writes to _stderr_. Aliased as `console.warn()` as well.

    console.error('database connection failed');

### console.dir()

Uses the _sys_ module's `inspect()` method to pretty-print the object to
_stdout_.

    console.dir({ foo: 'bar' });
    // => { foo: 'bar' } 

### console.assert()

Asserts that the given expression is truthy, or throws an exception.

    console.assert(connected, 'Database connection failed');

## process

The `process` object is plastered with goodies. First we will take a look
at some properties that provide information about the node process itself.

### process.version

The version property contains the node version string, for example "v0.1.103".

### process.installPrefix

Exposes the installation prefix, in my case "_/usr/local_", as node's binary was installed to "_/usr/local/bin/node_".

### process.execPath

Path to the executable itself "_/usr/local/bin/node_".

### process.platform

Exposes a string indicating the platform you are running on, for example "darwin".

### process.pid

The process id.

### process.cwd()

Returns the current working directory, for example:

    cd ~ && node
    node> process.cwd()
    "/Users/tj"

### process.chdir()

Changes the current working directory to the path passed.

    process.chdir('/foo');

### process.getuid()

Returns the numerical user id of the running process.

### process.setuid()

Sets the effective user id for the running process. This method accepts both a numerical id, as well as a string. For example both `process.setuid(501)`, and `process.setuid('tj')` are valid.

### process.getgid()

Returns the numerical group id of the running process.

### process.setgid()

Similar to `process.setuid()` however operates on the group, also accepting a numerical value or string representation. For example `process.setgid(20)` or `process.setgid('www')`.

### process.env

An object containing the user's environment variables, for example:

    { PATH: '/Users/tj/.gem/ruby/1.8/bin:/Users/tj/.nvm/current/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin:/usr/X11/bin'
	, PWD: '/Users/tj/ebooks/masteringnode'
	, EDITOR: 'mate'
	, LANG: 'en_CA.UTF-8'
	, SHLVL: '1'
	, HOME: '/Users/tj'
	, LOGNAME: 'tj'
	, DISPLAY: '/tmp/launch-YCkT03/org.x:0'
	, _: '/usr/local/bin/node'
	, OLDPWD: '/Users/tj'
	}

### process.argv

When executing a file with the `node` executable `process.argv` provides access to the argument vector, the first value being the node executable, second being the filename, and remaining values being the arguments passed.

For example our source file _./src/process/misc.js_ can be executed by running:

    $ node src/process/misc.js foo bar baz

in which we call `console.dir(process.argv)`, outputting the following:

	[ 'node'
	, '/Users/tj/EBooks/masteringnode/src/process/misc.js'
	, 'foo'
	, 'bar'
	, 'baz'
	]

### process.exit()

The `process.exit()` method is synonymous with the C function `exit()`, in which a exit code > 0 is passed indicating failure, or 0 to indicate success. When invoked the _exit_ event is emitted, allowing a short time for arbitrary processing to occur before `process.reallyExit()` is called with the given status code.

### process.on()

The process itself is an `EventEmitter`, allowing you to do things like listen for uncaught exceptions, via the _uncaughtException_ event:

	process.on('uncaughtException', function(err){
	    console.log('got an error: %s', err.message);
	    process.exit(1);
	});

	setTimeout(function(){
	    throw new Error('fail');
	}, 100);

### process.kill()

`process.kill()` method sends the signal passed to the given _pid_, defaulting to **SIGINT**. In our example below we send the **SIGTERM** signal to the same node process to illustrate signal trapping, after which we output "terminating" and exit. Note that our second timeout of 1000 milliseconds is never reached.

	process.on('SIGTERM', function(){
	    console.log('terminating');
	    process.exit(1);
	});

	setTimeout(function(){
	    console.log('sending SIGTERM to process %d', process.pid);
	    process.kill(process.pid, 'SIGTERM');
	}, 500);

	setTimeout(function(){
	    console.log('never called');
	}, 1000);

### errno

The `process` object is host of the error numbers, these reference what you would find in C-land, for example `process.EPERM` represents a permission based error, while `process.ENOENT` represents a missing file or directory. Typically these are used within bindings to bridge the gap between C++ and JavaScript, however useful for handling exceptions as well:

    if (err.errno === process.ENOENT) {
		// Display a 404 "Not Found" page
	} else {
		// Display a 500 "Internal Server Error" page
	}