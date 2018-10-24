# bytenode
A minimalist bytecode compiler for Node.js.

This tool truly compiles your Javascript code into `V8` bytecode. It can be used with Node.js >= 6.x.x (or maybe even Node.js v5), as well as Electron and NW.js (examples will be added soon).

It allows you to hide or protect your source code in a better way than obfuscation and other tricks. I have to say that I'm aware of the counter arguments against this "protect" attitude (for example: [01](https://github.com/electron/electron/issues/3041), [02](https://stackoverflow.com/questions/48890215/hide-source-code-of-electron-alteast-1-file-possible)), but it still a valid use case and the lack of this feature continues to keep many people from using Node.js.

It's more like how python generates `.pyc` of `.py` files, but in Node.js world ( `.js > .jsc` ).

## Install

```console
npm install --save bytenode
```

## How to use (programmatically)?

You have to run node with this flag `node --no-lazy` in order to compile all functions in your code eagerly. Or, if you have no control on how your code will be run, you can use `v8.setFlagsFromString('--no-lazy')` function as well.

```javascript
const bytenode = require('bytenode');
```

### How to compile Javascript code?

```javascript
let helloWorldBytecode = bytenode.compileCode(`console.log('Hello World!');`);
```
This `helloWorldBytecode` bytecode can be run saved to a file. However, if you want to use your code as a module (i.e. if your file has some `exports`), you have to compile it using `bytenode.compileFile()`, or wrap your code manually, using `Module.wrap()` function.

### How to run compiled bytecode?

```
> bytenode.runBytecode(helloWorldBytecode);
Hello World!
```

### How to compile a CommonJS module?

```javascript
let compiledFilename = bytenode.compileFile('/path/to/your/file.js', '/path/to/compiled/file.jsc');
```
```javascript
let compiledFilename = bytenode.compileFile('/path/to/your/file.js');
```
This function compiles your `.js` file, saves `.jsc` to disk, and returns the path to that compiled file `/path/to/your/file.jsc`.

The second argument is optional, the default behavior is to save the compiled file using the same path and name of the original file, but with `.jsc` extension.

### How to un bytecode compiled file?

```javascript
bytenode.runBytecodeFile('/path/to/compiled/file.jsc');
```
This will run your file and run whatever bytecode inside it.

```javascript
let myModule = require('/path/to/your/file.jsc');
```
Just like regular `.js` files. You can also omit the extension `.jsc`.

The `.jsc` file must have been compiled using `bytenode.compileFile()`, or have been wrapped inside `Module.wrap()` function. Otherwise it won't work.

## bytenode cli

```
Usage: cli.js [options] filename [filename2 filename3 ...]

  Options:

    -r, --run            run <file> [<file2> <file3> ...]
    -c, --compile        compile <file> [<file2> <file3> ...]

    -h, --help           output usage information
    -v, --version        output the version number
```

Some quick examples:

* Compile `express-server.js` to `express-server.jsc`.
```console
user@machine:~$ ./cli.js --compile express-server.js
```

* run your compiled file `express-server.jsc`.
```console
user@machine:~$ ./cli.js --run express-server.jsc
Server listening on port 3000
```

* Compile all `.js` files in `./app` directory.
```console
user@machine:~$ ./cli.js --compile ./app/*.js
```

* Compile all `.js` files in your project.
```console
user@machine:~$ ./cli.js --compile ./**/*.js
```
Note: you may need to enable `globstar` option in bash (you should add it to `~/.bashrc`):
`shopt -s globstar`

## Todo:
- [ ] Write some tests.
- [ ] Add some examples.
- [ ] Add an NW.js example (NW.js has a similar tool `nwjc`).
- [ ] Add an Electron example.

## Acknowledgements

I had the idea of this tool many years ago. However, I finally decided to implement it after seeing this [issue](https://github.com/nodejs/node/issues/11842) by @hashseed. Also, some parts was inspired by [v8-compile-cache](https://github.com/zertosh/v8-compile-cache) by @zertosh.
