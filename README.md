# Creates a 64 bit linux binary that can write itself to a file!
> Wait can't you just read your own binary file and then write it somewhere else?

Yes, buut what if it gets deleted? Or when the process was executed it wasn't given a valid name + PWD env variable?

> Ok but why?

Good question! For fun!

## What this repo does

### copyself.zig
Grabs bytes from address 0x300000 and assumes they are size 0xdeadbeef (this gets replaced in the binary later) and then writes it to disk


### replaceprogramheader.c
Reads the elf binary file generated from zig and replaces the PHDR program header with one that just loads the entire file into memory at 0x300000, (Also replaces 0xdeadbeef with the file size)
Probably very dependent on the specific elf's generated by zig.


### addprogramheader.c
Oh god. What if you really wanted to add an additional program header into an elf binary?
- Well that would be crazy.
- First off you'd have to update all the references in the elf file to point to the new offsets.
- Second you'd have to update all the absolute pointers in the data section of the binary.
- THIRD and worst of all you'd have to update all the absoluate pointers in the executable .text machine code...

So your best bet would be to dissassemble the entire file and rebuild it. Or maybe get to the file before the linker does...?

*Cough*

This program hackily replaces all references with the offset version, using pattern matching on the machine code to avoid accidentally matching too much.
Will most definitely fail if you use any assembler instructions that weren't in my testing binary.

## Deps
c compiler
zig 0.10.0 master

## Useful commands

```bash
readelf --all <binary> # see elf headers
objdump -d <binary> # dissasemble binary if it has valid section headers
```

## Vim hex editor setup
Open vim and then run:
```vim
:set binary
:%!xxd
```
then change stuff and when you close it run:
```vim
:%!xxd -r
:wq
```

# THIS IS NOT A QUINE!
This counts as cheating in quine world.
