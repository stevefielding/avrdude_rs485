## avrdude_rs485
Modified avrdude 6.1 to support RS485 network on Raspberry Pi. Designed for custom PiHat with 
seperate RS485_TX_EN and RS485_RX_EN. The board also has a RS485, RS232 receive mux.
You can modify for your hardware by editing the appropriate code in ser_posix.c
Uses a 6 byte sequence with nodeId to specify the RS485 node. See stk500.c stk500_getsync()
Added command line option -z \<nodeId\> to pass RS485 nodeId.
# Build and install
```
sudo apt-get update
sudo apt-get install -y build-essential bison flex automake libelf-dev libusb-1.0-0-dev libusb-dev libftdi-dev libftdi1
cd avrdude-6.1
./configure
touch configure.ac
touch aclocal.m4
touch Makefile.am
touch Makefile.in
*comment*    edit LDFLAGS line in Makefile  LDFLAGS = -lwiringPi
make
sudo make install
```
Ensure that you touch the files in the order shown above to adjust the time stamps on the files, otherwise automake will decide that it needs to run.
LDFLAGS = -lwiringPi in the Makefile is effectively removed when ./configure is run. You will need to edit Makefile after running ./configure.  
# Test
```
avrdude -C /home/pi/.arduino15/packages/m328pb/hardware/avr/1.1.4/tools/avrdude.conf -v -patmega328pb -carduino -P/dev/ttyUSB0 -b57600 -Uflash:w:/tmp/arduino_build_937706/Blink.ino.hex:i -z 1
```

See the documentation file for the details.

The latest version of AVRDUDE is always available here:

  http://savannah.nongnu.org/projects/avrdude


Important environment variables for ./configure:
================================================

CPPFLAGS: C preprocessor flags (*not* "C++")

This is the place to put additional (non-standard) -I options into.
For example, if your Windows system has LibUSB-Win32 installed into
\\WINDOWS\ProgramFiles\LibUSB-Win32, use

CPPFLAGS=-I/WINDOWS/ProgramFiles/LibUSB-Win32/include

to tell configure where to search for the header files.  (The use of
forward slashes rather than backslashes can often simplify things.
Note that the Windows system services internally treat both the same.
It's only cmd.exe which requires backslashes as the directory
separator.)

LDFLAGS: Linker options

This is the place to make additional library locations known to the
linker.  To continue the above example, use

LDFLAGS=-L/WINDOWS/ProgramFiles/LibUSB-Win32/lib/gcc

to make the linker search for "libusb.a" in that directory.


Linux users: make sure the header files are installed
=====================================================

While many Linux distributions install the libraries needed by AVRDUDE
(libusb, libelf) by default, they leave out the corresponding header
files.  Consequently, the configure script won't find them, so these
libraries could not be used.

Usually, the packages with the header files (and static libraries) are
derived from the regular package name by appending "-devel".  Thus,
make sure you have "libusb-devel" and "libelf-devel" installed before
running the configure script.  (Same goes for libftdi.)
