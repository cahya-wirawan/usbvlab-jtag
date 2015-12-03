USB-JTAG adapter for debugging and in-system programming of ARM microcontroller using
<a href='http://wiki.ullihome.de/index.php/USBAVR-ISP/de'>USB AVR Lab</a>. It was ported from <a href='http://code.google.com/p/estick-jtag'>estick-jtag</a> / <a href='http://code.google.com/p/usbprog-jtag'>usbprog-jtag</a>

## News ##
  * Apr 18, 2010: The early optimized version of OpenOCD 0.4.0 driver for USBProg was commited: <a href='http://usbvlab-jtag.googlecode.com/files/openocd-0.4.0-usbvlab-0.1.diff'>openocd-0.4.0-usbvlab-0.1.diff</a>

## USB AVR Labs pin ##
Currently the new pin's configuration is difference than the original.

PORTB configuration:
| **Pin** | **Function** |
|:--------|:-------------|
| 2       | TDI          |
| 3       | TMS          |
| 4       | TCK          |
| 5       | TDO          |

PORTD configuration:
| **Pin** | **Function** |
|:--------|:-------------|
| 0       | TRST         |
| 1       | SRST         |

And following is the pin configuration of USB AVR Lab:

<a href='http://usbvlab-jtag.googlecode.com/svn/wiki/images/USBVLab-pin.png'>
<img src='http://usbvlab-jtag.googlecode.com/svn/wiki/images/USBVLab-pin.png' width='233' />
</a>

## How to compile ##
Currently the [revision 1454](https://code.google.com/p/usbvlab-jtag/source/detail?r=1454) of OpenOCD is used, and it needs to be patched with
the driver for usbvlab-jtag. The OpenOCD's source code
can be downloaded from the usbvlab-jtag's repository, it was patched already with usbvlab-jtag code. Following is the step to get/compile
openocd and make usbvlab-jtag's firmware:
```
wget http://usbvlab-jtag.googlecode.com/files/openocd-r1454-usbvlab-0.1.tgz
tar xvzf openocd-r1454-usbvlab-0.1.tgz
cd openocd-r1454-usbvlab
sh ./bootstrap
./configure --enable-usbvlab
make
```

```
wget http://usbvlab-jtag.googlecode.com/files/usbvlab-jtag-0.1.bin
```

This firmware (usbvlab-jtag-0.1.bin) can be programmed to the USB AVR Lab using normal flashtool

## Test ##
LM3S2110 CAN Device Evaluation Board was used to test the transfer rate
of usbvlab-jtag. This Board from Luminary Micro has 64KB of FLASH and 16KB of RAM.

### Run OpenOCD ###
```
$ openocd -f lm3s2110_usbvlab.cfg

Info : USBVLab JTAG Interface ready
Info : JTAG tap: lm3s2110.cpu tap/device found: 0x3ba00477 (Manufacturer: 0x23b, Part: 0xba00, Version: 0x3)
Info : JTAG Tap/device matched
Warn : no tcl port specified, using default port 6666
```

### Loading to the FLASH ###
```
$ telnet localhost 4444
Connected to localhost.
Escape character is '^]'.
Open On-Chip Debugger
> halt

> flash probe 0
flash 'stellaris' found at 0x00000000

> flash erase_sector 0 0 63
erased sectors 0 through 63 on flash bank 0 in 0.277004s

> mdb 0x0 64
0x00000000: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff 
0x00000020: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff 

> flash write_image test-flash.bin 0 bin    
Algorithm flash write 2048 words to 0x0, 16246 remaining
Algorithm flash write 2048 words to 0x2000, 14198 remaining
Algorithm flash write 2048 words to 0x4000, 12150 remaining
Algorithm flash write 2048 words to 0x6000, 10102 remaining
Algorithm flash write 2048 words to 0x8000, 8054 remaining
Algorithm flash write 2048 words to 0xa000, 6006 remaining
Algorithm flash write 2048 words to 0xc000, 3958 remaining
Algorithm flash write 1910 words to 0xe000, 1910 remaining
wrote 64984 byte from file test-flash.bin in 58.598499s (1.082979 kb/s)

> mdb 0x0 64                                      
0x00000000: 00 40 00 20 f1 00 00 00 3d 04 00 00 3d 04 00 00 3d 04 00 00 3d 04 00 00 3d 04 00 00 3d 04 00 00 
0x00000020: 3d 04 00 00 3d 04 00 00 3d 04 00 00 3d 04 00 00 3d 04 00 00 3d 04 00 00 3d 04 00 00 3d 04 00 00 
    
> verify_image test-flash.bin 0 bin
verified 64984 bytes in 0.717032s

```


The test was made with following information:
  * Mac OS X 10.5.8, 2.33 GHz Intel Core 2 Duo, 2GB RAM
  * OpenOCD rev 1454
  * ARM GNU gdb (GDB) 7.0.1
  * AVR gcc version 4.3.2 (GCC)
  * Firmware was compiled with optimization: -Os
  * Target board: LM3S2110 CAN Device Evaluation Board
  * jtag\_speed 0
  * File size to flash: 64984 bytes
  * File size to ram: 15832 bytes