# bg80d

### What is it

A simple Z80 disassembler, built to support my [Ms. Pac-Man Disassembly](http://bartgrantham.com/projects/ms-pacman-disassembly/) project.

**WARNING** : This is beta code.  It works well for my limited use, but it's not particularly well-tested.

### What's special about it?

Instead of attempting to decode the opcode in code (heh) I broke the Z80 opcode specifications into separate files, `*.dat` in the repo.  Because of CB/DD/ED/FD opcode prefixes there's still some decoding that happens on the fly, but the operands for a given instruction are just a table lookup.  This allows the disassemler to be small, **less than 200 lines**, most of which is parameter/input file parsing.

Also, it has some features that I've found helpful in my documenting old Z80 code.  Code and data tend to be intertwined pretty badly in at least the Pac-Man ROM so I included the capability to specify ranges to decode/not decode, mixing in a file of annotations, and some other tricks.

### How do I use it?

`cat some_z80_rom.bin | ./bg80d.py`

You can pass in various input files that help bg80d understand how you want it to interpret the file.  In short, the range/skip/stride
```
# comments on lines by themselves are ignored, as are blank lines

0-7       # comments after rangespecs become part of the annotation stream
$68-$7c   # you can use one decimal format and two hex formats for memspecs:  1000 == $3e8 == 0x3e8
0x7D-$8c  # the two hex formats are case-insensitive, and you can mix and match (probably not a good idea)
```

Annotation files are any parsable memspec in the first column (1000 == $3e8 == 0x3e8) on a line by itself, followed by annotations.  The annotation(s) will be output immediately before the decoded memory location:
```
0
;;; RST0 - __start()
;;; Turns off interrupts, sets the interrupt table to $3F00, and jumps to boot()
8

;;; RST8 - memset(HL, A, B)
;;; Fill (HL)...(HL+B) with Accumulator
```

Other than that, for now you'll have to make do with `./bg80d.py --help`


## TODO

* better documentation!
* allow user to provide formatting string
  * ie. "%-5s{addr} $%.4X{addr}   %-6s{opcode} %-15{mnemonic} %-11{param} ;  %{desc}"
  * addr/opcode/operands in dec/hex/HEX
  * mnemonic (w or w/o operand substitutions)
  * description (w or w/o operand substitutions)
  * opcode_type, opcode_dest, opcode_src, opcode_summary 
* stride code could be cleaner
* annotation parsing could be cleaner
* does strides get decoded in an expected way WRT start/end vs. ranges/skips?
* possible that the ddcbnn opcodes are buggy
* How about a labels.txt for better integration with asms?
  * Could do addr->label replacement on output
