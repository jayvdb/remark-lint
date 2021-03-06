# ![remark-lint][logo]

[![Build Status][build-badge]][build-status]
[![Coverage Status][coverage-badge]][coverage-status]
[![Chat][chat-badge]][chat]

<!--lint disable list-item-spacing-->

**remark-lint** is a markdown code style linter.  Another linter?  Yes.
Ensuring the markdown you (and contributors) write is of great quality will
provide better rendering in all the different markdown parsers, and makes
sure less refactoring is needed afterwards.  What is quality? That’s up to you,
but the defaults are sensible :ok_hand:.

**remark-lint** is built on [**remark**][remark], a powerful markdown
processor powered by [plugins][remark-plugins] (such as this one).

## Table of Contents

*   [Installation](#installation)
*   [Command line](#command-line)
*   [Programmatic](#programmatic)
    *   [remark.use(lint\[, options\])](#remarkuselint-options)
*   [Rules](#rules)
*   [Configuring remark-lint](#configuring-remark-lint)
*   [Using remark to fix your markdown](#using-remark-to-fix-your-markdown)
*   [Editor Integrations](#editor-integrations)
*   [List of External Rules](#list-of-external-rules)
*   [Related](#related)
*   [License](#license)

## Installation

[npm][]:

```bash
npm install remark-lint
```

**remark-lint** is also available as an AMD, CommonJS, and globals
module, [uncompressed and compressed][releases].

## Command line

![Example of how remark-lint looks on screen][screenshot]

Use `remark-lint` together with [`remark-cli`][cli]:

```bash
npm install --global remark-cli remark-lint
```

Let’s say `example.md` looks as follows:

```md
* Hello

[World][]
```

Now, running `remark example.md -u remark-lint` yields:

```txt
example.md
        1:3  warning  Incorrect list-item indent: add 2 spaces  list-item-indent
   3:1-3:10  warning  Found reference to undefined definition   no-undefined-references
⚠ 2 warnings
```

See [`doc/rules.md`][rules] for what those warnings are (and how to
turn them off).

## Programmatic

Use `remark-lint` together with [`remark`][api]:

```bash
npm install remark remark-lint
```

Let’s say `example.js` looks as follows:

```js
var report = require('vfile-reporter');
var remark = require('remark');
var lint = require('remark-lint');

var file = remark().use(lint).process('## Hello world!');

console.log(report(file));
```

Now, running `node example.js` yields:

```txt
<stdin>
        1:1  warning  Missing newline character at end of file  final-newline
   1:1-1:16  warning  First heading level should be `1`         first-heading-level
   1:1-1:16  warning  Don’t add a trailing `!` to headings      no-heading-punctuation

⚠ 3 warnings
```

### `remark.use(lint[, options])`

Adds warnings for style violations to the processed [virtual file][vfile].

When processing a file, these warnings are available at `file.messages`, and
look as follows:

```js
{
  file: '~/example.md',
  reason: 'First heading level should be `1`',
  line: 1,
  column: 1,
  location: Position { start: [Object], end: [Object] },
  ruleId: 'first-heading-level',
  fatal: false,
  source: 'remark-lint' }
```

See [`VFileMessage`][vfile-message] for more information.

## Rules

[`doc/rules.md`][rules] describes all available rules, what they check
for, examples of markdown they warn for, and how to fix their warnings.

## Configuring remark-lint

**remark-lint** is a **remark** plug-in and supports configuration
through its [configuration files][cli].

An example `.remarkrc` file could look as follows:

```json
{
  "plugins": {
    "remark-lint": {
      "no-multiple-toplevel-headings": false,
      "list-item-indent": false,
      "maximum-line-length": 79
    }
  }
}
```

Where the object at `plugins['remark-lint']` is a map of `ruleId`s and
their values.

Using our `example.md` from before:

```md
* Hello

[World][]
```

Now, running `remark example.md` (**without `-u remark-lint`** since
our `.remarkrc` includes the lint plugin) yields:

```bash
example.md
   3:1-3:10  warning  Found reference to undefined definition   no-undefined-references

⚠ 2 warnings
```

In addition, you can also provide configuration comments to turn a rule
on or off inside a file.  Note that you cannot change what a setting,
such as `maximum-line-length`, just whether they are shown or not.
Read more about configuration comments in
[**remark-message-control**][message-control]s documentation.

The following file will warn twice for the duplicate headings:

```markdown
# Hello

## Hello

### Hello
```

The following file will warn once (the second heading is ignored,
but the third is re-enabled):

```markdown
# Hello

<!--lint disable no-duplicate-headings-->

## Hello

<!--lint enable no-duplicate-headings-->

### Hello
```

## Using remark to fix your markdown

One of **remark**’s cool parts is that it compiles to very clean, and highly
cross-vendor supported markdown.  It’ll ensure list items use a single bullet,
emphasis and strong use a standard marker, and that your table fences are
aligned.

**remark** should be able to fix most of your styling issues automatically,
and I strongly suggest checking out how it can make your life easier :+1:

## Editor Integrations

Currently, **remark-lint** is integrated with Atom through
[**linter-markdown**][linter-markdown].

I’m very interested in more integrations.  Let me know if I can help.

## List of External Rules

<!--
This list is ordered based on the name without prefix, so
excluding `remark-lint-no-` or `remark-lint-`
-->

*   [`vhf/remark-lint-alphabetize-lists`](https://github.com/vhf/remark-lint-alphabetize-lists)
    — Ensure list items are in alphabetical order;
*   [`vhf/remark-lint-blank-lines-1-0-2`](https://github.com/vhf/remark-lint-blank-lines-1-0-2)
    — Ensure a specific number of lines between blocks;
*   [`vhf/remark-lint-books-links`](https://github.com/vhf/remark-lint-books-links)
    — Ensure links in lists of books follow a standard format;
*   [`Qard/remark-lint-code`](https://github.com/Qard/remark-lint-code)
    — Lint fenced code blocks by corresponding language tags,
    currently supporting [ESLint](https://github.com/Qard/remark-lint-code-eslint).
*   [`vhf/remark-lint-no-empty-sections`](https://github.com/vhf/remark-lint-no-empty-sections)
    — Ensure every heading is followed by content (forming a section);
*   [`chcokr/remark-lint-sentence-newline`](https://github.com/chcokr/remark-lint-sentence-newline)
    — Ensure sentences are followed by a newline;
*   [`vhf/remark-lint-no-url-trailing-slash`](https://github.com/vhf/remark-lint-no-url-trailing-slash)
    — Ensure that the `href` of links has no trailing slash.

## Related

*   [`wooorm/remark-validate-links`](https://github.com/wooorm/remark-validate-links)
    — Validate if links point to existing headings and files in markdown.

## License

[MIT][license] © [Titus Wormer][author]

<!-- Definitions -->

[build-badge]: https://img.shields.io/travis/wooorm/remark-inline-links.svg

[build-status]: https://travis-ci.org/wooorm/remark-inline-links

[coverage-badge]: https://img.shields.io/codecov/c/github/wooorm/remark-inline-links.svg

[coverage-status]: https://codecov.io/github/wooorm/remark-inline-links

[chat-badge]: https://img.shields.io/gitter/room/wooorm/remark.svg

[chat]: https://gitter.im/wooorm/remark

[releases]: https://github.com/wooorm/remark-inline-links/releases

[license]: LICENSE

[author]: http://wooorm.com

[npm]: https://docs.npmjs.com/cli/install

[remark]: https://github.com/wooorm/remark

[logo]: https://cdn.rawgit.com/wooorm/remark-lint/master/logo.svg

[screenshot]: https://cdn.rawgit.com/wooorm/remark-lint/master/screenshot.png

[rules]: doc/rules.md

[api]: https://github.com/wooorm/remark/tree/master/packages/remark

[cli]: https://github.com/wooorm/remark/tree/master/packages/remark-cli

[remark-plugins]: https://github.com/wooorm/remark/blob/master/doc/plugins.md

[linter-markdown]: https://atom.io/packages/linter-markdown

[message-control]: https://github.com/wooorm/remark-message-control#markers

[vfile]: https://github.com/wooorm/vfile

[vfile-message]: https://github.com/wooorm/vfile#vfilemessage
