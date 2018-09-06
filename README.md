# BBC Micro stuff

A random grab bag of random bits for people that still use this
classic 1980s UK computer.

When using the BBC Micro, I use
[BeebLink](https://github.com/tom-seddon/beeblink), a package that
lets your PC act as a file server for the BBC Micro. This way, I can
use the PC to edit and assemble files for immediate use on the BBC
Micro.

BeebLink stores each BBC file as a loose file on your PC, plus an
associated .inf file that stores file attributes, so the tools here
are oriented towards this way of working as opposed to working
directly with disk images. (There are also
[other tools that work with this format](https://www.stairwaytohell.com/essentials/index.html?page=homepage).)

The tools are briefly described here. For more info, run each with
`-h` on the command line.

# BBCBasicToText

Python 2.7 script that converts tokenized BBC BASIC files to text.
Originally by [Matt Godbolt](https://github.com/mattgodbolt). I
updated it with support for line numbers and BASIC II.

Execute with `-h` to get a help page.

(If you want to use this to produce output for use with `*EXEC` on the
BBC, use the `--cr` option.)

Note: `BBCBasicToText` can be used in a pipeline, but when used this
way it might not work properly on Windows as it doesn't reopen stdin
in binary mode. (This will get fixed at some point, I promise...)

## Using with git

You can use `BBCBasicToText` as a git diff driver, so you can get text
diffs of changes to tokenized BASIC code.

To do this, ensure `BBCBasicToText` is on `PATH`. Then edit the
`.git/config` file in your working copy, and add something like this
to the end:

    [diff "bbcbasic"]
        textconv = BBCBasicToText -n

(The `-n` tells it to operate in a diff-friendly mode: no leading line
numbers, and line numbers in the program (e.g., `GOTO`, `RESTORE`)
replaced with numbered `@xxxx` labels.)

Then use the
[`.gitattributes`](http://git-scm.com/docs/gitattributes])file to
specify the diff driver for your BBC BASIC files:

    $.MYBAS diff=bbcbasic
    *.bbcbasic diff=bbcbasic

You can use wildcards (as in this example) if your BBC BASIC file
names conform to a pattern. There's no standard pattern, though, so
you may have to just list them out in the `.gitattributes` file in
each folder. Either way, it's worth the effort!

# dump_bbc_rom_info

Simple tool that scans sideways ROM headers and prints info to stdout.
I used this to have a quick look at [Wouter Scholten's monster ROM
archive](http://wouter.bbcmicro.net/bbc/bbc-software.html).

# ssd_create

Python 2.7 script that builds a single-sided disc image from .inf
files.

Supply list of files on the command line. Files that have a
corresponding .inf file are assumed to be BBC files, and other files
are ignored.

# ssd_extract

Python 2.x script that converts a SSD or DSD disc image into a folder
of .inf files suitable for use with BeebLink. Supply name of disk on
command line; it will create a BeebLink-compatible folder in the same
folder, named after the disc image. Alternatively, specify `-0` or
`-2` to have it output files from side 0 or 2 straight into the folder
specified.

Specify the `-b` switch to have it find tokenized BBC BASIC files,
convert them to text with `BBCBasicToText.py`, and save them to a
separate `raw` folder.

# screen_conv

Python 2.7 script that converts a BBC screen dump into an image.

Dependencies: PIL

Supply name on command line of dump of screen RAM (as saved by, e.g.,
`*SAVE X FFFF3000+5000`) and MODE the dump was taken from. Use `-o` to
specify output file. Specify `-r` to have image resized to 640x512,
making the image look reasonable on a PC. Specify `-p` and a list of
physical colour numbers, in order, to specify the palette (e.g., for a
MODE4 grab, `-p 13` to specify yellow on red.)

(Maybe one day I'll figure out how to make animated GIFs so that
flashing colours can be supported.)

# text2bbc

Along the lines of [`dos2unix`](https://linux.die.net/man/1/dos2unix),
converts files from modern standard line endings (lines ending with
LF, or, on Windows, CR+LF) to BBC-style ones (lines ending with CR).
