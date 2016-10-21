# tlapser360
Used to shoot time lapse 360 photos with cameras that support the Open Spherical Camera API.

This is a proof of concept script I wrote to run on  a raspberry pi with a Ricoh Theta S camera. It supports geotaging
with gpsd and camera metering via an adafruit LUX meter. Metering features are still a work in progress and require a familiarity with the Raspberry pi GPIO interface. With metering enabled you can force the exposure to ramp in a single dirrections, this is useful for things like sunrise and sunset. Communication with the camera can be done with via wifi or usb. USB features are new and with the rest of the script are experimental, I've had problems with my camera becoming un-responsive and needing power cycling. If you do not need the GPS or metered exposure settings of this script and only want the ability to capture, download, and delete images from the camera you may want to look at using ptpcam alone 'ptpcam --loop-capture=5 --interval=3'.


Early test fotage can be found here: https://www.youtube.com/watch?v=IugTnvYjy6A

Use at your own risk!


### Prereqs: 

- GPS support relies on gpsd and gpsd-clients being installed and configured.

- The bc package is needed for maths.

- LUX meter support was tested with a adafruit TSL2561 and some tweaks to IainColledge's
  example script. See this variable "LUX_METER_SCRIPT"
  https://github.com/IainColledge/Adafruit-Raspberry-Pi-Python-Code

- Theta s Usb support with libptp, Thanks to codetricity for the howto 
  http://lists.theta360.guide/t/ricoh-theta-s-api-over-usb-cable/65/3



### Usage:

- -I <Interval seconds> : This sets the sleep interval time between photos. Be aware this does not take into consideration any latency added by features of this script such as image downloads, usb control, gps metat data injection, ilong exposures, etc. This means if you set a 10 second interval your photos may be taken every 12 seconds. You can also set this to a lower threshold than the Ricoh Theta S can shoot using it's built in intervalometer. In testing I've been able to shoot a photo ever 3 seconds in low resolution and 5 seconds in high res without over running the on cammera buffer. 
- -U <y/n> default n. Usb mode for Ricoh Theta S. This will control the camera over usb and requires libptp and gphoto2.
- -W Wifi mode <y/n> Default y unless USB mode is enabled. Control the camera using the direct wifi connection and Opens Spherical camera API.
- -C <Image count>. How many total images you would like to take.
- -m <Exposure Program mode 1 2 4 9> 1 for Auto, 2 for Manual
- -G <0/1> GPS metadata injection. This relies on GPSD being installed and properly configured. It may also introduce latency so adjust your interval accordingly.
- -T <GPS Track log path and file name> Write a gps track log, relies on GPSD.
- -r <image resolution size h/l> Set the resolution of the image. I may depricate this and use -K.
- -i <iso> Camera iso ex. 100, 125, 160, 200, 250, 320, 400, 500, 640, 800, 1000, 1250, 1600 
- -s <shutter speed> Floating point or integer, based on wifi api. ie 0.0004=1/2500, 0.04=1/25, 0.4=1/2.5, 4=4 seconds.
- -w <White Balance> In WIFI mode (auto, daylight, shade, etc) USB mode in hex? (2, 4 ,8001) Check the api guides for details.
- -O <Output path /> Path to where you would like output image files.
- -d <0/1 if images are downloaded delete them from the camera.> 
- -F <Config file - NOT SUPPORTED YET> 
- -M < 0/1 use a TSL2561 LUX sensor for metering. if used we will add time to your interval > 
- -R <Ramp exposure speed up (longer shutter - Sunset) or down (shorter/faster shutter - Sunrise) based on external LUX meter.> 
- -A <Sunrise time (24hr no : ex, 6:00AM=600)> -P <Sunset time (24hr no ":" ex, 7:00PM=1900)>

#### Example:
- Using USB take a large resolution photo every 5 seconds for a total of 5 images with the camera set to manual mode, iso 100, white balance auto, and a shutter speed of .5 seconds while writing the images to a directory and leaving them on the camera.. 
	./tlapser360.sh -U y -I 5 -C 5 -m 1 -r h -i 100 -s 0.5 -w 2 -O /mnt/tmp/tlapser_test/

Copyright 2016 - Jason Charcalla
