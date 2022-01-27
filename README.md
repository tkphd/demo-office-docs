# Office Suite Documents and Git Version Control

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
educational tool. The intended take-away is that, while Git can and
should be used to track versions of and changes to your work, the
proprietary formats used by typical office suites are incompatible
with the way Git identifies changes. The best thing to do is adopt an
open, plain-text format (such as Markdown or LaTeX for documents, CSV
or TSV for tabular data) that is written one line at a time. The
alternative is to use the office tool's built-in features to track
changes, and to keep point-in-time backups of those files somewhere
else in case the binary file becomes corrupted.

## Microsoft Word: DOCX

The file [microsoft-word.docx](microsoft-word.docx) contains a copy
of the above README text. The original README is plain text: the
`.md` extension lets the reader know that it was formatted by the
author using [Markdown][md], but the file contains only ASCII text.
You can verify this using `cat` from a Linux or macOS shell, or
`Get-Content` from a Windows PowerShell:

* Linux or macOS shell:

  ```shell
  cat README.md
  ```

* Windows PowerShell:

  ```powershell
  Get-Content .\README.md
  ```

* Output:

  ```output
  # Office Suite Demo for Git

  [Git][gitbook] is a distributed version control system designed by
  «truncated for concision»
  ```

### Print the Word document in the terminal

The Microsoft Word document is not plain text, which we can verify by
attempting to print it out in raw form. I recommend using `head` to
limit the number of lines it will print:

* Linux or macOS shell:

  ```shell
  head -n 2 microsoft-word.docx
  ```

* Windows PowerShell:

  ```powershell
  Get-Content -Head 2 .\microsoft-word.docx
  ```

* Output:

  ```output
  C;TRZ>[Content_Types].xml (MN0by[%nY vlLR
                                         ǶXp$$i#Jh̼2|OaQ;Q:
                                                          reWX$w|:w"VDBDJyT)\$k(M n[E`'G( {
  ,^ys0pu                                                                                    ƚqJ"?(ɞK8GMWAB'tl&>ˊƅ\N*}$uEt F:ʤ]zDFs
  IuBi >cRM'+<iL}¶d4V;P5L/Eŀ}&word/theme/theme1.xmlZo6@Z.l[h=2mDE=0Xvh]&[/Gd&kE#(E."1ښqN|:Ѥĸ0AB#k.~x^(D@#~@zst"7,B^I}
  ȅhkz@H"*iS䓥Ls$J6.9-˶N*SB/cuN5=+K-J-깽QLU`UcT<UB5xR%TvS9%TYn1
  ```

### Hexadecimal view of the Word document

For a closer look, you can use a hexadecimal tool like `xxd`
(pre-installed for Linux and macOS, [available for Windows][xxd]) to
dump the raw data to your terminal, rather than having your shell
attempt to convert the data to text:

```shell
xxd microsoft-word.docx | head
```

```output
00000000: 504b 0304 1400 0600 0800 0843 3b54 525a  PK.........C;TRZ
00000010: 3e86 4c01 0000 1a05 0000 1300 0802 5b43  >.L...........[C
00000020: 6f6e 7465 6e74 5f54 7970 6573 5d2e 786d  ontent_Types].xm
00000030: 6c20 a204 0228 a000 0200 0000 0000 0000  l ...(..........
00000040: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000050: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000060: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000070: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000080: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000090: 0000 0000 0000 0000 0000 0000 0000 0000  ................
```

> #### What's in a hex dump?
>
> A "hex dump" reads binary data (a sequence of bits representing
> 0 or 1) in chunks of 4 bits, which can represent numerical values
> between 0 and 15. This value is converted to a hexadecimal value,
> which also represents numerical values from 0 to 15, printed as 0
> through 9 plus a to f. In the output above, there are ten columns.
> The first is a hexadecimal representation of the row's offset in
> the file data, in terms of bytes (8-bit sequences); `00000000`
> equals zero, `00000010` equals `0×16⁰+1×16¹+0×••• = 16` bytes, etc.
> Columns two through nine show `8×4 = 32` hexadecimal values or
> 128 bits (4 bits per hex) of data from the file. The tenth column
> prints sixteen ASCII characters, generated by taking the binary
> data 8 bits (1 byte) at a time; a `.` means the result is not
> printable.

The `C;TRZ` string is familiar from the `cat` output, but the really
interesting thing here is that first eight values: `50 4b 03 04`.
This is a [magic number][magic] or file signature, and it corresponds
to the [ZIP file format][zip], a common means of archiving and
compressing data.

### List the Word archive contents

We can use the `unzip` tool to list its contents:

```shell
unzip -l microsoft-word.docx
```

```output
Archive:  microsoft-word.docx
  Length      Date    Time    Name
---------  ---------- -----   ----
     1306  2022-01-27 08:16   [Content_Types].xml
     9853  2018-04-02 17:49   word/theme/theme1.xml
     2576  2022-01-27 08:24   word/settings.xml
     1277  2022-01-27 08:16   word/fontTable.xml
      497  1980-01-01 00:00   word/webSettings.xml
      679  2022-01-27 08:24   docProps/app.xml
      628  2022-01-27 08:24   docProps/core.xml
    32672  2022-01-27 08:24   word/styles.xml
    12976  2022-01-27 08:24   word/document.xml
      577  2022-01-27 08:16   _rels/.rels
     1781  2022-01-27 08:24   word/_rels/document.xml.rels
---------                     -------
    64822                     11 files
```

This confirms that, hiding behind a `docx` extension, we have a
single `.zip` archive compressing almost a dozen XML files from
63 kB (64,822 bytes) down to 13 kB.

### Get the plain text

Looking at the archived file names, it looks like a lot of
boilerplate for settings, fonts, etc. One file, `word/document.xml`,
has some potential interest. The original README text was a handful
of paragraphs, so it should be tidy and easy to read. Let's extract
that XML file, alone, and take a look:

```shell
unzip microsoft-word.docx word/document.xml
```

```output
Archive:  microsoft-word.docx
  inflating: word/document.xml
```

```shell
cat word/document.xml
```

```output
<?xml version="1.0" encoding="utf-8" standalone="yes"?><w:document xmlns:wpc="http://schemas.microsoft.com/office/word/2010/wordprocessingCanvas" xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006" xmlns:o="urn:schemas-microsoft-com:office:office" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:m="http://schemas.openxmlformats.org/officeDocument/2006/math" xmlns:v="urn:schemas-microsoft-com:vml" xmlns:wp14="http://schemas.microsoft.com/office/word/2010/wordprocessingDrawing"
«truncated to prevent bleeding from the eyes»
```

Inspecting the output, I am struck by two things:

1. I recognize some lines from README in the middle of the text dump!
   This is indeed the right file to inspect.
2. There are no line endings. While XML is a structured markup
   language that supports tidy organization by breaking lines where
   tags end (`<tag> ... text ... </tag>`<kbd>Enter</kbd>), this file
   was dumped as one un-interrupted line.

   That is not great for Git's line-at-a-time tracking.

<!-- links -->

[ascii]: https://en.wikipedia.org/wiki/ASCII
[c]: https://en.wikipedia.org/wiki/C_(programming_language)
[gitbook]: https://git-scm.com/book/en/v2
[magic]: https://en.wikipedia.org/wiki/List_of_file_signatures
[md]: https://www.markdownguide.org/getting-started/
[utf8]: https://en.wikipedia.org/wiki/UTF-8
[xxd]: https://sourceforge.net/projects/xxd-for-windows/
[zip]: https://en.wikipedia.org/wiki/ZIP_(file_format)
