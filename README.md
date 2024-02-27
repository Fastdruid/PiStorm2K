# PiStorm2K v2.0 - BOSS Edition.

This is a rework of the original PiStorm2K to add some additional potential capabilities while remaining 100% compatible with existing and future firmware. 

The original PiStorm2K was imported from Gerbers into KiCAD and then revised. The initial KiCAD PiStorm2K is also contained within this repo however there were a lots of bits cleaned-up on v2.0 that didn't happen on v1.0. 

# Why

An excellent question. I don't even have an Amiga 2000 (any donations of one welcome however!)
This all spiralled from LazaruStorm not playing nicely with the original Processor on my 68k. This took me down the hole of *why* it didn't, because it _should_. In short the issue is due to the E(nable) Clock that is used for 6800 type devices (which the CIA chips are). The 68k does not stop generating the E-Clock, there is no way to tell at what state it will start up in and so it's a random lottery if the E-Clock generated from the PiStorm happens to "line up". 

I then took a look at the Verilog firmware and thought "this appears reasonably understandable". From there I got the PiStorm to start off by listening for the E-Clock, and either generate one or sync to an existing one. After that I got it to correctly Bus Arbitrate and request the Bus rather than the brute force method LazaruStorm uses of just holding BR asserted. 

Once I'd got that working nicely I then got it to swap between PiStorm and original processor using a long hold Ctrl-A-A. 

At the same time I looked at the Zorro-II specs pinout for the Denise I'd just built and realised it was 98% compatible with the A2000 Co-Processor, had no 7MHz clock on pin 7 but could do the same as the A2000 and generate it from C1/C3.
I ordered one of those and created an adaptor to fit it into the Zorro-II slot and re-wrote the firmware to swap between modes on the CLK_SEL pin (because Denise can't do a long Ctrl-A-A but has a mechanism to disable items via numlock->reset).

I also designed an adaptor to fit the card inside the A500(+). By this stage I was experimenting with a bunch of stuff and wanted something where I could access the remaining lines on the CPLD and at the same time be a useful upgrade. Which is where this comes in.
With appropriate firmware it should be able to do the same in a B2000 as it does in the A500(+) and Denise. ie swap between 68k and PiStorm at will. In theory the firmware may also do DMA...but that is very, very untested. 

# Status
UNTESTED - Initial production ordered 26/02/2024

# Building 
## Options
   1) U6 & C13 are optional. Not so much use on the A2000, desired for the B2000
   2) H1, H2 and H3 are optional as they can be controlled by solder blobs however H4 is required. 
   3) R3 is optional and for 99.9% of use cases is pointless. 
   4) For A2000 R4 can be omitted. 

## Known issues
 1) V2.0 of the BOM incorrectly used 0603 for the resistors and capacitors where the footprint was 0402. Fixed in BOM v2.0.1
 2) V2.0 of the BOM incorrectly used a 680R resistor instead of a 68R for R3. Fixed in BOM v2.0.1
 3) V2.0 of the BOM incorrectly used a 1K1 resistor instead of a 1K for R3. Fixed in BOM v2.0.1
 4) Rotations of the chips are all wrong from the placement file. I need to understand here why KiCAD messed it up but if ordering from JLCPCB you will need to manually rotate them to the correct orientation. 

# Summary of changes
### Additional control lines.

INT2 => Pin 87  
CFGOUTn => Pin 92  
INT6 => Pin 1  
XRDY => Pin 95  
OVR => Pin 11  

### Additional Headers / Jumpers

INT PWR => INTernal PoWeR - Optional to power the RPi from the board or use a seperate Power Supply.   
EXT => Extra - For future expansion  
E RES => This is a bit of a specialist one, this edition can be used in a normal Zorro slot with some modifications. Depending on board you may need to add a 68R resistor to the E-Clock. This jumper **BYPASSES** it. A/B2000 users should either have a jumper or solder the blob.   
CLK SEL => CLocK SELect - Close to use C1/C3 for the clock. Note that the 7MHz clock is *ALWAYS* recommended over the C1/C3 if you have it.   
BJ => Pull down BOSS (pin 20) to GND to take over from the onboard processor.   
JTAG => TDI, TCK, TMS, TDO header (see section on JTAG).   
PWR => +5V, GND, VCC_INT and VCC. Normally VCC and VCC_INT will be identical at 3.3v however if MAX V is used with the second 1.8v regulator VCC_INT will be 1.8v.   
IPL2-0 => IPL2, IPL1, IPL0. - Another specialist one. (see "use in Zorro-II slots")  

### Solder blob jumpers

SJ1 - Close to use C1/C3 - semi-permanent version of **CLK SEL** Jumper. A2000 users should close this. B2000 should leave it open.   
SJ2 - Close to bypass 68R on E-Clock - semi-permanent version of **E RES** Jumper. A2000 and B2000 users should close this.  
BOSS - Close to pull BOSS down to GND - semi-permanent version of BOSS Jumper.  
D-BOSS - Cut to disable pin 20 (see "use in Zorro slot").  
TDI => Cut pin 1-2 to disable TDI to the CPLD. Close 2-3 to redirect RPi Pin 13 (GPIO27) to Pin 89 on the CPLD.  
TMS => Cut pin 1-2 to disable TMS to the CPLD. Close 2-3 to redirect RPi Pin 18 (GPIO24) to Pin 91 on the CPLD.  
TDO => Cut pin 1-2 to disable TDO to the CPLD. Close 2-3 to redirect RPi Pin 22 (GPIO25) to Pin 85 on the CPLD.  
TCK => Cut pin 1-2 to disable TCK to the CPLD. Close 2-3 to redirect RPi Pin 37 (GPIO26) to Pin 86 on the CPLD.  
7MHz => Cut to disconnect pin 7 (see "use in Zorro-II slots").  

### Extra pins
The remaining 4 extra pins are brought out to solder pads.   
P78 - Pin 78 CPLD  
P81 - Pin 81 CPLD  
P82 - Pin 82 CPLD  
P83 - Pin 83 CPLD  

# Use in Zorro-II slots. 

This can be used in an unbuffered Zorro-II slot noting a couple of potential issues.  

1) Zorro is a 100 pin slot, the Co-Processor is 86. It *must* be fitted so pin 1 is at pin 1. That means that there should be a space at the "front" of the Amiga. 
2) Pin 20 on Zorro-II is -12v. If you plug this straight in you will fry the CPLD. Cut the D-BOSS solder jumper and ensure there is no continuity across it.
3) There is no clock on pin 7. Depending on Zorro implementation this may be /OWN. If this is the case then it can be disconnected by cutting the solder blob link marked 7MHz.
4) Depending on Zorro implentation there may be no IPL0-2 lines. These are essential and therefore a header is supplied.
5) Depending on Zorro implementation there may or may not be a 68R between the Zorro slot and the CIA chips. This probably isn't a *massive* issue (after all the early A500's didn't) but a jumper is supplied just in case.

# JTAG programming.

The original PiStorm uses 4 pins from the Pi to allow quick and easy reprogramming of the CPLD. This has numerous advantages however it leaves 4 GPIO pins "off the table" that could be used. Nothing *currently* uses them however the PiStorm32 uses pins 13, 18, 22 and 37 connected to pins 31, 28,  4 &  33 which are used as PI_A[0], PI_A[1], PI_WR and PI_SER_CLK.
In theory these could be used however it makes programming a manual job to connect up a programmer. To make it slightly easier there is a 4-pin header which have the 4 pins required to flash the CPLD from the Pi using the normal procedure. 

If you wish to program the CPLD via this method use the following pinout to connect the Pi to the JTAG connector as follows.

<IMG SRC="https://www.raspberrypi.com/documentation/computers/images/GPIO-Pinout-Diagram-2.png">

Pin 13 on the Pi to TDI  (Pin 1, top of the board)  
Pin 37 on the Pi to TCK  (Pin 2)  
Pin 18 on the Pi to TMS  (Pin 3)  
Pin 22 on the Pi to TDO  (Pin 4)  

You will also need to connect power and ground to the Pi. To aid in this there is a second header next to the JTAG header marked PWR, connect power as follows.
Pin 1 or 2 on the Pi to +5V (Pin 1, top of the board).  
Pin 6,9,14,20,25,30,34 or 39 on the Pi to GND (Pin 2).  

I strongly recommend that you connect these with power off! You can then turn on your Amiga (ensuring the Pi cannot short on anything) which will boot the Pi and then allow you to flash as normal. 

Alternatively you can remove the PiStorm2k from the Amiga and provide power to the Pi via the USB power connector. This will then provide enough power back to the PiStorm2K to flash. 

# New Firmware!

This is the juicy part. There is new exciting firmware which attempts both to properly negotiate BOSS and maybe even allow Bus Arbitration which will (maybe) allow DMA. See the firmware folder for further details (if there isn't one then nothing is yet released).


# DISCLAIMER / LICENSE

Both the original PiStorm and this modification fall under the MIT License. 

THIS DESIGN, ANY HARDWARE PRODUCED FROM IT, ANY FIRMWARE OR SOFTWARE IS PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE DESIGN, SOFTWARE, HARDWARE, FIRMWARE OR THE USE OR OTHER DEALINGS IN THE DESIGN, SOFTWARE, HARDWARE or FIRMWARE.
