# nodeDocs

A one-page summary of the complete Node.js v26.12.1 API documentation, about 1% of its full length, written for reading straight through or listening with Microsoft Edge Read Aloud.

**Live site: https://nodedocs.pages.dev/**

## What is on the page

- All 68 modules of the official Node.js table of contents, in the official order, one short section each.
- Each section gives the purpose, how to import the module, key concepts, main APIs and important notes, including the stability label where the module has one.
- Each section ends with a link to that module in the official Node.js documentation at https://nodejs.org/docs/latest/api/
- About 5,500 words, roughly 37 minutes of listening at about 150 words a minute (a rule of thumb, not measured in Edge). The full documentation is about 534,000 words, or about 60 hours.
- No code samples, so the page reads well aloud.

## How it was made

The summary was written from the official documentation text. Every API name shown in code formatting was checked against the full documentation, and about 45 factual claims were checked individually; the rest was written from the source but not individually verified. A full-length single-page version of the documentation was published here before and is still in the repository history.

## Files

- `index.html`: the page
- `style.css`: basic reading styles with light and dark mode
- `README.md`: this file

## Hosting

Cloudflare Pages project `nodedocs`, connected to this repository. Each push to `main` is published automatically. There is no build step.

Documentation by the OpenJS Foundation and Node.js contributors; source nodejs.org.
