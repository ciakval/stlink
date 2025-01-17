# Compiling from sources


## Microsoft Windows (10, 11)

### Common Requirements

On Windows users should ensure that the following software is installed:

- `git` (_optional, but recommended_)
- `7zip`
- `cmake`
- `MSYS2`

### Installation

1. Install `git` from <https://git-scm.com/download/win>
2. Install `cmake` from <https://cmake.org/download/#latest> --> Binary distributions --> Windows x64 Installer<br />
   Ensure that you add cmake to the $PATH system variable when following the instructions by the setup assistant.
3. Install `MSYS2` from <https://www.msys2.org/><br />
   Follow the installation instructions on the website.
4. Install `mingw-w64` via the MSYS2 UCRT64 Shell: `pacman -S mingw-w64-x86_64-make`
5. Fetch the project sourcefiles by running `git clone https://github.com/stlink-org/stlink.git`from the command-line (cmd.exe)<br />
   or download and extract (`7zip`) the stlink zip-sourcefolder from the Release page on GitHub.

### Building

1. Open the command-line (cmd.exe) with administrator privileges
2. Move to the `stlink` directory with `cd C:\$Path-to-your-stlink-folder$\`
3. Execute `mingw64-build.bat`

Depending on the flavour of compilation the final executables will be placed in the following directories:
- Local compilation: `<project_root>\build-mingw\bin`
- Local installation: `C:\Program Files (x86)\stlink\bin`
- Package Generation (portable): `C:\Users\swift\Desktop\stlink\build-mingw\dist`

**NOTE:**

[ST-LINK drivers](https://www.st.com/en/development-tools/stsw-link009.html) are required for programmers to work with `stlink`.


## Linux

### Common requirements

Install the following packages from your package repository:

- `git`
- `gcc` or `clang` or `mingw32-gcc` or `mingw64-gcc` (C-compiler; very likely gcc is already present)
- `build-essential` (on Debian based distros (Debian, Ubuntu))
- `cmake`
- `rpm` (on Debian based distros (Debian, Ubuntu), needed for package build with `make package`)
- `libusb-1.0`
- `libusb-1.0-0-dev` (development headers for building)
- `libgtk-3-dev` (_optional_, needed for `stlink-gui`)
- `pandoc` (_optional_, needed for generating manpages from markdown)

or execute (Debian-based systems only): `apt-get install gcc build-essential cmake libusb-1.0 libusb-1.0-0-dev libgtk-3-dev pandoc`

(Replace gcc with the intended C-compiler if necessary or leave out any optional package not needed.)

### Installation

1. Open a new terminal console
2. Create a new destination folder at a place of your choice e.g. at `~/git`: `mkdir $HOME/git`
3. Change to this directory: `cd ~/git`
4. Fetch the project sourcefiles by running `git clone https://github.com/stlink-org/stlink.git`

### Building

#### Installation:

1. Change into the project source directory: `cd stlink`
2. Run `make clean` -- required by some linux variants.
3. Run `make release` to create the _Release_ target.
4. Run `make install` to full install the package with complete system integration. This might require sudo permissions.
5. Run `make debug` to create the _Debug_ target (_optional_)<br />
   The debug target is only necessary in order to modify the sources and to run under a debugger.
6. Run `make package`to build a Debian Package. The generated packages can be found in the subdirectory `./build/Release/dist`.

As an option you may also install to an individual user-defined folder e.g `$HOME` with `make install DESTDIR=$HOME`.

### How to avoid the error message: "Can not open shared object file"

When installing system-wide (`sudo make install`) the dynamic library cache needs to be updated with the command `ldconfig`.

#### Removal:

1. Run `make uninstall` to perform a clean uninstall of the package from the system.
2. Run `make clean` to clean the build-folder within the project source and remove all compiled and linked files and libraries.

### Cross-Building for Windows

Install the following packages from your package repository:

- `mingw-w64`
- `mingw-w64-common`
- `mingw-w64-i686-dev`
- `mingw-w64-x86-64-dev`

After following the steps for installation above, proceed with from the build dircetory itself:

```sh
$ sudo sh ./cmake/packaging/windows/generate_binaries.sh
```

The generated zip-packages can be found in the subdirectory `./build/dist`.

### Set device access permissions and the role of udev

By default most distributions don't allow access to USB devices.
In this context udev rules, which create devices nodes, are necessary to run the tools without root permissions.
To achieve this you need to ensure that the group `plugdev` exists and the user who is trying to access these devices is a member of this group.

Within the sourcefolder of the project, these rules are located in the subdirectory `config/udev/rules.d` and are automatically installed along with `sudo make install` on linux.
Afterwards it may be necessary to reload the udev rules:

```sh
$ sudo cp -a config/udev/rules.d/* /lib/udev/rules.d/
$ sudo udevadm control --reload-rules
$ sudo udevadm trigger
```

udev will now create device node files, e.g. `/dev/stlinkv3_XX`, `/dev/stlinkv2_XX`, `/dev/stlinkv1_XX`.

### Special note on the use of STLink/V1 programmers (legacy):

As the STLINKV1's SCSI emulation is somehow broken, the best advice possibly is to tell your operating system to completely ignore it.<br />
Choose one of the following options _before_ connecting the device to your computer:

- `modprobe -r usb-storage && modprobe usb-storage quirks=483:3744:i`
- _OR_
  1. `echo "options usb-storage quirks=483:3744:i" >> /etc/modprobe.conf`
  2. `modprobe -r usb-storage && modprobe usb-storage`
- _OR_
  1. `cp stlink_v1.modprobe.conf /etc/modprobe.d`
  2. `modprobe -r usb-storage && modprobe usb-storage`

## Build options

### Build using a different directory for shared libs

To put the compiled shared libs into a different directory during installation,
you can use the cmake option `cmake -DLIB_INSTALL_DIR:PATH="/usr/lib64" ..`.

### Standard installation directories

The cmake build system of this toolset includes `GNUInstallDirs` to define GNU standard installation directories.
This module provides install directory variables as defined by the GNU Coding Standards.

Below are the preset default cmake options, which apply if none of these options are redefined:

- `-DCMAKE_INSTALL_SYSCONFDIR=/etc`
- `-DCMAKE_INSTALL_PREFIX=/usr/local`

Please refer to the related [cmake documentation](https://cmake.org/cmake/help/latest/variable/CMAKE_INSTALL_PREFIX.html) for details.

Author: nightwalker-87
