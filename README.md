# i2c-lcd
A client/server package to control an I2C LCD display attached to your Linux system using I2C_LCD_driver.

### What is it
This is a rough stab at a software service to allow you to do interesting things from, say, a shell script, with an I2C LED panel.  The ones I mean are simple, character-based panels such as one might plug into a *Raspberry Pi* board, or, in my case, a *Rock64* board, or other similar embedded systems.  I wrote it -- and have tested it -- on something called a *Recon Sentinel RECON1*, a security appliance that is basically a *Rock64* board in a box.  These were quite cheap for a time when sellers still had large overstocks; cheaper than just buying the *Rock64* on its own.  They come with such a display attached, and of course you might as well use it.

### What's included
Two simple Python scripts, **lcd**, and **lcdd**.  The former takes information from you about what you want to do with the display and passes it to the latter, which manages the display for you.  The client will start the server the first time you try to do something with the display, or if it has for some reason stopped.  There's no need to manage it by way of a *systemd* unit or anything of that sort, though, if you want to write a unit for it, you should find that reasonably simple to do.  If you want to stop the server, you can do that by sending the correct command to the client as well, or you can just kill it.


### Set up
You need the following things on your system:
   * *Python 3*, or so
   * *I2C_LCD_driver*, which I got from [here](https://gist.github.com/DenisFromHR/cc863375a6e19dce359d)
   On my system, this driver is installed in **/usr/local/lib/lcd**, but you can put it elsewhere and just change the *sys.path.insert* at the top of the **lcdd** file.
   * The contents of this repository.
   
   

### Hey, why not just use *I2C_LCD_driver* directly

Because not everything is (or should be) written in Python, and because when the driver initializes on my system, it appears to clear the screen.  By making a single, persisitent process that manages the display, I can share it among various different sources of information without always clearing everything else off.

