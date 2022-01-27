# Office Suite Documents and Git Version Control

[Git][gitbook] is a distributed version control system designed by
Linus Torvalds to track changes to the source code (predominantly
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

On the first line, `C;TRZ` is familiar from the `cat` output, but the
really interesting thing here is that first eight values: `50 4b 03
04`. This is a [magic number][magic] or file signature, and it
corresponds to the [ZIP file format][zip], a common means of
archiving and compressing data.

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
<?xml version="1.0" encoding="utf-8" standalone="yes"?><w:document xmlns:wpc="http://schemas.microsoft.com/office/word/2010/wordprocessingCanvas" xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006" xmlns:o="urn:schemas-microsoft-com:office:office" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:m="http://schemas.openxmlformats.org/officeDocument/2006/math" xmlns:v="urn:schemas-microsoft-com:vml" xmlns:wp14="http://schemas.microsoft.com/office/word/2010/wordprocessingDrawing" «truncated to prevent bleeding from the eyes»
```

Inspecting the output, I am struck by two things:

1. I recognize some lines from README in the middle of the text dump!
   This is indeed the right file to inspect.
2. There are no line endings. While XML is a structured markup
   language that supports tidy organization by breaking lines where
   tags end (`<tag> ... text ... </tag>`<kbd>Enter</kbd>), this file
   was dumped as one un-interrupted line.

   That is not great for Git's line-at-a-time tracking.

### Add content to the Word document

After writing up to this point, I added the following to the end of
the top-level README:

> The intended take-away is that, while Git can and should be used to
> track versions of and changes to your work, the proprietary formats
> used by typical office suites are incompatible with the way Git
> identifies changes. The best thing to do is adopt an open,
> plain-text format (such as Markdown or LaTeX for documents, CSV or
> TSV for tabular data) that is written one line at a time. The
> alternative is to use the office tool's built-in features to track
> changes, and to keep point-in-time backups of those files somewhere
> else in case the binary file becomes corrupted.

I committed the Word version of the README beforehand. Let's walk
through what Git sees after making the 571-character change.

```shell
git diff microsoft-word.docx
```

```output
──────────────────────────────────────
modified: microsoft-word.docx (binary)
──────────────────────────────────────
```

Very helpful, `git`. Acknowledging that it's binary,

```shell
git diff --binary microsoft-word.docx
```

```output
GIT binary patch
delta 4373
zcmZXYbx;)Sx5t-SL1Km7r8}jUT2c_{bZL-~rCho}atT4YmJkGK5T!d7mrzPlP&yRp
z1yPU|?)%=kcgEknXU?C`=bV`nGvD*Pv^}t+K<E$=LjXVkDWKoDfdYP;vfo(VxKB2Y
«truncated for concision»
```

Now we're getting somewhere, but that seems like an awful lot of output
for just a 571-character change. How much data? First redirect the
patch to a file...

```shell
git diff --binary microsoft-word.docx > microsoft-word.patch
```

then use `wc` (**w**ord **c**ount) to count the number of
**c**characters:

```shell
wc -c microsoft-word.patch
```

```output
10840
```

10,840 characters is 11 kB of data, or 19× the amount of text we
actually added. Yikes. Let's take a look at why. Comparing the ZIP
contents against the original, with delta annotations added in:

```shell
unzip -l microsoft-word.docx
```

```output
Archive:  microsoft-word.docx
  Length      Date    Time    Name
---------  ---------- -----   ----
     1306  2022-01-27 08:16   [Content_Types].xml           # +   0 B
     9853  2018-04-02 17:49   word/theme/theme1.xml         # +   0 B
     2630  2022-01-27 10:38   word/settings.xml             # +  54 B
     1277  2022-01-27 08:16   word/fontTable.xml            # +   0 B
      497  1980-01-01 00:00   word/webSettings.xml          # +   0 B
      679  2022-01-27 10:38   docProps/app.xml              # +   0 B
      628  2022-01-27 10:38   docProps/core.xml             # +   0 B
    32672  2022-01-27 10:38   word/styles.xml               # +   0 B
    14349  2022-01-27 10:38   word/document.xml             # +1373 B
      577  2022-01-27 08:16   _rels/.rels                   # +   0 B
     1781  2022-01-27 10:38   word/_rels/document.xml.rels  # +   0 B
---------                     -------
    66249                     11 files
```

Most of the files remain the same (pass `-v` instead of `-l` to get
file hashes, but size is a good-enough proxy); there's a small
addition to `settings.xml`, plus 1.3 kB extra in `document.xml`:
about twice what we added directly.

However, Git does not see this file listing: it sees the ZIP wrapper,
which bundles and compresses the raw data. Git searches the file for
line endings (characters that don't display in text editors, but tell
it where a line wraps). How many "lines" does the Word document
contain?

```shell
wc -l microsoft-word.docx
```

```output
29
```

How much data is that, in total?

```shell
wc -c microsoft-word.docx
```

```output
12654
```

Git produced a binary patch of 10,840 characters (or bytes, as one
character is 1 B in size), which means 86% of the binary file (ZIP
archive) will be overwritten and stored under the `.git` folder. For
shared repositories, storing that much data every time a change is
written will quickly bloat the repository and make it extremely
unwieldy to synchronize with remote colleagues. To preserve sanity,
I'll revert the document to keep the incomplete version known to Git:

```shell
git checkout -- microsoft-word.docx
```

## Microsoft Excel: XLSX

The file [microsoft-excel.xlsx](microsoft-excel.xlsx) contains a copy
of the top-level README text, with one paragraph per cell.

### Print the Excel document in the terminal

The Microsoft Excel document is not plain text, which we can verify by
attempting to print it out in raw form. I recommend using `head` to
limit the number of lines it will print:

* Linux or macOS shell:

  ```shell
  head -n 2 microsoft-excel.xlsx
  ```

* Windows PowerShell:

  ```powershell
  Get-Content -Head 2 .\microsoft-excel.xlsx
  ```

* Output:

  ```output
  P!LA[Content_Types].xml (MN0H!%nY t*Q`Icձ-ϴ҂ڍ-Ǟ<:-!b 2pJ6}oEV;(Pˋt3vX(Iu
                                                                          p)eɠ깚
  QQ                                                                            ndz
    K?oH"X`U
  ׋(0DP[lSs$crBWR'2M֠M;E2M3w3	J9o7lC<%Kǜ[ARwߵw2''P!U0#L
                                                          _rels/.rels (MO0
  ?9Lҙsbgٮ|l!USh9ibr:"y_dlD|-NR"42G%Z4˝y7	ëɂP!jxl/workbook.xmlUmo0>iw     HݐBKwAH!T~I$'TG~<!4;#wqu*&rFqvGJy(v*K#FD.W	=ZMYbBS7ϛז
  ```

### Count the lines

The original README document had four paragraphs with each line
wrapped around the 70th character, creating 25 lines in total. The
Excel document has five lines (one for the header and one per
paragraph), but how many "lines" are in the file?

```shell
wc -l microsoft-excel.xlsx
```

```output
26
```

Comparing this to the `cat` output above, the line breaks do not seem
to correspond to anything meaningful.

### Hexadecimal view of the Excel document

For a closer look, you can use a hexadecimal tool like `xxd`
(pre-installed for Linux and macOS, [available for Windows][xxd]) to
dump the raw data to your terminal, rather than having your shell
attempt to convert the data to text:

```shell
xxd microsoft-excel.xlsx | head
```

```output
00000000: 504b 0304 1400 0600 0800 0000 2100 4c41  PK..........!.LA
00000010: 0211 5f01 0000 9004 0000 1300 0802 5b43  .._...........[C
00000020: 6f6e 7465 6e74 5f54 7970 6573 5d2e 786d  ontent_Types].xm
00000030: 6c20 a204 0228 a000 0200 0000 0000 0000  l ...(..........
00000040: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000050: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000060: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000070: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000080: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000090: 0000 0000 0000 0000 0000 0000 0000 0000  ................
```

As we had in the Word document, those first 8 characters (`50 4b 30
34`) are the [magic number][magic] for a [ZIP archive][zip]. Using
the `unzip` tool to list its contents, we see

```shell
unzip -l microsoft-excel.xlsx
```

```output
Archive:  microsoft-excel.xlsx
  Length      Date    Time    Name
---------  ---------- -----   ----
     1168  1980-01-01 00:00   [Content_Types].xml
      588  1980-01-01 00:00   _rels/.rels
     1687  1980-01-01 00:00   xl/workbook.xml
      698  1980-01-01 00:00   xl/_rels/workbook.xml.rels
     1258  1980-01-01 00:00   xl/worksheets/sheet1.xml
     8390  1980-01-01 00:00   xl/theme/theme1.xml
     1597  1980-01-01 00:00   xl/styles.xml
     1476  1980-01-01 00:00   xl/sharedStrings.xml
      790  1980-01-01 00:00   docProps/core.xml
      394  1980-01-01 00:00   docProps/app.xml
---------                     -------
    18046                     10 files
```

The archive compresses 10 files from 17 kB down to 9 kB; the dates
were apparently never set. This Excel archive appears to share some
boilerplate with the Word boilerplate we saw before. Instead of
`data/document.xml`, our cells are stored under
`xl/worksheets/sheet1.xml`, which we can extract for inspection:

```shell
unzip microsoft-excel.xlsx xl/worksheets/sheet1.xml
```

```shell
cat xl/worksheets/sheet1.xml
```

```output
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<worksheet xmlns="http://schemas.openxmlformats.org/spreadsheetml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006" mc:Ignorable="x14ac xr xr2 xr3" xmlns:x14ac="http://schemas.microsoft.com/office/spreadsheetml/2009/9/ac" xmlns:xr="http://schemas.microsoft.com/office/spreadsheetml/2014/revision" xmlns:xr2="http://schemas.microsoft.com/office/spreadsheetml/2015/revision2" xmlns:xr3="http://schemas.microsoft.com/office/spreadsheetml/2016/revision3" «truncated to prevent bleeding from the eyes»
```

The whole sheet is encoded as two lines. Unfortunately, I see nothing
recognizable: this is not the file storing what was typed. 

```shell
unzip microsoft-excel.xlsx xl/sharedStrings.xml
```

```shell
cat xl/sharedStrings.xml
```

```output
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<sst xmlns="http://schemas.openxmlformats.org/spreadsheetml/2006/main" count="5" uniqueCount="5"><si><t>Office Suite Documents and Git Version Control</t></si><si><t>Git is a distributed version control system designed by Linus Torvald to track changes to the source code (predominantly C). Since these files are plain text generally written with one statement per line, the natural way to track changes is one line, i.e., everything you type up until you press Enter.</t></si><si><t>Office suites (Microsoft, Google, Corel, OpenOffice, LibreOffice) are neither source code nor plain text. Modern office documents are structured collections of objects, typically organized as XML stanzas using plain text (ASCII or Unicode) for headings and metadata, with the text, images, tables, links, and data that you type, paste, and compute stored as binary blobs generated from an often-proprietary encoding.</t></si><si><t>Practically speaking, this means that although you can use Git to track changes in your Word documents (et cetera), it will not be efficient: every time you change a character, Git sees it as a change to the entire binary blob (which contains no line-endings inside). To track the change, Git stores the entire blob, even if only one single character was changed in the office software.</t></si><si><t>The purpose of this repository is to test and interrogate this as an educational tool.</t></si></sst>
```

There's our text. Like the Word document, the internal representation
is also a single line; even if Git could see this far into the ZIP
archive, it would still have to save a copy of this entire file every
time something changed.

<!-- links -->

[ascii]: https://en.wikipedia.org/wiki/ASCII
[c]: https://en.wikipedia.org/wiki/C_(programming_language)
[gitbook]: https://git-scm.com/book/en/v2
[magic]: https://en.wikipedia.org/wiki/List_of_file_signatures
[md]: https://www.markdownguide.org/getting-started/
[utf8]: https://en.wikipedia.org/wiki/UTF-8
[xxd]: https://sourceforge.net/projects/xxd-for-windows/
[zip]: https://en.wikipedia.org/wiki/ZIP_(file_format)
