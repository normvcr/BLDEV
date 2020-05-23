# BLDEV
Hierarchical C++ build environment using only Make files and simple utilities.  Includes auto C++ IO source code generation for enums and simple struxts &amp; classes.

## Introduction
The BLDEV system offers the software engineer a build environment without any layers between him/her and the Makefiles that control what gets done.  If your source code is a hierarchy of folders, the main tool, *bldev*, constructs a parallel hierarchy of developeent folders where the building takes place.  For any source folder that contains a Makefile.in, the corresponding development folder will contain a Makefile that includes the Makefile.in and various configuration Make files.

If BLDEV is not yet installed on your machine, a bootstrap process is used to build *bldev*.  Another utility that is built during the bootstrap process is *setdev*, which is used to create devleopment windows (xterm on Linux, and command prompt on Windows), that makes it convenient to separate different build environments e.g. Debug and Release.  It does this by allowing custom colours for the background and text, and putting a custom title into the development window.  Also, the user may write a script (Shell script on Linux, and Batch file on Windows) that is used to intiialize the run-time of the developement window e.g. to add shared object lcoations to the PATH variable.

## Source code generation
Optionally, the user may install the Clang/LLVM libraries, in order to build the *iogen* utility.  *iogen* parses enums and simple structs &amp; classes to create standard C++ iostream insertion/extraction operators.  This is particularly useful for using structs to hold parameter values.  Another utility, *meconv*, relies on gcc, rather than Clang/LLVN, and converts a set of C++ prerprocessor values into enum values.

## Documentation
The **Building.md** file contains details on the bootstrap process.  The **BuildEnvironment.pdf** describes how to use BLDEV.
