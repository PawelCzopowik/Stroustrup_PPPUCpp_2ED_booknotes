[<< Part I - The Basics](index.md) | [Home](index.md) | [Part III Data and Algorithms >>](part3.md)

# FLTK Setup
- Download, extract
- Open solution, C:\src\lib\fltk-1.3.5\ide\VisualC2010\fltk.sln
- Build (should be no errors, 2 warnings)
- Copy FL & GL folders to C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Auxiliary\VS\include
- Copy __.lib__ files C:\src\lib\fltk-1.3.5\lib from to C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Auxiliary\VS\lib\x86
- Copy __fluidd.exe__ to C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\Tools  __Not sure if this is necessary, maybe for cmd line__
- In project properties> Linker > Input, add the following: __fltkd.lib; wsock32.lib; comctl32.lib; fltkjpegd.lib; fltkimagesd.lib__

The VS paths above are already in the default search paths.


Part 2 - Input and Output
===


# Chapter 10 Input and Output Streams

# Chapter 11 Customizing Input and Output

# Chapter 12 A Display Model

## 12.2 A display model

Graphic objects (such as circle, square) get `attach()`'ed to the "window", then the display engine `draw()`'s them.



a
# Chapter 13 Graphics Classes

# Chapter 14 Graphics Class Design

# Chapter 15 Graphing Functions and Data

# Chapter 16 Graphical User Interfaces

[<< Part I - The Basics](index.md) | [Part III Data and Algorithms >>](part3.md)
