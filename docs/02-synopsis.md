[Contents](../README.md) | [Previous: About this documentation](01-documentation.md) | [Next: Assertion testing](03-assert.md)

Source: [https://nodejs.org/docs/latest/api/synopsis.html](https://nodejs.org/docs/latest/api/synopsis.html)

# Usage and example

## Usage

`node [options] [V8 options] [script.js | -e "script" | - ] [arguments]`

Please see the [Command-line options](13-cli.md#options) document for more information.

## Example

An example of a [web server](27-http.md) written with Node.js which responds with
`'Hello, World!'`:

First, make sure to have downloaded and installed Node.js. See
[Installing Node.js](https://nodejs.org/en/download) for further install information.

Now, create an empty project folder called `projects`, then navigate into it.

Linux and Mac:

```bash
mkdir ~/projects
cd ~/projects
```

Windows CMD:

```powershell
mkdir %USERPROFILE%\projects
cd %USERPROFILE%\projects
```

Windows PowerShell:

```powershell
mkdir $env:USERPROFILE\projects
cd $env:USERPROFILE\projects
```

Next, create a new source file in the `projects`
folder and call it `hello-world.js`.

Open `hello-world.js` in any preferred text editor and
paste in the following content:

```js
const http = require('node:http');

const hostname = '127.0.0.1';
const port = 3000;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello, World!\n');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

Save the file. Then, in the terminal window, to run the `hello-world.js` file,
enter:

```bash
node hello-world.js
```

Output like this should appear in the terminal:

```console
Server running at http://127.0.0.1:3000/
```

Now, open any preferred web browser and visit `http://127.0.0.1:3000`.

If the browser displays the string `Hello, World!`, that indicates
the server is working.

[Contents](../README.md) | [Previous: About this documentation](01-documentation.md) | [Next: Assertion testing](03-assert.md)
