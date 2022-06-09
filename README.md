# i2c-lcd
A client/server package to control an I2C LCD display attached to your Linux system using I2C_LCD_driver.

### What is it
This is a rough stab at a software service to allow you to do interesting things from, say, a shell script, with an I2C LED panel.  The ones I mean are simple, character-based panels such as one might plug into a *Raspberry Pi* board, or, in my case, a *Rock64* board, or other similar embedded systems.  I wrote it -- and have tested it -- on something called a *Recon Sentinel RECON1*, a security appliance that is basically a *Rock64* board in a box.  These were quite cheap for a time when sellers still had large overstocks; cheaper than just buying the *Rock64* on its own.  They come with such a display attached, and of course you might as well use it.

### What's included
Two simple Python scripts, **lcd**, and **lcdd**.  The former takes information from you about what you want to do with the display and passes it to the latter, which manages the display for you.  The client will start the server the first time you try to do something with the display, or if it has for some reason stopped.  There's no need to manage it by way of a *systemd* unit or anything of that sort, though, if you want to write a unit for it, you should find that reasonably simple to do.  If you want to stop the server, you can do that by sending the correct command to the client as well, or you can just kill it.  There's also a configuration file, **lcd.conf**.  Unfortunately, the people who write Python modules can't be bothered not to Windows, so the format of the configuration is rather more *.ini*-like than it should be.


### Set up
You need the following things on your system:
   * *Python 3*, or so
   * *I2C_LCD_driver*, which I got from [here](https://gist.github.com/DenisFromHR/cc863375a6e19dce359d) ([direct raw link](https://gist.githubusercontent.com/DenisFromHR/cc863375a6e19dce359d/raw/36b82e787450d127f5019a40e0a55b08bd43435a/RPi_I2C_driver.py)), but there's a slightly modified version in the lcd-driver subdirectory which you should use instead.
   On my system, this driver is installed in **/usr/local/lib/lcd**, but you can put it elsewhere and change the line in the configuration that locates it.  If it's in *sys.path*, just comment that line out in the configuration and it will be fine.  The version available in this repository is modified slightly to allows us to pass a device address in when we instantiate the driver.  This address may now be configured in *lcd.conf* so that you won't need to edit the code of the driver directly.
   
**Note:** At least on my Recon Sentinel, the I2C device address for the LCD is 0x3F. The driver uses a display at 0x27 by default.  In order to find the correct device address, apparently one can run something like *i2cdetect -y 1* on a system with the proper utilities installed, where 1 (being the lowest, perhaps that's not where the device is attached on your system) is the i2c bus number.  The output of that command will contain a table.  You're looking for addresses in that table which don't report *--* or *UU*.  One of those should be your display.


   * The contents of this repository.
   
Once you have that, do the following:
   
  1. Place the *lcd.conf file in your **/etc** directory.  
  1. Put the scripts anywhere you like, preferably in the system path.  Make them executable.  
  1. Edit the configuration file to contain:
     * The correct dimensions for your LCD
     * The device address of your LCD (see above for how to find it), and bus number if it's different from the default. 
     * The correct location of the server script
     * The correct locations for the PID file and interface pipe for the server to use
     * If necessary, the place where you installed *I2C_LCD_driver*
     
### Usage

Just run lcd directly.  In its simplest form, it should work much like echo.  You can say, for example:

*lcd This is a test.*

... or:

*lcd \`uptime\`*

```usage: lcd [-h] [-r] [-x] [-l [LINE]] [Text [Text ...]]

Control an I2C LCD display

positional arguments:
  Text                  Text to display.

optional arguments:
  -h, --help            show this help message and exit
  -r, --refresh         Just redraw what is supposed to be on the display
                        currently.
  -x, --exit            Stop the currently running display server, if one
                        exists.
  -l [LINE], --line [LINE]
                        The line on which the text should be displayed.
```
                        
### Hey, why not just use *I2C_LCD_driver* directly

Because not everything is (or should be) written in Python.  Bourne is a noble language and most of the data I want the LCD to show is really easy to get that way. Also because when the driver initializes on my system, it appears to clear the screen.  By making a single, persisitent process that manages the display, I can share it among various different sources of information without always clearing everything else off.

