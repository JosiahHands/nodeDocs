# nodeDocs

The complete Node.js API documentation (v26.12.1) as one continuous web page, made for listening from start to finish with Microsoft Edge Read Aloud.

**Open the page: https://nodedocs.pages.dev/**

**Short version: https://nodedocs.pages.dev/summary.html** is a summary of the whole documentation at about 1% of its length (about 5,500 words, roughly 37 minutes of listening), one section per module in the same order. Its Markdown twin is [summary.md](summary.md).

The site is hosted on Cloudflare Pages, connected to this repository: each push to the main branch is published there automatically.

The same page in the repository: [index.html](index.html), with its styles in [style.css](style.css).

## Limitations (please read first)

- Listening in Edge has not been tested yet.
- This is a single page of about 7.2 MB, so it may load slowly. Immersive Reader may refuse a page this large.
- Read Aloud (Ctrl+Shift+U) should work on the normal page view. To start at a particular section, select some text there first, then start Read Aloud (untested).

## About this page

- Source: the official Node.js API documentation, https://nodejs.org/docs/latest/api/
- Version: v26.12.1
- Contents: all 68 modules of the official table of contents, in the official order, on one page. A short contents list at the top links to each module. Links between modules jump within the page.
- Styles: basic reading styles in style.css (system font, 18px text, line height 1.6, a centered column of about 50rem, light and dark mode that follow the system setting, wrapped code blocks, bordered tables).
- Code samples that the website shows in two versions keep both, each with a short visible label (ES module version or CommonJS version). Other code blocks start with "Code example:", and every code block ends with "End of code."
- Type names are written for listening, for example "path (string or Buffer): filename or file descriptor" instead of the bracketed form used on nodejs.org.
- Earlier Markdown and plain text versions of this documentation are available in the repository history.

## Listening notes

- Code blocks and the History and "Added in" version lines are read aloud too. A lighter version without them can be made on request.
- DTLS: the DTLS page is documented on nodejs.org but is not in the official sidebar table of contents, so it is not included. It can be added on request.

## How to listen in Edge (untested)

These steps are a suggestion and have not been tested yet.

1. Open https://nodedocs.pages.dev/ in Microsoft Edge.
2. Press Ctrl+Shift+U to start Read Aloud.
3. Optional: press F9 first to open Immersive Reader, then start Read Aloud. This is also untested, and it may not work for a page this large.

Documentation by the OpenJS Foundation and Node.js contributors; source nodejs.org.
