# nodeDocs

The complete Node.js API documentation (v26.12.1) as one continuous web page, made for listening from start to finish with Microsoft Edge Read Aloud.

**Open the page: https://raw.githack.com/JosiahHands/nodeDocs/main/index.html**

This link is served by raw.githack.com, a free third-party service (not GitHub) that shows files from this repository as a web page. GitHub Pages is not switched on for this repository. To switch it on: Settings, Pages, Deploy from a branch, branch main, folder / (root). The page should then appear at https://josiahhands.github.io/nodeDocs/ (untested).

The same page in the repository: [index.html](index.html)

## Limitations (please read first)

- Listening in Edge has not been tested yet.
- This is a single page of about 7.2 MB, so it may load slowly. Immersive Reader may refuse a page this large.
- Read Aloud (Ctrl+Shift+U) should work on the normal page view. To start at a particular section, select some text there first, then start Read Aloud (untested).

## About this page

- Source: the official Node.js API documentation, https://nodejs.org/docs/latest/api/
- Version: v26.12.1
- Contents: all 68 modules of the official table of contents, in the official order, on one page. A short contents list at the top links to each module. Links between modules jump within the page.
- Code samples that the website shows in two versions keep both, each with a short visible label (ES module version or CommonJS version). Other code blocks start with "Code example:", and every code block ends with "End of code."
- Type names are written for listening, for example "path (string or Buffer): filename or file descriptor" instead of the bracketed form used on nodejs.org.
- Earlier Markdown and plain text versions of this documentation are available in the repository history.

## Listening notes

- Code blocks and the History and "Added in" version lines are read aloud too. A lighter version without them can be made on request.
- DTLS: the DTLS page is documented on nodejs.org but is not in the official sidebar table of contents, so it is not included. It can be added on request.

## How to listen in Edge (untested)

These steps are a suggestion and have not been tested yet.

1. Open https://raw.githack.com/JosiahHands/nodeDocs/main/index.html in Microsoft Edge.
2. Press Ctrl+Shift+U to start Read Aloud.
3. Optional: press F9 first to open Immersive Reader, then start Read Aloud. This is also untested, and it may not work for a page this large.

Documentation by the OpenJS Foundation and Node.js contributors; source nodejs.org.
