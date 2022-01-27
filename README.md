# Office Suite Demo for Git

[Git][gitbook] is a distributed version control system designed by
Linus Torvald to track changes to the source code (predominantly
[C][c]). Since these files are plain text generally written with one
statement per line, the natural way to track changes is *one line*,
i.e., everything you type up until you press <kbd>Enter</kbd>.

Office suites (Microsoft, Google, Corel, OpenOffice, LibreOffice) are
neither source code nor plain text. Modern office documents are
structured collections of objects, typically organized as XML stanzas
using plain text ([ASCII][ascii] or [Unicode][utf8]) for headings and
metadata, with the text, images, tables, links, and data that you
type, paste, and compute stored as binary blobs generated from an
often-proprietary encoding.

Practically speaking, this means that although you can use Git to
track changes in your Word documents (*et cetera*), it will not be
efficient: every time you change a character, Git sees it as a change
to the entire binary blob (which contains no line-endings  inside).
To track the change, Git stores the *entire blob*, even if only one
single character was changed in the office software.

The purpose of this repository is to test and interrogate this as an
educational tool.

<!-- links -->

[ascii]: https://en.wikipedia.org/wiki/ASCII
[c]: https://en.wikipedia.org/wiki/C_(programming_language)
[gitbook]: https://git-scm.com/book/en/v2
[utf8]: https://en.wikipedia.org/wiki/UTF-8
