# RP2040 Development Setup on Windows

Here is how to install and configure a C/C++ development environment for the Raspberry Pi Pico and other RP2040 dev boards, using the Raspberry Pi Pico SDK and Visual Studio Code on Windows. All of the software except Windows is available at no cost.

This is a fairly involved process. For a simpler solution, you may wish to use the [Arduino](https://www.arduino.cc/) IDE which supports RP2040 boards using the Mbed OS.

_Note:_ These instructions are up to date as of 2022-04-24.

## Acknowledgements

These instructions mostly follow these video tutorials by "Learn Embedded Systems":

* [How to Set Up Visual Studio Code to Program the Pi Pico](https://www.youtube.com/watch?v=mUF9xjDtFfY)
* [How to Set Up a Project in Visual Studio Code for the Pi Pico](https://www.youtube.com/watch?v=Q1Kfg8k54jM)

# Toolchain Installation

## ARM GCC Compiler

Go to the download page for the Arm GNU Toolchain:

[https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/downloads](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/downloads)

Locate the download for Windows host and AArch32 bare-metal target (arm-none-eabi).
Download the installer and run it.

_Note:_ In the installer, select "Add path to environment variable".

## CMake

Go to the CMake download page: [https://cmake.org/download/](https://cmake.org/download/). Download the 64-bit Windows installer.

Run the installer.

_Note:_ In the installer, select "Add Cmake to the system PATH for all users".

## Visual Studio Build Tools

There are two ways to install the Visual Studio Build Tools. Either:

- Install the "Build Tools for Visual Studio" package. It seems that Microsoft no longer supports this package, but as of now the 2019 version is available here: [https://aka.ms/vs/16/release/vs_buildtools.exe](https://aka.ms/vs/16/release/vs_buildtools.exe)

Or:

- Download Visual Studio 2022 Community Edition from here: [https://visualstudio.microsoft.com/downloads/](https://visualstudio.microsoft.com/downloads/). Be sure to install the C++ development tools. This is a much larger install. Visual Studio 2019 will also work, if you have that already.

## Python 3

Go to the Python download page: [https://www.python.org/downloads/](https://www.python.org/downloads/)

Download the 64-bit Windows installer for the current stable version and run it.

_Note:_ In the installer, select "Add Python to PATH", and if prompted to remove the max path length, do so.

## Git

Go to the Git download page: [https://git-scm.com/download/win](https://git-scm.com/download/win)

Download the 64-bit Windows installer and run it.

Select the following options in the installer:
*	_Destination Location:_ Default (or not)
*	_Select Components:_ Default
*	_Default editor:_ Select one you like.
*	_Name of the initial branch:_ Let Git decide
*	_PATH environment:_ Git from the command line and also from 3rd-party software
*	_SSH executable:_ Use bundled OpenSSH
*	_HTTPS transport backend:_ Use the OpenSSL library
*	_Line ending conversion:_ Checkout as-is, commit as-is
*	_Terminal emulator for Git Bash:_ Select either option
*	_Default behavior of "git pull":_ Default (f-f or merge)
*	_Credential helper:_ Default (Git Credential Manager Core)
*	_Extra options:_ Default (Enable file system caching on, Enable symbolic links off)
*	_Experimental options:_ Select "Enable experimental support for pseudo consoles"

## Raspberry Pi Pico SDK

Make a new directory named `Pico` (or anything else) where you wish to store the Pico SDK and your development projects.

Open a Windows command window (or Git Bash shell) and `cd` to that directory.

Clone the Pico SDK and example code into a local git repo:

    git clone -b master https://github.com/raspberrypi/pico-sdk.git
        Or, if an SSL private key has been configured:
        git clone -b master git@github.com:raspberrypi/pico-sdk.git
    cd pico-sdk
    git submodule update -init
    cd ..
    git clone -b master https://github.com/raspberrypi/pico-examples.git
        Or, if an SSL private key has been configured:
        git clone -b master git@github.com:raspberrypi/pico-examples.git

## Test Build

Open a "Developer Command Prompt" by searching for that in the Start menu.

Navigate (`cd`) to the `Pico` directory created above.

Set the path to the Pico SDK (only needs to be done once, not every time a build is done):

    setx PICO_SDK_PATH "..\..\pico-sdk"

_Note:_ The absolute pathname of the `pico-sdk` directory may be used here (e.g. `C:\Dev\Pico\pico-sdk`).

Close and re-open the Developer Command Prompt window to load that change. Navigate to 
the `Pico` directory again.

Build the Pico example projects:

    cd pico-examples
    mkdir build
    cd build
    cmake -G "NMake Makefiles" ..
        Or, if using an RP2040 board other than the Pi Pico (see src\boards\include\boards):
        Cmake -D"PICO_BOARD=adafruit_feather_rp2040" -G "NMake Makefiles" ..
    nmake

_Note:_ This will take a long time.

Find the file `blink.uf2` in the `pico-examples\build\blink` directory.

Connect the Pi Pico (or other RP2040 dev board) to the PC while pressing the Pico's `BOOTSEL` button.

Open the Pi Pico device in Windows Explorer.

Drag `blink.uf2` to the Pico's storage.

The Pico's LED should start blinking.

## Visual Studio Code

Go to the VS Code download page: [https://code.visualstudio.com/Download](https://code.visualstudio.com/Download)

Download the 64-bit Windows installer and run it.

Open a Developer Command Prompt and type `code` to run VS Code.

_Note:_ VS Code must always be run from a Developer Command Prompt window, otherwise it 
will not be able to find the build tools.

## CMake Tools Plugin

In VS Code, click on the "Extensions" button on the left.

Search for the "CMake Tools" plugin and install it.

Navigate to the CMake Tools settings: Click on the "Manage" button in the bottom-left, then select Settings, then Extensions, then CMake Tools.

Under "Cmake: Configure Environment", click "Add Item" and add an item named 
`PICO_SDK_PATH` with the value `..\..\pico-sdk`. _Note:_ The absolute pathname of the `pico-sdk` directory may be used here (e.g. `C:\Dev\Pico\pico-sdk`).

If compiling for a supported RP2040 board other than the Pi Pico, add another item named 
`PICO_BOARD` with the name of the board. The valid values are the names of the files in 
`pico_sdk\src\boards\include\boards` (omitting the ".h" file extension).

Under "Cmake: Preferred Generators", click "Edit in settings.json" and add this section to the file:

    "cmake.preferredGenerators": [
        "NMake Makefiles"
    ],

Save and close the settings file.

## Test Build from VS Code

In VS Code, open the folder `pico_examples`.

If prompted to select a compiler, select the one that has "arm-none-eabi" in its name. If not prompted and if "No kit selected" is shown at the bottom of the window, click on that and select "arm-none-eabi".

Click the "Build" button at the bottom of the window to build the example programs again.

Upload `blink.uf2` to the Pico again and its LED will blink.

# Create a New Project

Here is how to create a new RP2040 C/C++ project. In this example the new project will be named `NewProject`.

Create a sub-directory named `NewProject` in the Pico directory that was created above.

Copy the file `pico_sdk_import.cmake` from the `pico_examples`directory to the `NewProject` directory.

Open a Developer Command Prompt window.

Type the command `code` to run Visual Studio Code. _Note:_ VS Code must be run from a 
Developer Command Prompt window so that it gets the correct tool settings.

In VS Code, open the `NewProject` folder.

Create a new file named `CMakeLists.txt` and add these lines to the file. Use the name of your project in place of `NewProject`.

    cmake_minimum_required(VERSION 3.12)
    include(pico_sdk_import.cmake)
    project(NewProject)
    pico_sdk_init()
    add_executable(NewProject NewProject.c)
    target_link_libraries(NewProject pico_stdlib)
    pico_add_extra_outputs(NewProject)

Initialize the build configuration: Select Configure Tasks from the Terminal menu and wait for it to complete. (This may be done automatically when `CMakeLists.txt` is saved.)

_Note:_ If the Configure Tasks command fails or does nothing, it may be necessary to:
*	Select the GCC "kit" from the list at the bottom of the VS Code window, or
*	Exit VS Code, run it again by typing `code` in the Developer Command window, open 
the `NewProjects` folder, and click "Yes" if prompted to configure the task.

Create a C or C++ file named `NewProject.c` or `NewProject.cpp`. _Note:_ The filename should match the one specified in `CMakeLists.txt`.

Click the "Build" button at the bottom of the VS Code window and watch it compile.

Upload `NewProject.uf2` to the Pico board.

<hr /><div><div style="float:left; padding-right:10px;"><a rel="license" href="http://creativecommons.org/licenses/by/4.0/"><img alt="Creative Commons Licence" style="border-width:0; padding-top:10px;" src="https://i.creativecommons.org/l/by/4.0/88x31.png" /></a></div><div style="padding-left:10px;">© 2022 Len Popp<br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by/4.0/">Creative Commons Attribution 4.0 International License</a>.</div></div>