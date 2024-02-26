# PiStorm2K v2.0 - BOSS Edition.

This is a rework of the original PiStorm2K to add some additional potential capabilities while remaining 100% compatible with existing and future firmware. 

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
