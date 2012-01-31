# Music boxes for nerds

Out of curiosity I bought a fancy digital music device: a **music box**!  The
music is stored on punched tape, which is an "obsolete form of data storage",
[according to Wikipedia](http://en.wikipedia.org/wiki/Punched_tape). I don't
think so! I bought the music box at a <http://www.spieluhr.de> and choosed [the
cheapest version](http://www.spieluhr.de/Artikel/varAussehen.asp?ArtikelNr=4905) 
for 23.44€, so it is quite limited --- but still it provides a lot. You get:

* A 15 bit parallel central processing unit
* A nice wooden box with one input and one output interface
* An integrated power-supply that controls the processing speed ("hand crank")
* A recording medium with a sample piece of music and nine empty mediums
* A recording device to write one bit on the WORM medium ("hole punch")

## The processor

The music box does not support halftones but two octaves from c'
([Helmholtz_pitch_notation](http://en.wikipedia.org/wiki/Helmholtz_pitch_notation),
which is C~4~ in [Scientific pitch notation](http://en.wikipedia.org/wiki/Scientific_pitch_notation),
so you can only play songs in *C major* or *A minor* --- unless you do some musical data transformation.
called "transposition". Playing the same note without a pause in between will not
work neither.

## The recording medium and record format

The music box must be fed by paper stripes of 41mm width. There is a margin of
6\.5mm on each side and 15 parallel rows in between, so 2mm distance between
the rows. The vertical distance between notes is 4mm. To abstract the storage
properties from the physical properties, I created a [general Perl module for
punched tapes](http://search.cpan.org/dist/Punched-Tape) and learned something
about how data was stored just 50 years ago.

## Programming the music box

Punching holes is like directly programming in machine language operation
codes.  The punched tape module gives me an assembler. For instance I can
define mnemonics for notes and chords:

    C1    = "*_*_*__________"
    C2    = "_______*_*_*___"
	Cmaj7 = "*_*_*_*________"
	G     = "____*_*_*______"
	Am    = "_____*_*_*_____"

But a high-level programming language is obviously better. There are some
text-based notations for musical scores:

- ABC notation (<http://abcnotation.com>)
- LilyPond notation (<http://lilypond.org>)

For simple music, ABC notation looks easier, but LiliPond notation isn't much
more difficult. For the music box a subset of ABC notation should be enough.

### A subset of ABC notation

Each note is represented by a letter. Upper case letters denote the bottom
octave, followed by lower case letters and by lower case letters with an
apostrophe (in the same way a comma can be used to denote notes in lower
octaves).  The full span of my music box is:

    C D E F G A B c d e f g a b c'

Spaces and line breaks between letters are optional and bar lines can be put in
between. The basic note length is set with the `L:` field. The length of
individual notes is changed by numbers and slashes the following way:

- `c1` : basic length
- `c2` : double basic length (etc.)
- `c/` or `c/2` : half basic length
- `c//` or `c/4` : quarter basic length (etc.)

A selection of additional special characters includes:

- `z`, `x`, and `X` for pauses (with length modified same as note length)
- repeat/bar symbols (`|`,`[|,`|]`,`||`,`|:`,`:|`,`::`)
- `-` directly after a note to connect it to the following
- chords, for instance `[ceg]` for C-major
- `%` to start end-of-line comments
- `\` at the end of a line to join lines
- `&` sets the time point of the music back by one bar line

### Coding

Why hacking a quick solution if you can spent more time creating an abstraction
that nobody needs? I could not hesitate to create a Perl module for punched
tape: <https://github.com/nichtich/Punched-Tape>. This module is used to
finally create punched tape diagrams for printing. One layer above there is a
parser for simplified ABC notation that creates the tape. Finally there is a
command line client `abc2punch` to convert ABC notation to punched card images.

