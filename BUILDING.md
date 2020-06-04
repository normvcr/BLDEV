# BLDEV Development Environment: Building from source

BLDEV is a system for building C++ applications.  The BLDEV delivery contains source code for utilities that BLDEV relies on.  Consequently, a bootstrap process is needed to construct these build utilities.
The document, **BuildEnvironment.pdf**, has further  details on the bootstrap process, 
as well as descriptions of the various build utilities and scripts, and the normal (non-bootstrap) usage of BLDEV.

First download/untar/clone the *BLDEV* source code, the root of which is referred to as SourceTop.  The machine on which this source code resides is the *source* machine.

The remainder of the work is done from your *development* machine.  You must be able to *cd* to the source machine from the development machine.  On Windows with Visual Studio, work from the appropriate Command Prompt, chosen from the VisuslStudio drop-down menu, from the Windows Start menu (I use x64 native).  On Linux, you can work from any terminal.

The steps of the bootstrap process are:

1. Install prerequisites
1. Set up the PLATBIN environment variable
1. Create/edit configuration files
1. Build the main development utilities
1. Create a Development Window
1. (Optional) Build additional development utilities

After completing the bootstrap process, the PLATBIN folder will contain, at the minimum, the following three files:

* bldev -- Creates a development tree, paralleling a source tree
* setdev -- Creates a development window script
* setdev_TAG -- Development window script

**bldev** and **setdev** are the main development utilities.  **setdev_TAG** is a script that creates a development window, and initializes its run-time.  **TAG** is a short string that you choose as the name of the development environment in which you have bootstrapped **BLDEV**.  **setdev_TAG** can also be used for further development.  On Windows, **bldev** and **setdev** have the **.exe** extension, and **setdev_TAG** has the **.bat** extension.  On Linux, **setdev_TAG** is a Bourne shell script.

There are two optional features to consider when installing BLDEV:

- IO source code generation (persistance)
- Keyboard key code management

Both of these options are controlled by respective Make variables, as explained in section 3 for the Platform.inc configuration file.

## 1. Prerequisites

Packages to install for both Linux and Windows are:

* boost -- filesystem, log
* cli11 -- command line processing
* clang/llvm (optional) -- C++ source code parsing

### Linux
On fedora, do:

```
   dnf install boost-devel cli11-devel clang-devel llvm-devel
```

### Windows
First install [Visual Studio](https://visualstudio.microsoft.com/downloads).
Next, install the version of [Boost](https://sourceforge.net/projects/boost/files/boost-binaries/1.73.0/) that is compatible with the installed version of VS.  To find the correct Boost version, open the Windows "Apps & Features" from the Windows search field.  Hover over the *Microsoft Visual C++* Redistributable that you are using, likely the most recent version x64, and notice the number at the end, in my case, **14.2...** .  So, I am using the Boost package: **boost_1_73_0-msvc-14.2-64.exe**.

Here is the headers-only [CLI11](https://github.com/CLIUtils/CLI11) package.

#### Msys2

Gnu make, and its associated utilities, are a standard part of linux, but not of Windows. The [msys2](https://www.msys2.org/) package remedies this.  Notably, this package supplies bash/sh, make, gcc and many other utilities.  A version of make may alrady be on your system, so you may, for example, rename msys2's *make.exe* to be *gmake.exe*.  Any msys2 packages that you are missing, you can install using the supplied **pacman** utility.  For example:

```
   pacman -S make gcc
```

#### Clang/LLVM (optional)
Here are the distributions for [clang/llvm](https://releases.llvm.org/download.html).  However, I believe that the binary distributions do not include all the headers & libraries that are needed for development.  I downloaded and built *source* version 10, even though the version is 8 on my linux machine.  It turns out, the BLDEV source code works correctly with both versions.  It does take a fair amount of time to follow the build instructions and wait for the build to complete.  But, I feel it is worth it :smile:.

You will need a version of **tar** that handles the demands of untarring the Clang/LLVM archives.  Of the **tar** packages that I tried out, the one that worked without errors is [GnuWin tar](http://gnuwin32.sourceforge.net/packages/gtar.htm).  There is a **tar** that is shipped with windows, so, for example, you can rename the GnuWin *tar.exe* to be *gtar.exe*.

0. Install [python](https://www.python.org/downloads/windows).
1. Download the LLVM and Clang source distributions to seperate folders, uncompress each one using *xz -d* (from msys2) and untar each of them with *gtar.exe* (which you renamed from GnuWin).
2. Move the Clang-top-source-folder to be LLVM-top-source-folder/tools/clang .
3. Next to LLVM's top-source-folder and tar file, create a build folder.
4. cd build, and issue the command

```
   cmake -G "Visual Studio 16 2019" -Thost=x64 ..\llvm-10.0.0.src
```

of course, replacing the paramters for your particular situation.  For my most recent build, I had help from the [LLVM forum](https://llvm.discourse.group/t/llvm-not-building-in-vusual-studio/1139/1).  I ended up using a [CMake GUI](https://cmake.org/download/), rather than the command-line cmake shipped with Windows, so that I could do two things conveniently:

- Cut down the build time by setting **LLVM_TARGETS_TO_BUILD = X86**
- Work around a bug by activating **LLVM_OPTIMIZED_TABLEGEN**

There are, no doubt, more variables that can be set/unset to further cut down the build time of the libraries.

.....6. Inside the build folder are, now, various VS project files.  Load **llvm-libraries.vcxproj** into VS, and build (and wait).  You can use the results in-place -- no need to install.

The advantage of building Clang/LLVM is that you can build the **iogen** utility, which generates insertion/extraction operators for enums and simple structs & classes.  If you do not install Clang/LLVM, BLDEV will still be fully functional, but **iogen** will not be available to help in your own source code development.  You only need to build one flavour of **iogen**, say, Debug, since the Clang/LLVM libraries are not needed once **iogen** is built (Of course, you may still need the shared objects for the run-time of **iogen**).


#### Other Windows stuff

My favourite editor is emacs, and on Windows I pick it up at the GNU site [emacs](http://mirror.rit.edu/gnu/emacs/windows/emacs-26).  To make it more convenient , I use the *emax.bat*  batch file:

```
   GNUBIN\runemacs %1
```

where GNUBIN is replaced by the absolute path to the GNU bin folder.

##### Drive letters

With BLDEV, drive letters are used to elliminate white space in path names, and to access remote machines.

###### Local drive letters
Unfortuantely, Windows has popularized using white space in folder names.  To work around this, use drive letters.  Here is an example of setting up a drive letters to refer to local folders:

```
   subst k: "C:\Program Files (x86)\Windows Kits"
   subst v: "C:\Program Files (x86)\Microsoft Visual Studio"
```
Your subst commands can be collected into a batch file, and a shortcut placed into your Windows startup folder, which you can access by entering, *shell:startup*, into a *Run* window.

###### Remote drive letters
If, for example, the BLDEV source code resides on a Linux machine, you will need to assign a drive letter to access the source code when working with BLDEV.  In my case, Windows 10 is a virtual machine under Linux, and Oracle's VirtualBox, available via the package installer on linux, is able to assign permanent drive letters to requested Linux folders.

##### System Volume Information
To prevent Windows from creating the *System Volume Information*  folder on your Linux file system, especially the working copy of your source code :smile:, you may folllow the instructions of [Rich Gates](https://www.getusb.info/how-to-delete-and-keep-system-volume-information-folder-off-flash-drives).


## 2. PLATBIN Environment variable

Once you will have built the development utilties, they will be installed into a folder, PLATBIN, that is not part of any development environment.  Consequently, to create subsequent build environments, the bootstrap process is not needed, as the build utilites may be accessed from PLATBIN.

The PLATBIN environment variable should point to a folder, and should also be part of the PATH environment variable.  The PLATBIN folder will hold various development utilities and scripts.  It is easiest to set up PLATBIN as part of your usual logging-in procedure.

## 3. Configuration Files

The platform folders are in the config subfolder of SourceTop.  You can either edit an existing one (fedora30 or windows10), or create a new platform folder using one of these as a template. Suppose your platform folder is called, MyPlatform .  There are up to 4 files to edit in MyPlatform:

1. Makefile.cfg -- The configuration file: First part of Makefile
1. Platform.inc -- Included into Makefile after Makefile.cfg
1. platformInit -- Script to set up development window run-time
1. projectInit_TAG -- If platformInit is not enough for all your development environments.

The Init scripts have a **.bat** extension on Windows.  On linux, the scripts are for the Bourne shell.  The only flexibility in these names is that *Makefile.cfg* is arbitrary, and the *TAG* is arbitrary.

The first two files are required, as they are used in creating the Makefiles.

The two script *Init* files are used to set up the run-time of your development environments.  A platform can have many development environments e.g. Debug and Release.  The TAG is a name for the development environment e.g. TAG=dbg or TAG=rel.

**platformInit** might be required.

**projectInit_TAG** is not required, provided all the run-time initialization is already part of **platformInit**.

The **Makefile.cfg** is quite short, e.g. defining debug/optimization flags, and libraries specific to these compile/link flags.

**Platform.inc** has various Make variables which you edit, indicating where to find headers and libraries, and also library names.  The following variables should be confirmed:

* CLI11_CPPFLAGS -- The -I flag for finding the CLI11 header files
* BOOST_ROOT_DIR -- Check it properly defines BOOST_LIB_DIRS and BOOST_CPPFLAGS
* CLANG_ROOT_DIR -- If building iogen
* HAVE_IOGEN -- Comment this out if not building iogen on this platform
* VKEY_HEADER -- Comment this out if it does not point to the keyboard key codes

In each constructed Makefile, the last part is the inclusion of **config/Makefile.inc**, which contains the rules by which Make knows what to do e.g. invoke the compiler, linker and other build utilities.  You should not need to edit **config/Makefile.inc**.

## 4. Main utilities

cd to SourceTop and issue the command

```
   make CONFIG=config/MyPlatform/Makefile.cfg DEVTOP=devTop boot
```

where **devTop** is a folder on your development machine.  Usually, **devTop** does not yet exist, or is empty.  **devTop** will be the root of your development folder, paralleling the structure of **SourceTop/dev** on your source machine.  You could, theoretically, take **devTop=SourceTop/dev**, but it is highly recommended to separate your source folder from your development folder, even if your source machine is the same as your build machine.

After successful completion of this step, PLATBIN will contain the utilities **bldev**, **setdev**, and , on Windows, **setpalette**.  Also, at **devTop**, your entire development tree will have been created (but not built).

## 5. Development Window

You will often be working with multiple development environments e.g. Debug and Release.  These can be conveniently separated by working in custom development windows.  To create the development window script, setdev_TAG,, issue the command

```
   setdev TAG -f -d devTop --bg RRBBGG --fg RRBBGG
```

where,

* **TAG** is a short name of your choice for this development environment
* **devTop** is the folder you specified in the make command of Section 3.
* **--bg** and **--fg** specify the background and foreground colours of the development window.
* **RRBBGG** are six hex digits for the red, blue and green components of the colour.

**--bg** defaults to white (FFFFFF), and **--fg** defaults to black (000000).  If you are not happy with the colours, close the development window and try, again.  I have found, that leaving the foreground as black, you can form well-contrasted type by choosing pastel colours for the background i.e. colours close to white e.g. --bg DDFFFF gives a light  cyan background.

The script, setdev_TAG, is stored in PLATBIN, and can be invoked directly, or by the command

```
   setdev TAG
```
The development window (Linux xterm, or Windows command prompt) has the specified colours, and the window title, **TAG**.
Please note that you may not invoke **setdev**, or **setdev_TAG**, from another development window, as this would create ambiguity in the development environments.


## 6. Additional Build Utilities

This optional step builds the 3 utilities

* iogen -- Generates insertion/extraction source code for enums, and "simple" structs & classes.
* meconv -- Converts a set of C++ preprocessor macro values to enum values
* appendvk -- Creates enum values for the alphanumeric ascii characters

The **iogen** utility requires the Clang/LLVM libraries, so, if these libraries are not available, these three utils cannot be built as part of the bootstrap process.  However, **meconv** and **appendvk** can be built, later, as part of regular development.

After creating your development window, you are at, **devTop**, the top of your development folder.  Issue the command

```
   make init
```
After successful completion of this step, **PLATBIN** will contain the utilities **iogen**, **meconv** and **appendvk**.
