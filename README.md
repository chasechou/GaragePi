## GaragePi - The Raspberry Pi Garage Door Opener

When I was little, I used to push the garage door button from inside the garage and
then run out the garage while it's closing... carefully jumping over the sensor so
I don't trip the safety mechanism. It was easy back then and a little fun. But
nowadays, not so much. There are times when I wish I had a way to close (and open)
the garage door remotely and preferably with something I always have on me... my
mobile phone!

So, I looked into building something so I could use my smartphone as a garage door
opener. I've read about the Arduino and Raspberry Pi platforms before that I believed
would accomplish the task and, ultimately, I decided on the Raspberry Pi. The Arduino
platform after some research and cost analysis would have cost more than the Raspberry
Pi! That wasn't something I was expecting since the Raspberry Pi is more robust and
a full-blown mini-Linux computer. The possibilities are really endless.

A wifi Raspberry Pi connected to a 2-channel relay connected to my actual garage door
remote allows me to open and close my garage door with just a browser on my smartphone.

Below I'll go through the steps and the issues I ran into while working on this project.


## Step 1 - Parts

Below is a list of the parts I gathered together to get started. Prices will most likely
not be same.

* [Raspberry Pi (model B) + Case + Wireless Wifi Adapter](http://www.amazon.com/gp/product/B00D2CN730/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B00D2CN730&linkCode=as2&tag=codeblog-20) - $50
* [8GB or greater SD card](http://www.amazon.com/gp/product/B00200K1SY/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B00200K1SY&linkCode=as2&tag=codeblog-20) - $6
* [a power supply](http://www.amazon.com/gp/product/B00DZLSEVI/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B00DZLSEVI&linkCode=as2&tag=codeblog-20) with at least **1A** and **5V** output and a micro-usb connector. The power supply that comes with most smartphones nowadays should work just fine. Just check the label for the power ratings. The Raspberry Pi is documented to have a maximum power draw of 1A so it's better to have a power supply that is greater than 1A just to be safe else you might run into system stability issues causing reboots/freezes.
* [a 2-channel relay](http://www.amazon.com/gp/product/B0057OC6D8/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B0057OC6D8&linkCode=as2&tag=codeblog-20) - $10
* [female-to-female jumper wires](http://www.amazon.com/gp/product/B00BQA5BWU/ref=as_li_ss_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B00BQA5BWU&linkCode=as2&tag=codeblog-20) - $8
* ethernet cable
* a LED to test out the GPIO pins
* solder gun + solder (if hooking it up to a garage door remote)
* usb keyboard and mouse, HDMI monitor and HDMI cable. These are only necessary for the
initial setup until you have the Pi networked after which you can go headless and SSH in
from another computer.

Total cost for me: **$74** since I had a few of the needed parts already.


## Step 2 - Setup the SD card

I followed this guide to setup the SD card with NOOBS and bootup the Pi for initial setup.

[Raspberry Pi Quick Start Guide](http://www.raspberrypi.org/quick-start-guide)


## Step 3 - Setup the Pi and Install Raspbian

Setting up the Pi is pretty easy. Just hookup the HDMI cable, usb keyboard and mouse, plug-in
the SD card prepared in step 2, and finally turn on the Pi by plugging in the micro-USB power
supply. This should bootup NOOBS and step you through the process to install an OS. The one
you want for this project is Raspbian. Complete the Raspbian setup and you should have a
working Pi at the end.

*It's at this step that I encountered my first problem. I used the keyboard and mouse I had
from my main PC rig, a Logitech G110 keyboard and a Logitech G9 mouse. After NOOBS booted up,
I found I couldn't type or control the mouse. The problem turned out to be the USB ports on
the Pi doesn't provide enough power for the keyboard and mouse. You can get around this issue
by using a powered USB hub.*


## Step 4 - Setting up Wifi

You can skip this step if you're going to leave the Pi connected with ethernet cable. In fact,
I recommend skipping this step until the end to simplify things.

I didn't want the Pi to be tethered by an ethernet cable so I proceeded to try and set it up
with the USB wifi dongle. This turned out to be probably the hardest part of the project. I
followed a bunch of guides I found but none of them worked for me. It's not that the dongle
wasn't supported by Raspbian, but I couldn't get it to connect to my wireless router. After
much headache and tinkering around, I finally fixed the problem. You can read about the
details in my [wifi setup guide](https://coderwall.com/p/v290ta?i=2&p=1&q=&t%5B%5D=%21%21mine&t%5B%5D=%21%21bookmarks).


## Step 5 - Experiment with the GPIO on the Pi

What makes the Raspberry Pi so interesting to me are the GPIO pins which allow the Pi to
communicate and control other peripherals. I found this excellent
[guide](http://www.raspberrypi-spy.co.uk/2012/06/simple-guide-to-the-rpi-gpio-header-and-pins/)
to get a basic understanding of the GPIO.

My first test with the pins was to simply connect a LED I had lying around and see if I could
get it to light up.

LEDs generally have 1 leg which is longer than the other. The longer leg should be the + (positive)
connection. If the LED doesn't have a visible difference in the leg lengths, then you can look at
the top of the LED and you should see 2 small pieces of metal. The smaller side is the +
connection. Here's a good [diagram](http://www.societyofrobots.com/images/electronics_led_diagram.png).

Start by connecting a jumper wire to GPIO pin 1 for the 3.3v power and another wire to the GND
at pin 6. This should light up the LED. Yay! Ok, admittedly, that's not terribly exciting. It's
just a light.

Next, unplug the jumper wire from pin 1 and connect it to pin 7 which is GPIO4. The LED should be
unlit for now. To control the GPIO ports, there are a ton of different choices. Just google for
Raspberry Pi GPIO but I started with this wonderful framework called [WebIOPi](https://code.google.com/p/webiopi/).

Follow the [WebIOPi installation guide](https://code.google.com/p/webiopi/wiki/INSTALL?tm=6) and
run this:

    $ sudo webiopi -d -c /etc/webiopi/config

That should start the webiopi server on port 8000. You can then access the webiopi page in a
browser at http://192.168.1.60:8000. Of course, replace 192.168.1.60 with the IP of your Pi.

You should see a GPIO Header link on the main page. Click that link and you will be able to control
the GPIO using a web UI which looks like the board header.

![Picture](https://coderwall-assets-0.s3.amazonaws.com/uploads/picture/file/2508/Step_5_-_webiopi.jpg)

Click the 'IN' box to the left of GPIO4 to toggle that to say 'OUT'. Then try clicking on the box to
the right of GPIO4 with the number 7 in it. If everything's working then you should now be able to on/off
the connected LED.

Toggling the LED on/off was super exciting for me. It was the first time I had built anything on a
computer that could interact with the real world. The Internet of Things as they say. For better or
worse, it's coming and probably sooner rather than later.


## Step 6 - Wiring the 2-Channel Relay

Now it's time to replace the LED with the relay. Remove the LED from the jumper wires and reconnect
the wires according to this drawing below.

![Picture](https://coderwall-assets-0.s3.amazonaws.com/uploads/picture/file/2500/Step_6_-_wiring_relay.png)

*It was at this point that ran into my next problem. Every time I connected the relay and switched GPIO4
to 'OUT', the Raspberry Pi would crash and I'd have to unplug/reboot it. It took me awhile to figure out
that the power supply I was using wasn't supplying enough power for everything. Apparently, powering the
relay pushed it over the edge. The power supply I was using was from an old Motorola smartphone that was
rated to only output 750ma. After I switched that out to another power supply I had lying around with
1850ma, I no longer had stability issues with the Pi.*

You can test your connections now by going back to the WebIOPi page and toggle GPIO4 on/off and you should
be able to see a small light on the relay respond accordingly. If you do, then that's a good sign. It means
you are able to control the relay to open and complete a circuit.


## Step 7 - Connecting to Garage Door Remote

For this step, you can either run wires to the actual garage door opener button that's in your garage, or
use a spare garage door remote. Since I planned on leaving the Pi indoors where I have easy access to it, I
chose to hook it up to a remote. I pried open the remote casing and studied the circuit board a bit. The
first thing I did was to test if I could activate the remote without actually pressing the button so I
shorted these 2 points by touching them with a wire.

Voila!

I heard the sounds of the garage door creaking open. So, theoretically, I should be able to wire that to the
relay and have the relay open/close the circuit. Boistered by this knowledge, I looked at how I could solder
2 wires to the circuit and noticed that the remote was gracious enough to leave some extra holes that runs
in serial with the actual button. Then I soldered 2 wires to the remote and connected them to the left and
middle connections on the relay like this below.

![Picture](https://coderwall-assets-0.s3.amazonaws.com/uploads/picture/file/2514/Step_7_-_connecting_remote.png)

Now toggling GPIO4 in WebIOPi should actually open and close your actual garage door. You might notice when
you're doing this that the garage door only activates when the signal from GPIO4 is off. The relay I bought,
the Sainsmart 2-Channel Relay and if what I've read is correct, most other relays work like this. When the
signal is ON, the relay is open which means the remote won't activate. When the signal is OFF, then the relay
is closed and the circuit is complete which should activate the remote. Keep this bit of info in mind when
we work with setting up the WebIOPi code later.


## Step 8 - GaragePi Setup

GaragePi is built using the WebIOPi framework by Eric @ trouch.com. It is setup with this file structure below:

    pi@raspberrypi ~ $ tree garagepi
    garagepi
    ├── config
    ├── html
    │   └── index.html
    └── python
        └── garagepi.py

Download and extract the [GaragePi zip file](https://github.com/chasechou/GaragePi/archive/master.zip) to
**/home/pi/garagepi**.

Or you can also clone it from Github:

    git clone https://github.com/chasechou/GaragePi.git /home/pi/garagepi

WebIOPi by default is setup with a login and password. But I only plan on using this when I'm on my local wifi
network so I opted to remove the password protection completely. You do this by removing /etc/webiopi/passwd or
empty it, then restart the webiopi server.

    sudo rm /etc/webiopi/passwd

Run this command below to load up webiopi with the new config file:

    sudo webiopi -d -c /home/pi/garagepi/config

Then load this up in your browser... again, change the IP to the IP of your Pi:

    http://192.168.1.60:8000

You should see a simple page like this below, please feel free to dress up the page however you want.

![Picture](https://coderwall-assets-0.s3.amazonaws.com/uploads/picture/file/2507/Step_8_-_app.jpg)

Clicking the button on this page should act as if you were pushing the button on your garage door remote.

If that worked, then you can make GaragePi run at startup.

    sudo update-rc.d webiopi defaults

    # As a side note, you can start/stop the background service by doing:
    $ sudo /etc/init.d/webiopi start
    $ sudo /etc/init.d/webiopi stop

Then replace the default config file at /etc/webiopi/config with a symlink to the config file at /home/pi/garagepi/config.

    sudo rm /etc/webiopi/config
    sudo ln -s /home/pi/garagepi/config /etc/webiopi/config

Reboot and test it out.

    sudo reboot


## Step 9 - Save to Homescreen on Your Smartphone

I recommend you then save this page on the home screen of your smartphone, or at least bookmark it. Instructions
for [Android](http://mobile-pixels.com/pin-webapp-website-android-homescreen/) / [iOS](http://www.gottabemobile.com/2013/11/23/save-website-shortcut-ios-7-home-screen/).

![Picture](https://coderwall-assets-0.s3.amazonaws.com/uploads/picture/file/2505/Step_9_-_save.jpg)


## Have fun and hope it works for you!
