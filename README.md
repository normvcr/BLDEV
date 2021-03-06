# BLDEV
Hierarchical C++ build environment using only Make files and simple utilities.  Includes auto C++ IO source code generation for enums and simple structs &amp; classes.

## Introduction
The BLDEV system is a wrapper around bare Makefiles, and manages a hierarchy of source code folders.  BLDEV is not a replacement for the many excellent more fully-featured build system such as autotools, cmake or bazel.  Nonetheless, with BLDEV you may develop sophisticated applications, with different flavours e.g. Debug and Release, being conveniently separated from each other.  BLDEV has been used on Linux and Windows.  Since BLDEV uses only simple C++ utilities, which are part of the BLDEV distribution, and native scripting (Bourne shell on Linux and Command prompt on Windows), it is straight forward to port/extend BLDEV to other platforms.

### Source code generation
Optionally, the user may install the Clang/LLVM libraries, in order to build the *iogen* utility.  *iogen* parses enums and simple structs &amp; classes to create standard C++ iostream insertion/extraction operators.  This is particularly useful for communicating parameter values between the user and an application.  Another utility, *meconv*, relies on gcc, rather than Clang/LLVN, and converts a set of C++ prerprocessor macro values into enum values.

## Documentation
The **Building.md** file describes, in detail, how to install BLDEV on a machine which does not yet have the BLDEV utilities.

The document, **BuildEnvironment.pdf** reviews the installation of BLDEV, and goes into detail on the regular usage of BLDEV.

## Source code
The source code for BLDEV resides on the umbrella repository [Projects](https://github.com/normvcr/Projects), where the entire Proejects source may be obtained.

Alternatively, the BLDEV-specific source code may be obtained, as follows:

1. Do this only the first time cloning into the
[Projects](https://github.com/normvcr/Projects)
repository.

Add **--sparse** to the Projects clone command
>   git clone --sparse https://github.com/normvcr/Projects.git

2. Change to the Projects folder
>   cd Projects

3. Restrict sparse checkout to folders (not to cherry-pick files)
>   git sparse-checkout init --cone

4. Get the BLDEV source code with the supplied Bourne script
>   checkoutBLDEV

The advantage of this approach, is that projcts may be separately downloaded as convenient, but still reside within a common repository, thus avoiding redundant copies of common source code.

## License
BLDEV is distributed under the MIT license.  The folder *Attributions* contains licenses and acknwledgements for 3rd party code, which, themselves, may have conditions distinct from the BLDEV distribution license.
