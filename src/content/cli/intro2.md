---
title: Basic CLI usage
numberHeadings: true
---

## Display a file from the file system

<span class="tutorialStep"></span> From inside the `samples` folder:

```console
$ cat sample.txt
This is a text file.
$ ori sample.txt
This is a text file.
```

At this basic level, ori behaves like the `cat` command, but it can handle more than just files.

When you invoke ori:

1. It parses its arguments as an expression.
2. It evaluates that expression, looking up identifiers in the current scope (defined below).
3. If the value of the expression is a JavaScript module, ori imports the module and obtains its default export. If it's a JavaScript function, ori executes it.
4. It displays the result.

Here ori parses the expression `sample.txt` as an identifier. In JavaScript, `sample.txt` is not a valid identifier because it contains a period, but ori's expression parser can recognize file names as identifiers. ori looks up that identifier in the current _scope_. By default, the scope includes:

- the files in the current folder
- the functions exported by JavaScript modules in the current folder
- the functions built into ori

In this case, ori finds that "sample.txt" is the name of a file, and reads that file from the current folder. The file's contents become the result of the expression, which ori then renders to the console.

## Invoke a function

<span class="tutorialStep"></span> If you ask ori for the sample file `greet.js`, it returns the contents of that file:

```console
$ ori greet.js
export default (name = "world") => `Hello, ${name}.`;
```

<span class="tutorialStep"></span> If you leave off the `.js` extension, ori _invokes_ the function exported by that file:

```console
$ ori greet
Hello, world.
```

<span class="tutorialStep"></span> Or, with explicit parentheses:

```console
$ ori "greet()"
Hello, world.
```

When you ask ori to evaluate `greet`:

- It looks for a file called `greet` but doesn't find one.
- ori tries adding `.js` to see if `greet.js` exists. This time it finds a JavaScript module with that name.
- ori dynamically imports the module and obtains the default export (a function).
- Because the result is a JavaScript function, ori executes it.
- The function's result is the string "Hello, world.", which ori displays.

## Pass a string to a function

<span class="tutorialStep"></span> You can pass arguments to JavaScript functions from the shell:

```console
$ ori "greet('Alice')"
Hello, Alice.
```

ori accepts strings in single quotes. The double quotes shown above are parsed by the _shell_, and are necessary because the `bash` shell shown here would otherwise prevent ori from seeing the single quotes.

<span class="tutorialStep"></span> In the explorable graph paradigm discussed later, any function can be treated like a graph, and vice versa. This means you can use path syntax as a convenient alternative to specify a string argument:

```console
$ ori greet/Alice
Hello, Alice.
```

In this path syntax, the first path segment (`greet`) will be looked up in the current scope. All subsequent path segments (like `Alice`) are used as is. Otherwise, both ways of passing arguments behave the same.

ori lets you call a JavaScript function like `greet` from the shell without needing to write JavaScript code to parse command line arguments.

## Aside: Loading functions as ES modules

<span class="tutorialStep"></span> The `samples` folder you're working in includes a file called `package.json` that instructs Node to load `.js` files as ES (EcmaScript) modules:

```console
$ ori package.json
{
  "comment": "This file exists to tell Node to load .js files as ES modules",
  "type": "module"
}
```

By default, Node imports .js files as CommonJS modules. To allow ori to dynamically import JavaScript files in your own projects as ES modules, you will need to include a `package.json` file in the folder with your .js file or in any parent folder. That `package.json` should include the entry `"type": "module"`.

## Use ori as a general-purpose JavaScript shell tool

Suppose you have a collection of functions:

```console
$ ori double.js
export default (x) => `${x}${x}`;
$ ori greet.js
export default (name = "world") => `Hello, ${name}. `;
$ ori uppercase.js
export default (x) => x.toString().toUpperCase();
```

<span class="tutorialStep"></span> You can then use ori to mix and match these functions from the shell:

```console
$ ori greet
Hello, world.
$ ori uppercase/hi
HI
$ ori greet uppercase/there
Hello, THERE.
$ ori uppercase greet
HELLO, WORLD.
$ ori double greet
Hello, world. Hello, world.
$ ori double greet uppercase/there
Hello, THERE. Hello, THERE.
```

Here are the equivalent verbose forms with parentheses:

```console
$ ori "greet()"
Hello, world.
$ ori "uppercase('hi')"
HI
$ ori "greet(uppercase('there'))"
Hello, THERE.
$ ori "uppercase(greet())"
HELLO, WORLD.
$ ori "double(greet())"
Hello, world. Hello, world.
$ ori "double(greet(uppercase('there')))"
Hello, THERE. Hello, THERE.
```

ori lets you use the shell as a basic JavaScript console, so you can invoke and compose functions in any combination without having to write permanent code. This can be useful when you're experimenting, testing, or need to do one-off operations from the shell.

## Read files with ori

<span class="tutorialStep"></span> You can feed a file to a JavaScript function:

```console
$ ori sample.txt
This is a text file.
$ ori uppercase.js
export default (x) => x.toString().toUpperCase();
$ ori uppercase sample.txt
THIS IS A TEXT FILE.
```

This lets you pass files to your JavaScript functions without you having to write code to deal with files.

In this example, ori ends up passing a file buffer to the `uppercase` function. The `uppercase` function includes a `toString()` call which here will extract the text from the file buffer. It can then do its uppercasing work on the resulting text.

## Reading input from stdin

<span class="tutorialStep"></span> You can pipe data into JavaScript functions with the built-in `stdin` function:

```console
$ echo This is input from the shell | ori uppercase stdin
THIS IS INPUT FROM THE SHELL
```

The result of the `stdin` function will be the complete standard input fed to the ori command. This lets you pipe data to a JavaScript function that accepts a simple argument instead of needing specialize the function specifically to read data from the console.

## Writing output to a file

<span class="tutorialStep"></span> You can use regular shell features to pipe the output from your JavaScript functions to a file:

```console
$ ori sample.txt
This is a text file.
$ ori uppercase sample.txt > uppercase.txt
$ ori uppercase.txt
THIS IS A TEXT FILE.
```

&nbsp;

Next: [Using graphs with the ori CLI](intro3.html)