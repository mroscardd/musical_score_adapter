[more music software](https://wim.vree.org/svgParse/index.html)

### abc2xml

_abc2xml_ is a command line utility that translates [ABC](http://abcnotation.com/) notation into [MusicXML](http://www.musicxml.com/).

Most elements from ABC are translated, but some translations are only partially implemented.  
In general %%commands are _not_ implemented except:

*   %%abc-include. Allows you to include another .abc file. The included file, however, is not further checked for %%abc-include lines.
*   %%score, %%staves. When multiple %%score instructions are present only the first one is honoured.
*   %%scale, %%pagewidth, %%pageheight, %%leftmargin and %%rightmargin. The scale value is the distance between two stafflines in millimeters. The other values are also in millimeters unless they are followed by a unit (cm,in,pt).
*   %%MIDI program and %%MIDI channel (or I:MIDI ..) are translated when used in a current voice (i.e. after a V: definition). Instrument/channel changes mid-voice are translated to a text direction which carries the midi parameters as playback setting. In _%%MIDI program num_, the number should range from 0 to 127. In _%%MIDI channel num_, the number is starts from one. The midi translation supports mapping multiple voices whith different instruments to one stave. (i.e. the resulting xml part will have multiple instruments). This feature, though present in MusicXML is not supported by MuseScore, nor by Finale Notepad. These programs only allow one instrument per stave.
*   %%MIDI transpose is translated and has the same effect as _transpose=_ in the clef, i.e. only play back is transposed, not notation.
*   %%MIDI drummap is translated to an equivalent I:percmap
*   All %%instructions can be inlined: %%command ... == \[I:command ...\]

### Usage:

When you have Python installed:

\> python abc2xml.py \[-h\] \[-r\] \[-t\] \[-b\] \[-f\] \[-m SKIP NUM\] \[-o DIR\] \[-p PFMT\] \[-z MODE\] \[--meta MAP\] file1 \[file2 ...\]

Translates all .abc files in the file list to MusicXML. Output goes to stdout unless the -o option is given. Wildcards in file names are expanded.

*   Option **\-h** prints help message with explanation of the options
*   Option **\-r** shows whole measure rests in a merged staff. Otherwise (default), when a voice has no notes in a particular measure, the corresponding rest (of a whole measure) will not be shown when the voice is merged with other voices that do have notes in that measure.
*   Option **\-m skip num** skips _skip_ tunes and then reads at most _num_ tunes. Can be used when abc files contain multiple tunes (tune books) to select only a subset of the tunes. The default skips nothing (skip=0) and reads 1 tune (num=1).
*   Option **\-o dir** translates every .abc file to a separate .xml file with the same name into directory **dir**. For example, _\-o._ puts all xml files into the same directory where the input files reside.
*   Option **\-p fmt** sets the page format of the ouput. **fmt** should be a string with 7 floating point values sepatated by comma's without any spaces. The values are: _scale_, _page-height_, -_width_, and _page margin left_, -_right_, -_top_, -_bottom_. A scale value of 1.0 sets the distance between two staff lines to 6pt (2.117 mm). When the -p option is omitted the values default to A4 with left/right margins of 18 mm, top/bottom marings of 10 mm and scale = 0.75. The margin values are in millimeters.
*   Option **\-z mode** or **\--mxl mode** writes compressed xml files with extention .mxl. If **mode** is _a_ or _add_ both .xml and .mxl files will be written. If **mode** is _r_ or _replace_ only .mxl files are written.
*   Option **\--meta map** defines a mapping of ABC info fields onto MusicXML meta data types. The **map** is a string without spaces and using comma as separator. It specifies the MusicXML tag name for one or more ABC info field headers. For instance: **\--meta** _R:poet,Z:translator_ maps the _R:_ info field onto the _poet_ meta data type, and the _Z:_ info field onto the _translator_ meta data type. Valid MusicXML meta data types are: _composer, lyricist, poet, arranger, translator_ and _rights_. It depends on the music editor used, where and if the meta data appears on the score front page. MuseScore, for instance, displays _composer, poet_ and _translator_ in various useful positions in the header of the score. The _rights_ type can be (mis)used to display a block of text below the score.
    
    There are two default mappings: _C:_ is mapped to the _composer_ meta data type and _S:_ is mapped to the _source_ meta data type. All other ABC info fields: _R:,Z:,N:,O:,A:,G:,H:,B:,D:,F:,W:_ are by default translated to the _miscellaneous_ meta data type, unless the user provides a mapping for them with the --meta option.
    
    When multiple lines of the same field type appear, the lines are contatenated with a new line as separator. Also W: fields are treated in this way (concatenated). With the command line option **\--meta** _W:rights_ all W: lines appear as one block of text at the bottom of the score (with MuseScore).
    
*   Option **\-t** Use the first title field (T:) as file name. Question mark, comma and quote are replaced by underscore.
*   Option **\-b** The default line break character is set to EOL in stead of $ (the default).
*   Option **\-f** forces _abc2xml_ to calculate string/fret allocations for all notes in each tab staff. These allocations are added as notation elements to the XML file. Without **\-f** no string/fret notation elements will be added to the XML file, leaving the allocation to the score editor that reads the XML file. The current allocation algorithm in _abc2xml_ is very simple and will produce unplayable results in many cases. Explicit string numbers (finger decorations) can be added to the ABC code to get correct results. See [tutorial](https://wim.vree.org/js2/tabDrumDoc.html).
*   Option **\-** A single minus **\-** in stead of an input file name will make _abc2xml_ read from standard input. For example (Linux):
    
    \> cat test.abc | abc2xml.py -o. -
    
    does the same as
    
    \> abc2xml.py -o. test.abc
    
For more info see: [abc2xml.html](https://wim.vree.org/svgParse/abc2xml.html)  
Bug reports, remarks to:![](https://wim.vree.org/svgParse/index_email.png)