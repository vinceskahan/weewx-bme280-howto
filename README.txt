
=======================================================================
Snippets to add bme280 support to weewx, generally for something like
running rtldavis+bme280 rather than using a hard-connected datalogger.
=======================================================================

(these instructions are for a waveshare bme280 card)

Wire it up per the vendor instructions at:
  https://www.waveshare.com/wiki/BME280_Environmental_Sensor
or
  https://pypi.org/project/RPi.bme280

Follow the instructions on the pypi.org page until you run 
"i2cdetect -y 1" to verify your board is i2c which is 0x77 per
the pypi.org link above.

For a Waveshare BME280 the wiring setup for i2c is:
Green  = CS         = not connected
Orange = ADDR/MISO  = not connected
Yellow = SCL/SCK    = SCL   (pin 5)
Blue   = SDA/MOSI   = SDA   (pin 3)
Black  = GND        = GND   (pin 6)
Red    = VCC        = 3.3V  (pin 1)

On the pi5, even pins are outboard with pin2 furthest from the 
RJ45 connector so the connections are:

 -----------------------------------------------------
 |        3V3 =>                RED  x               |
 |        SDA =>               BLUE  x               |
 |        SCL =>             YELLOW  BLACK  <= GND   |
 |                                x  x               |
 |                                x  x               |
 |                                x  x               |
 |                                x  x               |
 |                                x  x               |
 |                                x  x               |
 |                                x  x               |
 |                                x  x               |
 |                                x  x               |
 |                                x  x               |
 |                                x  x               |
 |                                x  x               |
 |                                x  x               |
 |                                x  x               |
 |                                x  x               |
 |                                x  x               |
 |                                x  x               |
 |                                                   |
 |                                                   |
 | (RJ45)           (USB)         (USB)              |
 -----------------------------------------------------


Then install simply install the upstream driver ala:
  weectl extension install -y \
       https://gitlab.com/wjcarpenter/bme280wx/-/archive/master/bme280wx-master.zip

Make sure your weewx.conf is set up to match the hex address of the card
which for i2c should be 0x77.  Note that the extension default differs,
so you'll need to edit one line in weewx.conf set the i2c_address correctly.

Your weewx venv pip list will include:

(weewx-venv) pi@pi5:~ $ pip3 list
Package    Version
---------- -----------
bme280     0.7          <==== I 'think' this gets autoinstalled
configobj  5.0.8
CT3        3.3.3.post1
ephem      4.1.5
pillow     10.2.0
pip        23.0.1
PyMySQL    1.1.0
pyserial   3.5
pytz       2024.1
pyusb      1.2.1
RPi.bme280 0.2.4        <==== rpi libs for bme280 installed per upstream docs
setuptools 66.1.1
six        1.16.0
smbus      1.1.post2
smbus2     0.4.3
weewx      5.0.2
wheel      0.43.0

And your weewx extension list will include:

(weewx-venv) pi@pi5:~ $ weectl extension list
Using configuration file /home/pi/weewx-data/weewx.conf
Extension Name    Version   Description
bme280wx          1.0       Add bme280 sensor readings to loop packet data

Note - the weewx.conf snippet assumes you 'have' an external sensor
that provides the must_have reading(s), which would be the case if you
are 'also' running the rtldavis driver successfully.  You might need to
tweak the must_have settings for your configuration if that's not the case.
Be sure to follow the instructions on the bme280wx web page for details.
If you set pressureKeys = pressure, then weewx will do the altitude correction
into its barometer data element most skins want.


Short description is take the defaults, fix i2c_address in weewx.conf,
and go for it.
